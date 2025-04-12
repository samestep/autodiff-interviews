_00:00:02 Sam_  
Alright. So this is automatic differentiation user interview with Nico Clinco. Nico, thanks so much for taking the time today. Really appreciate you being with us. So yeah, I guess we can go ahead and get started. So, the first question is pretty broad. It's just, why do you compute derivatives in your work? What do you use them for?

_00:00:24 Nicola_  
For the moment, it is a relatively new project. I'm trying to use automatic differentiation for fluid dynamics. In particular, I'm interested in creating—to optimize the parameters of a CFD solver in an online fashion. And for this, we required to solve an adjoint problem. So, in order to formulate the adjoint problem and have the exact derivatives, I need to use automatic differentiation. And since the solver that I use, and most dynamic solvers, are written in Fortran, I'm trying to use automatic differentiation framework, in particular automatic differentiation with source code transformation.

_00:01:24 Sam_  
Okay, cool. So you said you're doing computational fluid dynamics, and you're trying to optimize the parameters of the model. Could you tell me a little bit more about that? What is the research question, or what's the application that you're looking at?

_00:01:36 Nicola_  
Yes. In particular, I'm trying to create a sort of general framework to optimize the shape of, for example, airfoils, such that we have the lowest drag coefficient as possible, while maintaining, for example, a lift above a certain tolerance. And meanwhile, we need to respect the discrete equations that we discretize from the solver. So, it is a general project, it requires some very more efforts. But yeah, I'm trying to do that. I'm relatively new to that at the moment.

_00:02:25 Sam_  
Okay, cool. So what it sounds like is, in a setting where maybe you're trying to design an airfoil, and you want to see how the airfoil performs in the simulator, you can use the differentiable simulator to optimize the shape of the airfoil. Is that accurate?

_00:02:43 Nicola_  
Yes. In particular—I mean, we not only use the simulator. We need to perform the simulations, and in parallel, we need to have an optimization framework such that we use the discrete derivatives. We use the derivatives with the automatic differentiation such that they are exact, because I need to solve another problem in parallel to the simulations.

_00:03:16 Sam_  
Okay. But, so, the goal of using the derivatives, is to optimize the shape of the airfoil? Is that the end goal?

_00:03:25 Nicola_  
In what sense? Sorry?

_00:03:27 Sam_  
So you're saying you're using this in an optimization sense, right? And so, the thing that you're optimizing, is the shape of the airfoil, to optimize its performance in the actual fluid dynamics?

_00:03:40 Nicola_  
Yes, in the solver. Yes, yes, yes.

_00:03:44 Sam_  
Okay. Cool. So, the downstream use of this might be, someone's designing an airplane, or designing part of an airplane, or designing some sort of physical thing, and then they would use some of these techniques in order to determine the shape of their object?

_00:03:59 Nicola_  
Yeah, exactly. Exactly.

_00:04:03 Sam_  
Cool. Nice. So yeah, any other high-level notes about what you're doing or the context, like maybe what you were working on before this that led to this work, or what does the prior work in this space look like?

_00:04:17 Nicola_  
In the prior—do you mean what I've done before of this work, or—?

_00:04:24 Sam_  
Either way. Either what other people have done in this space and what's different about this, or what you've done before that led you to work on this.

_00:04:32 Nicola_  
Okay. Before work on this, I work in the scientific machine learning. In particular, you can find one of my recent work, in which I'm creating a sort of filter for the LES (Large Eddy Simulation) in computational fluid dynamics. I have not used automatic differentiation, however. So, I have used Torch directly, the library of Torch, but I used that like a black box. And this is why I want to create a framework directly in Fortran, because I can optimize such that we respect the discrete equations of our solvers.

_00:05:20 Sam_  
Okay, so when you—oh sorry, go ahead.

_00:05:23 Nicola_  
This is the main reason, and since our solvers are written mostly in Fortran, I need to learn how to use, in the best way as possible—with the parallelization also, because most of these solvers are all parallelized—I need to learn how to use open-source transformation toolbox, such as OpenAD, Tapenade, and so on.

