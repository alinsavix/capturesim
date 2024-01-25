# capturesim
## An attempt to emulate OBS game capture & compositing timing


### Overview
There are sometimes questions about whether certain changes to the
cadence of frame capture w/ OBS would be beneficial, with some vague
definition of "beneficial".

This (really, *really* bad) python script attempts to emulate the way
OBS's game capture and compositing pipeline works and gather some stats
about it. This can help allow testing of different algorithms for frame
capturing, and easier study of the overall capture behavior.

Currently we require an input script that is a capture from
PresentMon, to provide realistic game frame timings for analysis. Several
sample files are included in the `pmcap` subdirectory.


### Current OBS Behavior
The current OBS behavior can be summarized as such, where `obs_ouput_interval` is how often OBS generates new frames for output (e.g. this would be 0.01666666 seconds for 60fps):

Game capture hook:

1. A game calls `present` for each game frame, at an arbitrary rate
2. The game capture hook intercepts these calls. If it has been longer than (obs_output_interval / 2) since the last frame was captured, the hook copies the game's frame to shared texture memory.
3. The capture hook passes on the `present` call to the driver

On the OBS side, running independently:

1. The compositor is invoked to generate a new output frame every `obs_output_interval`
2. At the time, whatever frame was most recently captured by the game capture hook is used in the compositing


### Using the sim:

The ultra-short version: `./sim.py --presentmon-file <filename>`


### Todo and such

A very non-exhaustive list:
* Need to make the architecture a bit more agreeable to having pluggable frame generation sources, game capture algorithms, etc
* One big outstanding question is: how do we manage command-line parameters for those various things? We can have multiple different subsystems, and each subsystem can have a handler that can take an arbitrary set of configuration options, and I'm not sure how to wrangle the CLI options for that.
  * Maybe just make everything use a config file?
* Reconsider output formats. Not sure what's useful here
* Graph rendering with various bits might be nice
* Figure out a list of other test modes/algorithms might be useful
* Figure out a *good* way to figure out if a given frame pacing/set of captured frames/whatever is better or worse than another
