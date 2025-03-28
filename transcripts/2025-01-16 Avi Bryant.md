_00:00:02 Sam_  
Okay, cool. So yeah, I'm Sam Estep, joined by Joshua Sunshine from Carnegie Mellon University. This is Avi Bryant, for an interview about automatic differentiation. We have two halves of this study that we're doing, one about users of autodiff, and the other's about experts, or authors of autodiff tools and papers. I feel like you kind of fit both roles, so we could start with either, and then kind of move between them. Does either of those sound like a more interesting place to start the discussion?

_00:00:37 Avi_  
Yeah, I would say, probably starting by being more user-focused makes sense. Any implementation I have done has been driven by my needs as a user. And so, I think really talking about the user needs is probably more interesting. I don't think I have any unique insights on implementation.

_00:01:01 Sam_  
Okay, sounds good. So, as far as starting questions, why do you compute derivatives in your work?

_00:01:09 Avi_  
Yeah. So, there are two main things, if I look over the last five-plus years. There are kind of 2 main types of derivatives that I've been interested in, and the first one has to do with probabilistic programming languages or sort of Bayesian inference—which kind of two ways of saying the same thing, in a way—where what I am interested in doing is sampling from a joint probability space that is not—we don't have some kind of closed form solution to be able to sample from it. Like you sometimes have in a very simple kind of Bayesian model. But here we have some kind of generative model involving prior distributions and likelihood distributions and conditioning on data. And we want to be able to sample from the the posterior of a parameter vector.

_00:02:31 Sam_  
Could you give an example, maybe, of a specific probability distribution that you would sample from?

_00:02:38 Avi_  
Yeah. So, let's see. I mean, hidden Markov models are one example. And so let's say that you are—a classic example might be that that you're trying to track a vehicle over time that is moving through space. And you know, at each time step there is some distribution of how far it could have moved, and how different its direction could be from the last time step. And then you also have some kind of noisy observation about its location, that might be from like a GPS reading, but where you have some margin of error of where it's localized. And so what you're trying to do is basically, look at a distribution over its paths through space. You know, this path is more plausible, kind of fits the data better. This path is less plausible. But in the problems that I am interested in and was interested in, just a single maximum likelihood estimate is not really what you want, because you want to capture the full uncertainty so that you can integrate some kind of decision function over your uncertainty. So, in case it's useful, the particular professional context that I first became most interested in this was working at Stripe, which is a payments processor. Basically, trying to ask the question probabilistically of, is this customer of Stripe, who is processing payments on the platform, going to go bankrupt, like basically, is their balance going to go negative? Because if their balance does go negative, then that generally ends up with Stripe eating the loss. However much their balance goes negative, that ends up with Stripe having to cover that. These are typically—the probability is always low, of these events happening. But the consequences are quite severe. And the mitigations are costly, but not as costly. The mitigation would be something like, maybe you just fire this customer, or maybe you do something like, you withhold, the way you would withhold income tax. You withhold a certain amount of the payments as a reserve against the possibility that the customer will go bankrupt. So, that annoys the customer. That might even make it more likely that they go bankrupt, 'cause you're restricting their cash flow, so it's not something you want to do a lot. But on the other hand, it's a thing that can protect you against this kind of tail risk. And so, understanding the full distributions and the complex cost function of taking this action versus that action and then integrating it over the distribution is valuable. So, there are a lot of techniques for doing this kind of sampling. Some of the oldest are Metropolis–Hastings, and many of them do not require derivatives. But the ones that scale best, especially as your parameter space grows—as you have higher dimensions—that have been most effective where they can be used, do require derivatives. And so, most of those are based around Hamiltonian Monte Carlo. Which looks a little bit like gradient descent with momentum, sort of. The kind of intuitive explanation is usually something like, in gradient descent you're trying to drop a bowling ball and let it roll down to the bottom and sit at the bottom, and in Hamiltonian Monte Carlo you're trying to like let a hockey puck skate on ice where it keeps oscillating around. It goes through the bottom, but then it goes a certain way up, and then it falls down again and makes another path, and then falls down again, and it makes another path, and you want a strobe light that is capturing a bunch of positions as this puck is doing this slidey thing back and forth. But like gradient descent, it works with finding the the gradient of, in this case, this probability function that you're interested in. So yeah, that's what I was interested in. I can keep talking for a long time, but you may have specific questions.

_00:07:57 Sam_  
Yeah, so I guess, probabilistic programming was one you mentioned. There was another one you mentioned earlier, I forget. We can probably dive into that one specifically, and then we can maybe pop back up. So, yeah, next question would be, how do you compute derivatives? What tools do you do you use for that?

_00:08:22 Avi_  
Yeah. So, I have used a variety of probabilistic programming systems as a user, and so, the ones I'm probably most familiar with: one is Stan, which has its own, autodiff system in C++. Stan does this thing where it takes your program, which is in a very restricted DSL, and then generate C++ code for that, which links to their C++ math libraries. And when it does that, it generates code to compute the gradients. I have also worked with PyMC, which uses Theano. I think it's probably the only thing that still uses Theano, which is a very early Python autodiff system. And so, that's doing—actually, not even totally sure. I think it's doing some kind of forward-mode. Actually no, it's probably not. It's probably doing some kind of abstract interpretation, tape, reverse-mode thing. I actually don't know. But it's whatever Theano does. And then there's a system that I implemented in Scala on the JVM, and what that's doing is basically using abstract interpretation to build up a DAG of a compute graph. And then it has an operation—the DAG is closed under taking a derivative—and so it has an operation where it transforms its compute graph into a compute graph for the derivative. And then it has, basically, a compiler to JVM bytecode from there. Notably, to a first approximation, this is dealing with scalars rather than dealing with tensors. The system I implemented does actually have some facility for doing simple vectorized operations, like for the case where you've got a linear regression, and it's basically the same structure, where you're just doing a bunch of vector, multiplies or vector additions or something, and then you're reducing a vector into a scalar at the end, then its DAG can handle that, but it doesn't do arbitrary, matrix operations or arbitrary-dimensional datasets. Which is quite different from most of the tooling—and and maybe I'm jumping ahead here but, most of the tooling out there that's designed for for deep neural networks is very ergonomically geared towards those those much larger tensors, and so on. Another thing, actually, to say, from a user perspective about—well, and also a little bit an implementer perspective—about this problem space is that Hamiltonian Monte Carlo, unlike, say, stochastic gradient descent variants, is a full batch method. And so, you're always computing with the entirety of your dataset, rather than with some subset of your observations.

