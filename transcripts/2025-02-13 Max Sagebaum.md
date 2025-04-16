_00:00:02 Sam_  
Alright. So, this is an automatic differentiation interview with Max Sagebaum. Max, thanks so much for taking the time today.

_00:00:13 Max_  
Thanks for having me.

_00:00:14 Sam_  
Yeah, of course. Yeah, so, we can kind of jump right into it. The first question that I have in my loose script here is, what sets your work in automatic differentiation apart from others? So, some examples might be: what gap motivated you to begin your work? How does this gap impact people who need to compute derivatives in practice? And then, how much of this gap might still remain today, either current work or future work or other factors?

_00:00:40 Max_  
Oh, that's a lot.

_00:00:44 Sam_  
We can do one at a time!

_00:00:46 Max_  
I studied at the Humboldt University when Griewank was still there. So Griewank was my grandfather of AD, or my father of AD, and I heard the lecture from him. And then I did my thesis about differentiating a software with Nico Gauger, and that started me in the direction. So I was always very—I like to program, and I studied math at the time. So, and since this AD is a nice connection between mathematics and software engineering, this really hooked me up, and that's why I like it. So, that's why I kept doing it. The reason I was doing it, or what financed me over the last fourteen years basically, is applying AD to software where it's not yet applied. So, a bunch of the work we've done on the TRACE code—that's the turbomachinery code developed by the DLR Cologne in Germany—and we applied there operator overloading at this time. So, we used the tool of Uwe Naumann, so it's the dco tool for that first. And during my time in Aachen we already started differentiating also the SU2 codes. This was a master thesis of Tim Albring, and for that we couldn't use dco. So we were looking for other tools, and then Tim used Adept, because at the time being, it was the only other tool that used expression templates. But during applying it to SU2, Tim made a lot of modifications to Adept, in order to make it work for us there. And then, when he moved to Kaiserslautern—in 2014 or so—we decided that if we want to continue to support SU2, we needed to basically do a new tool that is free to use, because dco is until now not open source, and this wouldn't have worked for SU2. So we needed a new tool that supports all the features. And so basically, the CoDiPack was evolved from that. And we talked with the developer of Adept—I think his name is Hogan, I don't know his surname or so.

_00:03:36 Sam_  
Yeah, let's check. I was reading the CoDiPack paper earlier today, but I forgot the Adept author's name.

_00:03:43 Max_  
Yeah, so, we contacted him, but he wasn't interested to basically make a new version of Adept that we could then develop together or so, and then that triggered that we basically develop CoDiPack by our own. And until now, that's still the driving force for me, to be present in AD, and to develop this further, because there are still some topics you need to tackle, especially these new communication patterns coming up. You need to develop something, this more specialization of linear algebra routines. You need to take care of this in the tools, and so on. So that's still the driving force, why I'm doing it.

_00:04:37 Sam_  
So I guess it sounds like the work kind of continues, right? Like as you're working on applying AD to specific domains, you keep needing more things, and you keep needing more work on the AD itself, as you're applying it to a given domain.

_00:04:54 Max_  
Yes, sure. And then always the thought: how can you generalize this? How can we make this as a helper for CoDiPack so that other people, or mainly, we can use this then in other software, so that our lives get simpler and simpler? New problems always raise new problems, but maybe it gets a little bit easier to solve when you have tools lying around.

_00:05:14 Sam_  
Yeah, makes sense. So is dco—I know in the CoDiPack paper it says dco is closed source—is that still the case?

_00:05:28 Max_  
That's still the case, yeah. So there's a new version, but that's not the real dco. So it's still distributed by *n*AG, and you don't get hold of it.

_00:05:43 Sam_  
So, is the reason for dco being closed source, to make it easier to continue funding the project? Or is there other reasons?

_00:05:53 Max_  
I don't know this for sure. So this is just what I've been learning by talking with the guys from Uwe Naumann's group, because that's developed by Uwe Naumann's group. Two or three positions by Uwe are funded from this, basically. Let's say, develop dco, tackle AD problems of the customers of *n*AG, and then apply it there. And that's basically their driving force for future research and future development of dco. So it's kind of funding this.

_00:06:33 Sam_  
Yeah, so that makes sense. So it's like, if dco were open source, then there just wouldn't be as much funding to fund—you said there are two or three people that are funded by the money that comes through dco licenses, I guess?

_00:06:47 Max_  
Probably, yes. So it always depends a little bit on what connections you had. Uwe was very close already with the *n*AG guys, and I think then it came naturally to be distributed under *n*AG. And so that's also the history and the possibilities you have. If Uwe wouldn't have done by himself and not with *n*AG, I think then he wouldn't have the driving force behind it, because *n*AG is a well-known name, and they can sell this. You cannot sell this by yourself.

_00:07:23 Sam_  
Right, yeah, that makes sense. So it's kind of like a partnership with a larger—I'm not familiar with—did you say "nack"? How do you spell it?

_00:07:33 Max_  
N-A-G.

_00:07:34 Sam_  
NAG software...

_00:07:37 Max_  
Numerical Algorithms Group.

_00:07:39 Sam_  
Okay. Yeah, so they're well known, I guess, in the scientific computing space, you're saying?

_00:07:46 Max_  
Um... I have no clue, I've never worked with them or so. But in finance they are quite big, I think.

_00:07:53 Sam_  
Okay, gotcha. Yeah, I'm looking—they have a nice webpage. They talk about HPC, AD... yeah, makes sense. And so, would you say that dco is the highest quality AD tool right now? Or is it CoDiPack? Is CoDiPack the highest quality one?

_00:08:20 Max_  
I would say, from a performance viewpoint, they are quite close together. Maybe one is in the one scenario a little bit faster, the other in the other scenario a little bit faster. So, feature-wise, dco has some features that CoDiPack doesn't have, or that are better developed. And then CoDiPack has some features that are not developed in dco, so, depends, then, on what you're using. So, for example, one feature that CoDiPack has is that we have Jacobian taping and primal value taping in the same tool that you can compare this. No other AD tool that I know of, at least operator overloading, has done this. Usually you have just one path that's implemented, and you cannot even exchange the identifier management for your AD tool in the background. So CoDiPack has different identifier management schemes and has the different taping approaches. So you can basically choose up out of four different tapes.

_00:09:35 Sam_  
Okay, so the four different tapes are the different kinds, like Jacobian taping, primal value taping, or...?

