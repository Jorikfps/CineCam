# CineCam

A small cinematic camera plugin for Paper servers. Save a few camera points, then let the camera fly along them in a smooth curve while you record. Made for trailers, showcases and build tours.

- Smooth curved path through any number of points (Catmull-Rom spline)
- The view direction is interpolated too, and always turns the short way round
- Soft start and stop (ease in/out), or loop the route endlessly
- Countdown before the camera starts, so you have time to hit record
- Save and load routes by name
- You are put back where you were, in your own game mode, when the route ends
- One Java file, no dependencies, easy to read and change

## Requirements

- Paper (or a fork such as Purpur) **1.21 or newer**
- Java 21

The default camera method uses a display entity with teleport interpolation, which needs Minecraft 1.20.2 or newer on the client and server.

## Installation

1. Download `CineCam-1.0.0.jar` from the releases page, or build it yourself (see below).
2. Put the jar in your server's `plugins` folder.
3. Restart the server.

## Usage

Everything is done with `/cinecam` (aliases: `/cam`, `/cinema`). You need the permission `cinecam.use`, which operators have by default.

1. Fly to your first shot, look where the camera should look, and run `/cinecam point`.
2. Repeat for every point of the route. Two points give a straight move, three or more give a curve.
3. Run `/cinecam play 30` to fly the route in 30 seconds. You are moved to the first point, a countdown runs, and then the camera starts.
4. Sneak or run `/cinecam stop` to stop early.

| Command | What it does |
|---|---|
| `/cinecam point` | Save where you stand and where you look |
| `/cinecam play [seconds]` | Fly along all points (default 20 seconds) |
| `/cinecam play <seconds> loop` | Keep flying the route until you stop it |
| `/cinecam play <seconds> stay` | Stay at the last point instead of going back |
| `/cinecam stop` | Stop the camera |
| `/cinecam list` | Show all points |
| `/cinecam goto <number>` | Teleport to a point |
| `/cinecam remove [number]` | Remove a point (the last one if no number is given) |
| `/cinecam clear` | Remove all points |
| `/cinecam save <name>` | Save the current points as a route |
| `/cinecam load <name>` | Load a saved route |
| `/cinecam routes` | List saved routes |
| `/cinecam reload` | Reload `config.yml` |

Points are kept per player until the server restarts. Saved routes are stored in `plugins/CineCam/routes`.

## Tips for good footage

- Press **F1** to hide your hotbar and chat.
- Lock the time and weather first, for example `/time set 6000` and `/weather clear`.
- Lower your FOV a little (60 to 70) and record at 60 fps.
- Fewer points with more distance between them look calmer than many points close together.

## Configuration

`plugins/CineCam/config.yml`:

| Setting | Default | Meaning |
|---|---|---|
| `method` | `entity` | `entity`: you spectate an invisible camera entity (smoothest). `teleport`: you are teleported every tick. Try this if the picture stutters. |
| `ease-in-out` | `true` | Start slowly and slow down at the end. Not used when looping. |
| `countdown-seconds` | `3` | Countdown on the first point before the camera starts. |
| `messages` | | Every text the plugin sends, in [MiniMessage](https://docs.advntr.dev/minimessage/format.html) format. Translate or restyle them freely. |

## Building

```
./gradlew build
```

The jar is written to `build/libs`. If you do not have the Gradle wrapper in your copy, run `gradle wrapper` once, or build with a local Gradle installation (`gradle build`).

## How it works

The whole plugin is `src/main/java/io/github/cinecam/CineCamPlugin.java`.

- `spline(...)` is a Catmull-Rom spline. It is used for x, y, z, yaw and pitch, so the camera passes exactly through every point you saved.
- `unrollYaw(...)` removes the jump at 180 degrees, so the camera never spins the long way round.
- `play(...)` runs a task every tick. It works out how far along the route the camera is, applies the ease curve, and moves the camera there.
- During playback the player is in spectator mode and spectates an invisible item display. The display has a teleport duration of one tick, so the client interpolates between the steps.

Good places to start if you want to change something: the ease curve in `play(...)`, the spline in `onPath(...)`, or the messages in `config.yml`.

## License

MIT. See [LICENSE](LICENSE).
