
1. First-Class Functions and Anonymous Functions(

    
Search in video
Introduction - My Interview Experience
0:00
once i was giving interview for a very
0:01
reputed indian startup i won't take the
0:04
name but that startup was a very famous
0:06
online food ordering startup the
0:09
interviewer over there asked me
0:11
that what is an anonymous function i
0:14
just quickly said that a function
0:16
without a name is an anonymous function
0:18
that was easy right then she moved on to
0:21
the next question which was what are
0:24
first class functions in javascript
0:26
okay
0:27
so
0:28
now i got nervous
0:30
because i never knew what first class
0:32
functions are
0:34
i have heard of callback functions i
0:35
have heard of higher order functions
0:37
i've heard of arrow functions pure
0:39
functions but i haven't heard of first
0:41
class functions what are they
0:43
and i got nervous the interviewer she
0:46
was looking directly into my eyes and
0:48
i was gone
0:51
i said that i don't know okay i don't
0:53
know the answer to this question so then
0:55
she moved on to the next question which
0:58
was what is the difference between a
1:00
function statement and a function
1:02
expression
1:04
and now that nervousness from here went
1:07
till here
1:08
okay i i didn't know i i didn't know
1:11
what a function expression statement is
1:14
so many things were going upon in my
1:15
head
1:16
i thought that statement expression both
1:19
were looking like the same so when the
1:20
nervousness went to the next level i
1:22
firmly said that
1:24
a statement which has functioned in it
1:27
is a function statement
1:29
and oh my god
1:32
the reaction of that interviewer was
1:34
like
1:34
she is just going to kill me there and
1:36
there itself she made a she made such a
1:39
weird face i mean like if i close my
1:42
eyes i can still like recall that face
1:44
okay and i knew that i am like gone in
1:47
the interview like
1:48
finished
1:51
so this video is dedicated to that
1:53
interview and that interviewer so let's
1:55
see what all these things are and uh
1:58
let's dive deep into the code now okay
What is a Function Statement in JS
2:02
so let's move so we will be covering all
2:04
these topics let's go upon these slowly
2:07
one by one okay so first of all let us
2:09
see what a function statement is okay so
2:13
so you know how we create a function
2:15
there is one way to create a function
2:17
like this so if we write a function
2:18
keyword and we give a name so this is a
2:21
function statement
2:23
okay
2:24
so if i so suppose our function does
2:27
nothing but just a console log okay
2:29
let's just console log and let's give it
2:31
a call
2:32
that's it right so this way of creating
2:36
a function is known as a function
2:38
statement that's it
2:39
that's all
2:41
okay
What is a Function Expression
2:42
and remember i have told you several
2:44
times throughout these videos that
2:46
functions are very beautiful in
2:48
javascript and functions are like the
2:50
heart of javascript right
2:52
so
2:53
a beautiful feature of function is that
2:56
you can assign it to a variable also
2:59
okay so suppose if i have a var b we can
3:02
assign a function to it okay we can
3:05
assign a function to it function acts
3:07
like a value okay you might have heard
3:10
this before i am revising it function
3:12
acts like a value okay so you it's it's
3:15
like a value which you are
3:16
initializing you are initializing this b
3:19
with a value like putting function
3:21
this into b so this is how beautiful
3:24
functions are in javascript in many
3:26
programming languages you can't do this
3:28
and people coming from like other
3:29
languages might find this crazy but
3:33
what this you can do okay
3:35
let's just log something inside this
3:37
also so let me do a console log of
3:39
be
3:40
called okay
3:42
so this is known as a function
3:44
expression and this is known as a
3:45
function statement both are ways to
3:47
create function and i was using this
3:50
since many years in our programming but
3:52
i never knew what the difference between
3:54
these
3:54
jargons are like and in the interview i
3:57
i was using this right a lot in my code
4:00
but i never knew in the interview that
4:02
this is what it is
4:03
right
Difference between Function Statment and Function Expression
4:05
so
4:06
the interviewer did not ask me what it
4:08
is the interviewer asked me the
4:09
difference between function statement
4:12
and a function expression so what is the
4:13
difference between
4:15
these two
4:16
so the major difference between these
4:19
two is hoisting
4:21
okay
4:22
so let's first see how do you call this
4:24
function okay so you call this function
4:26
by just calling it like this you know
4:28
and even in the case of function
4:29
expression you call it like this right
4:32
if i call it if i run this program so
4:34
see it prints a call and it prints b
4:36
called right so that is how we call it
4:38
but the difference between creating
4:40
functions with these two types is
4:42
hosting
4:43
so how does hoisting come into picture
4:46
so what if i call this function a
4:50
even before creating it
4:52
okay so
4:53
here i am creating the function but i
4:55
want to call it before creating it and
4:58
similarly for b also
5:00
so you have seen my hosting video right
5:02
so what do you think the output will be
5:04
if i call these functions before even
5:06
creating them in code what will happen
5:09
can you give it a guess
5:11
yes let's just see so one will throw out
5:13
an error which one the b
5:16
so that is the difference between a
5:17
function statement and a function
5:19
expression so during the hosting phase
5:22
during the memory creation phase a is
5:24
created a memory and this function is
5:27
assigned to a
5:28
but in case of a function expression
5:30
this b is treated like any other
5:32
variable
5:33
it is assigned undefined initially
5:36
until the code hits this line itself
5:39
so when the javascript engine executes
5:41
this line by line and it reaches this
5:44
line then only this function is assigned
5:47
to this variable b until then it is
5:49
undefined
5:51
okay so here the value is undefined and
5:53
you can and you can't call that b right
5:56
that is the major difference between
5:57
function statement and a function
5:59
expression so now let's move on to
What is a Function Declaration
6:01
function declaration so function
6:03
declaration is nothing this is another
6:05
jargon and function statement is also
6:08
known as
6:09
function declaration okay so they both
6:12
are the same thing if you say
6:14
function declaration or a function
6:16
statement these are both the same things
What is an Anonymous Function in JavaScript
6:18
so now let's move on to the anonymous
6:20
functions so anonymous functions as you
6:22
know a function without a name is an
6:24
anonymous function
6:26
this is an anonymous function
6:28
okay
Syntax Error & Anonymous functions
6:29
but let me tell you a little more about
6:31
anonymous functions so anonymous
6:33
functions
6:35
does not have their own identity okay
6:38
when i say it does not have its own
6:39
identity that means if you create an
6:41
anonymous function like this this will
6:44
result out to be a syntax error okay so
6:47
via syntax error let me tell you an
6:50
anonymous function looks like a function
6:51
statement right it looks like similar to
6:54
that right see if you clearly see this
6:56
looks exactly same as a function
6:57
statement but it has no name
7:00
but according to ecmascript
7:02
specification a function statement
7:03
should always have a name so this is a
7:05
invalid syntax let me run and show it to
7:07
you so if i save this run this so this
7:10
is c a syntax error and clearly see the
7:13
message which it says a function state
7:15
function statements require a function
7:17
name
7:18
okay so see it's a function statements
7:21
okay this function statements it is type
7:23
of a function statement and it requires
7:26
a name okay you need to give a name over
7:28
here you can't leave it anonymous and
7:30
create anonymous functions like this
Use/ Advantages of Anonymous Functions
7:32
so now when you can't create it like
7:34
this what is the use of it
7:36
so anonymous functions are used in a
7:38
place where functions are used as values
7:42
so i have said this a lot of times let
7:44
me repeat it once again functions are
7:46
like very beautiful in javascript i love
7:48
functions and functions i think are
7:51
heart of javascript they are so powerful
7:53
you can use them as values also right
7:57
and anonymous functions are used when
7:59
the functions are used as values okay
8:02
when they are used as values so when i
8:05
say they are used as values that means
8:06
that you can use use it to assign it to
8:10
some variable so just like you assign
8:11
any other value to a variable okay you
8:14
can assign this anonymous function also
8:16
to a variable
8:18
so it acts like a value here you can use
8:20
anonymous functions but in function
8:23
statements you cannot use an anonymous
8:25
function okay so that is why it says
8:27
function statements
8:29
require a function name okay you know
8:31
this knowledge of knowing what is a
8:33
function statement or expression a
8:35
declaration and all these things is very
8:37
important also it helps you a lot if you
8:39
are debugging programs so if you read
8:41
clearly the message right so function
8:43
statement so here it's not an expression
8:45
it's a statement so these things these
8:48
keywords will kind of click your mind
8:50
and you will better understand
8:51
javascript
8:53
and generally you might have seen that
8:54
when we are reading blog articles or we
8:56
are reading books sometimes
8:58
we it is very confusing right the author
9:01
uses the term expression somewhere it is
9:03
a statement somewhere so this is what it
9:05
means okay so this knowledge will be
9:07
very helpful when you are reading some
9:09
books of javascript some blogs of
9:11
javascript some stack overflow answers
9:12
this all knowledge will be very helpful
9:14
for that so now let's just see what is a
9:16
named function expression okay so let me
9:18
just first of all remove these errors
9:20
okay let me just comment it out and
9:23
let me bring these calls uh over here
9:26
once again
What are Named Function Expressions in JS
9:28
so now let me tell you what is a named
9:30
function expression so a named function
9:32
expression is like exactly the same as
9:35
this function expression but when this
9:37
anonymous function instead of using an
9:39
anonymous function here we use a
9:41
function with a name suppose if i gave
9:43
it a name so this becomes a named
9:45
function expression
9:47
it's kind of weird right
9:49
you are like kind of giving a name to
9:51
the function here also and you are
9:53
giving the name to the function here
9:55
also it's it's like weird right but this
9:58
is possible in javascript like you can
10:00
have the name of a function itself and
10:02
then put it into an expression so this
10:04
is known as a named function expression
10:07
remember a named function expression now
10:09
here there is a corner case also there
Corner Case Gotcha using Named Function Expression
10:11
is a gaucha okay so uh so you know we
10:15
can call this b function like this but
10:18
what do you think what will happen if i
10:20
do something like this x y z so can you
10:22
guess what will happen if you just call
10:24
this function x y z like this give a
10:26
guess
10:27
so you will get an error okay so it is a
10:29
very famous output question also so you
10:32
might get a mcq where
10:34
the person might have called this xyz
10:36
like this but it will throw out to be an
10:39
error okay so this will give a reference
10:41
error that xyz is not defined okay so
10:43
why it gives xyz is not defined so
10:47
if we see clearly this x y z if you put
10:50
a function like this right if you use
10:52
this function as a value so in this case
10:55
this x y z is not created in the
10:58
outer scope okay so this xyz is not a
11:02
function inside this outer scope in this
11:04
close global scope but it is created as
11:07
a local variable okay when i say a local
11:09
variable if you try to if you want to
11:11
access this function inside this
11:13
function okay if you want to access this
11:16
function inside this function
11:19
okay so suppose if you want to access
11:21
xyz you can access it over here right
11:25
you can access this function see it
11:27
prints xyz and you can access this
11:29
function over here but if you try to use
11:31
it outside like this it will throw out
11:33
to be an error where it is okay let's go
11:35
to console so it it gives you a
11:37
reference error that xyz is not defined
11:39
okay so this is what is called as a name
11:42
function expression and if you just
11:44
don't give a name over here it is like a
11:46
normal function expression or you might
11:48
call it as an anonymous function to
11:50
create a function expression okay so
11:53
something like this so let me just
11:55
revert it once again
11:57
um and just remove this
11:59
xyz
12:00
and let's move on to the next part okay
What is the difference between Parameters & Arguments?
12:03
one more thing which i have often seen
12:05
is that
12:06
a lot of programmer use parameters and
12:08
arguments as interchangeably terms but
12:11
no they are very different okay so these
12:14
two things parameter and arguments are
12:16
very different okay so whenever you are
12:18
creating a function so whatever you put
12:21
over here right these these identifier
12:24
or this labels right
12:26
over here are known as parameters so
12:28
that is why you can call it as a param
12:30
param1 okay so you might have seen the
12:32
syntax written a lot of places so this
12:36
identifier or the label
12:38
or this variable okay this is a local
12:40
variable in the function scope okay so
12:43
this this param1 and param two are local
12:46
variables inside this function you
12:47
cannot access this param1 and param two
12:50
outside okay so these local variables or
12:53
you can call it them as the identifiers
12:56
or the labels so whatever you might call
12:57
them so these are the parameters
13:01
parameters of a function
13:03
and the arguments which you pass over
13:05
here like if i uh give a one and a two
13:08
so this is known as arguments a lot of
13:11
people you know
13:13
use this as interchangeably and don't
13:15
know this that is why often when you
13:17
read a blog or you read a book then
13:19
somebody is using an argument somewhere
13:21
if somebody using parameter so you
13:23
should actually have a mental model of
13:25
what that person is talking about that
13:28
is when you will understand these blogs
13:30
and articles and books right
13:32
so once again the values which we pass
13:34
inside a function are known as arguments
13:37
and
13:38
these label and identifier which gets
13:41
those values are known as parameters
13:43
okay so now let's move on to the most
13:45
critical part which is first class
13:47
functions okay
13:49
so what are first class functions so
13:51
this is a very heavy term for what we
13:53
already know
13:54
so as i already told you and let me
13:56
repeat it once again because it is very
13:58
important functions are very beautiful
14:01
in javascript functions are very strong
14:03
functions are heart of javascript
14:06
and
14:07
just because it is like that
14:09
we call them as a first class functions
14:12
so the functions are so beautiful so
14:14
beautiful that instead of these
14:17
arguments we can even pass functions
14:20
inside another functions as an arguments
14:24
okay listen to this carefully arguments
14:27
so functions are treated as values right
14:29
i told like you can use an anonymous
14:32
function to pass in as a value also okay
14:34
that is how beautiful functions are and
14:37
you can receive that in the parameters
14:39
okay so if i pass this anonymous
14:41
function inside param1 and i try to
14:44
access this panel and see what we get
14:46
okay this is a perfectly valid
14:48
javascript okay see we got this
14:50
anonymous function inside it right and
14:52
there is one more way to pass this
14:54
function
14:55
okay inside this function okay suppose
14:57
it was a named function let's call it as
15:00
uh x y z okay and if i pass this x y z
15:03
like this we can do that too okay so
15:06
it's like passing another function
15:09
inside a function right we are passing
15:11
this x y z inside b
15:13
that's how beautiful functions are and
15:15
we can do that
15:16
and not just this if we pass this
15:19
function into this function we can also
15:22
return a function from a function
15:24
okay crazy it sounds right so we can
15:28
return a function from a function we can
15:30
return an anonymous function from a
15:32
function
15:33
and what will happen is when you invoke
15:35
this b right when you call this b
15:39
this function will be returned over here
15:42
right so let's just try to do a console
15:44
log of this okay so if i do a console
15:47
log of this
15:48
so uh and
15:50
let me just remove this x y z
15:53
and let me just remove this x y z also
15:56
and if i now
15:58
run this code so see
16:00
when we call this function so
16:03
this function anonymous functions were
16:04
returned from b and we did a console log
16:07
so it prints an anonymous function and
16:09
if it was a named function we can return
16:11
that too okay so what is first class
First-Class Functions in JavaScript
16:14
functions so first class functions is
16:16
nothing but this only
16:18
the ability to use functions as values
16:21
is known as first class functions
16:23
so let me repeat it once again the
16:25
ability of functions to be used as
16:27
values and can be pass this in an
16:30
argument to another functions and can be
16:32
returned from the functions
16:35
is this ability is known as first class
16:38
functions in javascript
16:40
nothing more than that okay these
16:42
ability of these functions to use an n
16:44
value and assign it to a variable and
16:47
can be passed into another functions and
16:49
can be returned out of another functions
16:51
this ability all over all together is
16:54
known as first class functions
16:56
that is what is first class functions
16:58
and it's not just in javascript it is a
17:00
programming concept it's in other
17:02
languages also not in every language but
17:05
in many languages also so our interview
17:08
question can be what are first class
17:10
functions in javascript so you have to
17:12
explain it like this okay when they are
17:14
treated as a value parsed into another
17:16
functions or returned from another
17:18
functions so this is known as a first
17:20
class functions okay this ability is
17:23
known as the first class functions
17:25
that's all it is
17:26
right so if you read blog articles or if
Functions are First-Class Citizens
17:29
you read books okay so a lot of times
17:31
you might also find a statement which is
17:33
functions are first class citizens let
17:36
me repeat it once again functions are
17:38
first class citizens okay so let me
17:42
write it down first class
17:44
citizens okay
17:46
so
17:47
when i say first class citizens it means
17:50
the same thing
17:52
first class functions functions are used
17:55
as first class citizens so it's both the
17:58
same thing okay so if you read it in
18:00
some blog or if you read it in some book
18:02
assume that this ability to be used like
18:05
values
18:06
makes the function as first class
18:08
citizens in javascript i know this is
18:11
kind of like a playing with english and
18:13
words right but that's how it is and it
18:16
is important to know all these things
18:18
because if we read it in some book we
18:20
should understand what that author is
18:22
trying to say right
18:23
javascript has a very vast community
18:26
right a lot of people are writing stack
18:28
overflow answers blogs blogs articles
18:31
this that and they use all sort of
18:33
notations so what i try to do is i am
18:36
just giving you this knowledge so that
18:37
next time you read these blog articles
18:40
you will properly understand what that
18:41
author is trying to say if somebody says
18:43
that functions are first class citizens
18:46
that means he is referring to first
18:48
class functions that means the ability
18:50
of these functions to be used as values
18:52
passed inside another function get out
18:55
from the function and like returned out
18:57
from the functions use them to assign
18:59
into a variable all these things make
19:02
say functions as the first class
19:04
citizens in javascript
19:06
remember will you remember this lifetime
19:08
remember this lifetime okay so one more
19:11
doubt you might get is that what if i
19:12
use a letter const over here so suppose
19:15
if i use a letter const over here so in
19:17
that case also it behaves the same way
19:20
just like let and const normal variables
19:22
do they are in a temporal dead zone
19:25
until it encounters this statement okay
19:28
so you it is treated exactly the same if
19:30
you want to get more information it is
19:32
in the latin cons video right go watch
19:34
that again if you are still confused
19:36
about it so in the latin const video
19:38
whatever rules are applied the same
19:40
rules are applied to these also so these
19:43
are nothing this const b is nothing but
19:46
just like a constant another variable
19:48
and this function over here is acting
19:51
just like any other value which is being
19:53
assigned to this b okay so that's how
19:56
and that's what it is okay
Info about Arrow Functions
19:58
so now this function statement in a
20:00
function expression or a function
20:01
declaration these all things can also be
20:03
written using arrow functions okay arrow
20:06
functions has come up as a part of es6
20:09
you can call it as ecmascript 2015 and
20:12
this let const arrow functions these
20:15
things were introduced in es6 okay so
20:18
these function statements function
20:20
expression this all things can be
20:21
created using arrow functions also okay
20:24
but let's not do this in this video
20:26
because i haven't told you what an arrow
20:28
function is
20:29
up till now in this series i have seen a
20:31
lot of people asking in comments that
20:34
what will happen in case of arrow
20:35
functions error functions error
20:36
functions don't worry people
20:38
i am covering the basics first of all
20:41
okay the basics of javascript because
20:43
when javascript was built it was not
20:45
built with arrow functions right it used
20:47
to have where it used to have function
20:49
keyword so let us first explore these
20:52
basic nitty gritty things about uh these
20:55
this beauty of javascript which was
20:57
originally made right and we will slowly
20:59
transition towards this es6 new things
21:02
also you know i am not avoiding let
21:05
const or
21:06
arrow functions or something because
21:08
those things are being used a lot right
21:11
i will make a separate video all
21:12
together just for covering arrow
21:14
functions that will be a very
21:16
interesting video i will be covering
21:18
everything like how how this keyword
21:21
works and how arrow function syntax
21:23
works how higher order function works
21:25
and how
21:26
statements declaration expression
21:29
everything we will be covering in arrow
21:30
function series just keep calm and wait
21:32
no issues everything will be covered
21:34
slowly slowly slowly slowly slowly
21:36
slowly slowly slowly slowly slowly and
21:39
trust me i'm not going anywhere i'll
21:41
teach you everything just keep on
21:43
watching this series and watch it in the
21:45
sequence okay in the next video we will
Teaser of the next video
21:47
be covering callbacks and higher order
21:49
functions those are very very very
21:51
important topics in javascript if you
21:52
want to learn functional programming
21:55
okay so don't miss that video that is a
21:57
very crucial video but before moving on
Thank you for watching Namaste JavaScript
21:59
to that video give this video a thumbs
22:01
up it gives me a huge motivation and see
22:03
you in the next video till then thank
22:05
you for watching namaste javascript
22:08
[Music]
22:24
[Applause]
22:27
me
)