_00:09:42 Max_  
Yes. So you have Jacobian taping with a linear index management approach, Jacobian taping with a reuse index management approach, and the same for primal value taping. And dco also used Jacobian taping with linear index management approach, but they changed the index management recently. They're doing fancy stuff that I have read a little bit about, but not fully understood. So it's not really a linear index management, something in between. So it's another holistic approach to reuse index management. And I think the reuse index management that we have—it comes close to what they have. Our approach might be a little bit better. I haven't had time to compare it, so I don't know which one comes out first there. But in general, the linear index management with the Jacobian taping is the most robust one that you can use, because if you have C-like memory operations in your code like `memcpy` or so, the type actually supports this. That's no problem. You don't get any errors. If you do the same thing with the reuse index management approach, you will get faulty results, and we cannot guarantee anything anymore.

_00:11:04 Sam_  
Yeah, that makes sense. That was the one part that I was a bit unclear on when I was reading the paper earlier today, 'cause I'm not familiar with the reuse index management approach. Is this specific to these sorts of operator overloading tools? Or does the same idea apply if you were doing source transformation?

_00:11:24 Max_  
It's kind of specific to operator overloading tools, but you have it also a little bit in source transformation. So in operator overloading tools, this basically manages the lifetime of the adjoint variable. So you know you have the lifetime of your primal variable, and that's coupled to the identifier. In the reverse mode you have the same, but just backward. So, and then you can reuse them. And the problem is, when you have C-like memory operations, your identifier gets copied, and you cannot keep track how often it was used. So then you have two times the same identifier, and the lifetimes then mix up, so you cannot control it anymore. And that's what is giving you problems. And with source transformation, it's kind of the same. So you need to detect when a value is overwritten and then store this. And this is kind of also—the lifetime of the value has ended. So you need to then restore it when the lifetime end of this value. So it's kind of a little bit of the same problem.

_00:12:37 Sam_  
Okay, yeah, makes sense. Yeah, I'll have to learn about it more. You were at AD in September, right? I forget.

_00:12:49 Max_  
I was at EuroAD, yes.

_00:12:51 Sam_  
Okay. The one in Chicago, or...?

_00:12:56 Max_  
Yes, yep.

_00:12:57 Sam_  
Okay. Do you remember Klaus gave a talk about the virtual memory approach for having the adjoint memory layout mirror that of the primal memory layout? It was a lightning talk.

_00:13:15 Max_  
Yes. Johannes and me sat down afterwards and tried to implement this in CoDiPack.

_00:13:22 Sam_  
Oh, okay, did it work out? Or not really?

_00:13:25 Max_  
I have to ask Johannes about that. He did this, and I think he did not delve any further into it because he would've had other things then to do.

_00:13:38 Sam_  
Okay. Yeah, the reason that I bring it up is because I'm working on a tool right now that's using that approach, that's basically just like, you have mirrored memory, and then in the reverse pass, you just use the same layout. And so I was kind of curious if the reason that I didn't know about these linear index or reuse index approaches was if the mirror memory layout approach is an alternative to those, and they're just different choices in that design space.

_00:14:14 Max_  
They are kind of different choices. So is this a source transformation tool or an operator overloading tool?

_00:14:19 Sam_  
It's source transformation. So I'm working on doing it for WebAssembly code. So you just take the WebAssembly, and then in WebAssembly, you can declare multiple memories that each have their own index space. So a pointer is just like an integer that's an offset into this big memory. So if you can just modify the source module to say "now I have two memories instead of one," then you can use the same integer index as a pointer in both of those, so they share the same layout. But I'm guessing that's something you can't really do in an operator overloading tool, because you're operating at a higher level, right, where you think of memory allocations and things like that?

_00:14:56 Max_  
Yes. So, if you're doing source transformations, this index management approach usually doesn't come up because you work with the original variables in your thinking and in how you design or how you generate, then, the code. And we in operator overloading tools, we don't have this, we don't have the original variable. So we need an identifier that we create by ourselves to manage that, and that can be anything. It could be ducks, it could be rectangles or so of different sizes, but numbers are usually very appropriate, and that's why we—

_00:15:35 Sam_  
Yeah, that makes sense. Yeah, that makes a lot of sense. Okay, cool. So, right, and so this doesn't apply to the tape, it only applies to the adjoint values, right?

_00:15:44 Max_  
Yes, yes.

_00:15:46 Sam_  
Yeah. Yeah, that is cool. So, I feel like I was kind of under-informed about these primal taping versus Jacobian taping, because I think in my mind, I had for a while only thought about Jacobian taping, and didn't realize primal taping was an option. And then I kind of flipped—so you're saying that Jacobian taping tends to be the more memory-efficient one of the two choices? Or is it kind of a toss-up, and you can choose between them in different cases?

_00:16:13 Max_  
If you are looking at it on the paper, then primal value taping is the more memory efficient choice. The problem is that Jacobian taping, you can apply more optimizations on the fly. For example, if you have a statement that has six arguments, and from these six arguments you have only three are active, then in a Jacobian taping approach, you just need to store the relevant information with respect to these three arguments. In a primal value taping approach, you still need to store the information with respect to the six arguments. And because, at least in CoDiPack, these three arguments are passive values, they have the identifier zero. So we cannot identify them directly. We in addition have to store the primal values. So we have to store the full everything, and in addition, some additional data. And it depends on the code how often you have these passive values, how this occurs, if it's really more efficient or not. So the paper where I introduce expression templates for primal value taping, on the Burgers test case you can see this because it's a completely optimized test case—you don't have any passive values, there are very, very few. So there, you see its advantage to have this primal value taping approach. But if I apply this, then, to, for example, SU2 or the TRACE code, there I get on par or higher memory, and "on par" for primal value taping means that it's slower. So I will use Jacobian taping.

_00:18:01 Sam_  
Yeah, okay, that makes sense. I'll have to look more. Can you correct my understanding if I'm wrong—my understanding is that for primal value taping, the idea is, if you do a nonlinear operation like multiplication or division, then you store the arguments on the tape, and then Jacobian taping is just, you just store the thing that you're going to multiply to get the adjoint in the backward pass. Is that accurate?

_00:18:23 Max_  
Yes, yes. Right. So, Jacobian taping stores the Jacobian, and for primal value taping, you store the primal value of the left-hand side—the value you are writing. So that's already an optimization. If you would store the primal value of the right-hand side of the arguments, that wouldn't be good. You can optimize it so that you store the argument on the left-hand side.