_00:05:53 Sam_  
That sounds good. Yeah, that makes sense. So you mentioned that the challenge is that you have to respect the discrete equations in the solvers. I'm not familiar with what that looks like. Could you tell me more about, what are these discrete equations? Why do they provide a challenge?

_00:06:09 Nicola_  
First of all, I think that the real challenge is that, with this program, we are able to differentiate all the programs. Okay? So, there is an head routine. And you choose the head routine. For example, if you take OpenAD like an example, there is an head routine, and then all the framework must start from this head. So, the real challenge is that I cannot find so much flexibility in what I'm doing. For example, you need to prepare all the source code. It is more likely that you need to write the code again with this source code transformation, such that we respect the line-guides of the transformation. So, the real challenge is that it is not automatic. And you need to code again everything from scratch. And most important, every subroutine that is called in the head has to be checked, and you must convert every type to those that contains derivatives. And this is really hard. This is really hard. Especially when you need to implement the derivatives in the adjoint mode. And there is the parallelization, the parallelization between several processor. For me, it is really challenging. And that's why I'm doing this survey also, that I hope that someone will help us in this direction, I think.

_00:08:10 Sam_  
Yeah, no, that makes sense. I mean, hopefully. We have a lot of cool people in the space. So it sounds like you're saying the challenge is that, if you try to apply the tools directly to the code that you already have, they don't work for some reason, and so then, you have to rewrite the code in order to make it fit into the way that the tools want it to be written?

_00:08:34 Nicola_  
Yes.

_00:08:34 Sam_  
So, the tools that you're talking about in this case, the ones you tried before—you were trying some tool before, and then it didn't work, and so you switched to Tapenade? Or what was that situation?

_00:08:44 Nicola_  
I worked with OpenAD, both in tangent and reverse mode. I was stuck on the fact that you need to specify—first of all, for what I know, when you differentiate the programs with OpenAD, you can specify only one independent variable and one dependent variable. Instead with Tapenade, you have much more flexibility, because you can specify more than one independent variable, and also more than one dependent variables. And this is the main motivation why I'm switching to Tapenade. And also, the most important thing is that it is well maintained. OpenAD, the last development of OpenAD was on 2014, if I don't—

_00:10:04 Sam_  
Okay. So it's basically a dead project? Over eleven years since the last change.

_00:10:11 Nicola_  
Yes, it is a dead project. And also, for example, the checkpointing: in OpenAD, I've not seen examples and easy ways to implement the checkpointing, for example. While Tapenade support that, and also, there are something for the parallelization. But I think that for the parallelization it's better to do the things from scratch.

_00:10:43 Sam_  
Yeah, that makes sense.

_00:10:43 Nicola_  
I think, at the moment.

_00:10:45 Sam_  
So, to make sure I understand: you already have a codebase written in Fortran for computing these functions, and you attempted to apply OpenAD to it, but it didn't work, and so now you're trying to apply Tapenade to it?

_00:11:00 Nicola_  
Yes.

_00:11:01 Sam_  
So, earlier you were saying something about discrete equations. I still didn't quite understand what you meant by that. What is a discrete equation?

_00:11:11 Nicola_  
For example, if you take a partial differential equation—and, sorry for the question: do you know the Navier-Stokes equations, I mean?

_00:11:21 Sam_  
I mean, I know about them, but I'm not an expert.

