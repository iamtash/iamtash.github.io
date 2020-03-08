---
layout: post
title:      "Interview Question Fest"
date:       2020-03-08 01:35:46 -0500
permalink:  interview_question_fest
---


I’ve been on the job interview train for the past 2.5 weeks, and as such have been asked hundreds of questions, some of which I struggled to answer well. Let’s dive into some.

1. What is an IIFE and why would you want to use one?

I didn’t know what my interviewer was referring to because even though I have read at least a little on the JS IIFE, I wasn’t aware that it’s pronounced “iffy”. An IIFE (immediately invoked function expression) is a function that’s invoked as soon as it’s defined. IIFEs are useful for data privacy as variables defined within the private scope of the function can’t be accessed from outside the function, and the fact that they can’t be invoked again furthers the privacy advantage. Because IIFEs are not saved to a globally scoped name or variable, they don’t “pollute” the global namespace, and thereby avoid any potential naming conflicts.

2. What is a closure and why would you want to use one?

According to MDN, a closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment), so a closure allows an inner function access to variables in its outer function’s scope. The outer function that defines the closure encapsulates the inner functions and data, thereby representing a JS pattern for data privacy.



More to come.

