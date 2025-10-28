## Step 1: Installing Android Studio

Go to the [Android Studio download archives](https://developer.android.com/studio/archive), and after agreeing to the terms and conditions, scroll down to "**Android Studio Meerkat | 2024.3.1 March 4, 2025**", and download the installer corresponding to your system (Windows, MacOS, Linux, etc).  
After the installer is downloaded, open it and complete the Android Studio installation process. If you have Android Studio Meerkat installed on your computer, you're ready for the next step.

## Step 2: FTC Robot Controller

Using GitHub Desktop or the Git CLI, clone the [FTC Robot Controller](https://github.com/FIRST-Tech-Challenge/FtcRobotController) repo from github to your computer, or download the project as Zip.  
Now open Android Studio and click on **"File -> Open" or open a project from your computer**, and select the FTCRobotController from where you cloned it or downloaded it.  
Now click on **"File -> Sync Project with Gradle Files"** and wait for the gradle sync to finish. When that is done, click on **"Build -> Compile All Sources"**. When the build is finished, you should be ready for the next step.

## Step 3: build.common.gradle

In Android Studio in the project files, go to `Gradle Scripts/build.common.gradle` and in `android { defaultConfig { } }` add `multiDexEnabled true` under `targetSdkVersion 28`.  
It should look something like this (I also included the entire file in this repo so you can check it fully. The `multiDexEnabled true` was added at line 56):  
```gradle
android {
    // ...
    defaultConfig {
        signingConfig signingConfigs.debug
        applicationId 'com.qualcomm.ftcrobotcontroller'
        minSdkVersion 24
        //noinspection ExpiredTargetSdkVersion
        targetSdkVersion 28
        multiDexEnabled true // Add this line
        // ...
    }
}
```

## Step 4: build.dependencies.gradle

In Android Studio in the project files, go to `Gradle Scripts/build.dependencies.gradle` and in the `repositories { }` add:  
```gradle
maven {
    url = 'https://maven.brott.dev/' // This is for Dashboard
}
maven {
    url = 'https://repo.dairy.foundation/releases' // This is for CachingHardware
}
maven {
    url = "https://jitpack.io" // This is for FTCLib
}
```  
Then, in the `dependencies { }` add:  
```gradle
implementation 'com.acmerobotics.dashboard:dashboard:0.4.17' // Dashboard
implementation 'dev.frozenmilk.dairy:CachingHardware:1.0.0' // CachingHardware
implementation 'org.ftclib.ftclib:core:2.1.1' // FTCLib
```  
The full file is also included in this repo, you can check there as well. The added lines in the file are: 4 to 12 and 25 to 27.

## Step 5: Build everything again

Redo the build process done at step 2. So go to **"File -> Sync Project with Gradle Files"** and wait for the gradle sync to finish. When that is done, click on **"Build -> Compile All Sources"**.

## Finally

You're done with the setup!  
  
You should be able to use the Dashboard, CachingHardware and FTCLib from now on!  
Here are some simple examples of code that should build without errors on your new setup:  
Dashboard:  
```java
// ...
import com.acmerobotics.dashboard.config.Config;
// ...

@Config
@TeleOp(name = "Test")
public class LinearOpMode extends com.qualcomm.robotcore.eventloop.opmode.LinearOpMode {
    public Servo servo;

    public static double pos;

    @Override
    public void runOpMode() throws InterruptedException {
        servo = hardwareMap.get(Servo.class, "servo");

        pos = 0.0;
        servo.setPosition(pos);

        waitForStart();

        while (opModeIsActive()) {
            servo.setPosition(pos);
        }
    }
}
```  
CachingHardware:  
```java
// ...
import dev.frozenmilk.dairy.cachinghardware.CachingDcMotorEx;
// ...

public class Arm {
    private CachingDcMotorEx motor;

    public Arm() {
        motor = new CachingDcMotorEx(hardwareMap.get(DcMotorEx.class, name), 0.005);
        motor.setMode(CachingDcMotorEx.RunMode.STOP_AND_RESET_ENCODER);
        motor.setMode(CachingDcMotorEx.RunMode.RUN_WITHOUT_ENCODER);
    }
}
```  
FTCLib (PIDController for example):  
```java
// ...
import com.arcrobotics.ftclib.controller.PIDController;
// ...

public class Control {
    private PIDController pid;

    public Control() {
        pid = new PIDController(kp, ki, kd);
    }

    public void setPID(double kp, double ki, double kd) {
        pid.setPID(kp, ki, kd);
    }
}
```
  
## If you're interested

For FSM programming in autonomous, I use a State Machine manager that I coded myself, which you can find more about in [the original repository](https://github.com/noobOriginals/Statement)!  
To use it in your project, follow these steps:  
In Android Studio in the project files, go to `Team Code/java/org.firstinspires.ftc.teamcode/` and create a package named `util`. In that package, create a new file `StateMachine.java` and copy paste the code from the [Statement Repository](https://github.com/noobOriginals/Statement) found in `Statement/src/app/core/StateMachine.java` into your newly created file. Lastly, in your newly created file, change the package from `app.core` to `org.firstinspires.ftc.teamcode.util`. So just replace the line:  
```java
package app.core;
```  
with:  
```java
package org.firstinspires.ftc.teamcode.util;
```  
In the future I plan on uploading that project to gradle repositories, so the setup process should change.  
### But, for now, this is it! Thank you!