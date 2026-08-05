This needs a lot more detail and connective tissue. Multipliers come out of nowhere, are explained very quickly 

 

Back in the Dark Ages, before general purpose computers existed, if you wanted a program that would compute a particular equation for arbitrary inputs you had to make a one-off device. Today's digital computing allows for general mathematics and logic - a program can be written for any particular math you might want to do, and rerun on the same hardware. Such is the blessing of digital computing.  
Without this … Analog Computers were used.  
Let's say you want to know the trajectory of a projectile given some initial conditions, drag coefficients, etc. You want to know where it might land, given the angle it was fired at, the initial speed, and so on. This is a set of differential equations. You could work it out on paper … but then what if you have a series of inputs, and you need it for all of them? That's a lot of work. And let's say you need the answers in a minute. Well, that's not helpful.  
So, barring access to a digital computer and a program that can do this as it hasn’t been invented yet, you turn to Analog Computing, and build an electronic circuit designed to solve this exact mathematical problem (but only this mathematical problem), for any reasonable inputs and initial conditions.  

  

Such circuits were in use earlier in the 1900s, and once digital computing came about they were largely put aside; programs are more versatile, more general purpose, and better in almost every way for usability.  

So how does this work? My aim was to construct such a circuit for the Lorentz Attractor differential equations as an exercise in understanding this. I did go from a template, but there's enough faffery involved that I ended up learning a lot too.  

For reference, the Lorentz Attractor equations are  

 


 

$\frac{dx}{dt}=\sigma(y-x)$ 

$\frac{dy}{dt}=-xz+rx-y$ 

$\frac{dz}{dt}=xy-bz$ 

 

 

But it’s not particular important to know this now. The main point is I tried to make "a circuit that did some math".  

  

First, we need to go over the basic building blocks of "mathematical circuits", for want of a better phrase, and then we'll build up to getting the whole circuit together.  

  

Op-Amps 

Op-Amps, or operational amplifiers, are a key circuit element here. I misunderstood them originally and assumed they exist to, well, amplify a signal. They have two inputs, and one output. The output is the difference of the inputs, amplified by some factor. So if you want one signal amplified, connect the other to ground (0v), and then that should be amplified on the output, and you just choose a unit with the amplification factor you want. Right? 

  


No.  

Their usage is a lot more arcane than this.  

The actual functionality has been written about extensively elsewhere, and I'm going to repeat a lot of it writ more simply, but here's some links: 

So, firstly, let's go over how they work - feel free to skip this, but it should help with later - and then I'll go over how they are used in Analog Computers. We're going to get to my actual circuit design eventually.  

 

The following is a diagram of an op-amp, with output $V_{out}$, and the two inputs $V_+$ and $V_-$. The other voltage lines are to power the whole thing, and these also provide upper and lower limits to the output voltage.  


There's also power lines to said op-amp, but these aren't important, they just give us the upper and lower bounds of what Vout can be. The op-amp equation is  
$V_{out} = A(V_+ - V_-)$ 
Typically $A$ is something on the order of 100 000. This is where my initial confusion lay - I assumed it would be 2 or 3, and maybe you'd buy an extreme one that had a factor of 10 or you'd chain a few to get a bigger one … no. It's actually meant to be infinite gain, in theory.  
I don't understand why - what use is something that amplifies a tiny difference infinitely?  

  

There's a missing piece though - the feedback loop. If we connect $V_-$ to $V_{out}$, then $V_{out}$ will amplify the difference between $V_+$ and … $V_{out}$. So if $V_{out}$ drops below $V_+$, $V_{out}$ is amplified to create a larger positive signal … bringing it back up. If $V_{out}$ goes above $V_+$, this creates a negative signal, bringing it back down. The stable equilibrium, which analog circuits like, is the state where $V_-=V_+$, and $V_{out}$ produces a steady-state signal. This is called the virtual short: $V_+=V_-$. Not quite a law, but just the stable steady state. Note that because the gain is "infinite", even tiny differences are amplified.  
 
We can now exploit this. Consider this connection: 

Op-Amp Voltage Follower Circuit | Spiceman
 

Now we have $V_{out} = V_-$, and $V_+ = V_{in}$. The op-amp will drive $V_{out}$ to be the amplified difference of $V_+$ and $V_-$, as per $V_{out} = A(V_+ - V_-)$. We can rewrite this, with our new information: 

$V_{out} = A(V_{in} - V_{out})$ 

Expanding and rearranging,  

$(1+A)V_{out} = AV_{in}$ 

and then 

$V_{out} = (\frac{A}{1+A})V_{in}$ 

Given that $A$ is ~infinite, but in practice 100 000 or so, this is practically 1 and therefore we have a "voltage follower". This means that $V_{out}$ is driven to whatever $V_{in}$ is. Why does this matter? We could simply just continue a wire from $V_{in}$ … what does this buy us? 

Well, apart from demonstrating the feedback mechanism of op-amps, this circuit "isolates" $V_{out}$ whilst allowing it to copy $V_{in}$. That is, $V_{in}$ might be a low-current signal and $V_{out}$ might require a higher current than $V_{in}$ can deliver, but because the op-amp is externally powered, $V_{out}$ can still be driven and stay the same voltage. Anyway, this is a digression. We don't necessarily need this for mathematics.   

  