_00:11:25 Nicola_  
Okay. For example, you have a physical model, and this physical model described the flow fields all around a wing, for example. So, this flow model has to be discretized, such that it is not continuous. So you have not the analytical formula, but you have a large program, such that you can convert your equations in fluid dynamics algorithm. And these are the discrete equations. In particular, you march in time—you divide your time simulation in several time step—and then you discretize in space. So, for example, every part of your fluid domain is a specific cell, and in this cell there are stored the thermodynamic variables, and so on. In this framework, you implement your operators—for example, the divergence operator or the gradient operator that says how the temperature varies along the domains of your fluids, for example. And you solve your discretized equations such that your flow fields and your thermodynamic variables is constrained to that discrete system, in time. Before, mostly of the work in don't take in account of this discretization, and optimize the parameters of the model—for example, the turbulence model—in an offline fashion. So, they take a low-fidelity solution and a high-fidelity solution, and then try to use a sort of neural network or something else to find a sort of mapping. And in this mapping, there isn't a connection with the discrete system. While instead, if you use automatic differentiation and a discrete adjoint solver, then you can constrain your optimization to your specific equations. This is why I need to use automatic differentiation.

_00:14:29 Sam_  
Okay. So, just to make sure I understand—sorry I'm not an expert in this space, so it might take me a bit.

_00:14:33 Nicola_  
Yes, yes.

_00:14:34 Sam_  
So, you have these Navier-Stokes equations, they model the fluid dynamics, but you can't compute them directly. So you discretize into cells and use forward Euler or backward Euler or something, in order to model the time steps. And there's a difference between those equations, because the discretization is just a different function. And so you are trying to differentiate the discrete equations directly rather than to differentiate the original Navier-Stokes equations. Is that accurate?

_00:15:05 Nicola_  
Yes. Yes.

_00:15:06 Sam_  
Okay. And so, why is that? What's the benefit? 'Cause I would naively expect that you would get a benefit from differentiating the original equations, because that would give you derivatives closer to the real derivatives. So what's the benefit of differentiating the discretized version?

_00:15:22 Nicola_  
One moment. The fact is that I'm not trying to use automatic differentiation for the discretized equation. I'm using automatic differentiation, for example, for computing the Jacobian of the system in an exact way. Because for solving an optimization problem, I need the exact—and it must be exact—Jacobian of the discretized system. And this is why I use automatic differentiation, because I need exact derivative values, not approximated with finite differences or something else.

_00:16:07 Sam_  
Right. Right, okay. So, typically, it sounds like you're saying in the prior work, people take the Navier-Stokes equations and then they use finite differences or some numerical methods to approximate the derivatives of those, but those are not as accurate as what you need. But since you already have procedures to compute the simulation, you can use automatic differentiation to compute more exact derivatives for that simulation, even if you can't use it to compute exact derivatives for the original equations themselves. Is that accurate?

_00:16:47 Nicola_  
Yes. But, again, I'm not trying to use the automatic differentiation for the space derivatives or time derivatives. I'm using automatic differentiation, for example, for differentiating the part of my discrete equation with respect to the solution of the system itself. So, for example, I differentiate the temperature at a certain time, with respect to the initial conditions. That it is quite different of differentiating the solution with respect to space. It's another thing is. So, I think, automatic differentiation is mandatory in this framework, because you need to differentiate quantities related to the system, the system itself, the flow field itself, that is constrained on your simulation, on your trajectories that you are simulating.

_00:17:53 Sam_  
Okay. Yeah, that makes sense to me.

_00:17:57 Nicola_  
For example, if it is better, you can consider, for example, a neural ODE. Neural differential equation. I'm trying to creating a sort of framework, but for fluid dynamics, specific for fluid dynamics.

_00:18:13 Sam_  
Okay. Yeah, that makes sense to me. So the idea is that, using prior techniques, you would be able to calculate the derivatives with respect to time or space, but to work those out by hand, that would be too cumbersome to work those out with respect to the actual parameters of your system and then code those up. So you have to use AD.

_00:18:36 Nicola_  
Yeah.

_00:18:36 Sam_  
Yeah. That makes sense. Cool. So, we talked a little bit about this question, so we don't need to spend too long, but, how do you compute derivatives? What tools do you use? I know you mentioned using OpenAD, JAX, and Torch before, and now you're trying out Tapenade. Do you ever use other sorts of tools, like use a computer algebra system and then write that in code, or work out the derivatives by hand?