_00:12:34 Sam_  
Yeah, I mean, the classic example in machine learning for stochastic gradient descent is—I'm not super familiar with other examples outside of machine learning of, what a stochastic gradient would look like other than, you're using a randomly sampled batch of your data. Is it common outside of machine learning for, you're just always gonna have the exact gradient, usually?

_00:13:16 Avi_  
Um... yeah, I mean, it depends what you call machine learning. So, there are a number of problems that involve working with very, very large datasets. And generally speaking, when I see people working with very, very large datasets, they try to somehow do something stochastic with them. And so, stochastic gradient descent is a common approach or pattern for dealing with the fact that you have a lot of data. In the probabilistic programming world, if you have datasets that large, you often try to reframe what you're doing as a machine learning problem. Basically, you try to learn using machine learning techniques—including stochastic gradient descent—a function that you can tell approximates the distribution that you want to sample from. And you're kind of trying to minimize the distance between your noisy samples of your posterior distribution and this distribution that you're learning using machine learning, using all of your data. And so it's this weird kind of feedback loop. What I am most familiar with is the cases where you have small enough data. So usually, the reason you're interested in tools for dealing with uncertainty is because you have a very small amount of data, and so your uncertainty is very large. And so usually in a Bayesian inference context, you have relatively small datasets, but relatively more complex generative models that you're trying to work with. And so it makes sense to do a full derivative.

_00:15:18 Sam_  
Yeah, makes sense. Earlier, you mentioned scalars, like scalar operations versus tensors or vectorized operations. When we were working on Penrose, obviously we did the same thing as you, we just have a graph of scalars. But one thing that I think about a lot in in this design space is the way that JAX does it, which is, you can write a function that's entirely in terms of scalars. And then you call `vmap`, and then it does this vectorizing map, where it pushes down the tensors of the shapes all the way through. So ergonomically, it feels like it gives you the ergonomic that you want of, "I'm thinking about this in terms of a function on an individual instance." But then, computationally, it doesn't redo this computation graph stuff for, all the different instances, because they all look the same. I guess I'm just kind of curious if that point in the design space relates to what you were saying.

_00:16:22 Avi_  
For sure, yeah. And I think there's a lot to like about JAX. And I think that in particular, that `vmap` operation is a very powerful and useful operation. And that if you're building a system around scalars, I think you can go a long way with a system that basically has scalars plus `vmap`. And that's effectively—I mean, certainly the system that I built for Rainier when I was at Stripe was more limited than JAX—but that's effectively what I did was, build a scalar system that had some kind of special-cased map operation. So you could do some amount of vectorized. And I find that very useful. I think as a user, JAX has obviously a huge amount of investment, a huge ecosystem. As a user, especially not as much—JAX would have been a reasonable choice in a lot of ways for the probabilistic programming stuff I'm doing. If I can jump a little bit into the stuff that I'm doing right now, which is much closer to Penrose, and involves constraint-based CAD systems, the difficulty with JAX is that it is relatively slow to compile. And so, what we're interested in is to make interactive modifications to a diagram, and if you add a point to a diagram, and then you need to wait 3 seconds for it to update, that's really unsatisfying. And so, the issue that we ran into with JAX was just that, although it was very fast to do iterations once it was compiled, it gave itself basically infinite amounts of time in which to do the compilation process. And so, that was unacceptable.

_00:18:21 Sam_  
Yeah. No, that makes total sense. I haven't played with JAX as much as you have, but I've had somewhat similar experiences, and it's like, it hopes that it'll like pay for itself in the asymptotic case, but not all cases are asymptotic. Do you know why it's slow to compile? Is it because JAX is implemented in Python? Or is it just not a priority when they're implementing it? Or some other reason?

_00:18:44 Avi_  
I mean, certainly I don't see why it would be a priority, because I think the target use cases are these machine learning use cases where you're, I mean, OpenAI is training stuff for months at a time, or something. It's just not—the few-second compile time is mostly just not on people's radar. And so, you have to have a small-enough optimization problem for that to to be meaningful as an overhead. But, small optimization problems could be really interesting. And so, I think it's a reasonable design tradeoff on their part. I found it very difficult to understand what was taking the time, and to make tradeoffs around that stuff. And also, one thing that I also find very difficult with JAX is, it does a lot of a lot of constant folding and so on, which is good, a lot of stuff feels a little bit like partial evaluation. But very unpredictably. And we've kind of got this weird mix of JAX semantics and Python semantics. And it feels like this magical black box that sometimes you get amazing results from. Sometimes you get very confusingly slow or memory-intensive results from. And it's from tiny differences that don't feel like they should have that big an impact, but they do. And so, reasoning about that, I found very difficult.

_00:20:31 Josh_  
Just to clarify, these differences that you're observing are in the compilation speed, or in the actual execution?

_00:20:40 Avi_  
Both compilation speed and runtime speed. But in particular, compilation speed was highly variable.

_00:20:51 Josh_  
Do you know if you have any examples of these pairs?

_00:20:57 Avi_  
Yeah, I could probably dig into—we used JAX heavily last summer, let's say, and then basically stopped using it because of these issues. And so, I could probably go back and pull some stuff up, but I don't have something at my fingertips.

_00:21:22 Josh_  
Yeah. Thanks.

_00:21:23 Sam_  
Fair enough.

