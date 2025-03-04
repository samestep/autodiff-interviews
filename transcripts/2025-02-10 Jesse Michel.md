_00:00:02 Sam_  
Alright, so this is an automatic differentiation interview with Jesse Michel from MIT. Jesse, thanks so much for taking the time. So yeah, I guess we can get started. The first question I have is, what sets your work in automatic differentiation apart from others? So, some examples might be: "What gap motivated you to begin your work?" "How does this gap impact people who need to compute derivatives in practice?" and "How much of this gap still remains today, after the work that you've already done?"

_00:00:32 Jesse_  
Right. So, I was doing work, or—I started my PhD working on differentiable rendering. So I was working—submitting my work to SIGGRAPH, sort of focusing on more applications-driven side. And I saw—I mean it was a green field in terms of the PL angle and the approach of systematically solving the problem of differentiating something like a renderer or physical simulator. I would say it was pretty green pastures when I started, and throughout my work, I would say I've developed some of the theoretical tools for studying the problems that arise here. But I've barely scratched the surface. I would say there are sort of two sides. There's a super-applied side that, well before my work, people were doing things that worked reasonably well. And after my work, some of my collaborators went off and built some production differentiable renderers using some of the techniques we worked on together and some other techniques. So I would say there's still a lot of opportunity, a lot of different approaches, both on the theoretical side—analyzing the systems that already exist in the wild—and also building on the theoretical foundations that I've worked on. So I'd say there's a huge amount of work, and it spans from the pure PL theory—like, how do we think about compositionality in AD in this domain—to very applied, which is, people are using these algorithms and they seem to work: what are they actually doing? And I think both of these are open and hard problems, and also, decreasing variance of existing systems—again, very applied, I think, is still open. And I think also on the application development side, there are lots of applications that we have yet to explore because we didn't have those capabilities.

_00:02:47 Sam_  
Yeah, that makes sense. So you're saying you started at it from a more applied standpoint, you came to do more theoretical stuff after that, and your work has touched on gaps in both. For the application side, what do those gaps look like? Are they performance, are they just things that you couldn't do before that you can do now?

_00:03:09 Jesse_  
So maybe I'll give a concrete example. One problem I worked on is differentiating through contact. So you have a ball, it bounces, and you want to optimize the location of that bounce point. And there were a number of ways to do this previously, but I think our approach was pretty creative. We looked at the Lagrangian energy, which is basically saying, given a physically invalid path that bounces, you give it an energy, and the minimum energy is the physically valid path. And so you end up framing it in a pretty different way. I think the traditional way is something like the shooting method where you take little steps, discrete steps. And I think there's an opportunity to revisit the way we've approached, for example, simulation. And to broaden our view. And it ends up making some problems that were easy before hard, and some problems that were hard before easy. But I think differentiating through contact is a good example of something that is a hard problem that's interesting, and that I think I made some progress on with my collaborators.

_00:04:25 Sam_  
Yeah, that is interesting. So, to paraphrase a bit, it sounds like your approach is that you're taking something that was a step-based simulation process and then turning it into more of an optimization problem?

_00:04:38 Jesse_  
Right. So typically you'd take a bunch of steps, and then you'd basically have some loss function that said "How well did I do?" and then you backprop, or something like that, through your optimization. Instead, what you do is you say "I start here, I end here, and I'm gonna put down some path that just hits these key points—and it might do whatever in other places—and then I'm going to move those knots, those points, so that it becomes physically valid or optimal."

_00:05:12 Sam_  
Yeah, that makes sense. That's cool. You also mentioned that there are some—so this, I assume, would be a problem that was harder before and becomes easier with this approach. What's an example of a problem that was easy before and becomes harder with this approach?

_00:05:28 Jesse_  
That's a good question. Right, so there are some technical challenges to this approach. One challenge is, how do you model something like friction or different time-dependent discontinuities? Something like—we had an example in our paper with a trick shot, like a golf trick shot, and you had a spinning fan, and you didn't want the golf ball to hit that fan. And there was some technical trickery that had to go on there. So I'd say, insofar as you can do extremely complex things with the setup that people already have, I would say it's not clear how to do many of these more sophisticated things, and our current solutions are definitely not production-ready yet.

_00:06:26 Sam_  
Yeah, no, makes sense. That's interesting. So you'd say the specific challenge would be these sorts of time-dependent discontinuities, where when you hit the discontinuity depends on what path you take up until that point?

