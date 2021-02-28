<template>
  <div>
    <div>
      <v-btn
        @click="playOriginalMelody()"
        large
        tile
        text
        outlined
        color="teal"
      >
        ⭐️ Original song
      </v-btn>
      <v-btn
        @click="playContinuationByAI()"
        large
        tile
        text
        outlined
        color="teal"
      >
        🌟 AI continuation
      </v-btn>
      <v-btn @click="playNewSongWithAI()" large tile text outlined color="teal">
        👶 AI new song
      </v-btn>
    </div>
  </div>
</template>

<script>
import * as Magenta from "@magenta/music";
import {
  JUMP_SONG,
  RNN_CHECKPOINT,
  VAE_CHECKPOINT,
} from "../constants/notes.js";

export default {
  name: "Instrument",
  data() {
    return {
      player: null,
      musicRNN: null,
      musicVAE: null,
      steps: 60,
      temperature: 1.5,
      stepsPerQuarter: 4,
    };
  },
  created() {
    this.player = new Magenta.Player();

    this.musicRNN = new Magenta.MusicRNN(RNN_CHECKPOINT);
    this.musicRNN.initialize();

    this.musicVAE = new Magenta.MusicVAE(VAE_CHECKPOINT);
    this.musicVAE.initialize();
  },
  methods: {
    playOriginalMelody() {
      if (this.player.isPlaying()) {
        this.player.stop();
      } else {
        this.player.start(JUMP_SONG);
      }
    },

    playContinuationByAI() {
      if (this.player.isPlaying()) {
        this.player.stop();
      } else {
        const quantizedNotes = Magenta.sequences.quantizeNoteSequence(
          JUMP_SONG,
          this.stepsPerQuarter
        );

        this.musicRNN
          .continueSequence(quantizedNotes, this.steps, this.temperature)
          .then((sample) => this.player.start(sample))
          .catch((err) => console.log(err));
      }
    },

    playNewSongWithAI() {
      if (this.player.isPlaying()) {
        this.player.stop();
        return;
      } else {
        this.musicVAE
          .sample(1, this.temperature)
          .then((samples) => this.player.start(samples[0]));
      }
    },
  },
};
</script>