_00:21:27 Avi_  
But yeah, so, I don't know how much you want me to be freewheeling, opining on these things.

_00:21:41 Sam_  
I mean, I have a lot of different questions, so we can definitely move along if you want to. I mean, the next question is just, what are some challenges you faced when computing derivatives. So I guess, we talked about one is, in a context where you're changing what is the function that you're taking of the derivative of, frequently, compilation speed can be a challenge. And that was something you faced with JAX. So, what are other examples of challenges?

_00:22:16 Avi_  
Yeah, well, I think one thing that is that is generally challenging is that, when you have this host language, and then you have the restricted subset of the host language, or interpreter built on top of the host language, or whatever it is, that you can actually take a derivative from, and those are almost never the same thing. I mean, you sometimes have a case like Stan, where it is a very specific, restricted language that was written to be fully differentiable. And then you just can do almost nothing in the language, but at least you know what you can and can't do in the language. But much more common is this case where, in, like, Python, where many of these tools are embedded, what works, what doesn't, what are the trade offs? JAX is extremely flexible, but it is effectively interpreting your Python program with some kind of abstract interpretation mode that then it is using to generate a program in a different system with different semantics. The original TensorFlow was in some ways much nicer this way, even though it was restricted, because it was very clear that what you were doing was building up a DAG of objects that was a very explicit compute graph, and that you were just using a library in the host language in order to describe your computation as a graph of objects. And so, there are often challenges in either understanding these two different languages, or two different sets of semantics going on, or where it is very clear, usually it's very clear because it's very restricted. And so then it's like, "How do I express the computation I want to do in this series of operations that TensorFlow is giving me?"

_00:24:35 Sam_  
So, could you give an example? I've been using these sorts of abstract interpretation systems for a while, and so to me, at this point they feel natural because I have a good mental model of them. But what would be an example of this being a challenge, like a specific thing?

_00:24:55 Avi_  
So, like you, I've been using them for a while, and so I have internalized a lot of it. But I have also observed other people who have used them less. So, a classic example is, you want to do an `if` statement. If `x` is less than 3, this value; otherwise, that value. And depending on what you're using, you may or may not be able to do that. That is to say, you may be able to use the native `if` statement in the language. And if it's something that's doing duals to do forward autodiff, then that'll work just fine? Or, you may have to do something where you use some special `if` that takes two closures, or maybe that eagerly evaluates two branches. So it's just the question of "How do I construct a branch?" varies from system to system quite widely, and can be quite unclear. And usually, depending on the system, maybe you've got some static type system where, if you try to actually use a native `if` statement, the compiler will complain at you. Or maybe you don't, and you're just gonna get some weird, mysterious runtime error where it's like, "Well, this abstract DAG is not less than the right," or whatever. So that can be very confusing. Branching, just generally, and as a extension of that, looping or recursion: huge variety in how well that's handled. But then also, very difficult to predict how—generally, with branching, that means you've got a discontinuity in the derivative. Sometimes that matters greatly. Sometimes that doesn't matter at all. And so, just understanding, "When is a branch problematic? When is it not? Is this problem that I'm having with my system not converging, because I have this branch? Is there some smooth calculation I could be doing instead of doing this branch, and if so, what is it?" All of that is just constantly biting people. And I find my experience working with other people who have slightly less experience than I do in this stuff is constantly telling them, "I know you want to do it that way, and it's natural to do it that way. But autodiff is not magic, and if you have those branches in there, you're gonna regret it." But then that becomes, people overcorrect, where it's just like, "Oh, I can't ever use branches." It's like, "Well, no, that's not true either." But it's fraught. So that's like a big problem that spans everything from "How do I do this computation?" to "How do I express this syntactically?"

_00:28:23 Sam_  
Yeah, no, that makes a ton of sense, especially the details of branches, and how they're different across systems. And then, what are you supposed to do not just numerically, but also syntactically and semantically. I have a list of other kinds of examples... we talked about some of these... Yeah, I guess we can move on to the next one for now. What do you consider when choosing a tool for computing derivatives?

_00:28:59 Avi_  
Yeah... I mean... I think I consider, certainly, the platform or host language that it's using. I consider what kind of compile time or other overhead it has, in terms of per-program overhead. I consider how it scales with regard to you either the number of parameters or the number of derivatives I'm trying to take of basically the same set of related computations. I consider how it might parallelize, and whether that's a CPU-based parallelization or a GPU-based parallelization, and how that relates to the scaling factors that I just talked about. Is it parallelizing based on how many parameter inputs I'm giving it? How many non-parameter, observational inputs I'm giving it? How many different functions with some common subcomputations I'm asking for the derivative of? How does its overhead change depending on if I'm varying, for example, the data? Like, I want to redo this computation with the same parameters but new data—is that cheaper than if I want to give it a new computation that it has to start with from scratch? And then, obviously, there's fuzzy ergonomic concerns. What are the computational primitive—in the sense that, can I introduce new parameters on the fly, which is something that some people really want to be able to do? And that typically has a large performance cost associated with it, and so I try to avoid that. But maybe another ergonomic thing that is related to that, but different, is, do I have to define the dimensionality of my problem all in one place? Like, do I have to tell it this is a function of you know, these $k$ parameters? Or can I compose different modules where each module can actually inject more parameters? Or another way to put that is, is there information hiding around the representation of something? And so, to ground this a little bit in an example that will probably be familiar to you, when we're doing geometric work, and I say, this is a a parametric angle, what is the representation of that angle? Is it a single scalar that's radians? Is it two scalars, one of which represents the $\cos$, and one of which represents the $\sin$? Or a point, similarly, are we representing this as $(x, y)$? Are we representing this in some kind of polar coordinates? There's a number of different representations that are possible. For angles, are we allowing—is 720 degrees different from 360 degrees, or is it the same? There's some representations that work better if actually, we're only between 0 and 180, and so is that okay? But, sometimes I just want to say, give me an angle. Give me a random variable that represents an angle. And, how many parameters, how transformed those parameters are, etc, etc, is all hidden. But there are some systems that would not allow you to do that. JAX is bad this way, for example.