_00:06:38 Jesse_  
Something like that. Another example is, how do you explore multiple bounces? So right now, the way you parameterize things, you sort of affix a point to a surface and you say "I'm going to have one bounce," and then you move around that bounce. It's not obvious in this framework, or I think in existing frameworks, how exactly to deal with this multiple-discontinuity problem. I remember Russ Tedrake, who's a robotics professor, wrote in his textbook that this is a challenge. So I think it's maybe more broadly a challenge.

_00:07:12 Sam_  
Yeah, I mean, in my experience, this seems like a pretty broad challenge in general. If you have some sort of—we know how to do optimization in a continuous or differentiable space, and then we also have some techniques for doing discrete optimization. But it seems like doing optimization in a more topologically rich space where you have some discrete components and some continuous components seems like a challenge across domains. I've talked to people in vector graphics who also have this challenge when optimizing vector graphics. Yeah, no, that makes sense.

_00:07:44 Jesse_  
When I say "these techniques," actually, I think some of the first work in this area was looking at differentiable vector graphics. This is Tzu-Mao's work.

_00:07:52 Sam_  
Yeah.

_00:07:43 Jesse_  
So I think definitely these techniques apply to those sorts of problems, but it's definitely not solved.

_00:08:02 Sam_  
Yeah. Yeah, makes sense. Cool. I guess the next question I had was, "What communities of users do you think could benefit from your work?" So, "What aspects of these communities motivate design choices in your work?" and also, "What end user communities do you consider out of scope for your work?" to manage the scope.

_00:08:23 Jesse_  
Right, so I would say, thus far my work has mostly been not user-facing, insofar as the literal systems that I build are prototype systems, and I think the main contribution is the language design and the theoretical analysis, and implementation, like operational semantics. I would say the target audience of this sort of work: definitely the graphics community—so, VFX, rendering, all of this sort of video production, simulation sort of stuff. That's the area I've looked at the most, and then moving out, there's physical simulation more broadly, and then moving out, there's material science and engineering.

_00:09:28 Sam_  
Okay, yeah, that makes sense. So, I don't know a ton about differential rendering, so maybe you can flesh out some more specific examples. I know there's style transfer, you and I have talked about a little bit, and then there's—I know a little bit about how differential rendering can be used for training world models for these multimodal language models nowadays. You also mentioned material science. Could you talk a little bit about how your sort of work would trickle down to material science applications?

_00:10:00 Jesse_  
Right. So, just to revisit the graphics quickly: basically, the sorts of applications are physically realistic rendering. That's what I think of as sort of the classic thing to do: you build your model, and it has tons of parameters, and it looks sort of like what you're trying to model. And then you take a picture of what you're trying to model, and you have it auto-tune, with gradient descent, the parameters to make it look just right. And this also allows for stuff like, yeah, mixing—this is like The Irishman. Going back to this material science sort of stuff, there's an example that we looked at that was stress-strain curves. So, you can imagine a spring. So it's typically Hookean, in the primary regime, in the classical, idealized sense. But when you have a composite material like rebar, or when you move from "good spring" to "Oh my gosh! The spring is stretching!" you end up getting different behavior, and optimization of those sorts of stress-strain curves end up being difficult. So, this will be like an inverse design problem, where I want to make a spring or some material that does some new task. And the question is, "How do I optimize the parameters? What's the best spring to get for my application?" or "What's the best type of rebar? How should it be reinforced?" And because—yeah, there are a number of interesting challenges here. It often requires higher-order optimization—a lot of stuff in computer graphics benefits from second-order optimization, and—yeah, there are a bunch of challenges here.

_00:11:57 Sam_  
Yeah, that makes sense. So when you say "higher-order," you're talking about higher derivatives in the optimization, not optimization within an optimization problem?

_00:12:05 Jesse_  
Yeah. There are also issues of nested optimization. Yeah. And then another example from my work is, I was looking at crack problems—so, fracture mechanics, this is in mechanical engineering. And the question is, how do you model crack propagation under strain? And it ends up—this is also one of these challenging—it falls under this formula of, you're taking a derivative of an integral with discontinuities. And really, these problems show up everywhere, and this sort of discontinuity is interesting because it's a singularity—it's like $1/x$ sort of deal, or $1/x^2$. So it goes like that. And the question is, when you integrate over the singularity, how do you model that problem? And there's a whole area called potential theory that studies these sorts of problems, and a lot of opportunity for automatic differentiation. And just to say, this bleeds into PDE theory. More broadly, differential equations. The integral and the derivative are intimately tied—integral equations and differential equations—and a lot of the techniques overlap. And there are, I think, new ways to attack, more broadly, applied science.

