---
layout: post
title: Essence of a code review
categories: isays
modified:
excerpt: When the code is flowing, easy to read, easy to follow, and easy to understand, the reviewer does not notice the code at all – the code is good.
tags: []
image:
  feature:
readtime: true
---

An aspiring flute player comes to a new teacher’s house for her first lesson. The teacher instructs the student to begin playing and then proceeds to attend to household chores – sweeping the floor, doing the dishes, watering the plants, etc. After an hour, the teacher tells the student that the lesson is over. The student asks the teacher how she did. The teacher replies "when I didn’t notice your playing, it was good. When I did notice your playing, it was bad." That is the essence of a code review.

When the code is flowing, easy to read, easy to follow, and easy to understand, the reviewer does not notice the code at all – the code is good. When the code causes the reviewer to pause, to ponder, to question, to hesitate, when the code is difficult to follow and understand, the code is bad.

Following are some points that needs to be imbibed for the code review process to be smooth and painless.

## Getting Ready (as reviewee)

So before you raise your brainchild to be pulled into real world (create pull request)!
Few points to ponder (the seven commandments)

* Never break the repository - Always hit the CTRL+SHIFT+B (if using visual studio) once and check your error console.

* Commits should be associated to Tasks/Bugs - Make sure you have a Ticket in your Task/Bug Tracking System for your change, if not, create one.

* Review before review - Review your own PR before anyone else does.

* The time to do an accurate code review increases exponentially with the size of the change as well as the size of the material impacted by the change - so keep your PR short.

* If the effort for a code review exceeds a threshold, it becomes increasingly likely that the review will be cursory rather than detailed and lose value - so keep your PR short.

* When in doubt, raise alarm! (ask other team members)

* Last but not the least, take your time, haste makes waste!

## Doing the job (as reviewer)

* The ability/effectiveness to act on feedback (from code review and other source) decreases rapidly with the passage of time - So review quickly.

* Formatting: Make sure code formatting is consistent and easy to read.

* Style: Are the variables declared as readonly? Are method variables defined close to the code where they’re used or at the start of the method?

* Naming: Do the field/constant/variable/param/class names conform to standards? Are the names overly short? Do the names (of fields, variables, parameters, methods and classes) actually reflect the thing they represent?

* Test coverage: Is there a test for this code? Do the tests cover a good subset of cases? Do they cover happy paths and exceptional cases? Are there cases that haven't been considered?

* Can you understand what the code does by reading it?

* Can you understand what the tests do?

* Are the exception error messages understandable?

* Are confusing sections of code either documented, commented, or covered by understandable tests? Are the wiki

* Calls outside of the service/application are expensive (Calls to the database, Unnecessary network calls)

* Using resources efficiently/effectively (Does the code use locks to access shared resources? Could this result in poor performance or deadlocks? Does the code close connections/streams?)

* Review actively - Not added as reviewer, don't be neutral, great learning opportunity awaiting you, go ahead put in your appreciations or apprehensions.

> "Listen! Do you smell something?" Ghostbusting requires keen senses.

---