_00:33:59 Sam_  
I was gonna say, this sounds like the difference between JAX and, like, PyTorch, whereas PyTorch would probably let you "This is a module, and I'm encapsulating the parameters that are being trained in here," yeah.

_00:34:10 Avi_  
Yeah, whereas JAX doesn't like doing that. Or Stan also, that wouldn't work in Stan's model. That kind of thing. You have to be very explicit about your parameter vector, as like, "This is a function of this parameter vector," and that's the top-level, main thing. So that's another thing that I would consider. But then, is recursion possible? Are loops possible? What are the restrictions on those? JAX has some pretty nice looping primitives relative to a lot of other stuff. But yeah, those are the ones that come to mind.

_00:34:58 Sam_  
That makes sense. I was thinking about this because you mentioned that you tried JAX for a while, and then ended up switching away from it. I guess a couple of questions. One is, how good or bad do you find tools generally are at advertising the things that are relevant to you in choosing one?

_00:35:15 Avi_  
Oh, horrible.

_00:35:15 Sam_  
Like, the difference between them and others—probably not very good.

_00:35:18 Avi_  
Yeah, they're horrible at it!

_00:35:18 Sam_  
And then I guess the second one is, how often do you find yourself trying out a tool, thinking that it might work, or it's hard to learn enough to make a decision without really playing with it, and then you end up throwing it away and picking another one?

_00:35:34 Avi_  
Yeah, I think that's quite common. I think a protective instinct that I've developed is to try to express programs—try to build another level of abstract interpretation in, basically, so that basically, what I am always doing is building an interpreter that interprets some version of my program and runs it on top of, you know, some other framework. And so it's like, we can interpret this program in JAX, or we can interpret this program in Torch or whatever. And the user code is relatively isolated from that decision. And that's also a good way of smoothing out some of these ergonomics questions, is basically, the ergonomics only matter in the JAX interpreter that you're writing, and your user code can stay away from that. It's annoying to have to do that. But otherwise, all of your code just becomes JAX code. It's not even Python code, it's JAX code. And then, if you ever want to migrate away from it, it's just a complete rewrite.

_00:37:11 Sam_  
Yeah, I mean, I've seen this happen as well. It's the basic primitive operations, and so it just goes through everything. Do you find that this is a distinguishing feature between the autodiff library you use, and other libraries? Or are there other domains in which it's like, "Yeah, if you're picking a library for logging," or I don't know, that's probably a bad example. But, other domains in which you would do a similar thing, or maybe other domains in which it's equally diffuse throughout your code, but the choice isn't quite as hard to make as autodiff library? I'm not sure if the question I'm asking makes sense.

_00:37:51 Avi_  
Yeah, right, so I think it is a combination of those two things. I think autodiff libraries both are exceptionally diffuse throughout your code, because it's the autodiff monad that infects everything you do. The autodiff parts of your code can only call other autodiff parts of your code. And so, you don't have a library call that's isolated over there. It's this entire swath of your code.

_00:38:27 Sam_  
And it is really like a monad, isn't it? I just kind of realized that.

_00:38:31 Avi_  
Yeah. I have, in fact, implemented a monadic autodiff previously, although it ends up like—one way to think about the differences between something like JAX and something like the early TensorFlow is, "Is this an applicative autodiff library, or is this a monadic autodiff library?" Applicative autodiff libraries tend to be much clearer, much easier to use, but then more restricted, necessarily, because they're applicatives, not monads. More restricted on what they can do. But yeah, certainly, the more monadic an autodiff library is, the more it tends to infect everything. And so then it's both that it's super pervasive, and that it's super unclear until you're pretty deep in what the problems might be, that will cause you to not want to be using it anymore. So, yeah, it does seem particularly problematic that way.

_00:39:43 Sam_  
Real quick, could you give an example of an autodiff library that's applicative, and one that's monadic, just so I have the difference clear in my head?

_00:39:51 Avi_  
Right, so when I was describing before, with TensorFlow 1.0, that you were explicitly building up a tree of operations, or a DAG of operations. So, I have an object that is a parameter node, and then I have an object that is a constant node, and then I have an object that I very clearly construct, that is adding these two things. That I would call applicative. And you can inspect that DAG and do transformations on it, with all of the information available to you. And I don't know if it's totally fair to call JAX monadic, but it has more of that feel, where you can have a JAX function that is an autodiff-able function that takes as one of its parameters another function that is an autodiff-able function. And that is opaque. I can't look inside that function. That function isn't some graph of objects that I can walk. The only thing I can do with that is to maybe wrap it in a "Transform this to the autodiff version," and then call it, or something. So that's much more of a monadic feel where you have this composition of black-box functions that you're working with and make up your program.

_00:41:39 Sam_  
Would you say that generally, over time the monadic style has become more popular? Because, TensorFlow 1.0 is older than a lot of the other modern things.

_00:41:51 Avi_  
Yeah, ancient. Possibly older than you. But yeah, I would say that's true. I mean, this distinction between monatic and applicative is a very static-typing, functional-programming kind of way of framing it. And of course, most of these operate in a much more loose, lots of runtime games, Python-and-so-on world. And so I don't think the terminology necessarily maps perfectly. But certainly, the move away from some kind of explicit description of the computation that that you're interested in to some kind of implicit description of the computation that relies on various abstract interpretation tricks, or whatever, in order to extract it from your program. That certainly has been a trend, that I'm not totally comfortable with.

_00:43:03 Sam_  
Yeah, okay, good to know. I've definitely been one of the perpetrators of this trend, obviously, but yeah, I'm trying to imagine what an applicative autodiff world would look like, or if there's not really a way to—I don't know, it feels kind of inevitable. I'll have to think more about this... Yeah, I don't know. Josh, do you have any thoughts so far? Any other things you want to ask?