_00:18:58 Nicola_  
Yes. Yes. Since I was stuck with OpenAD, I differentiated some subroutines of the original program by hand. In particular, I scanned all the giant subroutine that I had, and then I created by hand my derivatives. And this is one of the difficulties that I encountered, because with OpenAD there were some problems in the loop, and then for me it was necessary to code by hand, for security reason also. And then I verified that everything worked by finite differences, for example. But yeah, for me, I'm looking for something that, it is automatic.

_00:20:03 Sam_  
Yeah, yeah. So you mentioned, you coded them up by hand—you said something about "for security reasons" you couldn't use OpenAD? What do you mean by that?

_00:20:12 Nicola_  
Sometime, I cannot trust—maybe it is one of my fear, but I don't want to trust too much, to do all the things like a black box. And furthermore, if you code by hand, you can scan all the program and you can optimize that. And this is work, at least for the forward mode, because at the moment I'm developing code in forward, especially for the parallelization. This is the main reason. But it could be great if it could be done also in the backward mode. But you need to carefully trace all the program and all the variables in your program.

_00:21:10 Sam_  
Right, yeah, I know that parallelization is a huge difficulty for backward mode, because, you know, all the reads turn into accumulates, and all that.

_00:21:17 Nicola_  
Yes.

_00:21:20 Sam_  
So, okay, so you mentioned that previously, before you started trying to use Tapenade, you worked out the derivatives by hand, you implemented them, you checked the correctness using finite differences. How was that process? Was it mostly pretty smooth? Were there a lot of mistakes that you made that you had to fix by checking, or was it mostly fine?

_00:21:40 Nicola_  
It depends. Only on the loops. There were some troubles with OpenAD. For example, you cannot write the indexes—for example, if you want to fill all the array—if you have an array, for example, and you want to initialize this array without loops, for example, I encountered many troubles, and the derivatives were all wrong. Maybe it is one of my fault. But then I've done all explicitly with loop, for example. And also, you need to be really explicit with the indexes when you create your code. And yes, this is a mess, in my opinion. Because every time, if you have a giant loop, you cannot use short indexes, and you come up with a big huge code.

_00:22:57 Sam_  
Right, yeah, that makes sense.

_00:22:58 Nicola_  
For me.

_00:22:59 Sam_  
So it sounds like you're saying when you were using OpenAD, you had a lot of trouble with loops. Was this when you were trying to use OpenAD at first, or was this when—also—maybe I misunderstood. Was it like, you tried to use OpenAD, it didn't work, and so then you switched to implementing it by hand, and then after that, then you tried Tapenade? Those three things happened in that order?

_00:23:23 Nicola_  
First of all, I used OpenAD, and I had some problems. But the main reason that I'm switching to Tapenade is because it is more well-maintained, and because there is a community that can—

_00:23:42 Sam_  
Yeah, yeah. What I meant was, you mentioned that you at some point switched to implementing them by hand. So you worked out the derivatives by hand. Was that before or after you were trying OpenAD?

_00:23:54 Nicola_  
Before, before.

_00:23:55 Sam_  
Oh, okay. Okay, right, so when—

_00:23:57 Nicola_  
Because, for example, when you create programs with OpenAD—but I think that it can happen also with Tapenade—the program is not optimized. And so, in order to have more performance, you need to scan the code by yourself, I think.

_00:24:23 Sam_  
Right, that makes sense. So you're saying, is this true for both OpenAD and Tapenade—they expect you to take the code that they give you, and then modify it to make it efficient, and then run it?

_00:24:33 Nicola_  
Yes. Yes.

_00:24:33 Sam_  
Okay, that's interesting. So—

_00:24:36 Nicola_  
I think—one thing—this is one of the main difficulty that I have found, because as a fluid dynamicist, I need also to learn how to do automatic differentiation. But my job is different from a job of a developer.

_00:24:56 Sam_  
Right.

_00:24:58 Nicola_  
And this makes me a little bit confused. Yes.

