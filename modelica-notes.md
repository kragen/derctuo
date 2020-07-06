I haven't found a good explanation of what Modelica is, so here's my
effort.  It's a *multi-domain* textual language for *numerical
simulation* of models of *continuous-time* systems *hierarchically*
composed of *lumped* elements whose behavior is specified through
*acausal equations*.  It is particularly suited for designed physical
systems such as machines or chemical plants.  (Sometimes the term
"cyber-physical systems" is used to emphasize the importance of
control systems.)  Also, it has aspects to facilitate graphical
display of the models as block-and-line diagrams, and comes with a
large library of standard components.  Its fundamental basis is
ordinary differential algebraic equations of finite dimensionality,
but it also supports hybrid simulation with discrete events.

Here's my effort to explain my understanding of what this means,
bearing in mind that I've never used Modelica, so some of this may be
laughably wrong.

Multi-domain
------------

The aspects of interest of a machine such as a bicycle commonly span
domains such as the mechanical, electrical, thermal, hydraulic, and
even digital, with interactions between them.  A bicycle may have
mechanical aspects such as the transmission of power from the pedals
to the wheels through the sprockets, electrical aspects such as the
generation of power for lights from wheel hub generators and its
storage in batteries, thermal aspects such as the generation of heat
in a braking disc, hydraulic aspects if the braking system is
hydraulic, and digital aspects if there are sensors like a speedometer
or actuators like a brushless hub motor.

Modelica can describe models that cross these different domains;
however, typically each component only exists in one or two domains.
For example, a copper pipe might have mass, three-dimensional
orientation, electrical resistance, thermal mass, and hydraulic
roughness and diameter, thus crossing several domains; but typically
is only modeled in one or two of these domains.  I think this is
because Modelica simulators typically refuse to simulate if there are
some variables whose values they cannot determine, so using such a
multi-domain component is a nuisance, since it obligates you to
describe all the domains at once.

Consequently there is, for example, a Resistor component in the
standard library, and also a cross-domain HeatingResistor component,
which has a temperature and a thermal port.  I think that if you use
the HeatingResistor component you end up having to connect the thermal
port.

This also brings up a potentially larger issue, which is the
closed-world assumption of Modelica models: more or less inevitably
they assume that you have included all the important aspects in your
model.

Numerical simulation
--------------------

Given a model written in Modelica, implementations such as
OpenModelica can run one or many simulations of the model over some
time period from specified initial conditions.  These simulations can
be quite precise; for example, the standard Berkeley SPICE3 set of
components, is included in the standard library, and its accuracy has
been validated to some extent against SPICE3 itself.

However, there are a number of other things you might want to do with
a model other than simulate it.  You might want to do "model
identification" to estimate the model's parameters from measurements
of a real system; you might want to validate some behavior of the
model for all possible scenarios rather than just one (for example,
showing that the model is unconditionally stable); you might want to
optimize the model to find out what parameter settings are in some
sense "best"; you might want to rigorously prove that two models are
equivalent, or show how they differ; and so on.  As far as I can tell,
Modelica implementations do not typically support these other possible
operations, or give them much lower priority.  Even operations on the
differential-equation system other than initial-value problems are
generally unsupported.

The nature of the simulation is fundamentally numerical; although
discrete-time systems are supported, it's not really the focus of
Modelica, and I'm not clear that you're going to be able to write a
compiler or something in it.  I don't think there's any way to create
new objects during the course of the simulation.

Continuous-time
----------------

Fundamentally Modelica reduces your model, or at least the
continuous-time part of it, to a set of differential algebraic
equations which it can then numerically integrate with methods like
Runge--Kutta.  So most of your model variables theoretically take on
an infinite number of values during the simulation.  This separates
your model from the solver, allowing you to apply different solvers to
the same model.

Hierarchical
------------

A Modelica model can be used as an element in another, larger model;
many models consist only of interconnected smaller models, containing
no explicit equations of their own.  So, for example, a hydroponic
system might contain an irrigation system as an element, which
contains pumps, pipes, and a feedback control subsystem; the control
subsystem might contain a power supply, sensors, a microcontroller,
and actuators; the power supply might contain diodes, inductors,
optoisolators, transformers, resistors, and a buck controller; the
buck controller might contain transistors, diodes, and resistors.
Modelica can in theory model at all of these levels, reducing them all
to a single system of differential algebraic equations for simulation.

I haven't quite seen any Modelica models with that level of detail,
but I've seen people describe a number that come close to it.

Lumped
------

Although Modelica models are continuous in time, they are not
continuous in space; the elements of the system are idealized to
points.  So Modelica cannot model a continuous heat distribution
throughout a tank of water, a waveform moving through an electrical
transmission line, or the stress distribution in a strut, although if
you discretize these things yourself you can get it to simulate the
discretized approximation.

In particular, I think there isn't even a way in Modelica to model a
delay of a continuous-time signal, such as you might get from an
improperly terminated cable.

(However, I've seen people simulating, for example, the water-hammer
effect in a pipe with the proprietary Modelica simulator SimulationX;
I assume they're using a discretized approximation of the pressure
waves.)

Acausal equations
-----------------

Modelica models are composed of (possibly differential) equations
rather than causal relationships in which effects result from causes;
the standard example of this is the equation V = IR for a resistor,
from which you can calculate the current if you know the voltage, the
voltage if you know the current; if you know neither *a priori*, you
may still be able to incorporate it into a system of equations that
eventually allow you to determine both.

