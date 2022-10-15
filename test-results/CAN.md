# CAN Test Results

## Bandwidth
All bandwidth calculations where done by placing two nodes on the bus with one spamming messages, and the other sending 
a message of a higher priority if it ever dropped a message. When this message is sent, the other node will send slightly slower,
until the system reaches a stable speed. 

The system measures the average messages per second while in a stable system, which is listed below.

- **1mb baud**: Avg. 980 mps