_00:13:33 Sam_  
Yeah, no, that makes sense. So, you mentioned this idea of potential theory for this crack propagation problem. I don't know anything about that field. How does it relate to your work on distribution theory? Do those theories play nicely together, or are they kind of separate?

_00:13:48 Jesse_  
Right. So, the distribution theory that I mostly—the work I'm talking about is unpublished. So, my previous work, I looked at: you have something like a jump discontinuity depending on a parameter, you're integrating over it, and you take the derivative with respect to that parameter, and you get something like a Dirac delta, a spike.

_00:14:07 Sam_  
And this is not the singularity that you're talking about with the $1/x$?

_00:14:12 Jesse_  
So it turns out, distribution theory is about things that act like integrals, and it also works when you have something like $1/x$, where the intuition is, you approach the singularity from both sides at the same rate, so that the positive part and negative part cancel out. And that also can be formulated—this non-standard integral—can be formulated as a distribution. And then the tools from distribution theory translate. So it turns out, even with this weird integral, you have integration by parts, you can take derivatives. And distribution theory, as a framework, basically gives you technical tools you can apply so that you can do automatic differentiation.

_00:15:03 Sam_  
Nice. So, okay, so it sounds like this is a generalization or an application of distribution theory to a more specific or more generalized mathematical domain?

_00:15:13 Jesse_  
Yeah, I would say my work on singular integration falls within the framework of distribution theory, and applies it to a new domain and some new problems, with some algorithmic insights of how to sample in these settings.

_00:15:38 Sam_  
Yeah, makes sense. Going back a little bit to the differentiable rendering—because you were talking about the idea that you might want to make some sort of photorealistic rendering, you have a scene, you have a model that you've parameterized for that scene, and then you automatically tune it to look like the actual scene. I know that there's real-time rendering and then there's non-real-time rendering, and these tend to have different concerns or methods for doing things. Does your work fit more into one of those versus the other, or is it agnostic to whether the rendering is real-time or not?

_00:16:12 Jesse_  
So, I would say, this question goes beyond my expertise, insofar as I don't write very much GPU code, CUDA code, and I haven't looked at the real-time setting. So, I think I'm gonna mostly pass on this. I would say, I don't see a strong reason why it shouldn't work, but I also don't think I know enough to make a strong claim here.

_00:16:41 Sam_  
Okay, fair enough. Yeah, I'm not an expert in that area either, but was just curious in case you were. Cool. Yeah, next question—I don't know if it directly applies to you, so feel free to say if it doesn't. The broad question is, how does the performance—in the program, computer performance sense—of your work compare to related work? And I have some sub-questions, but I guess the broader question would be, "Does your work have numerical performance comparisons to other work, or is it just qualitatively different in a way that that doesn't really make much sense?"

_00:17:18 Jesse_  
So, there are some comparisons. I would say, theoretically, it has properties that previous work didn't have, like provable unbiasedness, finite variance, consistency. Those are the sorts of properties that it satisfies that are really hard to prove in other settings. We did have some comparisons to these other settings, and I would say the high-level takeaway is: if you have super big discontinuities, and you have a lot of them, being really accurate on that side of the problem is really important. And if you don't have that-big discontinuities, it turns out, if you do the standard thing where you ignore them, or if you account for them using pretty simple approximation techniques, it works pretty well. I would say you can get—from what we've seen, you can tune parameters so that these more ad hoc techniques end up working pretty well. And I think that is why that's the state of practice to some degree—you can get it to work. But it's additional user burden, and I would say a challenge. So yes, I have done some performance comparisons, and I would say the high-level takeaway is: if you have sufficient tools to do the sorts of algorithms I've been working on, you can get super low variance, and you get provable guarantees. Sometimes the variance that you're focusing on—as in, the variance from these moving boundaries—doesn't actually matter very much, and that's just not really the big part of the problem. And then, it doesn't really matter very much! And sometimes it's a big part of the problem, and being really careful is worth it. I would say there are also issues with expressivity. So, if you have a really nasty problem—if it's hard to sort of parameterize in certain ways—my techniques won't apply, and other techniques that don't have the same guarantees will apply. And probably there's some ideal world where you use a mixture of techniques. So I'd say, yeah, I think there's a place for my work. I also don't think that the style of algorithm I've been doing is the end-all, be-all, or the only way. I think there will be many ways, but I think it's one of many promising ways that has a particular place on the tradeoff curve, where it provides very low variance, but you need a little bit more from the user—in particular, a notion of invertibility.

