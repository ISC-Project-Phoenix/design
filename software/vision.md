# Safe At Any Speed: The Designed-In Functional Safety of Project Phoenix

## Overview
The goal for Project Phoenix is to create an autonomous go-kart to compete in
the EVGrandPrix Autonomous competition or some other equivalent competition. To achieve we had planned to have the kart be able to still
be able be driven manually so that training data for the end to end machine learning model could be collected with a real human
driver. However, the initial design would've kept autonomous operation and manual operation as two distinct states. 
So while a human could sit in the kart and drive it, during autonomous operation no human driver could be in the kart.
This approach was selected during the initial planning phases of Phoenix because it was and still is unnecessary to account for a human
driver as its illegal to have a human in the kart during autonomous operation at competition. Further, at the time 
it was unknown how hard or easy it was going to be to create the different drive by wire systems needed for autonomy.

However, after careful consideration and after seeing the incredibly quick progress on drive by wire we are planning on
moving away from the initial approach and moving towards a system that would work more like adaptive cruise control on a 
production vehicle. With the new plan we are going to have the ability to not only have a human driver control the kart while
all the autonomy hardware is still in the car in a purely manual state, but we also will have the ability to have the kart drive autonomously
while having a human driver sitting in the kart. Accomplishing this changes the scope of several of our drive by wire
modules but will allow for incredibly easy testing and usage as switching the kart from manual to autonomous would be 
simply the push of a button.


## Phoenix Adaptive Cruise Control
As mentioned earlier, this system will be an extension of the existing CAN network and drive by wire nodes. In order to
achieve a system that will allow human intervention during autonomous operation we need to implement the ability to
control Phoenix's throttle, brake, and steering system via traditional pedals as well as via CAN. The following is meant
to act as a high level discussion as to how we plan to accomplish this. For more low level implementation please see
the rest of the software documentation. All drive by wire systems will need to be able to accomplish the following:

- Be able to accept both commands from a human interface device (i.e: pedals, steering wheel) or via CAN
- Upon detection of input on one of the human interface devices, cease acting on CAN messages and only act on
inputs from human driver
- Send a CAN message to alert other nodes that autonomy needs to stop

The first point ensures that at all times the drive by wire systems will always accept human input and prioritize that
over CAN messages. The second point will ensure that if a human does take over the kart no CAN messages could conflict
with what the user wants to do. Finally the last point is to handle the case where a driver interacts with only one of
the controls. In this case the rest of the systems will still be controlled by the autonomy stack as they haven't been
interacted yet. To prevent this each drive by wire system will send a message to the main interface between the PC and
the rest of the CAN network to tell it to stop sending CAN control messages from the autonomy stack. This will leave
all the drive by wire systems in a state where the kart is free rolling with the user able to take control of the rest
of the kart at their leisure.

