---
id: intro
title: Introduction
sidebar_label: Introduction
slug: /
---
TACTO is a fast, flexible and open-source simulator for vision-based tactile sensors, such as [DIGIT](https://digit.ml). It provides models for the integration with PyBullet, as well as a renderer of touch readings.

:::important
The simulator is not meant to provide a physically accurate dynamics of the contacts (e.g., deformation, friction), but rather relies on existing physics engines, such as PyBullet.
:::

**For updates and discussions please join the #TACTO channel at the www.touch-sensing.org community.**

## Content
This package contain several components:

- A renderer to simulate readings from vision-based tactile sensors.
- An API to simulate vision-based tactile sensors in PyBullet.
- Mesh models and configuration files for the DIGIT and Omnitact sensors.

![demo_digit](/img/demo_digit.gif)