_00:20:28 Sam_  
Yeah. Okay, yeah, 'cause I think—this is interesting. 'Cause I have been thinking of performance in a pretty low-dimensional space of, you're trading off against "How much time do you spend running the optimizing compiler?" and then "How much time do you spend running your program?" But it sounds like you're saying there are many other dimensions that are important as well, like "How much time do you spend thinking about how to parameterize your problem?" "How much time do you spend working on these sorts of ad hoc techniques to deal with these little discontinuities?" So, would you say that you're focusing on domains in which those other axes that are more about programmer time far, far outweigh the axes of "How long does it take to actually run the program?"

_00:21:15 Jesse_  
Can you say that one more time?

_00:21:16 Sam_  
So, yeah, I guess to shorten it: it sounds like you're saying, when you talk about performance, you're more considering, "How much time does it take to even write the program and get something that works at all?" And then, how long it takes for the program to actually run is kind of secondary, once you've done that work?

_00:21:36 Jesse_  
I think that's right. I think, insofar as there is a fundamentally hard problem here at the level of "How should this code run?"—"What is the right way to model this problem?"—I think you have a higher-level concern. But that doesn't mean that—if it doesn't run on a GPU, and you're trying to do rendering, you're not going to be able to render very big stuff. So, I would say that's very important. But I would say this is a little bit earlier on the implementation stack in terms of the challenges that you face as someone trying to solve one of these problems.

_00:22:17 Sam_  
Yeah, that makes sense. So, would you say that your work is orthogonal, or doesn't directly affect how fast you can make it once you actually compile it down to the GPU? Or does your work have some implications for, like, "Oh, our technique, when you try to use it on an example that doesn't have all these sorts of crazy discontinuities, it ends up being harder to compile to efficient code," or something like that? I dunno.

_00:22:46 Jesse_  
Yeah, yeah. So, we do have for—this is my first paper, this is my collaborator Sai did most of the CUDA coding—you can write GPU kernels that do this sort of thing, and they sure are fast. Yeah, so, I don't know if this answers the question.

_00:23:08 Sam_  
Yeah, I think that answers it. So you're basically saying, it doesn't actually have a negative impact on the performance of the code that ends up running in the end. It's just a theoretical way of structuring the program.

_00:23:19 Jesse_  
Yeah, I don't believe it's a significant challenge to execute, at least in certain styles of implementation. I'm sure there's a large tradeoff space, and I don't want to overgeneralize. But I would say, if your problem doesn't have these issues—if the thing you're modeling doesn't have moving discontinuities—you compile to what you would have compiled to otherwise. So it doesn't get in your way. It's an additional chunk. And then, that additional chunk might take some time. And then there's probably some tradeoff between focusing on modeling this part of the problem and modeling other parts of your problem.

_00:24:07 Sam_  
Yeah. Yeah, makes sense. I guess digging a little bit further into this—in interest of, as you said, not overgeneralizing—what would be a specific example in, let's say, differentiable rendering, that has a lot of discontinuities as you mentioned, that would be a particularly good fit for your—let's say, a specific example that you or your collaborators wrote code to run this as a benchmark or something?

_00:24:35 Jesse_  
Right. So, I think the most clear example is in image stylization. So, you're trying to—I don't know if you remember, there's this Obama picture where it's a bunch of triangles, and it's red and blue, and it's very stylized. And these triangles, they're not all the same size, they're not all the same shape, and it has a certain artistic effect, boldness, to it. And the way that I think that this was made is, someone placed the verts, and moved them around, and made it look good. And the approach we had in our paper is, you take a bunch of triangles: you make a grid, and then you cut each square in half. And then what you do is you optimize these edges. You move around the edges, to minimize the discrepancy between this triangulated image—where each triangle has a single color to it—and the original image. So then you get a triangulated version of the image, that's "optimal" in some sense, in that it matches the original image. And so you can get these effects that would have to be done manually, in an automated way. And this is, I think, a good example because it has tons of triangles and lots of discontinuities between the triangles.

_00:26:07 Sam_  
Yeah. Yeah, makes sense. So this is an example where it was just not really feasible to do it automatically before your work, and then this unlocked this new application? Cool. Let's see... do you have any other comments or insights or things to say about performance, or comparison to other work?

_00:26:37 Jesse_  
Um... Maybe it's worth just contextualizing my work in the broader world. So, in the PL community, pretty early on, there's a paper, "Smooth Interpretation," that basically used the approach that also showed up in graphics and diff-ras, of blurring out these discontinuities and accounting for them that way. And, on my work on singularities, there was work that used a similar algorithm that we use, this symmetrical sampling algorithm. So I would say, these problems have come up before—prior to my work, they were, I would say, known—and I think I've done some work in generalizing the solutions, and systematically characterizing them, and formalizing how to solve them. But I would say there's a rich history, dating back quite, a while of these problems coming up, and even in the math literature. And mathematicians—Hadamard, Dirac—working on these sorts of problems at the theory end, as well.