_00:25:03 Sam_  
Yeah, yeah. So it sounds like it's frustrating to you that you have to spend so much time working with these tools when they should just get out of your way and do their job, basically.

_00:25:12 Nicola_  
Yes. Yes, yes, yes.

_00:25:14 Sam_  
Yeah. That makes a lot of sense. So, before you were trying OpenAD, when you were working out the derivatives by hand and coding them by hand, what was that process like? Was it relatively straightforward? Was it pretty error-prone, or was it mostly fine?

_00:25:29 Nicola_  
It was really error-prone. And furthermore, since most fluid dynamics solvers have a structure for solving the equation that is sparse, really sparse, you need to create all the data structures—original data structures—that contains your derivative in a specific orders. Such that, for example, when you discretize your domain in cells, you need to have the neighboring cells stored in a particular way. So it is really error-prone. And yeah, it is not easy. There are some programs, some fluid dynamics solvers that now performs automatic differentiation and global automatic differentiation. And indeed, in the last week, I was searching for them. But yeah, starting from zero, it's not easy. It's not easy.

_00:26:40 Sam_  
Okay. So, was it that you had your program, you wrote out the derivatives by hand, and you succeeded, but then you were like, "I don't want to do that again, I'll try AD"? Or was it that you didn't get all the way through, and by the time you had—you kind of gave up and then switched to AD?

_00:26:59 Nicola_  
No. Heh... it's really frustrating, that, I know. But, for the moment I decided to use some programs that already englobe automatic differentiation, and then modify them. This is because there are many—one of the programs, if I can say it—yes, I can say it?

_00:27:33 Sam_  
Yeah.

_00:27:33 Nicola_  
Okay, is for example, ADflow (Automatic Differentiation Flow). You can check it in GitHub. In the paper that present this program, it is written that all the subroutine are differentiated by a standalone. So, for example, every subroutine is differentiated, and then all the subroutines are glued together. Especially for the parallelization, as I told you, and also for optimization reason. Because, for example, in backward mode, when Tapenade or OpenAD construct the tape, they put a lot of temporary variables. And yes, at the moment I decided to start from a program that is builded, and then use other space discretizations. Because what I'm trying to do is to create this framework for my space discretization of my solver. Instead, online, you can find only a specific type of space discretization for the fluid dynamics simulator. And I'm trying to emulate this for my case.

_00:29:02 Sam_  
That makes sense. That makes sense. Okay, let's see, I have some other questions. I think we've talked about some of these before... Yeah, we talked about a bunch of these. Okay, great. I guess—yeah, more specific question: tell me about the most complicated piece of code you've written for computing a derivative. The worst bug that you've experienced while you've been working on code to compute derivatives.

_00:29:34 Nicola_  
Computer, or in the framework of automatic differentiation?

_00:29:38 Sam_  
Either way.

_00:29:40 Nicola_  
Okay.

_00:29:42 Sam_  
Something specific.

_00:29:45 Nicola_  
Okay, since I work with fluid dynamics, I'm working with—okay, I cannot be so much specific. I can be specific, but I need to be understandable. So, I faced many issues with the fluid dynamics solver that solves the compressible Navier-Stokes equation in the context of the spectral element method. That is a particular space discretization of the Navier-Stokes equations. And I faced problems with the parallelization when I tried to implement a filter—yes, I don't know how can define that. But I try to implement a stabilization strategy for a test case that is the turbulent channel flow. In this framework, all the processor communicates together. Not all the processor. In particular, your domain is discretized with the method, and every processor has to communicate to the master rank some quantity at each iteration of the solver. And these must be highly optimized, because you cannot have bottleneck in your code. This was the worst thing thing that I've done. Regarding the automatic differentiation instead, I tried to implement a simply forward neural network with the OpenAD recently, two months ago, and I was struggling in the backward mode. Because between layer, if I don't remember bad, between layer you need to store temporary variables. And I needed to scan all the code, but it teached me a lot, I remember, because I have seen all the program from scratch.

