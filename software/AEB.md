# Automatic Emergency Braking

## Overview
The Automatic Emergency Braking (AEB) system is designed to be a supplemental safety system
for the automatic driving. The goal of AEB is to lower the speed of the kart as much as possible
before a potential collision, to mitigate the damage to both objects. This is necessary as the E2E inference
is non-deterministic, and thus requires additional deterministic safety systems to ensure functional safety.    

## Inputs
- **Two Radars** - to reduce noise.
- **Encoder data** - used to determine kart velocity.

## Outputs 
- **EStop** - direct connection to Estop circuit to allow for emergency braking.
- **Motor Start/Stop messages** - Sent over CAN.

## Algorithm:
[![](https://mermaid.ink/img/pako:eNpdUkFugzAQ_MrK5_ABDq0INOqlvYSqB4iULV7AlbGRbVJFiL_XBtKk7AHB7IxnFu_IKs2Jxawx2LeQZ6UCX0khlHAniKIeDUpJMoqe4AeFKz79A2ptIE3ewSBHAx1Ziw2dFm1gQaDvxzxPYVCcDCiwVGnF7TMkFzKeDboGidZBN62eG7cLyf9mHtCVcNeNXyjfglVjqCo-eo6O7oILGoFfcqPwzJuoVEtn7wG4kp3xtDiS4nDmwgYxdNppc17PSGdKNr6ihTBmS5Kvs7rWkG21_w7Jv_8mn-7u2aPPS5Eb0TReSdbp_rShKT2zDmuaOQWQCpHOD9xQB7hd04I_DLWeEnpsxzoyHQru730MnJK5ljoqWexfOdU4SFeyUk2eioPTx6uqWOzMQDs2zP82E-g3pmNxjdJ6lLjwsd6WXZpXavoFMXO77g)](https://mermaid.live/edit#pako:eNpdUkFugzAQ_MrK5_ABDq0INOqlvYSqB4iULV7AlbGRbVJFiL_XBtKk7AHB7IxnFu_IKs2Jxawx2LeQZ6UCX0khlHAniKIeDUpJMoqe4AeFKz79A2ptIE3ewSBHAx1Ziw2dFm1gQaDvxzxPYVCcDCiwVGnF7TMkFzKeDboGidZBN62eG7cLyf9mHtCVcNeNXyjfglVjqCo-eo6O7oILGoFfcqPwzJuoVEtn7wG4kp3xtDiS4nDmwgYxdNppc17PSGdKNr6ihTBmS5Kvs7rWkG21_w7Jv_8mn-7u2aPPS5Eb0TReSdbp_rShKT2zDmuaOQWQCpHOD9xQB7hd04I_DLWeEnpsxzoyHQru730MnJK5ljoqWexfOdU4SFeyUk2eioPTx6uqWOzMQDs2zP82E-g3pmNxjdJ6lLjwsd6WXZpXavoFMXO77g)

