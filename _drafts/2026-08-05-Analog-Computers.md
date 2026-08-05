Back in the Dark Ages, before general purpose computers existed, if you wanted a program that would compute a particular equation for arbitrary inputs you had to make a one-off device. Today's digital computing allows for general mathematics and logic - a program can be written for any particular math you might want to do, and rerun on the same hardware. Such is the blessing of digital computing.  
Without this … Analog Computers were used.  
Let's say you want to know the trajectory of a projectile given some initial conditions, drag coefficients, etc. You want to know where it might land, given the angle it was fired at, the initial speed, and so on. This is a set of differential equations. You could work it out on paper … but then what if you have a series of inputs, and you need it for all of them? That's a lot of work. And let's say you need the answers in a minute. Well, that's not helpful.  
So, barring access to a digital computer and a program that can do this as it hasn’t been invented yet, you turn to Analog Computing, and build an electronic circuit designed to solve this exact mathematical problem (but only this mathematical problem), for any reasonable inputs and initial conditions.  

  

Such circuits were in use earlier in the 1900s, and once digital computing came about they were largely put aside; programs are more versatile, more general purpose, and better in almost every way for usability.  

So how does this work? My aim was to construct such a circuit for the Lorentz Attractor differential equations as an exercise in understanding this. I did go from a template, but there's enough faffery involved that I ended up learning a lot too.  

For reference, the Lorentz Attractor equations are  

 

$$ 

\frac{dx}{dt}=\sigma(y-x) 

\frac{dy}{dt}=-xz+rx-y 

\frac{dz}{dt}=xy-bz 

$$ 



But it’s not particular important to know this now. The main point is I tried to make "a circuit that did some math".  

  

First, we need to go over the basic building blocks of "mathematical circuits", for want of a better phrase, and then we'll build up to getting the whole circuit together.  

  

Op-Amps 

Op-Amps, or operational amplifiers, are a key circuit element here. I misunderstood them originally and assumed they exist to, well, amplify a signal. They have two inputs, and one output. The output is the difference of the inputs, amplified by some factor. So if you want one signal amplified, connect the other to ground (0v), and then that should be amplified on the output, and you just choose a unit with the amplification factor you want. Right? 

  


No.  

Their usage is a lot more arcane than this.  

The actual functionality has been written about extensively elsewhere, and I'm going to repeat a lot of it writ more simply, but here's some links: 

So, firstly, let's go over how they work - feel free to skip this, but it should help with later - and then I'll go over how they are used in Analog Computers. We're going to get to my actual circuit design eventually.  

 

The following is a diagram of an op-amp, with output $V_out$, and the two inputs $V_+$ and $V_-$. The other voltage lines are to power the whole thing, and these also provide upper and lower limits to the output voltage.  


There's also power lines to said op-amp, but these aren't important, they just give us the upper and lower bounds of what Vout can be. The op-amp equation is  
$V_out = A(V_+ - V_-)$ 
Typically $A$ is something on the order of 100 000. This is where my initial confusion lay - I assumed it would be 2 or 3, and maybe you'd buy an extreme one that had a factor of 10 or you'd chain a few to get a bigger one … no. It's actually meant to be infinite gain, in theory.  
I don't understand why - what use is something that amplifies a tiny difference infinitely?  

  

There's a missing piece though - the feedback loop. If we connect V- to Vout, then Vout will amplify the difference between V+ and … Vout. So if Vout drops below V+, Vout is amplified to create a larger positive signal … bringing it back up. If Vout goes above V+, this creates a negative signal, bringing it back down. The stable equilibrium, which analog circuits like, is the state where V-=V+, and Vout produces a steady-state signal. This is called the virtual short: V+=V-. Not quite a law, but just the stable steady state. Note that because the gain is "infinite", even tiny differences are amplified.  
 
We can now exploit this. Consider this connection: 

Op-Amp Voltage Follower Circuit | Spiceman
 

Now we have $V_out = V_-$, and $V_+ = V_in$. The op-amp will drive $V_out$ to be the amplified difference of $V_+$ and $V_-$, as per $V_out = A(V_+ - V_-)$. We can rewrite this, with our new information: 