This basic feedback mechanism and mathematical trick, relying on the op-amp to drive $V_{out}$ however necessary to produce the requisite voltage, can then be exploited to create several more useful circuits: 

  

The scaler (or amplifier, but now that's become an overloaded term): 

Op-Amp Inverting Amplifier Circuit | Spiceman
 

Consider the inverting scaler first. $V_+ = 0v$, and by the virtual short steady state $V_- = 0v$. Therefore all current from $V_{in}$ must flow through the feedback resistor, thanks to Kirchoff's Current Law. $\frac{Vin}{R_1} = \frac{(0-V_{out})}{R_2}. Solving for $V_{out}$ gives $V_{out} = \frac{-R_2}{R_1}V_{in}$. By choosing $R_2$ and $R_1$, we can scale the input voltage by various factors, eg. $R_2 = 2*R_1$, to double it.  

  

Basic Amplifier Configurations: the Non-Inverting Amplifier ...
The non-inverting follows with only a little more working: 

$\frac{V_{in}-0}{R_1} = \frac{V_{out}-V_{in}}{R_2}$ 

$V_{out} = R_2(\frac{1}{R_2} + \frac{1}{R_1})V_{in}$ 

So $V_{out} = (1+\frac{R2}{R1})V_{in}$ 

Note that this can only scale greater than one, but you can always just chain two inverting scalers to achieve fractional scaling 

  

  

The Inverting Summer 

Op Amp Inverting Summing Amplifier - CircuitLab
 

This follows from Kirchoff's Current Law, the fact that currents add together - the current at $V_-$ is the sum of the currents from each of $V_1$ and $V_2$ - therefore $\frac{-V_{out}}{R_3} = \frac{V_1}{R_1} + \frac{V_2}{R_2}, so we get $V_{out} = \frac{-1}{R_3} (V_1R_1+V_2R_2) 

By setting $R_2 = R_1 = R_3$, we can cancel out the resistance scale factors, and $V_{out} = -(V_1+V2)$ 

This can be made to do general weighted linear equations - a dot product with static weights, if you will. Since Kirchoff's Current Law tells us that all the currents at the left node are summed, we can have as many signals as we want, with resistances, leading into this. The resistances can be different for each input voltage. These resistances form the static weights, the voltages are summed, and then the result is scaled by the feedback resistor to get  

$V_{out} = \frac{-1}{R_{feedback}} (R_1V_1 + R_2V_2 … )$ 

  

The Subtractor 

Arithmetic Circuits
 

This is slightly more complicated <explain> 

TODO 

  

There's a few more blocks, but the point hopefully is clear by now - we can use these circuit elements - op-amps and resistors - to do some simple mathematics on voltages. We could create, for example, a circuit that produces an output voltage y where $y =  2x + 3$. You then just need to have a dial that lets you set $x$, and then you measure $y$.  

Simple, easy to do on paper, but if you needed to produces results quickly and could set and read voltages quickly, and also digital computing had not been invented, then such a circuit could be useful.  

Still, probably too simple and contrived to be worthwhile.  

  

Earlier I mentioned I was attempting a differential equation. These contain derivatives integrals. How do we perform that with op-amps?  

Integrals are just adding a lot of small bits up, and we have adders, but a major drawback of analog computing is that there is no storage. For this, we need a time-based element. Enter the capacitor. 

Capacitors work in a much more nuanced and complex way than I'm about to describe, but the following should be all that's necessary to know for the topic at hand. A capacitor has two plates, and if there is a voltage difference across them then one side charges up to equalise the voltage, neutralising the difference. This can then quickly discharge if the voltage across suddenly changes due to external factors.  

  

So if we connect a capacitor across the op-amp feedback loop: 

Integrator Limitations: The Op-Amp's Gain Bandwidth Product - Technical  Articles
 

Then as $V_{in}$ changes, $V_{out}$ is driven to be the same as $V_{in}$ (so that $V_+$ is 0). But capacitors introduce some time delay - they must charge up to neutralise a voltage difference, it's not immediate. I'll not go into the derivation here, but we know that the voltage across the plates of a capacitor is equal to the charge on the capacitor, divided by the specific parameters called capacitance, which is really just a factor derived from physical construction (materials, dimensions, etc). $V_{in}$ changes, and the charge therefore changes. If $V_cap$ is the voltage across the capacitor, then 

$V_{cap} = \frac{Q}{C}$ 

$V_{cap} = V_--_{Vout}$, but $V_-$ is 0 due to the feedback loop, so $V_{cap} = -V_{out}$ 

The rate of change of $V_{out}$ over time is proportional to the rate of change of charge over time. But rate of change of charge over time is current, and the current flowing in is $I_{in} = \frac{V_{in}}{R_{in}}$ 

So $I = \frac{dQ}{dt} = C\frac{dV_{out}}{dt}$ 

Solving for $V_{out}$, we get  

$V_{out} = \frac{1}{C} \int I_{in} \,dt 

 and $I_{in} = V_{in}/R_{in}$, so 

 $V_{out} = \frac{1}{RC} \int V_{in} \,dt 

TODO factor of R 
… and thus we can compute the integral of $V_in$ over time.  
