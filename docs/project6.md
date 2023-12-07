# VR Music Video: THUNDR (liKo)

----

<p style="text-align:center;">
<img width="250" alt="Screenshot 2023-11-08 at 12 37 19 PM" src="https://media.giphy.com/media/BJtWqBAXtoatUmTqAx/giphy.gif">
</p>


-----


## How to Play

<b> 1. Open the Web Application (Desktop or Mobile):</b>
<p style="text-align:center;">
<a data-8code="t7uuk"></a>
<script src="//cdn.8thwall.com/web/share/embed8.js"></script>
</p>

<b> 2. Press the Play button to launch the experience</b>
<p style="text-align:center;">
<img src="/src/play.png"></img>
</p>

<b> 3. Tap the ground to place thunder clouds in the sky overhead</b>

<p style="text-align:center;">
<img src="/src/thundercloudbutton.png"></img>
</p>

<b> 4. Press the Thundercloud button to create a thunderstorm </b>

--------

### Animation Rig Overview

The `animationRigComponent` is responsible for managing animations within a Three.js environment. Here's a technical breakdown of its functionalities:
### Constants

```
const LoopMode = {
  once: THREE.LoopOnce,
  repeat: THREE.LoopRepeat,
  pingpong: THREE.LoopPingPong,
}
```
- `LoopMode`: Defines different looping modes (`once`, `repeat`, `pingpong`) for animations using constants from the Three.js library.

----

### `animationRigComponent` Object

#### Initialization (`init()`)

```
init() {
    this.model = null,
    this.remoteModel = null,
    this.mixer = null,
    this.activeActions = []
    let {remoteId} = this.data
    remoteId = remoteId.charAt(0) === '#' ? remoteId.slice(1) : remoteId
    const remoteEl = document.getElementById(remoteId)
    remoteEl || console.error('ramx: Remote entity not found. Pass the ID of the entity, not the model.'),
    this.model = this.el.getObject3D('mesh'),
    this.remoteModel = remoteEl.getObject3D('mesh')
    
  },
```

- Fetches and verifies required elements (`model` and `remoteModel`) for animations.
- Listens for the loaded event of the required elements and initializes when both are available.

#### `load()` Function

```
load() {
    const {el} = this
    this.model.animations = [...this.remoteModel.animations],
    this.mixer = new THREE.AnimationMixer(this.model),
    this.mixer.addEventListener('loop', (e) => {
      el.emit('animation-loop', {
        action: e.action,
        loopDelta: e.loopDelta,
      })
    }),
    this.mixer.addEventListener('finished', (e) => {
      el.emit('animation-finished', {
        action: e.action,
        direction: e.direction,
      })
    }),
    this.data.clip && this.update({})
  },
```

- Loads animations onto the `model` from the `remoteModel`.
- Sets up a mixer to manage animations and emits events on loop and finish of animations.


#### `update(prevData)` Function

```
update(prevData) {
    if (!prevData) return
    const {data} = this
    const changes = AFRAME.utils.diff(data, prevData)
    if ('clip' in changes) {
      return this.stopAction(),
      void (data.clip && this.playAction())
    }
    this.activeActions.forEach((action) => {
      'duration' in changes && data.duration && action.setDuration(data.duration),
      'clampWhenFinished' in changes && (action.clampWhenFinished = data.clampWhenFinished),
      ('loop' in changes || 'repetitions' in changes) && action.setLoop(LoopMode[data.loop], data.repetitions),
      'timeScale' in changes && action.setEffectiveTimeScale(data.timeScale)
    })
  }
```
- Monitors changes in component data and updates animations accordingly.
- Modifies active animation actions based on changes in data properties.

#### `stopAction()` Function
```
stopAction() {
    const {data} = this
    for (let i = 0; i < this.activeActions.length; i++) data.crossFadeDuration ? this.activeActions[i].fadeOut(data.crossFadeDuration) : this.activeActions[i].stop()
    this.activeActions = []
  }
```

- Stops ongoing animation actions based on the updated data.

#### `playAction()` Function

```
playAction() {
    if (!this.mixer) return
    const {model} = this
    const {data} = this
    const clips = model.animations || (model.geometry || {}).animations || []
    if (!clips.length) return
    const re = wildcardToRegExp(data.clip)
    for (let clip, i = 0; clip = clips[i]; i++) {
      if (clip.name.match(re)) {
        const action = this.mixer.clipAction(clip, model)
        action.enabled = !0,
        action.clampWhenFinished = data.clampWhenFinished,
        data.duration && action.setDuration(data.duration),
        data.timeScale !== 1 && action.setEffectiveTimeScale(data.timeScale),
        action.setLoop(LoopMode[data.loop], data.repetitions).fadeIn(data.crossFadeDuration).play(),
        this.activeActions.push(action)
      }
    }
  }
```
- Plays animations based on the provided clip pattern.
- Selects animations from available clips based on a wildcard match and configures their properties before playing.

#### `tick(t, dt)` Function

```
tick(t, dt) {
    this.mixer && !Number.isNaN(dt) && this.mixer.update(dt / 1e3)
  }
```
- Handles the Three.js animation loop by updating the mixer with the elapsed time (`dt`) if the mixer exists and `dt` is a valid number.

#### Export
- Exports the `animationRigComponent` for use in the larger codebase.

This component enables dynamic loading and management of animations onto 3D models within a Three.js scene. It allows for flexible control over animation playback, durations, looping, and time scaling, responding to changes in component data.










