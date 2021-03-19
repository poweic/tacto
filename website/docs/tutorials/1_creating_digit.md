---
id: digit
title: Building Your First Tactile Simulator
---

In this tutorial, we will be building a tactile simulation that can simulate the touch of a ball on a DIGIT sensor (see below).

We will walk through how to create a world in PyBullet physics engine, use Tacto to create a tactile simulator, and finally use [pybulletX](https://github.com/facebookresearch/pybulletX), a lightweight PyBullet wrapper, to put everything together as an interactive demo.

![demo_digit](/img/demo_digit.gif)

## Creating a World in Physics Engine

Tacto is designed to work with PyBullet and use it for physics simulation.
Unless there is something to touch or interact with, having a tactile simulator itself is not enough.

Let's first create a world in PyBullet.
In this tutorial, instead of using PyBullet's API directly, we will use helper functions from [pybulletX](https://github.com/facebookresearch/pybulletX) to simplify the process.
`px.init()` creates a physics client, loads an infinite plane (`plane.urdf`), sets the gravity to `9.8` downward.
```python
import pybulletX as px
px.init()
```

Once the world is set up, let's load two rigid bodies in it:
1. the 3D model of an object to touch, and
2. the 3D model of the tactile sensor itself.

Here, we are going to use a checker ball and a [DIGIT](https://digit.ml/) as the tactile sensor.
`px.Body()` returns an object that consists of the unique rigid body ID returned by `p.loadURDF()`, as well as other meta data such as the path to the URDF file, the initial base position and orientation, and physics client ID, etc.
This encapsulation gives us better control over the object.

```python
import pybulletX as px

px.init()

obj = px.Body(
    urdf_path="examples/objects/sphere_small.urdf",
    base_position=[-0.015, 0, 0.04],
    global_scaling=0.15
)

digit = px.Body(
    urdf_path="meshes/digit.urdf",
    base_orientation=[0.0, -0.707106, 0.0, 0.707106],
    use_fixed_base=True
)
```

![digit and a ball](/img/digit_and_ball.png)

## Creating a tactile simulator

Tacto is a simulator for vision-based tactile sensors. It can render two images: RGB and depth. Both of them have the same dimension. To create a simulator, one need to specify the width and the height of the image:
```python
import tacto
sensor = tacto.Sensor(width=120, height=160)
```
DIGIT is the sensor we get by default. We can also pass a configuration file as an additional argument to `tacto.Sensor(..., config_path=...)` to get different type of sensor (ex: omnitact).

Now comes the most important part, we need to **register** the ball to the sensor and **bind** the sensor to the rigid body of the DIGIT (`digit`) in PyBullet so that we know where these rigid bodies locate in the world and how they interact with each other (a physically accurate dynamics of the contacts).
```python
# Bind the simulator (sensor) to the rigid body of DIGIT sensor by providing
# its unique ID (digit.id) in PyBullet.
sensor.add_camera(digit.id, [-1])

# Register the ball to the sensor.
# To add more objects, simply call add_body multiple times.
sensor.add_body(obj)
```

## Putting it All Together

Finally, to demonstrate the capability of the tactile simulator, we use some help from pybulletX to launch a thread that steps the physics simulation by calling `p.stepSimulation` indefinitely. 

Here is the final code:
```python
import tacto
import pybulletX as px

def main():
    px.init()

    sensor = tacto.Sensor(width=120, height=160)

    obj = px.Body(
        urdf_path="examples/objects/sphere_small.urdf",
        base_position=[-0.015, 0, 0.04],
        global_scaling=0.15
    )

    digit = px.Body(
        urdf_path="meshes/digit.urdf",
        base_orientation=[0.0, -0.707106, 0.0, 0.707106],
        use_fixed_base=True
    )

    sensor.add_camera(digit.id, [-1])
    sensor.add_body(obj)

    t = px.utils.SimulationThread(real_time_factor=1.0)
    t.start()

    while True:
        color, depth = sensor.render()
        sensor.updateGUI(color, depth)

    t.stop()

if __name__ == "__main__":
    main()
```
