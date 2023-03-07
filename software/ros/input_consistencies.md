# Input Consistency

We need to keep wheel, throttle, and brake inputs consistent across sim and IRL, else the NN will not work correctly.

First, some definitions:

**steering ratio** - The function that converts steering wheel angle inputs to virtual ackerman wheel angles (the
average of the angles of both steering wheels). Inverse steering ratio just refers to virtual ackerman -> steering.

**cmd_vel** - I use this term to refer to any form of encoding where throttle is encoded as a speed, and turning is
encoded
as angular speed

**Our inputs percolate through the system as follows:**

- logi wheel outputs a cmd_vel value during initial training
    - Because the driver works with angles, and cmd_vel is just an encoded virtual ackerman angle, we can emulate a
      steering ratio by applying a function to the
      degrees of steering input to virtual ackermann output.
    - Throttle and brake work as percents here, but output as speeds, so we need to have a max speed and braking speed
      value to convert these percents to cmd_vel values
- data logger logs that wheel angle as an input as a percent
    - To convert to percents, we need to know our wheel lock angles. To covert throttle and brake, we need to know our
      max expected acceleration and deceleration values.
    - This is basically just converting the logi wheel values back
- The NN is trained on that data, and will essentially learn our steering ratio by seeing how different steering wheel
  inputs change trajectory
    - This means that the steering ratio as configured on the controller is essentially seen as the expected ratio from
      here on out
- During a run, inference will output steering wheel, throttle, and braking percents it expects to lead to a good
  trajectory, then converts these to a cmd_vel
    - The expected max throttle, braking, and wheel locks are needed here to convert these percents to cmd_vel fields,
      by simply multiplying. To convert to an ackermann message and then finally a twist message, we need to apply the
      steering ratio.
- These are consumed by phnx_io_ros as cmd_vel form, then converted to exact wheel angles, and throttle and brake are
  converted to percents
    - The expected max throttle and braking are needed to convert to percents, and the inverted steering ratio is needed
      to convert the virtual ackermann wheel output to the steering wheel angle input to send over can
- Finally, the steering motor receives the wheel angle over CAN, moving the motor, and ultimately the wheels through the
  steering rack
    - This is where the actual steering ratio is applied. If we sent commands expecting a different ratio, we will not
      get the virtual ackermann angle we expected


- Alternatively during training, inference will output the same as above. This time, it will affect sim, and its command
  values will
  be logged by data logger to train off of.
    - So long as sim has matching max wheel locks (applied to the virtual ackermann angle, so we find this by applying
      the steering ratio to the max angle)
      and the initial model passed to training (which uses the pipeline with the logitech wheel above) has a correct
      configuration, all should be well.
    - Also relies on throttle and brake having matching curves IRL to sim.

**In terms of ackermann and twist conversions:**

- **logi_g29** ackermann -> twist
- **inference** ackermann -> twist
- **phnx_io_ros** twist -> ackermann
- **gz_io_ros** twist -> ackermann

From this we can see that there are a few main values we need to keep consistent:

- **Max throttle** - Velocity we use to mark full throttle input. For IRL this value is just a way to encode 1.0, but in
  sim this value will be the actual speed the kart moves at. Thus, it should match the max speed of the kart.
- **Max brake** - Negative velocity we use to mark full brake input. Same as above.
- **Max wheel angle** - Max angle of the steering wheel (when we hit the locks). Assumed to me symmetric. Nice to have
  this
  in yaw for ROS, but doesn't really matter.
- **Steering ratio** - Well defined by this point

And then some non-values we need to keep consistent:

- **Throttle and brake curves** - Sim should have matching throttle and brake curves to IRL. This is linear atm, so we
  should be fine.
- **Twist and Ackermann conversions** - We really need to make sure these are all working properly

**Nodes in phoenix that rely on these values, and can be a cause of inconsistency:**

- **Logi_g29**
    - Max throttle
    - Max brake
    - Max wheel angle
    - Steering ratio
- **data_logger**
    - Max throttle
    - Max brake
    - Max wheel angle
- **inference**
    - Max throttle
    - Max brake
    - Max wheel angle
    - Steering ratio
- **phnx_io_ros**
    - Max throttle
    - Max brake
    - Inverse steering ratio

# Conclusion

Ultimately, most of this consistency can be achieved by simply setting the same correct constants across the codebase.
Most of this consistency relies on the logitech wheel using the right ratio, since this value gets encoded into the NN,
and thus effects all data collected during training from then on.

It would be wise to make a library that contains the steering ratios, as well as ackermann to twist and twist to
ackermann
functions used in between each of the steps in the pipeline.