---
layout: post
title:      "I Can't Believe I Built a Real Program"
date:       2019-05-11 17:51:28 -0400
permalink:  i_cant_believe_i_built_a_real_program
---


It really happened. I started with a stark naked Atom window and, bit by bit, built what I feel confident calling a working program. 

The process of creating a CLI data gem has been a journey not without headache. In fact, let’s dive into the very first and perhaps most memorable headache.

Headache #1: Initial Setup

The very first step of project set-up was running the following command in my terminal:

```
bundle gem houston_dog_adoption
```

In one fell swoop, this created a local repository populated with all the files and structure needed for my gem to be a gem. It was not intuitive for me, however, to know what to do moving forward. In bash, I could see that the repository existed, but where? And how on earth was I supposed to push it up to a repository in GitHub?

Not even a few weeks later I feel so silly for racking my brain over these simple concepts. 

The gem was brought to life locally on my computer, as evidenced by the line output in bash:

```
Initializing git repo in /path/houston_dog_adoption
```

Storing the repository locally allowed me to move it to its logical home and open up the project in Atom.

After plenty of trial & error, I eventually understood and could properly execute the steps to pushing the local repo to GitHub.

1.  Change directories in bash to the new repo created by bundle gem.
```
cd /path/houston_dog_adoption
```

2. Create a new repository in GitHub.

3. Embrace the very helpful instructions in the new empty GitHub repo for pushing code.

4. Run the following commands in bash:

```
git add ./path/Houston_dog_adoption
```
This stages additions or changes to code. I think of it as the “ready” in “Ready, set, go!”

```
git commit –m “first commit”
```
This commits changes and associates them with a message you type to describe the changes. I think of it as “set” in “Ready, set, go!”

```
git push –u origin master
```
This magically sends your work to your repo that lives in GitHub. If you’re a newbie like me, you get a thrill from refreshing the repo page and seeing the new code!

The other major aggravator of the set-up headache was structuring and understanding all of the files with respect to their organization, purpose, and interaction. Obviously <bundle gem> did a lot of this for me, but I suspect only a programmer can get their program to actually work. The coding I have done thus far has familiarized me with the purpose of ./bin and ./lib; bin is responsible for running your program, and lib houses files that contain the meat of the program, in this case, a .rb file for each of my three classes. This was still true for the gem repo, with some slight additional complexities.

Once I added tidbits of functional code to my files, sorting out how the files would require each other was *shockingly* frustrating. To be honest, I was able to understand exactly what needed to be done within the files *in theory* by adding <require_relative ‘path’> lines, but what tripped me up was the path syntax. The file tree created by bundle gem was more nested than I was used to, so as the newbie that I am, I had to think about how to notate accordingly. After more trial & error, I am pleased to report that I was able to get the engine running.

During the initial set-up of my project, I felt pretty frustrated with the fact that I was struggling to complete step zero. How could I write a whole program if I could barely lay the foundation? As it turned out, most of my classmates shared similar frustrations, and when I spoke with my older brother about it, who is a Senior Developer, he expressed that everyone hates the initial set-up, so much so that it has the assigned nickname ‘yack shaving’. 

This part of my experience has taught me that if you’re really struggling with something seemingly simple, chances are you are not alone, and it is definitely not a reflection of your capability. Doing something totally new to you is never easy, and it certainly gets easier with practice.

Once I was poised to start churning out code, I found that I was able to apply pretty much everything we have learned thus far in both Procedural and Object-Oriented Ruby, which was awesome. Of course I got stuck and frustrated plenty of times, but ultimately I felt totally equipped to break down problems into pieces and leverage the concepts we’ve learned. 

In fact, I would like to wrap up this post with acknowledgments to some concepts that, upon initial introduction, intimidated me, but are now powerful tools at my disposal.

•	Methods, arguments, & helper methods
•	Strings, Arrays, & Hashes
•	Enumerators 
•	Flow control
•	Instance variables & methods
•	Class variables & methods
•	Self
•	RegExp
•	Nokogiri

Ruby, you da real MVP.

