<template>
  <div>
    <v-btn
      v-for="note of notes"
      @click="playNote(note)"
      v-bind:key="note"
      large
      tile
      text
      outlined
      color="teal"
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
  data() {
    return {
      synth: null,
      notes: GAMMA_NOTES,
    };
  },

  created() {
    this.synth = new Tone.Synth().toDestination();
  },
  
  methods: {
    playNote(note) {
      this.synth.triggerAttackRelease(note, "8n");
    },
  },
};
</script>