_00:43:30 Josh_  
Yeah, I guess, there's a different view, I suppose, which is also something of a half baked thought, so just excuse that. Which is that, the challenge of the monadic-style autodiff—can we recover some of the features of these applicative-style while using the monadic style, when people want it that way? In particular, is there a way to be more transparent in our debugging or other kinds of modes, so that we can see what is happening, even when we have this transformed function or the like?

_00:44:19 Avi_  
Which I think JAX tries to do. So, in JAX you have the high-level main user interface that is more of this modadic style that involves putting annotations on Python functions. But then it will give you, I think it calls them pytrees.

_00:44:40 Sam_  
Yeah, or jaxprs.

_00:44:42 Avi_  
Right, jaxprs. Thank you, yeah, pytrees maybe has to do with data input to it or something. Anyway, but yeah, right. So you can look at the jaxprs, you can write some kind of transformation on the jaxprs. Once it has done its abstract interpretation pass, it basically is taking this monadic system and turning it into an applicative system. It just doesn't really expect people to be directly using the applicative API. So, yeah, I think you can do that. On the other hand, if you've got that extra layer of, systems that are—you have this like weird it's-not-quite-Python language you're writing stuff in that's getting transformed, and then, something else is coming in and taking the output of that transformation and transforming it further, we're deep into Lisp-macrology-type, "How the hell are we supposed to understand what this is actually doing?" stuff.

_00:45:46 Sam_  
Yeah, for sure. Yeah, I don't really have any more thoughts on this particular topic right now. But I'll have to think about it later. The next question I have here—I'm gonna skip one of them—the next one is, tell me about the worst bug you've dealt with in code that you've written for computing a derivative.

_00:46:07 Avi_  
Hmm. Um... All right, nothing is immediately coming to mind, but I'll let that percolate and maybe come back to you with something.

_00:46:45 Sam_  
OK.

_00:46:46 Avi_  
I've written many bad bugs. Just, actually calling one of them to mind...

_00:46:51 Sam_  
Yeah, recall is always a challenge for me at least. Cool, I mean, I think that covers a lot of the questions we had for the user part. I'd be excited to move on to the author part, because I think this might provide some nice balance. So yeah, first question is, what sets your work in audodiff apart from others, like, what gap motivated you to begin this work? How does this gap impact people who compute derivatives in practice?

_00:47:19 Avi_  
Yeah, I mean, the main motivation was a very particular operational context where we wanted to be able to run derivatives on a JVM-based distributed compute cluster, without distributing a lot of native binaries. So, we wanted to be running in Java or on the JVM, and we wanted to be staying in that pure-JVM environment, and that had to do with the commercial operational context that we were in, and what was easy to get technical buy-in from the broader engineering organization. And so, that was the one piece. And then the other piece was that we had this context of Bayesian inference with relatively small amounts of data. I mean, the reason we were using this big distributed cluster is because we had a huge number of different computations we wanted to run, but each computation was on relatively small amounts of data, and so we were doing full-batch, full-derivative stuff. And so, the combination of wanting to run in JVM, and then also wanting to take advantage of partial evaluation opportunities presented by doing full batch and having the data available to us, is what motivated writing our own thing.

_00:49:17 Sam_  
So would you say that the things that already existed at the time, either most of them didn't run on the JVM at all, and the ones that did run on the JVM, put simply, weren't fast enough for the kinds of computations you were doing?

_00:49:30 Avi_  
I think that's accurate, yeah. Basically nothing ran on the JVM that wasn't a toy, and the toys were too slow.

_00:49:38 Sam_  
Okay, yeah, that makes sense. I guess the other part I had of that was, how much of this gap remains today? Like, would you say that you solved it, or other people have done JVM stuff since then that fills this gap, or is it still kind of like, there's parts of it that are unsatisfied?

_00:49:54 Avi_  
I haven't seen a lot of activity of autodiff on the JVM since then. I also have certainly not seen a lot of adoption of our work. So it's not like we solved the problem, and no one else needed to! But I think basically, the world has moved so much to Python that what everyone does instead is, it has become much more common for companies to support having huge clusters of Python-based systems. And so, that operational desire to run on the JVM, just isn't important anymore. If someone did have a reason to run on the JVM, I think they would run into the same issues that we saw, and maybe would stumble across our work, or maybe not, but I don't think it's solved from that point of view.

_00:50:56 Sam_  
Yeah, sure, that makes sense. What about the more recent work you're doing with CAD stuff? What would be your answers to those questions for that context?

_00:51:04 Avi_  
So in that context, the huge thing is latency of, how much overhead there is for some new computation.

_00:51:16 Sam_  
And I assume that's combined with runtime performance as well, right? 'Cause I assume that there exist systems which provide low latency, but low performance, and you're trying to get a balance of both. Is that accurate?

_00:51:29 Avi_  
Yeah, that's accurate. And you know, in particular, the particular reason I would say that we are most concerned about runtime performance is that—so, the optimization problems that we're trying to work on involve some degree of geometric constraints that are Penrose-like. But then also, more physical, like Monte Carlo, simulation stuff, that's more like walk-on-spheres, the kind of stuff that Keenan does. And so those can really blow up in terms of, basically, the number of floating-point operations you end up wanting to do. And so it's important to us to, on the one hand—or another example is the inverse rendering problems, would be another thing that's kind of similar to what we're trying to do. So, yeah, I have not tried Dr.Jit, or however we're supposed to say that. That, in some ways, feels like it's aimed at very similar problems to what we're doing. But I don't think they have any kind of low latency constraints. And similarly, JAX was great in terms of how it performed when we did a test, once it's compiled. But it's this combination of really wanting to be able to tune that tradeoff around latency versus runtime performance.

_00:53:34 Sam_  
Yeah, I mean, it feels like a classic JIT problem of, if you're writing JavaScript code. This is the kind of stuff that we've been doing for decades. I'm just trying to figure out why—

