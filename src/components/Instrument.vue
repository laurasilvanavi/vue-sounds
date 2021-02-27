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
import CatSample from "../assets/cat-sample.mp3";

export default {
  name: "Instrument",
  data() {
    return {
      synth: null,
      notes: GAMMA_NOTES,
      volume: null,
      sampler: null,
    };
  },
  created() {
    this.synth = new Tone.Synth().toDestination();

    this.volume = new Tone.Volume(-5);
    this.sampler = new Tone.Sampler({
      G2: CatSample,
    }).chain(this.volume, Tone.Destination);
  },
  methods: {
    playNote(note) {
      this.synth.triggerAttackRelease(note, "4n");
      this.sampler.triggerAttack(note);
    },
  },
};
</script>
