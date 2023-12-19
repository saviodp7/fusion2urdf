# fusion2urdf

## Installation

Run the following command in your shell.

#### Windows (In PowerShell)

```powershell
cd <path to fusion2urdf>
Copy-Item ".\URDF_Exporter\" -Destination "${env:APPDATA}\Autodesk\Autodesk Fusion 360\API\Scripts\" -Recurse
```

## What is this script?
This is a fusion 360 script to export urdf from fusion 360 directly.

This exports:
* .urdf file of your model
* .launch and .yaml files to simulate your robot on gazebo
* .stl files of your model

## Before using this script

Before using this script, make sure that your model has all the "links" as component, unground your base and define it as "base_link". 

In addition to that, you should be careful when define your joints. The **parent links** should be set as **Component2** when you define the joint, not as Component1.

Also, make sure components of your model has only bodies. **Nested components are not supported**.

## In some cases, before export Turn off "Capture design history"

##### Important!
This script currently supports only "Rigid", "Slider" and "Revolute".

## Complex Kinematic Loops and Spherical joints (may be fixed later):

DO NOT use Fusion 360's inbuilt joint editor dialouge for positioning joints, even in **kinematic loops**.

As you can see below, when fusion initailly forms joints, it might not align where you want it to align to. In the image below, the cylinder's cap side doesn't exactly coincide with the position of the pin where it needs to be join. The red arrow shows the mismatch in initial joint positioning by fusion. If you were to manually drag the parts and align them as shown below, it would cause cascading problems with the visual and collision properties of certain links. 

![Capture](https://user-images.githubusercontent.com/37873142/133146628-c4c2b8dd-ac7b-41e8-bd62-1d2c2b80adce.PNG)

Below you can see one of the cylinders is mismatched as compared to the others (red and grey colors are cylinders) 
(The below urdf is visualized in pybullet)

![image](https://user-images.githubusercontent.com/37873142/133141659-440a0a4a-1afa-4751-99ba-fc3db02f7450.png)

See Also: Similar to this issue, but only for a few axes [here](https://github.com/yanshil/Fusion2PyBullet/issues/6) (turns out there was a fusion API change back then, and the exporter wasn't yet updated [See this commit](https://github.com/syuntoku14/fusion2urdf/commit/8786e6318cdcaaf32070148451a27ab6e4f6697d), but it now is)


**The fix for this is to leave Fusion's joint controls unedited and form joints for the robot joints (See below)**


A similar issue with another set of joints at the ankle was fixed by following the above fromat. [Here is the video](https://www.youtube.com/watch?v=0hfkm7vv5o8&ab_channel=JRohit)

For spherical joints, it is better to keep them revolute and define the joints as spherical, later in the generated URDF(provided the urdf parser in your visualizer/physics engine(gazebo,webots,pybullet,mujoco,etc) supports spherical joints, in pybullet it does).
The ankle joint below has 4 spherical joints and only two of them were defined as revolute while exporting from fusion 360. The other 2 spherical joints were created in pybullet using pybullet's inbuilt functions for creating kinematic loops.(see the gif below)

![youtube-video-gif](https://user-images.githubusercontent.com/37873142/133144404-45d9e444-8ddb-4b5f-8970-6e637b750faa.gif)

## How to use

### Install in Shell 

Run the [installation command](#installation) in your shell.

### Run in Fusion 360

Click ADD-INS in Fusion 360, then choose ****fusion2urdf****. 

**This script will change your model. So before running it, copy your model to backup.**

Run the script and wait, then a folder dialog will show up. Maybe some error will occur when you run the script. Fix them according to the instruction. You will see many "old_components" in the components field, please ignore them. 

You have successfully exported the urdf file. Also, you got `.stl` files in your package. This will be required at the next step. The existing fusion CAD file is no more needed. You can delete it. 

The package created will be required in the next step. Move them into your ros environment.

## In your ROS environment

Place the generated _description package directory in your own ROS workspace.
Then, run catkin build in catkin_ws.

```bash
cd ~/catkin_ws/
catkin build
source devel/setup.bash
```

Now you can see your robot in rviz. You can see it by the following command.

```bash
roslaunch <robotname>_description display.launch
```

If you want to simulate your robot on gazebo, just run
```bash
roslaunch <robotname>_description gazebo.launch
```