_00:53:47 Josh_  
Can I ask a question before you go on? This tuning bit caught my ear. So, I mean, usually this is a thing that's not tunable in almost any programming language context.

_00:54:07 Avi_  
Oh, um... it's not that I necessarily expect it to be adaptive or expect the user to be tuning it. And I'm not making any claims that anything we have done to date allows that. I'm really just saying, this is a point in the design—there's a tradeoff here, and we want to be able to choose exactly where we end up on the design space. The choices other people are gonna make based on their particular needs are probably not gonna be the choice we need to make. Does that make sense?

_00:54:42 Josh_  
Yep.

_00:54:42 Sam_  
Yeah, so the tuning is in the engineering of it.

_00:54:45 Avi_  
Yes, exactly. We want to be able to say, "Oh, this optimization is taking too long. We're gonna throw it out and replace it with a different optimization that's worse, but faster." That kind of thing. And keep making those choices based on how well it's working for us.

_00:55:08 Sam_  
Yeah, I guess I'm trying to figure out whether this is the kind of thing where any given setting that matters enough is gonna be worth it to re-engineer "solved" things in order to fit that context. I don't know if I have any more sophisticated thoughts about that.

_00:55:32 Josh_  
I mean, I guess there's the other version of this question, which is, "Can you get the top-level performance in JAX without any tradeoff in terms of compilation speed by just doing compilation in a smarter way?" I don't know. Do we know that there's something fundamental about the way they're constructing their graph, that it has to take that long?

_00:56:02 Avi_  
No, I mean I certainly don't know that. And also, we're not trying for infinitely low latency. I mean, we're talking about interactive use, so anything that takes less than a 60th of a second is kind of okay. And so, if we can get compilation speed down to ten milliseconds on fairly large computations, then I'm happy. And going to one millisecond is not gonna make an immense amount of difference. So, yeah, it's possible.

_00:56:54 Josh_  
How big are you talking? Can you give a sense of scale?

_00:57:00 Avi_  
Yeah, I mean, not that big. I mean, I think, if you were gonna do a graph of primitive operations, how many nodes with the graph have? Certainly thousands, but not hundreds of thousands. Or at least, if you had a system that could handle thousands of nodes, and compile in less than ten milliseconds, and have very good performance, then I think that would be worth adopting.

_00:57:49 Josh_  
Yeah, I'm actually surprised it's as small as that, given the description you gave us earlier of these.

_00:57:57 Sam_  
Yeah, it does sound pretty small. It sounds like an under-ambitious goal to me, but I think I just don't have a good feel for the scale of the kind of computations you need to do to achieve performance.

_00:58:04 Avi_  
Well, I could be underestimating it. I mean, it may be tens of thousands is the right thing, but I'm pretty sure it's not hundreds of thousands.

_00:58:14 Sam_  
Yeah. Interesting. Yeah, I should definitely play around with it after this. Josh, any other thoughts? I have other questions that we could talk about.

_00:58:28 Josh_  
No, I don't have anything right now. I actually need to go use the restroom quickly. I'll be right back. You guys can keep going.

_00:58:33 Avi_  
OK. Cool.

_00:58:34 Sam_  
OK. Um, yeah, I mean, the next one I had is, what communities of users do you think could benefit from your work? Obviously, this is CAD, so it's within a specific context, but I guess, there's two sides of it, like, what kinds of users of computer-aided design do you see benefiting from this particular program? And then I guess, also, do you see this as being applicable beyond just the domain of CAD? Are the two halves of this.

_00:58:59 Avi_  
Right. Yeah, I mean, I guess... the broad space that is interesting to me that this is an instance of—but where the whole broad space, I think, would potentially find this work useful—is human design that is informed by or informing optimization. So, you have some interplay between constraints and choices that the human is making visually, interactively, in real time, and solutions to optimization problems that the computer is giving as its response to those. And certainly, all the Penrose stuff is very much within this category also. I'm not quite sure how to distinguish this from all the—I guess all the LLM stuff—or other generative machine learning stuff these days—it's not really running optimization problems interactively with the human. It's more that it has pretrained a model, and that it's just sampling from the model interactively through human guidance. So I guess that's the distinction, is that this is for simpler optimization problems, but where the optimization problem is actually happening interactively as part of some kind of conversation with a human, rather than the machine learning approach, which is that you go away and run an optimization problem for a year, and then you come back with a bunch of weights that you use to interact with the human.

_01:01:25 Sam_  
Yeah, makes sense. I guess I'm a bit curious, because I've read a couple of papers on using audodiff for CAD, and I'm curious whether you had read those before, or played with those systems, and they were just slower than yours, or not as flexible as yours, or if your work relates to those at all.

_01:01:45 Avi_  
So, it is relatively common to use autodiff in solving constrained sketch problems. But usually, in the systems—and certainly in any commercial system I'm familiar with, and even, I'm not aware of any research systems that really do much more than this—usually that ends at the—OK, let me take a step back. In a typical CAD system, like in a typical kind of parametric CAD system like Fusion 360, you draw a 2D sketch on some plane. You constrain that sketch. You effectively set up an optimization problem. It's going to solve that for you. You can drag points around, and it will re-solve it. And then, you perform some further geometric operations on that sketch, like, you extrude the sketch into 3D, and then you intersect it with this other thing, and then you chamfer the edges, or whatever. And you are no longer in—if it's doing autodiff, it is only doing autodiff on that bare initial 2D geometry. It's not doing autodiff on all of those subsequent geometric operations. And so, what I want to be able to do is, same kind of workflow, you set up your your 2D sketch, you go through, extrude this, intersect with that, slice off this plane, whatever. And then you say something like, "And the total volume of this shape needs to be 37." And then it goes back to any choices that are degrees of freedom, that are unconstrained, and revisits them and optimizes them, and comes up with new values for them. So that requires an end-to-end differentiable geometry kernel, all the way to your final 3D shape.

