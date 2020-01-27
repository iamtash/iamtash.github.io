---
layout: post
title:      "React-Redux: Go With the Flow"
date:       2020-01-26 21:07:34 -0500
permalink:  react-redux_go_with_the_flow
---

In my most recent project, which happens to be my final portfolio project for the Flatiron School Software Engineering Program, I had the pleasure of building a React-Redux frontend backed by a Rails API. I decided to take a stab at building a rough draft of what could eventually become my portfolio website. Over the course of the last section of the curriculum, I found myself struggling to grasp the big picture of React and how and why the Redux pattern worked. Building this project helped me wrap my head around these tools in a way I could not have gotten from anything else. 

I will break down an example of React-Redux flow from my project, and though it may seem like a lot, I'm pretty sure it is so many orders of magnitude less than the Vanilla JavaScript equivalent.

Users visiting my site have the option of 'liking' my projects, which looks like this.

<iframe src="https://giphy.com/embed/J2hFwj9d0VyVUn6t2C" width="480" height="136" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/J2hFwj9d0VyVUn6t2C">via GIPHY</a></p>
So basically all that's happening is pretty soon after the 'like' button is clicked, the amount of displayed likes increments by one and the 'like' button is no longer clickable, right? Well, yes, but there's actually a LOT going on that enables this seemingly simple feature!

Enter React.
```
import React, { useState } from 'react'
import { connect } from 'react-redux'
import { likeProject } from '../actions/projects'
import CardContent from './CardContent'

const GuestProjectCard = props => {
    const [disabled, setDisabled] = useState(false)

    const handleLike = () => {
        setDisabled(true)
        props.likeProject(props.id)
    }

    const button = (
        <button 
            className="btn btn-primary float-right" 
            onClick={handleLike}
            disabled={disabled} >
            Like
        </button>
    )

    return (
        <>
            <CardContent {...props} buttons={button} />
        </>
    )
}

export default connect(undefined, { likeProject })(GuestProjectCard)
```
First, we declare 1) a new state variable called `disabled` initially set to `false` and 2) a function `setDisabled()` that updates it using the State Hook. The `button` element is assigned a `disabled` attribute that points to that state variable, thereby rendering a perfectly able button to the DOM. When clicked, the like button's `onClick` event handler calls `handleLike`, which in turn will update `disabled` to `true`. Due to this change in internal state, React will trigger a re-render of the component, now rendering a disabled like button to the DOM. 

Enter Redux. The component is wrapped in `connect()` whose second argument is the action creator `likeProject `. `connect()` passes in the dispatch function to `mapDispatchToProps`, giving the component access to the prop `likeProject` that will execute `dispatch(likeProject())` when called. The event handler additionally passes in the `id` of the project, which is available via `props`, to the action dispatch.

So what exactly happens when our event handler function calls `props.likeProject(props.id)`? Liking the project involves some asynchronous communication with the server, i.e. we want the like to be persisted to the database such that the row that represents the project internalizes the new like. This means we have to do a bit more than just dispatch an action to our reducer to update the Redux store.

Enter Thunk. Redux-Thunk middleware allows us to not only return a function inside of our action creator instead of a POJO, but it passes that function our dispatch function as an argument. This sets us up to dispatch actions however many times and in whatever order we want. 
```
export const likeProject = id => {
    return dispatch => {
        axios.patch(`${url}/${id}`, { like: true })
            .then(resp => {
                const { id, likes } = resp.data
                dispatch({
                    type: 'LIKE_PROJECT',
                    project: { id, likes }
                })
            })
            .catch(error => console.log('project liking error', error))
    }
}
```
In this case, we first want to send a `PATCH` request telling the server which project to like -- the `id` passed by the component will show up in `params` read by the controller action in our API. Assuming all goes well server side and the promise resolves, the client will receive a JSON response containing the updated project. We'll use this response to create an object that will serve as the action payload in the subsequent action dispatch.

Back to traditional Redux.
```
export default function projectsReducer(
    state = {
        projects: [],
        requesting: false
    }, action) {
        switch (action.type) {
            case 'LIKE_PROJECT':
                return {
                    ...state,
                    requesting: state.requesting,
                    projects: state.projects.map(project => {
                        if (project.id === action.project.id) {
                            return {
                                ...project,
                                likes: action.project.likes
                            }
                        } else return project
                    })
                }
            default:
                return state
        }
    }
```
For efficiency I only included the relevant case in the reducer. When we hit our reducer function, the switch statement evaluates `action.type`, matches it to the `'LIKE_PROJECT'` case, and will proceed to execute the statement inside the case clause. The` return` statement is careful to return a new state object rather than overwrite current `state`. The value we assign to the `projects` key is generated by mapping over `state.projects`and returning the current `state` of each project, UNLESS of course, the project's `id` matches the `id` of the project provided by our `action`, AKA the project that was liked by the user and updated by the server, then that project's `likes` property will be updated to reflect the `action payload`.

Now that the Redux `store` has been updated, the React component that connects to that slice of `state` 
```
import React, { Component } from 'react'
import { connect } from 'react-redux'
import Projects from '../components/Projects'
import ModalFormContainer from './ModalFormContainer'

class ProjectsContainer extends Component {

    render() {
        const { projects, currentUser } = this.props
        return(
            <div className="container" style={{marginTop: '20px'}}>
                {currentUser.loggedIn ? <ModalFormContainer /> : null}
                <Projects projects={projects} />
            </div>
        )
    }
}

const mapStateToProps = state => {
    return {
        currentUser: state.currentUser,
        projects: state.projects
    }
}

export default connect(mapStateToProps)(ProjectsContainer)

<ProjectsContainer 
    <Projects projects={projects} 
        <ProjectContainer key={project.id} project={project} 
            <AdminProjectCard {...project} />
        />
    />
/>
```
. . . and passes it down to child components 
```
<ProjectsContainer 
    <Projects projects={projects} 
        <ProjectContainer key={project.id} project={project} 
            <AdminProjectCard {...project} />
        />
    />
/>

// pseudo JSX to convey chain of passing props to children
```
. . . will effectively pass down updated `props`. And the `AdminProjectCard` component where it all began will undergo a change in `props`, which will trigger a re-render from React to reflect any changes to the DOM -- in this case "Likes: 2" will change to "Likes: 3". 

Ten months of part-time bootcamp later, I can break a like button down like nobody's business. 