_00:18:48 Sam_  
Wait, wait, say again? So let's say that I have like—I'm multiplying like `a` times `b`, and I'm assigning that to `c`. You're saying that I only store the value of `c`, and I don't need to store the values of `a` and `b`?

_00:19:00 Max_  
Yes, because `a` and `b` are already active values, they have an identifier. And so when `a` and `b` have been assigned, you have stored them already, so you can access them under the identifier. So therefore you don't need to store them.

_00:19:16 Sam_  
Right, right, I see what you're saying. Yeah, okay, so the idea is, if you just wait until you see a multiplication, then you might store—if you multiply `a` times `b`, and then later I multiply `a` times `c`, then I'll have stored `a` twice. So you want to not store a thing multiple times. Yeah, okay, so in a source transformation setting, where you can kind of look at the whole thing, I guess, you would probably just do some sort of data flow analysis of this, right? Like, is this what Enzyme does? They just do like a lot of data flow analysis? And so, does the concept of primal value taping versus Jacobian taping not really apply in that case, since they're just doing a lot of analysis beforehand anyways?

_00:20:04 Max_  
It doesn't apply because you have the primal values usually available. So therefore—you could also do source transformation with the Jacobian taping approach. But then the advantage that you just need to store the overwritten values wouldn't anymore apply, because then you need to store all the Jacobian values of everything. So therefore, you usually don't think about this.

_00:20:33 Sam_  
Yeah, okay, that makes sense. Yeah, sorry, this got kind of a little bit in the weeds.

_00:20:39 Max_  
No problem. So maybe to add something there on Jacobian taping versus primal value taping: what we have talked about, this applies just when you say, "Okay, I'm looking about real values," that you do it on `double` values. If you look into AD for domain-specific languages, what I'm trying to develop for C++, and what we have seen in, for example, JAX, PyTorch, or so, they are basically doing AD on larger structures like matrices, vectors, and so. Then Jacobian taping becomes very inefficient, because the Jacobians you need to store would be tensor objects of very high dimension. And then it's more appropriate to store primal values.

_00:21:38 Sam_  
Right. Yeah, exactly. Because yeah, it seems like in general, these C++-space tools like all operate on the level of individual scalars. But that's largely because the code is not running in parallel. And then once you have to start thinking about running things in parallel, it makes more sense to think about things at a matrix or tensor level. Right? Is that more or less accurate?

_00:22:02 Max_  
Yes, yes, that's accurate. But usually the problem starts already because with operator overloading, it's simple because the number of operations you need to overload is like, the number fifty or so. If you look, for example, at Eigen and see what kind of operators you have there, it's like in the range of, I would say, one thousand. That's really heavy stuff. To handle everything becomes quite cumbersome, and that is the advantage of PyTorch and so on. So, first, you have reflection in the language that helps you because you can on the fly exchange the type you are using. And NumPy is very standardized in these—so everybody, if you do something scientific, you use NumPy. And if you handle AD for NumPy, then you're basically done. And then you need some extra cases for a few stuff, but that's it. So that's really what's driving these implementations.

_00:23:10 Sam_  
Yeah. Yeah, makes sense. And so, this is something I've been curious about, because you have PyTorch, and then you have JAX, which takes it a little bit further, and they're like, "Hey, we have like a restricted language." They use reflection to build up some sort of expression, and then you can do things like, take a function that you're mapping over a bunch of different elements and then vectorize that. So then, instead of having that function run over and over on scalars, it automatically works at the level of tensors, and then operates over tensors, and then you can compile that all at once, so that you can do kernel fusion, things like this. And so, to me that seems like, "Oh, you would expect that to be the best." But then also you have Enzyme, which takes the exact opposite approach of, optimize everything before you even think about automatic differentiation, and then running automatic differentiation on that, on a much, much lower level representation. And it seems like—I don't currently have a good feel for what are the relative performance of those two tools. But I guess—I don't know. I've been asking a lot of people this question of, what are your thoughts on this idea of, should you optimize first, should you differentiate first? Is it domain specific?

_00:24:19 Max_  
Oh... good question. So I think it's kind of the same approach. When Enzyme sees the low-level LLVM code that already has the optimized version, then the compiler has already done the fusion of everything, and made probably also already the vectorization and so on. And Enzyme can work on that stuff. And that is very efficient because Enzyme doesn't basically see loop structures really—it just sees "Okay, now I'm here in this regime, and do the reverse of it." And when you optimize again and then compile it, the compiler can then already see "Okay, I'm doing a loop basically over this stuff in the reverse, I can vectorize this again." So this is kind of what you gain there. And if you do this on a higher level, that you say "Okay, I do reflection on this, generate the kernel, compile this down"—then you are basically writing a compiler again, and the optimizations in the compiler. And Enzyme gets this basically for free, that it can use the compiler optimization that's already there. So therefore, I would think it's kind of the same. It's just on a different level of what you need to do by yourself.

_00:25:53 Sam_  
Yeah, that makes sense. I mean, this is kind of like what it ends up being, is like, any sufficiently advanced automatic differentiation tool becomes a compiler, right? And so Enzyme, you're saying, just wins by saying "Well, we're just going to use the compiler that already exists, and then interface with all of its internals," and then—yeah, that makes sense.

_00:26:15 Max_  
Yep. And so the problem is with operator overloading, we don't have this. So we cannot generate anything. Well, the other way around: in C++ operator overloading we don't have this because C++ has no reflection. So I'm really looking forward—so in C++26, they want to do the first meta operator, and that you can reflect about things. That will not yet include that you can reflect about the body of a function and the statements that are there. So then that will be probably C++29. But this could also be very interesting, that you can say "Okay, now in my operator overloading tool, I see the function here that is given me, I can then go through the function and do a source transformation on the fly and then integrate this in my operator overloading tool," or even do a full source transformation with this. That could be very interesting.

_00:27:19 Sam_  
Right. Yeah, I mean, that makes sense to me. I guess what I wonder is, what's the benefit then of doing, of using an operator overloading tool instead of using a fully compiler-based tool like Enzyme?

