DPRC
====

Data / Process / Representation / Control

It is an extention of famous Model/View/Controller paradigm.

Data
----

* This layer is a simple representation of the data.
* What you call it **Model** in MVC.
* There has to be no processing taking place in this layer.


Process
-------

* It is where you process your data and make it ready to be presented.
* It is half of what you call it **View**. Or maybe some part of **View** and some part
of **Model**.


Representation
--------------

* Here is The Final Result.
* The rest of what you classically would name it **View**.
* User sees here, And user interacts with here.


Control
-------

* A classical **Controller**
* What molds all part of the system together.


The MVGC
--------

You could call DPRC, MVGC if you will. So it would be MVC with an Extra G
before the end.

* *Data -> Model*
* *Process -> View*
* *Representation -> GUI*
* *Control -> Controller*


The Relationship between these things
-------------------------------------

* Everybody sees *Control*.
* *Control* sees everybody.
* *Data* sees nobody but itself (Except, obviously, control).
* *Process* sees *Data*.
* *Representation* sees *Process*.


Implementation
--------------

I will try to make a simple implementation of this model here, with the help
of C programming language.
What I am trying to achieve is to create a universal framework for Desktop
programs, so they could be written in any language, Extended in any language,
And remain robust and highly extensible.

The work here is a result of many hours of thinking about a text processor
I was trying to write. And eventually I found out that what I was thinking
was not ``Text editor'' related at all, but a model, an extension of MVC,
which could be applied to any desktop application.
It isn't hard to think what led me to this model: I was trying to solve every
problem out there for an application. How to make it Infinitly extensible.
How to make it network transparent. How to make it Programming language
agnostic. etc.

All *Datas*, *Processes*, *Representations*, And *Controls* are what I call
compositing **Objects** of a program. They each live in their own separate
and isolated world. Each *Object* can live in its own thread, Process in the
same machine, Or in a process over the network. So the way *objects*
communicate is through [ZeroMQ](www.zeromq.org) ports.
Actually, *Datas*, *Processes*, And *Representations* only know the port of
their *Control*. And it is the *Control* wich links the other objects to each
other. This way *Control* trully can control the communication between them,
And agregates them together in an effective way.

The protocol used for data transport between objects is
[MessagePack](http://msgpack.org/). There are two types of communication that
can occur: *Action* and *Event*.

###Action
Is simply an RPC. each object offers a set of *actions* and other objects
can call on them.

###Event
Is also a mean of communication, but in the reverse order. An object notifies
the world that someting hapened.


Both *Actions* and *Events* can have an arbitrary JSON object parameter, but
they do not have any return.

The direction of an action is usually from the *Control* to other objects.
But *Event* flows from objects to the *Control*.

The way it all works is as follows:
The *Conttrol* is two folded. One part of it receives *Events*, The othe part
emits *Actions*.
These two parts are what the *Control* is specialized at.
 Control responds to an action which makes other object to register for a
certain kind of events. When that event is received, *Control* automatically
emits an action to the registered object. This makes it possible to build
special channels of communication between objects. When **Data** is created,
automatically registers for its channel to the **Process**. so **Process**
actually don't see the **Data**. It doesn't know that if **Data** trully
receives the messages it wishes that receives. **Process** just faithfully
emits the action it wishes the **Data** to act, as special *Events* of its
channel of communication. Then **Controll** receives the event, And
consequently makes sure that **Data** receives the apropriate action as the
result. Here, in between, if someone wishes to intercept with the channel he
can do that as a **Control** plugin.

The *Actions* and *Events* are what I call them generally as *messages*.
Messages are the *verbs* of communication and job in the **DPRC**. As the
result, objects are just a composition of a bunch of handlers to the
messsages.

All objects are plugin-able. A plug in is just a piece code which modifies
existing handlers or adds new ones.


Further things to work on
-------------------------

The main problem with this model is that the components are highly separated
and weakly linked together. So if a component fails others wouldn't notice.
But because all of the components are crutial to the system, and their
functionality is irreplacable by other parts of the system, if One fails
all of the system fails, silently.

I want to enforce that any part of the system can fail at any time. For that
to work I need to specify a common solution for uncover a failure, and recover
from it.


License
-------

The license of this document is Artistic License 2.0 as can be found here:
http://www.perlfoundation.org/artistic_license_2_0

Also, All source codes in this repository are licensed MPL version 2.0 as can
be found here: http://www.mozilla.org/MPL/2.0/, unless otherwise specifically
stated in the source code itself.