_01:04:16 Sam_  
Yeah. So, I remember, we had a reading group last summer, I think. And this is one of the papers that we read, is called "Automatic Differentiable Procedural Modeling." Again, I'm not an expert at all in the CAD space. I'm just curious if this is more of the classical systems that you're talking about, or if it's more similar to the systems that you guys are building.

_01:04:41 Avi_  
This is more similar. I should look into this in more detail. I mean, I think this is such a broad design space, and so, I think where we end up is going to be different in a bunch of ways from this. But, unlike traditional CAD systems, this is very clear that it is doing a full 3D scene as a differentiable compute graph.

_01:05:21 Sam_  
OK. Yeah, makes sense.

_01:05:26 Avi_  
From this abstract, it doesn't seem like they have a user interface for this.

_01:05:34 Sam_  
Oh, yeah. Does it not let you—I had to log in with Carnegie Mellon to view the rest of the thing, because they have a lot of diagrams, but I don't know.

_01:05:36 Avi_  
Maybe. Yeah, if you can email me the PDF or whatever, that would be helpful.

_01:05:45 Sam_  
Yeah, I'll send you... I don't know if that's allowed to say in the recording of this. I guess, we talked a little bit about performance—how does the performance of your work compare to related work—I don't know if you have more things to say about that.

_01:06:08 Avi_  
I mean, the main thing that I can say about performance—the current CAD work, I don't really have anything to say yet, and honestly, the autodiff side of this is not a primary focus right now. So I don't have any particularly interesting autodiff results to share. But with the previous probabilistic programming stuff, the most interesting thing about performance, from my point of view, was that, with the applicative, very static representation of the computation, and with the awareness that you're doing full-batch, and so, although you have your parameter vectors that are unknown inputs to this computation, you also have a large number of constants that are inputs, that you do use the same ones each time. It was possible to get asymptotic speedups compared to other systems, by doing very aggressive constant-folding, partial evaluation stuff. And I think that that was the thing that I took away from that, that was the most interesting. Also that, for anyone who ever cares about this, generating JVM byte code where you're only using primitive operations, and you're basically not using any of the object and method dispatch machinery, can be extremely fast, because the JVM's JIT is extremely good at at, generating machine code for a bunch of floating point operations.

_01:08:15 Sam_  
Yeah. So, are you generating JVM bytecode in this project as well?

_01:08:19 Avi_  
No, we're not using the JVM in this project, but when I was using the JVM—a lot of people generate LLVM stuff. But I think actually, generating JVM bytecode is easier maybe comparable as a path to get native performance. I mean, Wasm would be another thing that people would probably do these days.

_01:08:51 Sam_  
Yeah. The times change, and the times stay the same.

_01:08:53 Avi_  
Yeah, exactly.

_01:08:56 Sam_  
Yeah, no, that all makes sense. I'd be curious to learn more about the constant folding stuff. Is that a kind of optimization that you see in other autodiff systems or contexts? Or is it unique to this CAD context?

_01:09:15 Avi_  
I have not seen anyone—so, I think it is somewhat unique to a scalar-heavy compute graph. Because usually, I mean, I guess it's harder to simplify, or it's less likely that you can make significant simplifications of something where a lot of your constants are vectors. They might cancel out on one row, but that doesn't mean they're going to cancel out of the other row, kind of thing. Unless you have a lot of sparse matrices—actually, did I ever introduce you to, or did you ever talk to Erik Strand at MIT?

_01:10:16 Sam_  
I don't think so.

_01:10:18 Avi_  
So slight tangent. But, Erik is doing some really interesting work around projective geometric algebra, where he's doing a lot of this, basically, constant folding that I'm talking about, in order to take what are very general—geometric algebra has this "everything's a bunch of matrices that are mostly very sparse and turn them into extremely compact numeric code," because it's like, "Okay, I know this is always a zero, and so these terms never matter, and blah blah blah." So, yeah, I'll send you—if the repo's public, I'll send you some links. He hasn't published anything about it yet, but it's pretty interesting.

_01:11:15 Sam_  
Cool.

_01:11:17 Josh_  
Sounds like someone to interview.

_01:11:18 Avi_  
Yeah, now, as far as I know, he's not doing any autodiff stuff, but it would be an obvious thing to do with his work, is then to layer an autodiff system on top of it. But, he has a Rust system that generates a Rust library, but could also be targeted at generating a JavaScript library, or whatever. So, yeah, it's interesting in that way, too. But, to try to step back from that tangent a little bit, I think I have not seen people doing the kind of aggressive constant folding stuff that we were doing. And I think that the tradeoff—and Sam, you and I have like talked about this a little bit—the tradeoff is how much you're willing to inline everything and then do a whole-program optimization, versus how much you're trying to maintain some kind of function boundaries or module boundaries in your compiled code, that mirror your input code. And obviously, there's tradeoffs to each, and binary size, it's kind of unpredictable. But often, you end up with faster but larger binaries, when you do these kinds of optimizations.

_01:13:06 Sam_  
Yeah, assuming that you have enough time to do all the optimizations on all the inlined code.

_01:13:12 Avi_  
Yeah, and certainly there are trade offs here around compilation time. And I think this is part of why JAX is so slow, is that it's doing some of this stuff. And JAX is probably the widely used system out there that does the most of this stuff, but there's not enough written about it for me to know for sure, what it is and isn't doing. But I think, basically, because so much of what we end up wanting to deal with are these kinds of closed-form algebraic expressions, there's a lot of prior art in computer algebra systems on how to simplify those closed-form expressions that can be applied here, that don't apply to general-purpose compilers where you're dealing with a lot of data structures and control structures, and so on. But in geometry code, often you end up with just huge trees of a bunch of algebraic primitives, and being able to do a bunch of algebraic simplification, and constant folding, and so on, through those huge trees can be quite useful, I think. There's a another system out of Google that does some of that kind of analysis, that's automatic integration rather than automatic differentiation, that I would have to pull up the citation of. But it takes, I think, a similar approach of basically trying to create one giant algebraic expression that it can then rearrange in very aggressive ways in order to get things into known forms that it can then automatically integrate.