_00:27:33 Max_  
So, one of the differences is that Enzyme doesn't apply always. So, that's one of the problems. And operator overloading is usually more robust with respect to what techniques you can use, or how legacy code is handled. So if you're doing too much pointer magic and so, then Enzyme will have a big problem differentiating this. And with operator overloading, you usually get then errors that you can handle, and then program something specific for that. So, I think there's still the valid approach. And one other thing is that with Enzyme, you are locking yourself in with the Clang toolchain, and for industrial partners, that's not an option.

_00:28:29 Sam_  
Right, okay.

_00:28:30 Max_  
That's really not an option.

_00:28:32 Sam_  
So, right, yeah, I'm curious about two things there, I guess, one: so, why is it that for these industrial partners, they're not able to use the Clang toolchain? Why is that? They use GCC, or they use a different toolchain entirely?

_00:28:47 Max_  
They want to be free in what they choose. So, for example, the Intel compiler is still very, very good—if you have low-level C codes, the Intel compiler is still very good at producing very fast and optimized code. And if you could then basically have your toolchain for your regular application, and then a different toolchain compiler-wise for your AD compilation—but usually people don't want to do that. So you say "Okay, I want to have one of the same toolchain for everything I use." Therefore that's the thing. And I heard the statement that GCC is usually better at vectorizing stuff than Clang is still doing, because the vectorizer in Clang is still very rudimentary, in general.

_00:29:49 Sam_  
Interesting. Yeah, I didn't know that. For some reason—yeah, I don't use GCC much, because I work a lot with Rust, and so Rust has an LLVM backend and the GCC frontend is not really there yet. I guess I kind of just always went, "Oh, I'll just assume LLVM is the fastest," but I guess the other compilers are still pretty competitive.

_00:30:11 Max_  
Yep. Also, that's being less of a problem nowadays, but you still have some companies that use GCC 9 or so. So then if you tell them "Okay, now you need on your cluster, in order to compile it there or to use this, install always the newest version of Clang," and they say "Stop, stop, stop. We are still at GCC 9." So, first you would need to update that. So that's also a restriction.

_00:30:42 Sam_  
So, what is the cause for these sorts of companies having these old GCC versions? Is it that the newer GCC versions don't support their old codebases? Or is it something else?

_00:30:56 Max_  
Honestly I don't know, so I can only guess. And one thing is, they are locked in with some old Linux distributions, for example, that they don't want to update because it would be a major bottleneck. It's also kind of a validation that you need to guarantee for some things you compute, and so on, that you get the same results. And if you go to a different compiler, you get different results, and you need to redo the validation, which can include, for example, wind tunnel tests and so on. And this is very expensive, and that's why they are leaving the complete environment stable and don't want to have any updates.

_00:31:47 Sam_  
Interesting. So you're saying, if you update from GCC 9 to a newer version of GCC, you'll get different answers from your floating-point programs?

_00:31:57 Max_  
Yes.

_00:31:58 Sam_  
Oh, crazy. I had no idea! Why does that happen?

_00:32:03 Max_  
Because the optimizations get better and better, and then you get different floating-point results. And this can be quite visible. So what we had once, what was a bug report to me, is that when we, in the TRACE code, that we say, "Okay, we can do a vector mode there," and we take three vector dimensions, and compute the same in all three vector dimensions, then the result in the third vector dimension was different. And the result was different because, for the first two vector dimensions, the compiler was using regular floating-point computations. And for the last one, because he knew it was the last dimension he always computes, he was optimizing differently. And there he wasn't using then some SIMD registers. And that gave a different result, because just the precision then changed in the computation. And that was quite visible in the adjoint results. So, it was near, but it was a little bit off. And then you ask yourself, "Okay, is this a bug? Is this a feature?" Answer was, it's numerical errors due to compiler optimization.

_00:33:24 Sam_  
Crazy. Okay, I didn't realize this, 'cause I know that, you know, if you're using IEEE 754 or if you have semantics attached to the floating-point, then you could say "Only optimizations that give the same results are allowed." But it sounds like you're saying—is it GCC specifically, or if you're doing SIMD stuff, or there's a flag to say "I'll allow floating-point optimizations that give slightly different results"—this is common if you're working in these toolchains?

_00:33:56 Max_  
So, there's an option in the GCC toolchain and also in the Clang toolchain that allows such numerical optimizations that are allowed or forbidden by the IEEE standard. And for MTU, for example, for some time, it wasn't allowed to set this flag, so you could only have the strict flag set so that you couldn't optimize things it knows are mathematical equivalent, but are not on a floating-point level equivalent. These kind of optimizations were not allowed. So, I think they changed it at some point back, so that they allow it nowadays. And these changes come basically how the CPUs implement stuff. So they need to enforce IEEE standard, but in order to do that, they need to compute with a higher precision. And for some operations you have this higher precision, for some not. And therefore you can have different rounding depending on the CPU you use, and also, which kind of operations you use on the CPU, which, as example, I've given you just with these three vector dimensions.

_00:35:31 Sam_  
Right. Yeah, 'cause I know—I guess I was under the impression that nowadays, pretty much every computer that you'll see is gonna implement the IEEE 754 standard, and so they'll all get the same answers for floating point, as long as you don't do compiler optimizations. Are you saying that this is not really true in modern hardware, or these companies will be using older hardware for which this wasn't the case in the past, even if it's true for typical consumer hardware nowadays?

_00:35:53 Max_  
That's still true for all hardware, I think, because every manufacturer implements these operations a little bit differently. And internally, the CPUs don't compute with 64 bits—they compute with probably 90, 92 bit, or some—so, in order to do the rounding, and how many bits they have, and how these micro codes they use come to that result—that we don't know, and that is different from every vendor to vendor, and can be even different from architecture to architecture. For example, if you imagine the Meltdown bugs and Intel where they changed the microcode, how things are done—that can change even for the same processor, from one version to a different one.

_00:36:44 Sam_  
Interesting. Yeah, I was not aware of this.

_00:36:48 Max_  
I wouldn't say it's always the case, and usually it's probably quite stable for an architecture, but it can happen.

_00:36:55 Sam_  
Yeah. Okay, so then—I don't know if you'd know the answer to this one, but—again, as I mentioned, I do a lot of work with WebAssembly, and so, in WebAssembly, you have strict semantics for all the floating-point operations, and the only case in which it's not completely determined is the NaN bit patterns. So you can give different NaN bit patterns, but everything else has to be the same. And so, do you happen to know how that would be implemented in a Wasm compiler? Is it just going to be slower on some CPUs? Or 'cause it seems like, as I understand it, it's not actually CPU dependent, but I'm not sure how they're doing that. I just kind of assumed that CPUs were capable of doing deterministic or equivalent floating-point math, but maybe I was just mistaken about that.

