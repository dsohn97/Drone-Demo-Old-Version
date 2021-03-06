# Droneflight_Demo

Welcome to the Droneflight Demo FUSEE App, which illustrates a method to calculate movement of objects and the camera in Space using `Quaternions`, This method works for 1st and 3rd person views.

`Droneflight.cs` contains the source code for a working FUSEE application showing 
a 3D drone model with 3 different camera types.  
The drone model was created using Blender and imported as `.fus` file. 

## Controls

The Controls are mapped to

* Camera
	* `Q` change `_cameraType`
		* `Free Camera` 1st person free flight
			* `WASD` move in space
			* `LMB + mouse move` rotate view
		
		* `Drone Camera` 3rd person follows drones FOV
			* Drone movement
				* `WS` fowards, backwards
				* `AD` to the sides
				* `RF` up, down
				* `LMB + Mouse move` rotates drone

		* `Follow Camera` 3rd person rotates around drone
			* Camera movement
				* `RMB + Mouse move` rotates camera around drone


## Problem

If you Just use normal rotations for moving around in 3D space you get the Problem that your rotatons   will depend on each other so if you want to rotate an object like this 

```cs
	_drone.Rotation.Y(Yaw)
	_drone.Rotation.X(Pitch)
```
you will get a Rotation around the y-axis and then most likely your x-axis rotaton will rotate your drone in an unwanted direction if your Yaw angle is close to 90° it will even result in a roll instead of a pitch.

`Local Yaw + Local Pitch = Global Roll`

## Solution

So to avoid this we will use Quaternions for our calculations  

Quaternions are 4 dimensional numbers with 1 real part `w` and three imaginary parts `i, j and k`

Fusee has an already implemented quaternion class with which you can create a quaternion from an angle input

```cs
	public Quaternion Orientation(float Yaw, float Pitch) { 
		Orientation = 	Quaternion.FromAxisAngle(float3.UnitY, Yaw) *
	               		Quaternion.FromAxisAngle(float3.UnitX, Pitch);
	    return Orientation;
	}
```

after creating this quaternion we can use it to transfrom our direction vector

```cs
	var forward = float3.Transform(float3.UnitZ, orientation(Yaw, Pitch));
```

With the direction correctly calculated we can now create for example our LookAt Matrix with it

```cs
	RC.View = float4x4.LookAt(position, position + forward, float3.UnitY);
```

or change the position of an object

```cs
	_drone.Translation.Z += float3.Transform(float3.UnitZ * a, orientation(Yaw, 0));
```
	

