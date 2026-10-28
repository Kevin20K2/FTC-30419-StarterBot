# FTC 30419 StarterBot

Robot code for FTC Team 30419, based on the FTC Robot Controller SDK 12.0.0 for the BIOBUZZ (2026–2027) season.

## Get started

1. Install Android Studio Narwhal 3 Feature Drop or later, as required by the included SDK documentation.
2. Ask the repository owner for access to this private repository, and sign in to GitHub.
3. Clone the full project:

   ```powershell
   git clone https://github.com/Kevin20K2/FTC-30419-StarterBot.git
   ```

4. Open the cloned project folder in Android Studio, rather than opening only `TeamCode`.
5. Let Gradle sync finish and install any Android SDK components it requests. The project specifies Android API 30 for compilation. Use Android Studio's bundled Java runtime for Gradle.
6. Select the `TeamCode` run configuration, connect the Robot Controller device, and run the app to install the team code.
7. Connect the Driver Station, activate the robot hardware configuration described below, and select the desired OpMode.

The build was verified locally by the team on October 27, 2026. This is a reported local verification, not an automated build check. Building from another computer still requires its own Android SDK setup.

## Project layout

| Location | Purpose |
| --- | --- |
| `TeamCode/src/main/java/org/firstinspires/ftc/teamcode/` | Team Java code; make normal robot behavior changes here |
| `FtcRobotController/` | FTC Robot Controller application and SDK samples |
| `gradle/`, `gradlew`, `gradlew.bat` | Shared Gradle wrapper used to build the project |
| `build.dependencies.gradle` | FTC SDK dependencies, currently 12.0.0 |
| `libs/ftc.debug.keystore` | Shared FTC debug signing key required by the build |
| `doc/FTC-SDK-README.md` | Original FTC SDK documentation and release notes |

## Available OpModes

Both OpModes appear in the `StarterBot` group.

| Driver Station name | Source file | Purpose |
| --- | --- | --- |
| `BioBuzz StarterBot Teleop` | `BioBuzzStarterBotTeleop.java` | Driver control of the drivetrain, intake, launcher, and feeder |
| `StarterBotAuto` | `StarterBotAuto.java` | Launch for approximately 10 seconds, then command a 120 mm reverse drive |

## Robot hardware configuration

The active Robot Controller configuration must use these exact names. Both OpModes look up all seven devices during initialization.

| Configuration name | SDK device type | Role |
| --- | --- | --- |
| `left_drive` | `DcMotor` | Left drive motor |
| `right_drive` | `DcMotor` | Right drive motor |
| `intake` | `DcMotor` | Intake roller motor |
| `launcher` | `DcMotorEx` | Launcher motor with encoder feedback |
| `windmill` | `CRServo` | Launcher feeder |
| `left_intake_servo` | `CRServo` | Left intake servo |
| `right_intake_servo` | `CRServo` | Right intake servo |

Hub names, port assignments, and configured motor models are not recorded in the Java files. Match these to the actual wiring and motor specifications; add the team's verified port map here when available. The servo entries above require continuous-rotation servos.

The code reverses the right drive motor, right intake servo, and windmill servo. Confirm that these directions match the assembled robot. Connect the launcher encoder to its corresponding motor encoder port.

## Driver controls

Controls use gamepad 1.

| Control | Current behavior |
| --- | --- |
| Left stick forward/back | Drive forward/back |
| Right stick left/right | Turn |
| Right trigger | Run intake forward |
| Left trigger | Reverse intake |
| Right bumper, held | Run launcher at a target of 1250 encoder ticks/second |
| X | Unassigned; reserved for a future feature |

Intake power starts at right trigger minus left trigger. While the right bumper is held and measured launcher velocity exceeds 1200 ticks/second, the code adds 0.5 to intake power. Intake power is also applied to both intake servos.

The windmill feeder runs automatically while the right bumper is held and measured launcher velocity exceeds 1200 ticks/second. Releasing the right bumper or falling to 1200 ticks/second or below stops the feeder. X has no assigned action.

## Autonomous behavior and tuning

`StarterBotAuto` starts in its launch state. It commands a launcher target of 1250 ticks/second and runs the windmill when measured velocity exceeds 1200 ticks/second. After approximately 10 seconds, it stops the launcher and feeder, sets intake power to zero, and commands a reverse drive of 120 mm at 0.5 power. It declares completion after the left encoder remains within 10 mm of the target for more than 2 seconds.

The drive calculation assumes 96 mm wheels and 537.7 encoder ticks per wheel revolution. The rotation helper assumes a 402 mm track width. Confirm these values against the actual robot before tuning autonomous movement.

Current implementation details to account for when testing:

- TeleOp and autonomous both use a launcher target of 1250 ticks/second and a feed threshold of 1200 ticks/second, matching the student version.
- Autonomous adds 0.5 to its stored intake power on every loop while the launcher exceeds the threshold; that value accumulates rather than resetting each loop.
- Drive completion checks only the left encoder and has no movement timeout. It does not confirm that the right side reached its target.
- The complete state does not explicitly zero drive power; the motors remain in `RUN_TO_POSITION` until the OpMode ends.

## Team workflow

Pull the latest changes before starting work. Make changes on a branch, commit them, and open a pull request for team review before merging into `main`.

Keep the full project together so teammates can clone and build it. Git excludes generated build files, local Android SDK paths, Android Studio settings, personal signing keys, and local `.env` files. The supplied FTC debug keystore stays included because the build uses it.

## Credits and licenses

This project includes the FIRST Tech Challenge Robot Controller SDK and StarterBot code credited in its source headers to Base 10 Assets, LLC. Preserve the source copyright and license notices, the root `LICENSE`, and files under `doc/legal/`. See the [original FTC SDK README](doc/FTC-SDK-README.md) for SDK documentation, links, and release history.