_00:32:18 Sam_  
So you mentioned you were having difficulties with parallelization. Was this when you were using Tapenade already? Or is this when you were writing the derivatives by hand? Like, what tool were you using when—the bug earlier with the parallelization?

_00:32:31 Nicola_  
No, I was using still OpenAD for the parallelization. Tapenade is one week that I used that, and I have not tried this yet.

_00:32:40 Sam_  
Okay. So when you were using OpenAD, and you had this issue with the parallelization, was it like, you ran the code and it gave the wrong answer? You ran the code and it was too slow? Or the code wouldn't run at all? How did you come across this issue?

_00:32:54 Nicola_  
In particular, I was stuck in how to create—the problem was from the basis, because since the fluid dynamics solvers are really sparse, the main difficulties were to create data structures that are suitable also for the parallelization. So, every processor has to store the processor from which the things come, and then send and receive only the things that are required, in an unblocking fashion. And while in the forward mode it is relatively easy, in the backward mode it is really hard, especially for the spectral element method, because in the spectral element method—it is a method of fluid dynamics, okay? And you need to be really careful on how to manage the parallelization in this case. This was one of my difficulty.

_00:34:15 Sam_  
Right. So just to make sure I understand: you already had an implementation to compute the simulation without derivatives, right? And this was already parallelized, it sounds like?

_00:34:25 Nicola_  
Yes.

_00:34:26 Sam_  
Okay. So, you have this, and then you attempted to feed this code to OpenAD at first. And then what happened? Did it give an error? Did it give you code that was slow, code that was wrong? What happened?

_00:34:39 Nicola_  
It was impossible to give all the code to OpenAD, because the input/output in the code was merged together with the numerical core part. So, I created all the numerical core as a standalone module, and then I feed that to OpenAD. But I experienced really many issues in doing that, because there were, first of all, unknown variables. So OpenAD puts a lot of variables, and also, some variables are not well understood, so they were not set to active. And so, I took every single subroutine, and then I differentiated by a standalone. And then I glue it together. And then, when I had done that, some subroutines were differentiated by hand. And one of the reason, it is because with OpenAD, one must specify only one independent variable. And all the dependent variables, the program provides itself.

_00:36:18 Sam_  
Right, okay, yeah, that makes sense. Yeah, it sounds like there's a lot of difficulties getting things to fit into OpenAD. So, since you've switched to Tapenade, how has your experience been? You mentioned that it's been about a week. Have you had challenges using Tapenade? Have you had any similar issues? Has it mostly been pretty smooth?

_00:36:38 Nicola_  
It seems more affordable. It seems more affordable, also because there are many examples on the GitHub, many programs use Tapenade, and also there is one of the Discord channel in which there are discussions about that.

_00:37:06 Sam_  
By Discord, do you mean on the Differential Programming Discord, or on a different Discord?

_00:37:10 Nicola_  
Yeah, yes. Automatic differentiation Discord. And yes, for me, it seems easier, especially for the tuning. So, you can specify what you want and when you want. With OpenAD, again, only one independent variable, all the subroutine must to be linked within the head subroutine, and then, it is hard.

_00:37:48 Sam_  
Yeah, makes sense. So, right, going back a little bit: we talked a little bit about your experience writing derivatives by hand. Could you tell me a bit more about—it sounds like you really don't want to keep writing derivatives by hand. Are there a lot of people in your field who do still write derivatives by hand? Or is that not very common?

_00:38:09 Nicola_  
It depends. One of the program that I see that before, that is open source—ADflow—has some subroutines that are differentiated by hand. This is because you can have a fine tuning of what is happening. But in contrast, it is right that you can fine-tune what is happening. But at the moment, it is really hard to take every single step, every single operation, and differentiate by hand. So, in my opinion, yes, there are many people, at least in my world, that use differentiation by hand. But, on the other hand, it could be great if everything will be automatized.

_00:39:11 Sam_  
Yeah.