_00:37:51 Max_  
So, with NaN patterns and how this is handled in the IEEE standard, I cannot give you any clue.

_00:37:59 Sam_  
Yeah, not for the NaN bit patterns, but I guess I was under the impression that for everything other than the NaN bit patterns, you were guaranteed to get the same results according to the IEEE standard.

_00:38:11 Max_  
Yes, you... yeah, usually yes. But in practice it's usually different.

_00:38:21 Sam_  
Okay, so yeah, that makes sense. So you're saying like, there's still a lot of hardware that doesn't actually implement the IEEE standard correctly, I guess?

_00:38:30 Max_  
Um...  yeah, but still, the IEEE standard—so I don't know this by heart, but there are some—they just defined how the result should look and how you should round, what's computed. And what you have in your values, that's not defined. So it just defines how you should round in certain use cases, but how you get to that value that you then can round, that's not totally specified.

_00:39:07 Sam_  
Right. Right, but I mean, if—yeah.

_00:39:12 Max_  
If your CPU supports the same—well, yeah, let me answer it that way: for the standard operations—multiplication, addition, and so on—this is fixed. There, you will probably get the same result on everything. But for fused multiply-add operations, how do you implement the standard? Do you implement it on the result, on the results in between? And usually they implement it on the complete result. But how the results in between get rounded and so, that's not defined. And that's there where you get numerical differences.

_00:39:59 Sam_  
Okay. So you're saying the standard is ambiguous about when you're allowed to round or not, for certain operations that could be thought of as composites, or could be thought of as atomic operations? Is that more or less accurate?

_00:40:12 Max_  
Yes, yes.

_00:40:14 Sam_  
Okay. Yeah, 'cause I know, for instance, WebAssembly doesn't have a fused multiply-add operation. And I'm guessing that this is the reason why, but I'll have to look into this later. So that makes sense.

_00:40:25 Max_  
So probably it doesn't has, per se, fused multiply-add. But the compiler in the background, the—I don't know if it's compiled or if it's interpreted—

_00:40:40 Sam_  
You can do either one.

_00:40:40 Max_  
The compiler or interpreter can use fused multiply-add operations in its optimizations.

_00:40:47 Sam_  
Right. Yeah, yeah. So I guess this would be like, since you compile it at runtime, just-in-time, you know what hardware you're running on. So you know the behavior of your CPU, and whether you're allowed to use fused multiply-add, but you can't have that in the semantics of the IR, or something like that.

_00:41:06 Max_  
Yes.

_00:41:07 Sam_  
Okay, interesting. Yeah, that is really interesting. I didn't know this. Sorry for the long tangent about IEEE, but yeah. Um... yeah, I have various other questions. I don't know if you have any other thoughts about the things we've talked about so far, or I could move on to other questions that I had written here.

_00:41:27 Max_  
Yeah, please move on.

_00:41:30 Sam_  
Okay. Let's see... we talked about some of these things... Yeah, I guess... I'd be curious to know a specific instance—you mentioned that you're working on this ice sheet modeling application. In this specific application, what's an example of something that came up that exposed a limitation of CoDiPack, or the AD tool that you're using, that then you had to go back and make changes to the tool? Is it that these sorts of changes that you have to make to the tool come up at a similar rate over time, or do they decrease over time as the tool gets more general and you encompass more domains?

_00:42:21 Max_  
So, usually, at the core of CoDiPack, we don't need to do that much anymore. So maybe a new math operation, but that's implemented in a few minutes. So that's not a problem. What usually comes up is in optimizing stuff. For example, in ISSM, they have the matrix assembly, where they sum up a lot of values. So, if you do this in a regular way with CoDiPack, then you have a sum—with the Jacobian taping, that is an operator with two arguments. And so you need to store... 2 times 12 bytes, plus 1 byte, would be 25 bytes per operation. But if you sort these sums and then make them larger, so that you can then say "Okay, I sum now up the whole column at once, with 20 arguments," then, these 20 arguments would have been previously 20 or 19 different operations. So 19 times 25 would be something. And if you now have this one operation, then you have just 12 times 20 arguments, which is a much lower value. And so that's what I'm usually looking at these codes. So, I do a trace of the functions where the most memory is written on the tape, and then I look at what's done there and try to optimize this. And then this then employss that I say "Okay, this I need to do in ISSM because it's very special," or "I can provide a general helper for CoDiPack and then use this helper in ISSM, because it's more CoDiPack specific." So that's where general CoDiPack developments come from.

_00:44:21 Sam_  
So you're growing a library of helper functions and utilities in CoDiPack for these sorts of cases?

_00:44:28 Max_  
Yes.

_00:44:31 Sam_  
Makes sense. Do you feel like the process of this sort of performance engineering for AD is significantly different from the performance engineering process without AD, or is it pretty similar?

_00:44:46 Max_  
It's different. Because usually, performance engineering for the main code just looks on access patterns—how you access your data, how you can go through your CPU, do you have register spillage, and so on—if your L1 cache fits, and so on. That's what you look at in the primal performance measurement. With operator overloading AD, you can throw all that out of the window. Everything spills, your L1 caches will always miss, and so on. So, that you don't have. So therefore you just look at how to reduce memory, because when you need to access less memory, you are usually faster and more efficient, or can have larger problems. So that's what you usually look at. So it's usually completely different. And the other way around makes—what you usually want to do for AD is to reduce the number of operations you do. And if you do that, that helps usually the primal program. But normally, the primal program isn't hit that hard by numerical overhead. For example, one thing I analyzed in ISSM is that they did a matrix-matrix multiplication, and for CoDiPack this was a large chunk of memory on the tape. But since these usually have been diagonal matrices with just ones, for the numerical evaluation, it was very fast, so they didn't even see this. So to optimize this for CoDiPack was just: have an extra `if`, look if it's just a diagonal matrix with ones, then you don't need to do anything here, and `return` this. This helps also the primal program, but usually that wouldn't show up in the performance analysis.

_00:46:55 Sam_  
Right. That is interesting. I've run into this before in a program that I was trying to see why it was so slow. And it was like, make a scalar, create an identity matrix, multiply the scalar by that, and then multiply that by another vector. And I was like, "Why are we doing this?" It changed the program from being linear time to being quadratic time. But it's interesting to hear that this shows up in practice in a lot of other cases.

