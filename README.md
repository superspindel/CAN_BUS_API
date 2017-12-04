# CAN_BUS_API
CAN bus API and Wheel Sensor implementation

Develop a CAN bus API for cortex-m0
Implement a wheel sensor for existing model car

## What is a CAN bus
Can bus is a form of communication primarily found in the automotive industry and/or industrial facilities. It allows for multiple devices to communicate on a shared medium, using an agreed upon protocol of communication.
There exist 2 types of CANs, 2.0A and 2.0B. The A can carry 11 bits while the B can carry 29 bits. 

## How CAN bus works
Communication is done via either a single wire or dual wire. The devices can either send a dominant 0, or a recessive 1. When a node (MCU) pulls the wire to 0 the line will transmit 0 even if other nodes pull the wire to a 1. Messages are then sent in different frames:
Data frame
Remote frame
Error frame
Overload frame
Where each frame starts with an identifier to the sending node. The idea is to have all of the nodes to be able to transmit at the same time, and if a node transmits 1 and sees a 0 on the line, it will go from sending to transmitting since a node with higher priority is trying to transmit at the same time.

### Example

Id 0001 trying to send at the same time as id 0010.

This would result in node with id 0001 sending and 0010 receiving. 

Since all nodes send on the same bus, and there is no id on the receiver all nodes can hear all transmissions, and as long as the transmission has been sent completely and correctly to one other node, an acknowledgement will be retransmitted to the node. Therefore the node can not know that the frame has reached its intended destination. 

The good thing about this is that collisions can occur, but instead of resending, the most important message will be transmitted without being canceled out.
Error noticing is handled via 5 different methods:
### Bit monitoring
Read the level on the bus after transmitting:
### Bit stuffing
After transmitting 5 of the same value bits, 1 bit of opposite value is stuffed in the transmission and is later removed by the receiver.
### Frame check
Check size of different frames and see that they fit the set standard.
### Acknowledgement check
All nodes on the bus as stated earlier that get a correct frame will transmit a dominant level on the acknowledgement slot in the message, if the message was received correctly.
### Cyclic redundancy check
Checksum that is transmitted that is checked by the receiver to notice errors in the transmitted frame.

## Why use a CAN bus
Today cars for example, we have a computer at every subsystem. We have a computer for the transmission system, engine etc. All this sensors needs to be connected in someway to be able to read important values from these systems. To be able to organize these messages in a sophisticated way we use the CAN. CAN is a message protocol which gives us the option to prioritize messages. With this ability we can then prioritize what we think are the most important information.
## MCU
* STM32F042K6
* ARM Cortex M0
* Embedded CAN controller

Controller area network (CAN) The CAN is compliant with specifications 2.0A and B (active) with a bitrate up to 1 Mbit/s. It can receive and transmit standard frames with 11-bit identifiers as well as extended frames with 29-bit identifiers. It has three transmit mailboxes, two receive FIFOs with 3 stages and 14 scalable filter banks.

## AlphaCar project
Previous work on this project that had as a focus to allow for a dual core processor in the middle of the car to communicate with 4 separate wheel nodes that measure the wheel speed and handle breaking of the wheels independently with regards to the information that is transmitted from the dual core processor via the can bus. 

## Code base
We’ve been given the code base written in c++ for the AlphaCar project. There is a CAN bus implementation there that we are to rewrite in rust. We’ve been in contact with Sebastian that was the last person involved in this project, hired in from Greppit as a consultant to make sure everything worked. 

## Current wheel sensor implementation
The wheel sensor works by having holes in the rim of the wheel that the MCU measure how many rotation has occurred in a set specific time. There is no need to see if it is reverse of forward speed since there is no reverse. This will update the MCU and the information should then be transferred over the CAN to the central processing unit in the dual core MCU board.

## Things to do 
* Get CAN communication working using a debugger on a host and an old board from the AlphaCar project as the wheel node.
* Get the board to work with the wheel sensor to calculate information about the speed of the car.
* Design an API for the CAN bus that is standalone from the processor in mind.
* Implement the API from 4 with documentation, tests and proof of correctness.
