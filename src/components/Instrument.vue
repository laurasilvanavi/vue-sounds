<template>
  <div>
    <div>
      <v-btn
        v-for="note of notes"
        @click="playNote(note)"
        v-bind:key="note"
        small
        tile
        text
        outlined
        color="teal"
      >
        ♪ {{ note }}
      </v-btn>
    </div>
    <div>
      <v-btn @click="toggleMelody()" small tile text outlined color="teal">
        🎹 Melody
      </v-btn>
      <v-btn @click="toggleBass()" small tile text outlined color="teal">
        🎸 Bass
      </v-btn>
      <v-btn @click="toggleNoise()" small tile text outlined color="teal">
        💨 Noise
      </v-btn>
      <v-btn
        @click="playRandomMarioSample()"
        small
        tile
        text
        outlined
        color="teal"
      >
        🍄 Mario
      </v-btn>
      <v-btn @click="speedUpBpm()" small tile text outlined color="teal">
        👟 Speed up
      </v-btn>
      <v-btn @click="stopInstruments()" small tile text outlined color="teal">
        ✋ Stop
      </v-btn>
    </div>
  </div>
</template>

<script>
import * as Tone from "tone";
import {
  DOGIANO_GAMMA_NOTES,
  MELODY_CHORDS,
  BASS_CHORDS,
  MARIO_SAMPLES,
} from "../constants/notes";
import DogSample from "../assets/dog-sample.mp3";

export default {
  name: "Instrument",
  data() {
    return {
      polySynthSquare: null,
      polySynthSaw: null,
      notes: DOGIANO_GAMMA_NOTES,
      volume: null,
      sampler: null,
      mediumVolume: null,
      melodyPart: null,
      bassPart: null,
      noise: null,
      isTransportStarted: null,
    };
  },
  created() {
    this.mediumVolume = new Tone.Volume(-15);
    this.initDogianoInstrument();
  },
  methods: {
    initDogianoInstrument() {
      this.sampler = new Tone.Sampler({
        E4: DogSample,
      }).chain(this.mediumVolume, Tone.Destination);

      this.polySynthSquare = new Tone.PolySynth(Tone.Synth, {
        oscillator: {
          type: "square",
        },
      }).chain(new Tone.Volume(-25), Tone.Destination);
    },

    playNote(note) {
      this.polySynthSquare.triggerAttackRelease(note, "4n");
      this.sampler.triggerAttack(note);
    },

    toggleMelody() {
      if (this.melodyPart) {
        this.melodyPart.mute = !this.melodyPart.mute;
      } else {
        this.polySynthSaw = new Tone.PolySynth(Tone.Synth, {
          oscillator: {
            type: "fatsawtooth",
          },
          envelope: {
            attack: 0.01,
            release: 0.4,
          },
        }).chain(this.mediumVolume, Tone.Destination);

        this.melodyPart = new Tone.Part((time, chord) => {
          this.sampler.triggerAttackRelease(chord.note, chord.duration, time);
          this.polySynthSaw.triggerAttackRelease(
            chord.note,
            chord.duration,
            time
          );
        }, MELODY_CHORDS);

        this.playPart(this.melodyPart);
      }
    },

    playPart(part) {
      if (!this.isTransportStarted) {
        Tone.Transport.toggle();
        this.isTransportStarted = true;
        Tone.Transport.bpm.value = 132;
      }

      part.start(0);
      part.loop = true;
      part.loopEnd = "4m";
    },

    toggleBass() {
      if (this.bassPart) {
        this.bassPart.mute = !this.bassPart.mute;
      } else {
        this.bassPart = new Tone.Part((time, chord) => {
          this.polySynthSquare.triggerAttackRelease(
            chord.note,
            chord.duration,
            time
          );
        }, BASS_CHORDS);

        this.playPart(this.bassPart);
      }
    },

    toggleNoise() {
      if (this.noise) {
        this.noise.mute = !this.noise.mute;
      } else {
        this.noise = new Tone.Noise("pink").start();
        const filter = new Tone.AutoFilter({
          frequency: "8m",
        }).chain(new Tone.Volume(-20), Tone.Destination);

        this.noise.connect(filter);
        filter.start();
      }
    },

    playRandomMarioSample() {
      this.playSample(
        MARIO_SAMPLES[Math.floor(Math.random() * MARIO_SAMPLES.length)]
      );
    },

    playSample(sampleName) {
      new Tone.Player({
        url: sampleName,
        autostart: true,
      }).chain(this.mediumVolume, Tone.Destination);
    },

    speedUpBpm() {
      Tone.Transport.bpm.value += 20;
    },

    stopInstruments() {
      this.playSample(MARIO_SAMPLES[5]);

      this.stopInstrument(this.melodyPart);
      this.stopInstrument(this.bassPart);
      this.stopInstrument(this.noise);
    },

    stopInstrument(instrument) {
      if (instrument) {
        instrument.mute = true;
        instrument.stop();
      }
    },
  },
};
</script>