_00:39:12 Nicola_  
In my opinion, so.

_00:39:14 Sam_  
Yeah, it would be pretty great. So, the people who do still do a lot of derivatives by hand, what are their reasons for it? Is it because they've tried the tools and the tools aren't very good, or they don't know about the tools, or they haven't heard of them, or just, their workflow works for them, or there's some benefits—they need more performance—or, what are the reasons that people typically do still not use these tools?

_00:39:43 Nicola_  
Okay, in the paper, it was written that they need performance. So they write and code the derivatives mainly for performance. But I think that—at the same time, if you look at some subroutines in which there is the parallelization, again, they are written by hand because there isn't a support for parallelization with most open source code transformation. There is, for example, a tool—oh no, I don't remember—OMP? I don't remember. There is a tool, but they prefer to do everything from their hand. For security reason again. They wrote "for security reason". Okay, for security reason, and also for performance, to avoid all the taping, all the variables. Yes.

_00:40:51 Sam_  
Right. Okay, so it sounds like in general, when you mentioned it "for security reason" again, the idea is that, they don't trust that the AD tool will give the right answer? Or just, they don't trust that it will be fast enough? Or they don't trust it for some other reason?

_00:41:11 Nicola_  
I think that they don't trust that—for performance.

_00:41:16 Sam_  
Right, yeah.

_00:41:16 Sam_  
And again, with parallelization, you cannot create a black box and give everything to the program.

_00:41:26 Sam_  
Yeah. So, do you think that it's just kind of impossible for these AD tools to handle the parallelization properly? Or is it—I don't know. I've thought about this, and I've wondered—'cause the way that people do it in machine learning, right, is that instead of writing the loops all themselves, they compose the program out of blocks. Or, if you're using JAX—you've used JAX before, right?

_00:41:53 Nicola_  
Yes.

_00:41:53 Sam_  
So if you're using JAX, you might write an individual function that operates on a scalar, and then you'll use `vmap` to map that over a tensor. And so that gets automatically parallelized, and things like this. So I guess I'm curious—

_00:42:07 Nicola_  
One thing. The fact is that, in JAX, when you, for example, perform the parallelization with `vmap` or `pmap`—`pmap` for parallel stuffs—you don't need to communicate between specific processors. Instead, every processor do the thing, and then you have, at the end, a reduction operation. During this process, your functions have operations that are independent between processors. Instead, what a fluid dynamicist wants is to have an automatic differentiation framework such that we can, in the function itself, communicate the derivatives between specific processors. That is different between optimizing the weights of a neural network that don't communicate between processor. If I'm not wrong.

_00:43:20 Sam_  
Yeah. So, I mean, that makes sense. But I know that there are some parts of neural networks that do have communication, right? Like the multilayer perceptrons or sometimes dense. Attention blocks also have communication between different things. It's not just fully in parallel. But I guess, are you saying, the parallel computation patterns are different? Or is it just that—so, I guess what I'm asking is, is it that it's not possible to encode this sort of computation in JAX, or it's just that people haven't done it yet, and people typically do it in Fortran?

_00:44:03 Nicola_  
No, in JAX it is done. With `pmap`, everything works, so—

_00:44:11 Sam_  
Right, but what I mean is, for these computational fluid dynamics problems, like let's say the simulation that you're doing, right? Would it be possible to code that in JAX or not?

_00:44:21 Nicola_  
Yes, it will be possible to code in JAX. The problem is that all the legacy solver, the original solver, is in Fortran, and mostly of the ancient solvers are written in Fortran. So why I must rewrite that in JAX and in Python, while I can have the basic solver in Fortran? So, this is the fact that I'm using automatic differentiation in Fortran, because I want to use the Fortran solver without coding it from scratch. But, as I told you, nowadays, in my opinion, one has to rewrite—creating a differentiable solver in Fortran, it is more likely to write again the fluid dynamics from scratch.

_00:45:33 Sam_  
Yeah.