_00:47:20 Max_  
Yeah. One interesting bug I found before Christmas was they're doing this sum of the matrices. And when they took care of something, they changed the column index to `-1`. But in the outer sum they never checked if the index they are now doing is `-1`. So what they were doing—they were, "Okay, I'm tackling the first row, summing it up, changing everything to `-1`." Then they would recognize "Oh, I have a `-1` here," they would sum up the `-1`. Then the next row was something else. So the numerical computations they were doing, they are getting larger and larger because this `-1` column they were never using afterwards was accumulating and accumulating when they were going through the process. So, and for this specific operation, that was a numerical overhead of about 90% or so.

_00:48:22 Sam_  
Right. And this was an overhead that even showed up in the primal program, or only showed up once you were looking at AD?

_00:48:30 Max_  
I haven't measured how much influence it had on the primal program, but I would assume it would also make a difference. The problem with ISSM is that this implementation they're using there—this is specifically for CoDiPack, so they usually use it only when they do the AD evaluation. When they do regular primal computations, they're using the PETSc matrices, and there they don't implement it by themselves.

_00:49:01 Sam_  
So, you're saying, they have two separate codebases that are meant to do the same thing—one of them uses AD, and then one of them doesn't use AD but it uses other matrix libraries to make it more efficient for just the primal part?

_00:49:14 Max_  
Yes.

_00:49:15 Sam_  
Interesting.

_00:49:16 Max_  
So, they have an abstract matrix and an abstract vector in ISSM, and they have two implementations for that—one that is their own, and one that's driven by PETSc. And then they can even switch this on the fly, so they could compile it once, and then they could use both and just flip a switch in the configuration file. So, that's possible. But for CoDiPack they're restricted to using just their own implementation. And one of the projects that I'm currently doing is to enable this with the PETSc matrices, so they can basically throw away this other implementation.

_00:50:00 Sam_  
Oh, I see. Yeah, 'cause it seems kind of crazy to maintain two different versions of the code in order to use AD—I'd expect that they would drift out of sync a lot.

_00:50:09 Max_  
The interesting thing about this is, that's just a very small part. So it's not the big part of the code. I would say it's like 20%. So it's not very small, but also not the majority.

_00:50:24 Sam_  
Right, interesting. And you're saying it requires changes to the core of CoDiPack in order to support this matrix library, just kind of another—you know, you define the derivatives for all these matrix operations, and then it ends up working out?

_00:50:38 Max_  
Yes. So that's like what we did with MeDiPack where we handled MPI—it's just using CoDiPack in the background, and CoDiPack has all the basic features that we need. And then it's just a different library that sticks all those things together to make it work.

_00:50:57 Sam_  
Yeah. Yeah, makes sense. And so, were you saying that this is an example in which you're not really able to use the Jacobian taping approach anymore because you're working at the level of these matrices? Or is that kind of unrelated?

_00:51:10 Max_  
The current implementation uses Jacobian taping and works on this level. So, but when I store—for example, today I've done the matrix-vector multiplication—I then store there the primal matrix. But I need to store the argument and cannot store the result because I just see the matrix here, and how this matrix is created I cannot take care of. The next step would be to apply this domain-specific language approach, that I say "Okay, I decouple these matrices and vectors from the internal CoDiPack values and have them as first-class objects." But then I basically—that's what I'm doing right now for my habilitation thesis right now—but then I need a new AD tool that can handle all this stuff basically. That's what I also kind of presented at the AD conference.

_00:52:10 Sam_  
Yeah, yeah, yeah. Nice. Yeah, I'll have to look back at your presentation from September. Sweet. Yeah, we're kind of getting close to time. I had a couple of other questions I could ask, but I guess, if you had any specific things that we didn't get a chance to talk about or wanted to say in the remaining few minutes.

_00:52:32 Max_  
We can also do this a little bit longer if you want.

_00:52:35 Sam_  
Yeah, that's fine with me. I don't have anything.

_00:52:41 Max_  
So then just shoot the next question.

_00:52:44 Sam_  
Okay, great. Let's see... Yeah, I had a question that was like, "Are there any expressiveness limitations of your work—like, do you handle arrays, mutation, recursion?" But I think we've kind of covered that. I assume, since CoDiPack is an operator overloading tool, like, recursion is totally fine, I assume, right?

_00:53:07 Max_  
Yes, that's usually—some language features, we usually don't have any problems. So only when the people do very nasty casting stuff, that gives us problems. But otherwise, that's all okay.

_00:53:27 Sam_  
Yeah, that makes sense. Are there any—

_00:53:27 Max_  
The only thing I would wish for is that I would be able to overload the ternary operator.

_00:53:34 Sam_  
Oh...

_00:53:36 Max_  
That would be really nice.

_00:53:38 Sam_  
Oh, right, for conditionals. So... huh. So, how does this end up coming up in practice? Is it like, you have a branch... this is specifically for the ternary operator and not for `if` statements, or it applies to both?

_00:53:55 Max_  
For `if` statements, that's not a problem because we don't see the `if` statements, CoDiPack just sees the path that's taken, records this path. For ternary operators, you get compiler errors. For example, if you have a ternary of `a - b` and `a + b`, for CoDiPack these are two different types.

_00:54:21 Sam_  
Oh, right!

_00:54:21 Max_  
Because of the expressions. And C++ is only allowed to do one user-defined conversion, and so it cannot deduce a common type it could convert both to—just can convert the one and the other. So then you get hard compiler errors, which you need then to fix. The usual fix is that you cast both arguments to the regular computation type, and that's it. But if you have a code that's littered with these ternary operators, it's a lot of work.

_00:55:01 Sam_  
It gets really messy and gross, yeah. Interesting. I would not have thought of that. But that makes sense. So when you cast it, does that impact the performance at all, because it changes from this sort of static call to the dynamic call? Or does it not really matter?

_00:55:17 Max_  
So the cast in that case is still static, because the type is fixed. It depends on where the ternary is. If you have an assignment and then the ternary operator, then that doesn't change the performance at all, because then you create an object, just assign this object, and that's optimized away by CoDiPack. It is a little bit different if you have a larger statement, and in this statement you have a ternary operator. Then if we could create an expression from this ternary operator, then this would be included in the expression. And then we could optimize this as we optimize everything else. So, because we do the casting in this ternary operator, then basically, we record an extra statement for that. And that's not so good.

