# FRC-Drivetrain-Quiz
Congratulations on making it through our WPILIB lessons. You are soon to finish your programming training, but this quiz is a test of how you can apply what you've learned. This is the final assignment, yet not the end of your learning. After doing this quiz, you will understand how to code a basic drivetrain; a driveable robot.

Feel free to use any resources available to you, including our slides, google, etc.

**Note that the code provided are just examples of what we have done in the past;**

**Sadly, as of 2021, our access to the physical robot is unlikely. We have tried using 3rd party simulation software to no avail, so your code will unfortunately, not be runned on the robot.**

---

## Setup
Now that we know some git :), you can use clone! Run `git clone https://github.com/Arctos6135/FRC-Drivetrain-Quiz` in your terminal, in whatever root directory you will be working on.
* If you would like to commit to your own repository while you make your changes, fork the official repository and work on your copy, This will allow you to push to an online repository.

## Dependency Management

For this project you are going to be using the `CANSparkMax` class to control REV Robotics SPARK MAX motor controllers. However, this is a component from a **3rd-party vendor library**.
[This page](https://docs.wpilib.org/en/latest/docs/software/vscode-overview/3rd-party-libraries.html) gives you an overview of how they work and how to use them.
Specifically, [this part of the page](https://docs.wpilib.org/en/latest/docs/software/vscode-overview/3rd-party-libraries.html#managing-vs-code-libraries) tells you how to add vendor libraries in VS Code.
You will need the REV Robotics SPARK MAX library. No other libraries are required. (The CTRE Phoenix and Kauai Labs libraries are often used for our robot, but they are not required for this project.)
* If you are using the online installation option for your vendor libraries, use this: https://www.revrobotics.com/content/sw/max/sdk/REVRobotics.json

---

## Your Tasks

**If you would like to get extra reenforcement, WPILIB has documentation on [_Structuring a Command-Based Robot Project_](https://docs.wpilib.org/en/stable/docs/software/commandbased/structuring-command-based-project.html)**

**In addition, we have provided links to the documentation of most classes you may need to implement, at the bottom of this document.**

---

### (0) Write the content of `Constants.java`

#### Background Knowledge
This class contains constants which should be access by the other classes in the robot.

### Coding!
Your variables should be defined within the class declaration, with the access modifier `public static final <type>`.

**This class should not be instantiated**

---

### (1) Write the contents of `RobotContainer.java`

#### Background Knowledge

This class contains the majority of the mappings between components.

#### Coding!
* The definition of this class has purposefully been left out, as it should not be difficult to implement.

* Before the constructor:
    * Instantiate a object of class `XboxController`, using `XboxController(int port)`

* Inside the constructor:
    * Instantiate a new `Drivetrain` subsystem, passing the necessary arguments to the constructor.
    * Set your drivetrain instance's default command to a new `TeleopDrive` instance, using `SubsystemBase.setDefaultCommand(CommandBase command)`
    * Not required, but if you'd like to add buttons (`JoystickButton`) to your drivetrain, feel free to look at the documentation [here](https://first.wpi.edu/FRC/roborio/beta/docs/java/edu/wpi/first/wpilibj/buttons/JoystickButton.html).

---

### (2) Write the contents of `Drivetrain.java`

#### Background knowledge
**WPILIB has documentation for the [`SubsystemBase`](https://first.wpi.edu/FRC/roborio/release/docs/java/edu/wpi/first/wpilibj/command/Subsystem.html) class**

The subsystem is mainly used for interacting with a group of the robot's components, using methods and commands we create. All subsystems have a default command.

#### Coding!

Inside the `Drivetrain.java` file:
* Make a method e.x. `setMotors(double left, double right)`, that can set the power of the left and right motors, respectively.
    * Utilize the `CANSparkMax.set(double factor)` method, to directly control the output of the motors
        * Note that the motors internally clamp their output between 0 and 1 (0%, 100%) throttle.

* The constructor `Drivetrain(int leftMaster, int leftFollower, int rightMaster, int rightFollower)`, is provided, where the arguments are the ports each motor is connected to.
**Please use this constructor, as our future robots will likely keep this specific motor layout**
    * Instantiate the follower and leader motors of class `CANSparkMax`, using `CANSparkMax(int port, MotorType.kBrushless)`
    * Set the follower motors to follow the leader motors using the `CANSparkMax.follow(CANSparkMax)` method
    * Stop both motors using the `CANSparkMax.stopMotor()` method
    * Set the left motor to inverted direction, through `CANSparkMax.setInverted(True)`, so that they rotate in the same direction as the right motors by default.

---

### (3) Write the `TeleopDrive.java` file

#### Background knowledge
**WPILIB has documentation for the [`CommandBase`](https://first.wpi.edu/FRC/roborio/release/docs/java/edu/wpi/first/wpilibj2/command/CommandBase.html) class**

Essentially, the `CommandBase` class has extra methods for making it easier to interface with the `command` class. You are welcome to try using the `command` class as an interface, however it does not provide much functionality on its own.

All commands have access to some overrideable methods: 

* `initialize()`, which is called once, when the command is started
* `execute()`, which is called while the command is active
* `end(boolean interrupted)`, which is called once, when the command has ended
* `isFinished()`, which determines whether or not a command is finished. (should not need to modify)

**Our team currently uses the formulas: `left = y + x`, `right = y - x`, to control the motors direction and magnitude**


**Only read this if you plan on implementing your own custom implementation of tank steering**

>It is important to know that our drivetrain follows a tank driving model (Differential Drive), also commonly known as skid steering. 
>
>This consists of a two drivewheels on either side, with two more follower motors paired with each. The idea is to achieve a wide range of motion by changing the direction and magnitude of the leader motors on each side. 
>
>The whole reason we use the leader-follower system, is to imitate tank tracks, which are often a single powered wheel (drivewheel) with multiple constrained yet unpowered follower wheels (roadwheels), connected by a track.
>
>Recommendations to achieving various motion:
>* Turn on the spot (aka zero point turning / neutral steering):
>    * Ensure the motors rotate in the opposite direction, with the same magnitude
>* Turning left while driving (aka skid steering):
>    * Turn the left wheels slower than the right, or the right faster than the left
>* Turning right while driving (aka skid steering):
>    * Turn the right wheels slower than the left, or the left wheels faster than the right
>* Moving forwards or backwards:
>    * Make sure the motors are turning in the same direction, with the same magnitude
>* Stopping the robot (breaking):
>    * Set the motor's power to 0, and activate any active breaking configurations you have set for the motor controller

#### Coding!

Inside the `TeleopDrive.java` file:
* The constructor `TeleopDrive(Drivetrain drivetrain, GenericHID controller, int fwdRevAxis, int leftRightAxis)`, is provided, where the arguments are the drivetrain subsystem, the controller, and the ports of the vertical and horizontal axis of the joysticks. 
**This is an example constructor, and you are free to implement your own, with different arguments.**
    * Use the `this` keyword to save references to each of the previously mentioned variables; you will use them later. 
        * **Keep in mind variable shadowing, make sure to declare your variables before using `this`.**
    * Make sure the subsystem is registered with the command, by using the `addRequirements(drivetrain)` method.
    * Within the `execute()` method:
        * Retrive inputs from your controller using `GenericHID.getRawAxis(int axis)`. (your GenericHID should be your xbox controller instantiated in `RobotContainer.java`)
        * Add your own implementation of a tank driving program, or use the equation we provided. You are also free to use the code from our 2020 repository.
    * Within the `end()` method:
        * Set your motor's output power to 0, with the command you made, e.x. `setMotors(double left, double right)`.

---

# Documentation links:

Information from [WPILIB](https://docs.wpilib.org/en/stable/) and [Rev Robotics](https://www.revrobotics.com/content/sw/max/sw-docs/java/com/revrobotics/package-summary.html)

| Class Documentations: |
|--|
|[GenericHID](https://first.wpi.edu/FRC/roborio/beta/docs/java/edu/wpi/first/wpilibj/GenericHID.html)|
|[CANSparkMax](https://www.revrobotics.com/content/sw/max/sw-docs/java/com/revrobotics/CANSparkMax.html)|
[CommandBase](https://first.wpi.edu/FRC/roborio/release/docs/java/edu/wpi/first/wpilibj2/command/CommandBase.html)|
|[SubsystemBase](https://first.wpi.edu/FRC/roborio/release/docs/java/edu/wpi/first/wpilibj/command/Subsystem.html)|
