---
title: "Communication between processor and other devices"
date: 2020-03-13 16:25:00 +0200
categories: [Blogging, Tutorial]
toc: false
---

# Introduction
This article is about understanding how processors interract with other devices.

All the following information is based on stm32f769ni processor, but it applies to all types of processors.

# A processor's world

## A bus
A bus is a collection of physical wires carrying (usually digital) signals between two or more devices. For most practical purposes, the signals of a bus need to be processed together to get meaningful information. Most of the parallel communication protocols are implemented using buses. Nowadays, the term "bus" is also used to describe physical signal arrangements which implements serial protocols, however our definition holds since, at physical level, the signals of the bus must be interpreted together.

Especially in case of parallel communication buses, the number of signals comprising the bus is an attribute of the bus and it is depicted in schematic and block diagrams.

## An address bus
An address bus is a bus whose signals form the binary representation of a memory address. The address bus is used as a fast method to communicate memory addresses between devices that may require them.

For a better understanding, let's look at the image below. We have a CPU with an address bus of width 8 and each of its signals carries one bit of the memory address. The final value can be converted into hexadecimal, since we are used to talk about addresses in this format (the CPU couldn't care less about our nifty hexadecimal format).
![Image loading...]({{ site.baseurl }}/assets/diagrams/address-bus.png)

## Address space
An address space is the set of all addresses which can be signaled (i.e. put) on an address bus. The address space is an abstract concept. The size of the address space (i.e. the number of possible addresses) is given by the size of the address bus (i.e. the number of signals in the bus, usually called the "bus width"). For `N` signals, the address space allows for $2^N$ possible addresses.

## Memory map
The memory map of a processor is a picture of the address space showing what regions of the address space are mapped to physical devices and what is their interpretation.

Behind every memory map there is some physical memory. Not all the address space has a physical memory behind. The areas of the address space not backed by physical memory, together with other "forbidden" areas are marked as "Reserved" in the memory map.

# How processor access devices
There are few methods to access a physical device through the address space:
1. by mapping the internal memory of the device to a range of addresses in the address space.
Each byte of the physical device is mapped to a portion in the address space.
![Image loading...]({{ site.baseurl }}/assets/diagrams/mapping.png)

2. by interacting with the device using a set of registers already mapped in the address space (but the registers are not in the device, they are in the processor or SoC)
This type of access is used instead of the first type when the device memory we want to access would occupy too much of the address space (or even not fit at all and it is not possible to be mapped). Another case when a device is accessed by the processor using registers is when the device does not allow direct access to its internal memory. Instead such devices offer protocols such as SPI or I2C for interraction.
![Image loading...]({{ site.baseurl }}/assets/diagrams/register.png)

3. by using aliases meaning that a device mapped in the address space is mirrored to another portion of the same address space. An example of aliasing is the mechanism implemented in stm32 processors which allows you to boot from different physical memories by setting two pins. The processor reads the pins and will alias some portion of the address space (where the desired boot memory is mapped) to address 0x00000000, such that the processor can find its reset vector.