_00:45:34 Nicola_  
This is the problem.

_00:45:35 Sam_  
Yeah, yeah, yeah. So that's what I'm asking is, since it sounds like you're having to do a lot of writing stuff from scratch anyways, what's the benefit you get from writing it from scratch in Fortran versus—it seems to me if you were to write it from scratch in JAX, then it would be easier to parallelize the derivatives when you run it through JAX, to compute the derivatives. Is that more or less true? Or is it still easier—even though you have to write it from scratch—is it still easier to write it from scratch in Fortran using Tapenade compared to using JAX?

_00:46:07 Nicola_  
It depends. I think not. It's strange, but I think that it is easier to write it in Tapenade, especially if one knows carefully what the program is doing. That's because if you differentiate a function in JAX, for you, it is a black box. While if you use Tapenade, you know exactly what the program is doing, and you can optimize that. So I'm looking for something that, it is optimized—first of all, it is automatic. You can use that either by a black box, or, for performance reason, you can open all the trace and then modify that for performance reason. I'm looking for something like that. This is why I want to continue with Tapenade and Fortran. I want to start here.

_00:47:19 Sam_  
Yeah, that makes sense. So, it sounds like you're saying, you would be open to using other tools if they allowed you this flexibility of taking the derivative that the tool gives you as a starting point, and then modifying that to get what you want. So, right, I guess what I'm curious is, you mentioned that when you wrote derivatives by hand, it was not great, and so was the reason that, when you wrote it the first time you had bugs? Was it that it just takes time to write it the first time? Is it that when you write it the first time it's okay but then when you have to modify it, you sometimes miss things? Like, what are the difficulties with writing them by hand?

_00:48:01 Nicola_  
Do you mean by hand? So, every subroutine by hand, for example?

_00:48:08 Sam_  
Yeah, if you're writing all the derivatives by hand. Is it just that it takes too long the first time? Or is it that you have bugs? What's the reason that it's bad?

_00:48:17 Nicola_  
First of all, it was really error-prone. Because I remember that for a small subroutine with three or four loop, I was stuck at least five hours, something like that, because you need to recognize all the operators, all the linear operations, and then you must recast them in a proper way.

_00:48:40 Sam_  
So when you say five hours, you mean, you wrote it, and then you had a bug and you spent five hours trying to debug?

_00:48:46 Nicola_  
Yes.

_00:48:46 Sam_  
Yeah, yeah.

_00:48:47 Nicola_  
Yes.

_00:48:48 Sam_  
Yeah. No, that makes sense. I realize we're getting close to time. So, I don't have anything right after this, so I can keep going. But if you need to go, that's fine. I've gone through all the specific questions I have, but if you have anything else—either following up on what we talked about or things we didn't get a chance to talk about?

_00:49:09 Nicola_  
For me, again, my dream is to have a framework that acts like a black box, but if you want, you can modify that, such that you can trace everything. You can put small pieces. You can modify that without being an expert. And also have more documentation as possible, first of all. Especially for one that is not an expert. It is not an expert, and his primary focus is not automatic differentiation, but it is, for example, fluid dynamics.

_00:50:08 Sam_  
Right, right, right. Yeah, that makes sense.

_00:50:08 Nicola_  
At the moment I cannot find something like that. I think that computational physics is still far away from automatic differentiation. At the moment. So it could be great to create a certain workflow like that.

_00:50:32 Sam_  
Yeah. So it sounds like what you're saying is you want the flexibility, but you also want the tool to mostly be out of the way. And you want to be able to focus on the actual work you're doing rather than having to worry about automatic differentiation all the time. Yeah, that makes sense.

_00:50:47 Nicola_  
Yes, yes.

_00:50:48 Sam_  
Alright, this has been really great. I can stop the recording whenever—and if you have any closing thoughts, or should we call it here?

_00:50:55 Nicola_  
For the moment, yeah, no.

_00:50:55 Sam_  
Cool. I'll stop the recording, then.