_00:56:12 Sam_  
Right. So the idea is, CoDiPack is able to look into things at the level of individual statements. Anything that's within the same statement, you can do optimizations. But anytime you cross a statement boundary, you can't. And the ternary operator basically makes you cross a statement boundary when you wouldn't have to if you could overload it.

_00:56:30 Max_  
What I'm just thinking about this—maybe I will do this as an exercise on the weekend—We could add to CoDiPack a special function that has this ternary operator, that mimics the ternary operator. And then we could tell people "Use that specific function for CoDiPack instead of the ternary operator, and you get the same deal." And it's optimized. That's probably something I could do on the weekend.

_00:56:54 Sam_  
Yeah. The only thing you wouldn't be able to do is short-circuiting, right? Because if you had a function, then you would have to evaluate both arguments always. But I assume that's fine in a lot of these arithmetic cases, right?

_00:57:06 Max_  
In operator overloading, it's done... um... we wouldn't really need to. It depends on if it's really a function call, then in C++ both would be evaluated either way. Yeah, it depends on the compiler how it's done. If it's just expressions we get there, then, because our expressions are lazy-evaluated, we just evaluate the path we will take then.

_00:57:40 Sam_  
So you're saying they're lazily evaluated, even in the forward pass? If you pass two expressions to a function, and then one of them doesn't end up getting used, then it just goes away?

_00:57:53 Max_  
Yes.

_00:57:54 Sam_  
Huh, interesting. How do you do that? Because like, the naive way I can think to implement this would be that, when you evaluate the expression, you evaluate the primal value, and then you also store the stuff on the tape, or like the procedure for the reverse pass, but I'm guessing you do it in a smarter way. I probably should just go back and read the paper in more detail.

_00:58:13 Max_  
So, the way you have headed it is correct. If I give expressions as arguments to a function, they don't get evaluated, but usually functions are not defined that way. Functions are defined in the primal values of the program, and that's our CoDiPack type. So if you give this expression as the argument, then it's automatically converted to the type, and then, therefore it's stored. So, usually we don't have this short-circuiting—it's always evaluated. But if the function is templated and you give the expressions, then it would be—

_00:58:55 Sam_  
Oh!

_00:58:56 Max_  
Then it would work. But then you can have very other nasty stuff that can be going on there that gives you problems. So, storing expressions is always, you have to do with care.

_00:59:10 Sam_  
Yeah. Yeah, makes sense. Huh. Interesting. I'll be curious to see if it ends up working out. I'll have to take a look and see if there's a pull request on CoDiPack after the end of the weekend. Let's see. Yeah, I have a few other things—I guess the main last question I had was, what gaps do you see as currently existing in automatic differentiation, or what are the most important gaps? Which ones are you interested in pursuing in your future work? What other gaps existed that you're not interested in pursuing? And then I guess, what communities of users do you see as currently underserved by AD tools?

_00:59:49 Max_  
Okay. So you mean this in a very general way, and not with respect to operator overloading?

_00:59:57 Sam_  
It could be either specific or general. Just kind of like, what do you think are the most interesting things that aren't there, or aren't good enough?

_01:00:05 Max_  
So, let me work this up from specific to more general. So, for C++, I think operator overloading tools are very well served. So, there isn't really much that needs to be done. What we need probably to do, and what I'm currently working on, is this AD for domain-specific languages that we tackle to differentiate Eigen or so, or standard libraries that are used very often. The problem with C++ here is that you don't have these standard libraries that everybody is using. There are 100 different linear algorithm packages, and you cannot tackle all of them. So that's a big C++ problem. Staying with C++, I think we have a big problem in tackling source transformations here. So, Enzyme is a good step in having source-to-binary transformation, but usually, as I already mentioned earlier, if you then get locked in in the Clang tool chain, and people don't want to have this, especially if it's about debugging. So, for example, what if the Enzyme derivative is not correct? How do you debug that?

_01:01:24 Sam_  
Yeah. Right.

_01:01:26 Max_  
So, there, you currently don't have anything about that. So there, I think we really would need a good source transformation tool, that doesn't need to cover everything of C++, but it should be able to cover the basics and cover these basics very robustly. So, in a side project I'm doing, I wanted to differentiate something with Tapenade and Clad. And Tapenade wasn't just written for C++, so—Tapenade knows to understand a little bit of C, but C++ is still very, very problematic. And I haven't used Clad—I will use it in the next future. I think that's a very good tool, but also there, the source transformation aspect is not their main aspect. They're also source-to-binary, and this source transformation is just a byproduct and not really focusing on this. And I think support would need to be increased there. So, this is basically speaking about C++ and source transformation. In general, I think a lot of people are using Enzyme, and Enzyme is used in a lot of languages. And what's problematic there is that Enzyme—if it understands the code, it's okay, it works, and it's really nice and fast. That's really good. But I think the support for Enzyme—what you can do, what are the arguments—the documentation there is really lacking, and that should be a real focus. Maybe to the point that we rewrite Enzyme from scratch to know what has been learned there, and to make it more general, so that it's better extendable and can tackle more use cases, because often it runs into very weird problems and you cannot really debug it. You cannot really see what's the problem, what you can change. For example, one example I had—I was differentiating Eigen routines with Enzyme, if I were compiling them and then optimized with optimizations on, Enzyme worked. If I disabled that and had `-O0` optimization, Enzyme stopped working, and that's a no-go. So, it should be robust there.

_01:04:06 Sam_  
Interesting. So, I'm curious to learn more about that. I'm trying to see if Billy would be willing to be interviewed as well, because I'd really be curious to hear his perspective. Is it just that Enzyme needs more development effort to work out these bugs? Or is there some sort of deeper reason why it would fail without optimizations when it doesn't fail with optimizations? Or, is there anything preventing it from giving better error messages about when it doesn't understand the program? Or is it just, there's nothing to be done really?