_00:28:01 Sam_  
Yeah. Yeah, makes a lot of sense. Yeah, thanks for the contextualization. Um, yeah, let's see... a lot of the questions I have here are kind of more specific to non-GPU settings, but I guess it could be interesting to talk about them. So, the next question I have is, what are the expressiveness limitations of your work? And then the sub-questions I had were like, do you handle arrays, mutation, recursion? These are kind of classic automatic differentiation things that I'm guessing are less relevant in your context.

_00:28:33 Jesse_  
So, I think they are relevant. I think it would be nice to have—I don't know, it's always nice having more features. I'm not going to be like, "You shouldn't think about recursion." But I would say, in an extremely simple setting, there are still very interesting problems. And I would say, this is something that has surprised me about the state of the AD literature, where I would say people are doing very sophisticated work, working on really advanced language features and analyzing them very carefully. And I would say, there's a green pasture of, you're trying to model, automatically differentiate through, harder problems. And I think that's an untapped area. And I think there are a lot of problems here. Again, I think it's really early.

_00:29:33 Sam_  
Yeah, makes sense. Yeah, this is something I've thought about. I don't know if this is directly the same as what you're saying, but one thing I've been thinking about is, the difference between taking a program—like an arbitrary program that someone's written that computes a function—and then doing a lot of static analysis on it to figure out efficient and numerically stable ways to compute the derivatives, versus giving someone a richer library of primitives, or maybe higher-order functions to compute things that maybe—well, if you just weren't computing derivatives, would compile down to the same code—but if you are computing derivatives, all these little pieces maybe have richer differentiable semantics attached to them. It feels similar to the work that you're doing where you're like, "Hey, we have integration as a primitive," or we have—I don't know—maybe the Dirac delta or these other sorts of distribution-theoretic concepts as primitives in the language, and then you talk about them at a higher level, and then you compile them down. I feel like—sorry, I'm going kind of long on this—but I feel like another place where this dichotomy shows up is the JAX approach of automatic differentiation—of, you have these high-level, pure-functional programs operating on tensors—and then the Enzyme approach where you just run a lot of optimizations on the code first, and then you differentiate that lower-level thing. And both seem to have good performance in different scenarios. So I guess—I don't know—I'm curious if you have any thoughts on that. I'm guessing you have more of a preference toward the "work at a higher level" thing based on your work, but I don't know. I'm curious if you have thoughts on that dichotomy in general.

_00:31:19 Jesse_  
So, I would say, I would brand myself as a non-expert on performance as a primary consideration. I look at algorithmic, asymptotic performance, and I make sure my implementations work on problems. But I'm not in the business of squeezing out juice, which I think is very important, but I would say, don't have a strong opinion on what's the best way to do this. I would say both Enzyme and JAX have the ability to introduce high-level operators, like integration. And it is possible to implement the sorts of things that I've been working on in either of these languages. And I would say the high-level problem—the technical problem—of, you have a nasty continuous operator, and you maybe have discontinuities as well, and you're trying to figure out how to best take derivatives—I think is quite a broad problem, that at this point—I'm saying, even just thinking about how to implement a fluid simulator where it's not super hacky, where you have a lot of control, where you're at the right tradeoff, where you're not working at the level of matrices, and hand-coding your derivatives, and your derivatives are also right, and you also can control performance considerations—I think this is pretty open. And at this point, especially from the PL perspective, there's very little work: "How do I implement an ODE solver in a way that's robust and applies to a handful of different types of problems?" I think that's something that's shockingly, to me, not heavily represented in the PL community.

_00:33:32 Sam_  
Yeah. Yeah, no, makes sense. Yeah, let me think for a second. I guess one thing that I wonder about is, specifically what kind of person do you envision writing code that uses your approach? Maybe you're not writing the tools that someone is going to use, but you're building theoretical techniques that could be used in, as you said, various different tools. So, a person writing this sort of code is gonna have to have various different kinds of expertise, right? They have to have expertise in their domain, and then maybe expertise in distribution theory to think about the concepts for your work—maybe not—or, how much expertise do they have to have in—maybe they're more familiar with C++, and they're not as familiar with functional programming language constructs or ways of thinking of things. I don't know, I'm just curious: do you feel like there's a gap in the sorts of expertise or familiarity that people are gonna need to use tools that are downstream of your work? Or is that also kind of irrelevant?

