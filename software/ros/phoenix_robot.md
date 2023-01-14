# phoenix_robot

This package contains launch files for running phoenix IRL.

there are three main launch files:

- inference.py.launch: Runs the production version of phoenix, using the NN inference to drive the kart
- data_collect.py.launch: Runs phoenix in data collection mode, labeling images for offline training
- common.py.launch: Launch file that launches nodes common between the above two files

## Ros Config

![](images/phnx_robot.svg)

Currently, github isn't on mermaid 9.3.0, so we can't embed this mermaid directly.
Here's the [source.](https://mermaid.live/edit#pako:eNqFVE1r3DAU_CtCkNsuhh51KJQk0EtzCeRkEG-lZ6-6-jBP8m5DyH-vbLle4vXWvtiyZkbzRk_64Cpo5ILHBAmfDLQEbn_-VnuWH2UhxidsmArOBZ9fNpC4HE3CXWOsFYR6gdSQ4BZ3sD0ugB0FvQYEdap9wT48sJ9A-gKEzPiE1IDCWOYCnKQWQkzO9vvv49LShrZFyhPDSLBKgUOCyhldUXso3EcohO7o_0gTJIU4Kwn27IdIaMeeYwodG4MpvDX8KJQFBfuhUg_JjDE5B15PTudapt_57RMFa5EmRLqYmGQKMteOlDH-q_y6zyqj5Rlt0dBkzihddi7jxSR1_CrxnyWyEoVDSJVy-qr3O7wP8IQWQydH-iLtuwsurc0BjFH-K38j0tGSHBnyGtit6YgTquht0LadT6pFcXY-dJXx7Ybn1QZMBMZnrhzG017d4ArdxJNgj69vkQ1tYhy0Q7fPJl5eyqfxDRJ6hZk8nqLNojycr9uxcnKWinfOzd3CV_hBB8d33A3NZnS-YD4GjZqnIzqsucifHvscjq157T8zFPoUXt-94iJRjzved_p6JXHRgI35L2qTAv0ql9Z4d33-BZQQo5I)