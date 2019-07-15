---
layout: post
title:      "Coffee Lovers Only: Sip with Caution"
date:       2019-07-14 16:02:53 -0400
permalink:  coffee_lovers_only_sip_with_caution
---


Of this project, the following was perhaps the most difficult block of code to write, because when a user sips a new cup, four different models with complex associations are involved, so making sure existing rows in the database aren’t duplicated or overwritten is a bit complicated.


```
post '/cups' do
        if logged_in?
            if params[:roaster][:name] == ""
                if !params[:coffee][:roaster_id]
                    redirect "/cups/new"
                end
                @coffee = Coffee.find_or_initialize_by(name: normalize(params[:coffee][:name]), roast: params[:coffee][:roast], roaster_id: params[:coffee][:roaster_id]) 
            else
                roaster = Roaster.find_or_initialize_by(name: normalize(params[:roaster][:name]))
                if roaster.persisted?
                    if @coffee = roaster.coffees.find_by(name: normalize(params[:coffee][:name]), roast: params[:coffee][:roast])
                    else
                        @coffee = roaster.coffees.build(params[:coffee]) 
                    end
                else
                    @coffee = roaster.coffees.build(params[:coffee])
                end 
            end
            
            @cup = @coffee.cups.build(params[:cup])
            @cup.user = current_user
            if @cup.save
                flash[:message] = "Your cup has been posted!"
            end
            redirect "/cups/#{@cup.id}"
        else
            redirect '/login'
        end
    end

```

In the `POST ‘/cups/’` route, the controller action that the form rendered by `GET ‘/cups/new’` posts to upon submission, we start from the outermost level of the associated models and gradually build our way in, from Roaster, to Coffee, to Cup, to User. In this CupsController route, we are working with user input passed in by a params hash that is structured like this:
```
{"cup"=>{"brew"=>"Drip"}, "coffee"=>{"name"=>"intenso", "roast"=>"dark", "roaster_id"=>"10"}, "roaster"=>{"name"=>""}}

```

First, we check to see if `params[:roaster][:name]` is blank, meaning the user did not 1) select the option to “post a new Roaster” and 2) fill in the corresponding text box with the name of a new Roaster. Because the new cup must belong to a coffee that in turn must belong to a roaster, if a new roaster was not selected, then an existing roaster must be selected. If this is not the case, `params[:coffee][:roaster_id]` would have a falsey value of `nil`, so the new cup form would be reloaded. Otherwise, when an existing roaster is selected (and new roaster is not selected), we will first see if there is an existing instance of Coffee in the database that matches the attributes that make up a valid Coffee instance – *name*, *roast*, and *roaster_id*; if this instance of Coffee doesn’t already exist, it will be instantiated with the attributes input by the user with this line of code:
```
@coffee = Coffee.find_or_initialize_by(name: normalize(params[:coffee][:name]), roast: params[:coffee][:roast], roaster_id: params[:coffee][:roaster_id])
```

Then, going back to the user’s option to post a new roaster, when this option is selected, we will first check to make sure a matching instance of a Roaster doesn’t already exist in the database, and instantiate one if it indeed does not with this line of code:
```
roaster = Roaster.find_or_initialize_by(name: normalize(params[:roaster][:name]))
```

So we will end up with the local variable `roaster` that either points to 1) an existing row in the roasters table in the database or 2) a brand new instance of a Roaster not yet associated with any coffees and not yet persisted to the database. If the former is true, we will check if a row already exists in the coffees table that matches `params[:coffee]` and belongs to the Roaster instance; 
```
if @coffee = roaster.coffees.find_by(name: normalize(params[:coffee][:name]), roast: params[:coffee][:roast])
```
and if such a Coffee instance has not already been persisted, we will build one off of the roaster with this method:
```
@coffee = roaster.coffees.build(params[:coffee]) 
```

In the latter case of the local variable `roaster`, we will still execute the above line of code. The only difference is a new instance of Coffee is being built off a new, un-persisted instance of Roaster.

At this point, we have identified and/or built the instance of Coffee that our new cup will belong to, as well as the instance of Roaster that the instance of Coffee belongs to. The second to last attribute we need to build a valid instance of a Cup is the *brew* attribute, which we can pass into the Cup initialize method with this line of code and simultaneously assign the new cup to its corresponding coffee with this line of code:
```
@cup = @coffee.cups.build(params[:cup])
```

And finally, to render our new cup fully valid, we associate it with the user that it belongs to:
```
@cup.user = current_user
```

At this point, we’ve done everything we can to maximize the possibility that we are in fact building a valid instance of Cup, so we will save it, and if calling #save on the cup successfully persists `@cup` to the cups table, we will inform the user of success.
```
if @cup.save
        flash[:message] = "Your cup has been posted!"
end
```

And finally take the user to view the new cup’s show page.
```
redirect "/cups/#{@cup.id}"
```

And there you have it, a valid ActiveRecord Cup object, persisted to the database, poised for the user to edit or delete if they so please. 

