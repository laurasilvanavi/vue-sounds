<template>
  <div>
    <v-btn
      v-for="note of notes"
      @click="playNote(note)"
      v-bind:key="note"
      small
    >
      ♪ {{ note }}
    </v-btn>
  </div>
</template>

<script>
import * as Tone from "tone";
import { GAMMA_NOTES } from "../constants/notes";

export default {
  name: "Instrument",
  data: function() {
    return {
      synth: new Tone.Synth().toDestination(),
      notes: GAMMA_NOTES,
    };
  },
  methods: {
    playNote(note) {
      this.synth.triggerAttackRelease(note, "8n");
    },
  },
};
</script>