_00:34:54 Jesse_  
Right, so, okay, so when it comes to, let's say, functional versus imperative, I'd say that either way, you can handle it. These techniques should apply in either scenario. Sorry, what was the earlier part of the question?

_00:35:14 Sam_  
I guess, yeah, I was just thinking, to what extent does your work introduce new kinds of knowledge that or expertise that people have to have.

_00:35:28 Jesse_  
Oh, right! You were asking about the users! Yes, okay, so—right, the sort of users that I've been thinking about are people doing rendering, or doing physical simulation, or sort of scientific computing more broadly. And these people tend to be more—I would say the work prioritizes an understanding of continuous math more than, I would say, traditional PL work, for example, or AD work. And so I think a lot of the users come in with a lot more math knowledge. Just to give a sense, when I talk to people who work in scientific computing and I mention distribution theory, they're like "Oh yeah, I love distribution theory, I learned it in my Intro to Scientific Computing 1 class! We went over it." Because when you're doing PDE solving, it's the way you do it; where the intuition is that it's a complete function space. Imagine you're trying to find a function that satisfies some property, and your limiting sequence might approach something that's not a function, but is a distribution. So it ends up being—because it's sort of the closed space, it's the right space to work in when you're doing something like solving a differential equation. So even people whose programs never talk about distributions—

_00:37:07 Sam_  
Right. They're thinking about it in their heads.

00:37:09
You need it as a modeling tool, and then you work through the math and discretize it to a bunch of matrices, and then you implement it in something like MATLAB. I'd say that's not an unusual setup, where people have a lot of the skills already. Maybe not the skills to implement the compiler, or the training to do that, or the knowledge of performance engineering or that sort of thing, but they definitely have exposure to continuous math, analysis, those sorts of things.

_00:37:44 Sam_  
Yeah, no, makes sense. So it sounds like you've talked to at least some people who are doing really applied scientific computing or graphics stuff in this field. Those people that you talk to, do they often end up just, like you said, working out the derivatives themselves and then implementing without using automatic differentiation? Is it common to use automatic differentiation? Are there people for which there exist automatic differentiation tools that they could use, but they don't for various reasons?

_00:38:16 Jesse_  
I would say the state of the world is piecemeal. So, I remember, we got reviews, we were working on some sort of automatic differentiation work. People were like "Why? We just hand-code them, it's not that hard. So fast. Why are you even doing this?" So I think it is not—in the machine learning community, I think it is very unusual to be hand-coding your derivatives. I think in graphics and scientific computing, it's not super strange to do that. And I think that's to some degree reflective of the development of the tools to solve those problems. I know Enzyme's made some inroads in better serving this community. But I think this is still—in short, I think the world right now is a bit piecemeal, because I think a good-enough answer is still in the making. In other words, the TensorFlow moment, or whatever you want to say—the production system—I don't know if we've seen it yet. It's not obvious to me that we have, and I think there will be such a moment, because I think there is a need, if the right tool is there.

_00:39:45 Sam_  
So, when you say "a TensorFlow moment," is this gonna be for—there'll be a moment for all scientific computing? A moment for graphics? Because TensorFlow—and obviously all these ML tools—were huge for ML, and that just encompassed the whole field of ML, it seems. But I guess I'm curious how many different pieces the scientific computing or graphics communities would need to be carved up into, for each individual piece to be served by one tool.

_00:40:17 Jesse_  
Yeah, so that's a really hard and very, very good question. And I don't think I have the answer. I mean, when you look at a space as rich as PDE solving, the idea that there will be a single tool to rule them all seems... uh, bold. But, I think it's more like, you build out something for a really big simulator, and you get it to work, and it works really well, and then it slowly percolates. And maybe that'll only apply to computational fluid dynamics, and this will just be a computational fluid dynamics thing. But I would say the high-level takeaways, the theoretical development, the compiler development, the core technical problems—I think there are a lot of themes that appear across these domains, and a lot of holes in the way we do things now.

_00:41:30 Sam_  
Yeah. Yeah, makes sense. Wait, I had a thought... what was it? Oh yeah, right, because science is a huge fractal: you can look at it, and at every level of detail you look at, it just is more complex. And it's also growing over time: every area is just growing exponentially. So I guess, there's these two forces that are in opposition: there's the force of, "it's useful for there to be a tool that works across a broad variety of domains," because then that tool gets more buy-in, and then it becomes better for everyone who uses it. But then also, the field is growing over time, and so, it's gonna become more fractured, and different sub-areas are gonna have their different concerns and needs. So I guess, for automatic differentiation specifically, what do you see as the relative pace of those two forces? Is it gonna be like, "tools are going to become more and more fractured over time," or "tools are gonna become more condensed over time"? Let's say next five, ten years, I don't know. This is probably an overly broad question.

