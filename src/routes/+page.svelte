<script lang="ts">
    import {Soundfont} from "smplr"
    type note = "crotchet"|"quaver"|"semiquaver"|"minim"
    const rhythms:note[][] = [["crotchet","quaver","quaver"], ["crotchet","semiquaver","semiquaver","semiquaver","semiquaver"],["crotchet","crotchet","minim"]]

    const chordAndTheirRespectiveNotes = {
        "I":["C","E","G"],
        "ii":["D","F","A"],
        "IV":["F","A","C"],
        "V":["G","B","D"],
        "vi":["A","C","E"]
    }

    const chordProgressions = ["I","ii","V","vi","V","I"]

    const BEAT_LENGTHS: Record<note, number> = {
        minim: 2.0,
        crotchet: 1.0,
        quaver: 0.5,
        semiquaver: 0.25,
    };
    const secondsPerBeat = (bpm: number) => 60 / bpm;

    const generateMusic = async ()=>{
        const context = new AudioContext();
        const piano = Soundfont(context, {
            instrument: "acoustic_grand_piano"
        });
        await piano.ready

        const beats_per_minute = 60;
        const beatSec = secondsPerBeat(beats_per_minute);
        let currentTime = context.currentTime + 0.1;

        chordProgressions.forEach((chord,index)=>{
            const rhythm = rhythms[index % rhythms.length]
            const notes = chordAndTheirRespectiveNotes[chord as ("I"|"ii"|"IV"|"V"|"vi")]

            rhythm.forEach((r,index2)=>{
                const random_pitch = notes[Math.floor(Math.random() * notes.length)];
                const is_last_note = (index2==(rhythm.length-1))
                const pitch = is_last_note?notes[0]:random_pitch
                const durationSec = BEAT_LENGTHS[r] * beatSec;

                piano.start({
                    note: `${pitch}4`,
                    time: currentTime, 
                    duration: durationSec * 0.9, // Slight gap so repeated notes articulate cleanly
                    velocity: 80,
                });
                currentTime+=durationSec;
            })
        })
    }
</script>

<button onclick={generateMusic}>Generate music</button>