_01:15:23 Sam_  
Yeah, makes a lot of sense. Yeah, I'm still excited to explore more of this design space, because it feels like there's a whole spectrum in between those, but it's the kind of thing I've been like noodling with my head, and we'll see if I get to writing code about it. Yeah, I guess we talked about expressiveness, because I have questions here like, "Do you handle arrays?" "Do you handle mutation?" And obviously, the answer is all "no" because it's just a flat graph. Yeah, I guess the last question I have is a broad, future-facing one, of, "What gaps currently exist in autodiff?" "Which ones are you interested in pursuing in future work?" "Which ones do you think you're not interested in pursuing, that you think would be interesting for other people?" "What communities of users are currently underserved?"

_01:16:19 Avi_  
Yeah, well, I mean, I think this whole community that we're talking about of graphics, or diagramming, or geometry, or however you want to draw the boundary, feels to me relatively quite underserved, relative to the machine learning community. I mean, basically anything that's not machine learning feels underserved compared to the machine learning community. Anything around physics simulation feels underserved. Not unserved, but underserved relative to machine learning. And I think GPUs are obviously a really interesting—the very well-served machine learning community makes very good use of GPUs, but one of the ways in which other communities feel underserved is that they are not taking advantage of GPUs to the same extent. So, I mean, concretely, TinyAD is really cool, but TinyAD is CPU-only. And it's very easy to imagine a GPU TinyAD—or maybe not very easy to imagine, but it's possible to imagine. Or something along those lines. But there doesn't seem to be a lot of work in that area. So TinyAD's insight, basically, that for geometry problems, really fast forward mode could be just as good, or better, than a technically asymptotically better reverse mode. I think, applying that lesson to, or porting that lesson over to the GPU—which just came up in the Discord again, and so on—is a is a really interesting one. But, anytime we're compiling something for the GPU, we definitely get into these abstract-interpretation, host language versus... And so, that's a funny one where usually the advantage of forward mode is that integrates really nicely with the host language, and you don't have to play a lot of games there. And then suddenly we're taking that advantage away again. But anyway, I think there's lots of interesting space to explore there.

_01:18:56 Sam_  
Yeah, I agree that would be interesting. I mean, I feel like the other insight of TinyAD is, it works when you have maybe a lot of parameters, but most of them don't interact with each other.

_01:19:08 Avi_  
Yes. Yeah, the Jacobian's very sparse.

_01:19:11 Sam_  
Yeah.

_01:19:12 Avi_  
Yeah, no, that's right. That's also a really nice insight. And I would say, certainly for probabilistic programming, this is true. And I suspect this is true, for, a lot of diagramming stuff. The term "loss function," or whatever, that that you're trying to take derivative of is almost always a sum of some very large number of terms. And those terms, you know, may—in this TinyAD clique-y way—there may be clusters of those terms that are the only place where certain parameters appear. And so, decomposing those giant summations into a bunch of independent things that you could take the derivatives of independently, is potentially very useful. But, is that something that's just an under-the-hood optimization? Or is that actually something you could make explicit, and build a system around the awareness that that's fundamental to the structure of the problem. I don't know.

_01:20:31 Sam_  
I mean, I know they make it explicit in TinyAD, 'cause you have to explicitly choose the groups. We didn't really go—in Penrose, every one of these terms gets compiled to its own WebAssembly function, but beyond that we didn't do anything sophisticated. Yeah.

_01:20:52 Josh_  
On the ergonomics side, or the software engineering side, are there what you consider really fundamental problems with all the major systems? About helping people debug them, or just the translating existing code into these things. I mean, we've talked about some of these, but do you think of any of them as calling out to you?

_01:21:19 Avi_  
So, it is rare for systems—if systems are basically just operating with scalars, then it's relatively easy to have good static typing and good tool assistance for that. Most of these people are using something like Python anyway, which, its support for static typing is limited. But, in principle at least. For when we're getting into full tensor stuff, all of the fondness of these Python libraries for weird shape broadcast things, and basically making it as hard as possible for you to know what the shape of a particular value is at any point in time, I think, compounds with the complexities around this host-language-versus-not confusion. And so, just generally speaking, having better statically typed numerics and then better statically typed differentiability would be really nice. I think the Swift for Tensorflow tried to do a bunch of this stuff, but didn't really go anywhere. You know, Futhark is a really interesting point in this space, where it has very good type information on its arrays, and has a very different approach to how you do these tensor operations. But certainly, in the mainstream, I feel like it could be really helpful to have better static typing for these things.

_01:23:09 Sam_  
Yeah, makes a lot of sense. Yeah, I mean, it's funny. I remembered when—

_01:23:15 Josh_  
Should be conscious of time.

_01:23:19 Sam_  
Yeah, I don't really—I was just about to wrap up.

_01:23:22 Avi_  
Yeah, good.

_01:23:23 Sam_  
I didn't really have any other—I'm kind of out of questions. So if there's anything else that you wanna say or talk about in the remaining couple of minutes that we have 'til 4:30, then I would be happy to talk about it, but I feel satisfied.

_01:23:43 Avi_  
Yeah, no, I think I'm good. That was a nice long conversation. I mean, we can always talk about stuff more. But yeah... we... we were talking about organizing like a sort of autodiff retreat.

_01:24:04 Sam_  
Oh, yeah, we can probably talk about that after we end the recording.

_01:24:09 Avi_  
Yeah, sure. I mean, I feel like we picked some tentative dates, but I'm not sure if those are real or not. Just to check in on that.

_01:24:18 Sam_  
Yeah, I'm happy to chat after we turn off the recording.

_01:24:21 Avi_  
Sure.

_01:24:24 Josh_  
Why don't we just go ahead and turn it off?

_01:24:27 Sam_  
Alright. Thanks Avi! This is awesome.