_00:42:25 Jesse_  
Yeah, right, so, I don't know if I'm qualified to answer this. I don't think I am, but I'm happy to speculate.

_00:42:32 Sam_  
Yeah, yeah, sure.

_00:42:33 Jesse_  
Wildly speculate! After saying I'm wildly speculating. So, I think there's a level of execution that is really important to making something just pleasant to use. I personally love JAX—I love using JAX, it makes me really happy. I think it's great, I think it's super flexible, both at the performance level and at the implementation level. It's just like—I really like it. And I think I've seen, in looking at the literature on scientific computing, people starting to take up JAX, and try to implement fluid dynamics simulations, and molecular dynamics, and these sorts of problems, in JAX. So, I think we're definitely seeing some degree of consolidation towards these extremely well-invested-in and well-executed approaches, that are very flexible. I would say, when it comes to what national labs will do—or what works for a specific application—I would not be surprised if expert systems continue to be better than generalist solutions, and that having your stuff in Fortran might just be what you need sometimes.

_00:44:18 Sam_  
Partially because there's libraries, and partially because it's the language you and all of your collaborators know, right?

_00:44:25 Jesse_  
I would say that's part of it, and partially it's that Fortran just made some really good decisions. It turns out, having lots of language features sometimes makes it hard to make your code fast, and sometimes less is more, right?

_00:44:40 Sam_  
Yeah, no, I mean, it literally is, yeah—I mean, there's the tradeoff between, if you're using a language versus if... the language using you, I guess. I don't know, sorry, that was a bad way of saying it, but—

_00:44:50 Jesse_  
No, I like that. I actually like it.

_00:44:53 Sam_  
Uh... yeah. I don't know, any other thoughts in that area? I had one other high-level question after this, but—

_00:45:05 Jesse_  
Yeah, I guess, maybe to speak a little bit more about rendering. I think right now there's a really interesting thing where differentiable renderers are sort of implemented separately, like Mitsuba or SLANG.D. They're implemented separately from our main machine learning systems. So there's some divergent development, and I think there's good reason for it to some degree, in that there are lots of things you need in rendering, like thinking about integrals, and lots of inference, sampling stuff that I think is really—yeah, they do a lot of sophisticated stuff, that maybe is a little bit specific to the setup. But I would say I don't see any reason they shouldn't merge eventually. But also, the performance considerations are different. I mean, you're working at the frontier of performance, expressivity. And so, it's not surprising, but I would love to see these fields come together, and I don't see that they shouldn't. Maybe just to make a quick mention of probabilistic programming and these sorts of probabilistic modeling problems: I would say lots of the problems in probabilistic modeling and in computer graphics, there's some overlap there. And I'd say, insofar as computer graphics is, from an industry perspective, a more mature field, there's a lot of tools, really amazing tools, that do a lot of crazy things. So I would love to see more—I think there's some opportunity for cross-pollination in that there's been a lot of PL work in probabilistic programming, and a lot of engineering work in computer graphics, as well as some amount of PL work like the stuff I've done, or a handful of other people in the PL community have done. Like Jonathan Ragan-Kelley, for example. So, yeah, that is to say, I think there's a lot of opportunity for cross-pollination, and, ideally, the same challenges that you have for doing efficient inference in probabilistic programming—getting a low variance estimator—these things are also one of the core concerns in computer graphics. So, I think I would love to see consolidation between those two fields. Whether we'll meet in the middle—we'll be able to make it all the way to the same system the machine learning people are using—I'm not sure. But I think there's probably lots of opportunity for machine learning to improve, by, for example, making models that have moving discontinuities. Yeah.

_00:48:12 Sam_  
Yeah. Okay, I have two follow-up questions here. One is: there's the JAX-style or CUDA-style way of thinking about parallel programming, and then there's the shader-style way of thinking about parallel programming. And these are kind of different. And I'm not an expert in either of them, I'm hoping to learn more in the next couple of months. I guess do you see those as also trending toward consolidation, CUDA-style versus Slang-style, shader-style? Or do you think those are gonna stay distinct?

_00:48:47 Jesse_  
That is definitely beyond my expertise.

_00:48:49 Sam_  
Okay, fair enough.

_00:48:50 Jesse_  
Yes. No, I'm—if you find out, tell me, I'm interested! But yeah, I would say I am not that applied in my rendering work.