_01:04:40 Max_  
I actually don't know, because I have just used Enzyme as a user. I have not gone into the details how they handle all the stuff, how they do it. So I cannot really tell you. But in my perspective, it's a lack of focus. So, the focus was currently to get it working for the cases they had, and to demonstrate that it works. But there is no, I would say, a "company focus" behind it, that you basically have to sell a product. And in order to sell this product, you have to make it user-friendly, to make good documentation, to give good error messages. So I don't mean by that that they need to sell Enzyme, but kind of how you view it. So, for example, with CoDiPack, if you look at the documentation, we have very extensive tutorials. And that was always the focus, so people could basically pick up CoDiPack, work alongside the tutorials, and then do some stuff. And this kind of development focus is currently not there for Enzyme, for example, I would say. But Enzyme is not there alone. I think that's a lot for people. In the autodiff Discord, somebody said that all AD tools are badly documented. I restrained from writing there, "I would make the exception for CoDiPack," but alone the statement by itself, I think it speaks for itself that the focus of AD tool researchers is just for the use case and they get this out. But it's not a company perspective for making a product that's sellable.

_01:06:29 Sam_  
Yeah. I mean, I would say also, PyTorch and TensorFlow are probably also exceptions to this. But it seems like there's a huge gap between the machine learning AD community and the, I guess, older-school C++ AD community or scientific computing community. I'm curious if there's a way to bridge the gap between these communities, but I'm not really sure at the moment.

_01:06:52 Max_  
So for example, the JAX development team consists of five people, and one of the Google employees who presented something—for two years or so, joined a talk—said, "That's a very small team." But if you view this in the perspective of AD development, it's basically the largest team there is for development of an AD tool.

_01:07:15 Sam_  
Right. Right.

_01:07:12 Max_  
And—you have to think about this—they are doing this continuously for their full work. So usually I can only work on CoDiPack for, I would say, one-tenth of my time or so. All the other stuff, I have to take care of other things. So they—five people full-time nonstop. So that also gives them a very different drive.

_01:07:42 Sam_  
Yeah, I mean, that's just where the money is, I guess, right? There's money in ML, and there's not really nearly as much money in all these other cases. And also it seems like, resources are spread more thinly, right? In machine learning, you have PyTorch and you have JAX, and that's kind of—that's kind of it. But in scientific computing, there's like, as you said, there's an explosion of, there's so many linear algebra libraries, there's so many AD tools, right?

_01:08:07 Max_  
Yep.

_01:08:08 Sam_  
I don't know. I kind of wonder if the community would benefit from more consolidation of effort. But I also wonder if it's just like—it's so fun to make your own tool that everyone wants to build their own tool. I dunno.

_01:08:20 Max_  
Yes, so actually, it's also quite easy to write an AD tool. So you can write a forward tool in half a day, and if you know what you're doing, you can write a reverse tool in one day. And the problem is to support this and to write all the extra stuff that you don't need just for yourself, but other people need, and that then takes, like, month or two months or so. And that's the question, if you're willing to do that.

_01:08:46 Sam_  
Yeah, right. Yeah, and also, is it possible for there to be a tool that covers everyone's use cases well enough that somebody doesn't need to go off and write their own, right? Because all the papers on tools are like, "Oh, the other tools that exist weren't good enough for this case, and so we made our own."

_01:09:04 Max_  
Yeah. So, but what the views is from a user perspective? If the user searches for an AD tool, for example, C++, and he then looks "Okay, there are ten different C++ tools out there, which should I use?" If he goes to autodiff.org and has a list—that's a very big list. And I think, as a community, it would be good to first have also a duplication of tools that you say "Okay, I've worked on this tool, I'm no longer working on it, and I don't really see this—that should be used by other users." If people would start saying this, I think that would be already really helpful. And then maybe reorganize autodiff.org to say "Okay, for C++, these are the major tools, and this is best for that use case and that use case." That would, I think, help a lot.

_01:10:07 Sam_  
Yeah.

_01:10:08 Max_  
But, to start this, then you really—yeah, stuck a bees nest or so. So that's—

_01:10:20 Sam_  
Right. Yeah, I mean, it definitely seems like, you know, if you could have it so that the tools landscape is designed so that you have carved out different spaces where different tools make sense, then that would be great. But it's like, how do we get from point A to point B? I dunno.

_01:10:36 Max_  
Yes, yes, that's really true. So, there are good tools out there, and most of the tools that are still in development have very good use case and they have the user base. And if this could be better defined, "if that's your use case then use that tool," I think that would help a lot.

_01:10:54 Sam_  
Yeah. Yeah, that is really interesting. I hadn't really thought about—I hadn't heard someone put it that particular way before. So that was a really interesting way of putting it. Thanks. Yeah... I don't really have anything else in particular. So, if you have any—oh. Sorry, I closed the window. If you have any other things, I still have time, so I'm happy to chat more. But I've kind of worked through my tentative script.

_01:11:23 Max_  
No, I don't have anything more. I would have a question for you. So you said you're currently developing a new AD tool for WebAssembly?

_01:11:35 Sam_  
Yeah, it's just a side project. It's not really my main focus. But yeah.

_01:11:39 Max_  
So, is this the first AD tool for WebAssembly? Or are there already things out there?

_01:11:44 Sam_  
No, this is, like, my third tool for WebAssembly. Yeah, I had one that I wrote a paper on a year or two ago. And then before that, I had a different one that we didn't publish. And yeah, basically, the use case is using AD for diagramming. So you're like, "Hey, I want to draw a diagram, but I don't want to have to lay everything out manually." So I use AD to lay out the parts of the diagram, and then my cost function says, "These things shouldn't overlap, this should be inside this." But, since a person is editing the diagram a lot, you also want—you want it to run in a web browser. So WebAssembly. And you also want it to compile pretty quickly, because they're going to be tweaking the program that generates the diagram. And the programs generally aren't very big, so they don't run for very long. So it's kind of a different use case. Oh. Sorry, I can't hear you anymore.

_01:13:14 Max_  
You should hear me now.

_01:13:17 Sam_  
Alright, I can hear you now. The audio quality is a bit weird, but...

_01:13:20 Max_  
Yes, so, my headphones just shut themselves off because they are out of energy. So, therefore, I've now in my regular speakers, or my regular microphone. So, this is now worse quality than before?

_01:13:38 Sam_  
It's fine. I thought there was a bit of background noise, but it seems to be gone now.

_01:13:43 Max_  
Okay good, then, it's probably adjusted, because... yeah. Okay.

_01:13:49 Sam_  
I'm happy to keep chatting. Should I keep the recording going, or should I stop the recording now, since it seems like we're kind of done with the interview part?

_01:13:55 Max_  
I think you can stop the recording, yes.

_01:13:58 Sam_  
Okay.
