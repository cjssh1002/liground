<template>
  <div>
    <AnalysisHead/>
    <AnalysisEvalRow id='game_clock'/>
    <EngineStats/>
    <div v-if='active' class='processing-bar'/>
    <div class='analysis pv-window'>
      <div class='multipv-line grid-parent' v-for='pv in multipv' :key='pv.type'>
        <div class='multipv-eval float-left-child'>
          {{ pv.cpDisplay }}

        </div>
        <div class='multipv float-left-child'>
         {{ pv.pv }}
       </div>
     </div>
    <div id='engine_details'>
      {{engineDetails}}
    </div>
    </div>
    <div class='analysis game-window noselect'>
      <div v-for='move in moves' :key='move.ply'>
        <div class='move-field'>
        <div v-if='move.ply % 2 == 1' class='float-left-child move-number'>{{(move.ply+1) / 2}}.</div>
        <div class='float-left-child move-name' v-bind:class='{ active : move.fen != $store.getters.lastFen && move.fen == $store.getters.fen}' @click="updateBoard(move)">{{move.name}}</div>
        </div>
      </div>
    </div>
    <JumpButtons v-on:flip-board="$emit('flip-board', 0)"/>
     <div class='console-log' id='textarea'>
       <p v-for='line in stdIO' :key='line.type'>{{line}}</p>
     </div>
     <input type='text' id='lname' name='lname' :value='fen' size='60'>
  </div>
</template>

<script>
import AnalysisHead from './AnalysisHead'
import AnalysisEvalRow from './AnalysisEvalRow'
import JumpButtons from './JumpButtons'
import EngineStats from './EngineStats'

export default {
  name: 'AnalysisView',
  components: {
    AnalysisHead, AnalysisEvalRow, JumpButtons, EngineStats
  },
  methods: {
    updateBoard (move) {
      this.$store.dispatch('fen', move.fen)   
    }
  },
  props: {
    fen: {
      type: String,
      default: ''
    },
    reset: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    active () {
      return this.$store.getters.active
    },
    stdIO () {
      return this.$store.getters.stdIO
    },
    engineDetails () {
      return this.$store.getters.idName + ' ' + this.$store.getters.idAuthor
    },
    message () {
      return this.$store.getters.message
    },
    counter () {
      return this.$store.getters.counter
    },
    multipv () {
      return this.$store.getters.multipv
    },
    pv () {
      return this.$store.getters.pv
    },
    cp () {
      return this.$store.getters.cpforWhiteStr
    },
    pv2 () {
      return this.$store.getters.pv2
    },
    cp2 () {
      return this.$store.getters.cpforWhiteStr
    },
    moves () {
      return this.$store.getters.moves
    }
  },
  watch: {
    reset: function () {
      this.$store.dispatch('resetMultiPV')
    }
  }
}
</script>

<style scoped>
input {
  font-size: 11pt;
}
#game_clock {
  margin-top: -10px;
  margin-bottom: -20px;
}
.pv-window {
  background-color: white;
  font-weight: 100;
  white-space: nowrap;
}
.game-window {
  height: 270px;
}
.analysis {
  border-radius: 3px 3px 3px 3px;
  font-family: 'Noto Chess';
  font-weight: 100;
  border-color: #888;
  border-width: 1px;
  border-style: solid;
  font-weight: 200;
  width: 600px;
  overflow-y: scroll;
}
#engine_details {
  font-size: 8pt;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  font-style: oblique;
}
.console-log {
  border-radius: 3px 3px 3px 3px;
  border-color: #888;
  border-width: 1px;
  border-style: solid;
  font-family: monospace;
  font-weight: 100;
  font-size: 8pt;
  width: 600px;
  height: 200px;
  overflow-y: scroll;
  white-space: nowrap;
  text-align: left;
}
p {
  margin: 0px;
}
.multipv-line:hover {
  background-color: #d3e1eb;
  cursor: pointer;
}
.multipv-line {
  border-width: 1px;
  border-color: #333;
  border-top: 0;
  border-right: 0;
  border-left: 0;
  border-style: solid;
  text-align: left;
}
.multipv {
}
.multipv-eval {
  padding: 5px;
  font-weight: 1000;
  font-family: sans-serif;
  width: 70px;
  text-align: center;
}
.move-field {
  color: #333;
  color: black;
  background-color: #111;
  text-align: center;
  font-size: 12pt;
  padding: 0px 0px 0px 0px;
  border-color: #222;
  border-width: 1px;
  margin-right: 5px;
  pointer-events: none;
  min-width: 20px;
  white-space: nowrap;
  main-max-width: auto;
  gauge-gap: 17px;
}
.move-field:hover .move-name {
  background-color: #2196F3;
  cursor: pointer;
  border-radius: 4px 4px 4px 4px;
  color: #fff;
}
.move-field:hover .active {
  background-color: #2196F3;
  cursor: pointer;
  border-radius: 4px 4px 4px 4px;
  color: #fff;
}
.move-field .active{
  background-color:#444;
  border-radius: 4px 4px 4px 4px;
  color: #fff;
}
.grid-parent {
  align-items: center;
}
.move-number {
  color: #777;
}
.move-name {
  margin-right: 4px;
  pointer-events: auto;
}
.processing-bar {
  height: 5px;
  width: 600px;
  background-color: #6ca040;
  background-color: #2196F3;
  background-image: url('../assets/images/analysis/bar-highlight.png');
  animation: bar-anim 1000s linear infinite;
  transition: width 1s;
}
@keyframes bar-anim {
  0% {
      background-position: 0 0;
  }
  100% {
      background-position: 100000px 0;
  }
}

</style>