_00:49:02 Sam_  
Fair enough. Ah shit, I had another question but I forgot what it was. I'll ask you if it comes back to me. Um... yeah, the last question I had on my tentative script was, "What gaps currently exist in automatic differentiation?" I know we've talked about this throughout the time, but if you have maybe specific gaps that you're interested in pursuing in future work, or other gaps that you're like "Hey, this exists, but I'm not really interested in pursuing," or communities of users that you think are currently underserved?

_00:49:35 Jesse_  
Right. So, right, so the communities that I've been highlighting are doing things like physics or physical simulation, solving inverse problems, rendering, engineering, robotics. I would say those are the users I think of as underserved by AD systems at this point, and I would say there is a lot of interesting approaches to solving these problems. And I think there's a barrier to the current PL community breaking into this area, insofar as there is a fair amount of continuous math there. And also, someone's trying to solve some gnarly problem, and they came up with some gnarly solution. And there's a lot of work in trying to pull out generalizable concepts, and not getting lost in the forest. So I think there are challenges here, but also, I think, it's a pretty rich space, and I think the gaps look like: rendering, integration... To give an example of a few technical tools that I haven't seen in AD work, that I think would be useful for AD work, in the presence of integration: differential forms. So I've just started doing more with differential forms, and they just seem super powerful.

_00:51:13 Sam_  
Exterior algebra, as a first-class construct?

_00:51:17 Jesse_  
Yeah, yeah, or even just hidden in the compiler as a way to stay sane while you're doing really hard stuff. Exterior calculus—there's a whole region in the computer graphics community, as well as in the applied math community and physics community, that is fluent in these tools, and they seem very powerful and very useful, and I think there is definitely an opportunity to bring them to bear and use them to build better compilers, to understand how to build better compilers. Just to give an example. And then of course, distribution theory. And I'd say these are little-used technical tools that have a lot of power.

_00:52:13 Sam_  
Going back to something we talked about earlier—I realize we're getting close to time—you mentioned that some users are like, "Hey, I just work out my derivatives and then code them by hand, and it's not that hard." What are your thoughts on that? Do you feel like maybe they're just really good at it, and AD wouldn't actually be much of a value-add for them? Or maybe they underestimate how much of a value-add? Or maybe PL people, because they don't know continuous math very well, overestimate the value-add of AD?

_00:52:45 Jesse_  
Yeah, I would say, a bit of everything that you said. I would say, there are definitely people who have done things a way for a long time, and it's just, they don't want to do things a different way. And sometimes that's a mistake. But I also think, talking to some people with views like this and looking at their applications and thinking about them, I'm like, well, shoot, yeah! AD can't really serve you in its current state! It actually might be the least time, and the outcome that you have the most control over, to hand-code them. And insofar as that's true, I think that's a reflection—I don't think these are the sorts of problems that are insurmountable. I would say these are cases where—people making their optimal decisions to solve their problems are not using AD, and that's because AD tools are not yet mature enough, on this axis, to serve their needs.

_00:53:48 Sam_  
Yeah, makes sense. Yeah, after we turn off the recording, I'll probably ask you if you have any specific people that I can get in touch with who are doing this sort of work and not using AD tools, for the other half of this interview study, 'cause I think that would be super fascinating.

_00:54:03 Jesse_  
Yeah, Reviewer 2.

00:54:3 Sam
Yeah. Or if you happen to know anyone specific. But, yeah, we can talk about it after the recording. Yeah, we're pretty much at time. Any closing thoughts that you want to say or talk about in the next couple minutes?

_00:54:19 Jesse_  
Yeah, I would maybe like to call out some work that I think is fun and crazy, in a very positive way. There was a functional automatic differentiation paper. I think it showed up in NeurIPS. It was by Min Lin. He's at the Sea Lab in Singapore. And I just read this paper, and they were thinking about calculus of variations sort of stuff. There are all of these techniques that show up in engineering, that right now, you do a bunch of math on paper, and then you implement something for your compiler, and your compiler doesn't know everything about the problem setup, and then it can do less potentially than it could do otherwise. And also, there's more user burden, where the user has to do all this work before they write their code. And, I dunno, I read this paper and it made me happy. It seemed different than the way people had been thinking about the problem so far, and it seemed to engage with the sort of problems that show up in engineering. And I think broadly, this is what I'm really excited about. If there's one takeaway—if someone's a grad student just starting off or looking for a place to do work—I think this intersection of these sort of scientific or physical problems, and AD, is very rich green pastures, and I think there's a lot of fun to be had here. And a lot of potential.

_00:56:13 Sam_  
Sweet! Sounds good. Yeah, I guess we can probably end it there. Yeah, great note to end on. Let me end the recording here.
