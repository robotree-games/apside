<template>
  <a-scene v-pre physics environment="preset: starry; groundColor: #fff; ground: hills; 
      groundYScale: 8; fog: 0; groundTexture: walkernoise; grid: none; lightPosition: 0 1 -0.2">

      <a-assets timeout="100000">
        <audio loop preload="auto">
          <source src="./descent.wav" type="audio/wav">
        </audio>
        <a-mixin id="cube" geometry="primitive: box; width: 0.33; height: 0.33; depth: 0.33"
                 hoverable grabbable stretchable draggable droppable
                 event-set__hoveron="_event: hover-start; material.opacity: 0.7; transparent: true"
                 event-set__hoveroff="_event: hover-end; material.opacity: 1; transparent: false"
                 dynamic-body shadow></a-mixin>
        </a-mixin>
      </a-assets>
      <a-entity camera look-controls wasd-controls position="0 1 1"
                capture-mouse
                raycaster cursor="rayOrigin:mouse"
                static-body="shape: sphere; sphereRadius: 0.001"
                super-hands="colliderEvent: raycaster-intersection;
                             colliderEventProperty: els;
                             colliderEndEvent:raycaster-intersection-cleared;
                             colliderEndEventProperty: clearedEls;">
      </a-entity>
      <a-entity class="transformer" position = "0 0.6 -1"
                color-randomizer audiohandler droppable static-body
                collision-filter="collisionForces: false"
                geometry="primitive: box; width: 0.5; height: 0.5; depth: 0.5"
                event-set__dragon="_event: dragover-start; material.color: orange"
                event-set__dragoff="_event: dragover-end; material.color: purple"
                material="color:purple" shadow>
        <a-entity text="value: Drag&drop to change color;
                        width: 0.5; wrapCount: 12; align: center"
                  position="0 0 0.25">
        </a-entity>
      </a-entity>

      <a-entity audiohandler class="cube" mixin="cube" position="0 0.265 -1" material="color: red" ></a-entity>
      <a-entity audiohandler class="cube" mixin="cube" position="0 0.265 -0.5" material="color: red" ></a-entity>
      <a-entity audiohandler class="cube" mixin="cube" position="-1 0.265 -1" material="color: blue" ></a-entity>
      <a-entity class="cube" mixin="cube" position="-1 0.265 -0.5" material="color: blue" ></a-entity>
      <a-entity class="cube" mixin="cube" position="1 0.265 -1" material="color: green" ></a-entity>
      <a-entity class="cube" mixin="cube" position="1 0.265 -0.5" material="color: green" ></a-entity>
      <!-- ground collider keeps objets from falling -->
      <a-box static-body width=100 height=0.001 depth=100 visible="false"></a-box>
  </a-scene>
</template>

<script>
import 'aframe'
import 'aframe-environment-component'
import 'aframe-particle-system-component'
import 'aframe-physics-system'
import 'super-hands'
import 'aframe-physics-extras'

export default {
  name: 'HelloWorld'
}
     //color randomizer
      AFRAME.registerComponent('color-randomizer', {
        play: function () {
          this.el.addEventListener('drag-drop', function (evt) {
            evt.detail.dropped.setAttribute('material', 'color',
              '#'+(Math.random()*0xFFFFFF<<0).toString(16))
            // color randomizer credit: http://stackoverflow.com/questions/1484506/random-color-generator-in-javascript#comment6801353_5365036
          })
        }
      })
    AFRAME.registerComponent('audiohandler', {
      init:function() {
        let playing = false;
        let audio = document.querySelector("audio");
        this.el.addEventListener('click', () => {
          console.log('click')
              if(!playing) {
                  audio.play();
              } else {
                  audio.pause();
                  audio.currentTime = 0;
              }
              playing = !playing;
        });
      }
    })
      // forward mouse and touch events to the super-hands entity
      AFRAME.registerComponent('capture-mouse', {
        init: function () {
          this.eventRepeater = this.eventRepeater.bind(this)
          this.el.sceneEl.addEventListener('loaded', () => {
            this.el.sceneEl.canvas.addEventListener('mousedown', this.eventRepeater)
            this.el.sceneEl.canvas.addEventListener('mouseup', this.eventRepeater)
            this.el.sceneEl.canvas.addEventListener('touchstart', this.eventRepeater)
            this.el.sceneEl.canvas.addEventListener('touchmove', this.eventRepeater)
            this.el.sceneEl.canvas.addEventListener('touchend', this.eventRepeater)
          }, {once: true})
        },
        eventRepeater: function (evt) {
          if (evt.type.startsWith('touch')) {
            evt.preventDefault()
            // avoid repeating touchmove because it interferes with look-controls
            if (evt.type === 'touchmove') { return }
          }
          this.el.emit(evt.type, evt.detail)
        }
      })
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
