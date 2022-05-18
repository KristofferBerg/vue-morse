<template>
    <div>
        <div style="float: left; width: 50%">
            <img style="min-width: 600px;" src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/International_Morse_Code.svg/315px-International_Morse_Code.svg.png" />
        </div>
        <div style="float: left; width: 50%;">
            <h1>testing</h1>
            <div style="display: block; background: #000; color: #0f0; width: 400px; margin: 0 auto; padding: 30px; word-wrap: break-word; height: auto; overflow: auto; text-align: left;">{{stringToMorse}}</div>
            <hr>
            <textarea v-model="string" style="padding: 10px; height: 200px; width: 400px; text-transform: uppercase;"></textarea><br>
            <button @click="readString()">Read Now</button>
            <br>
            frequency:
            <input v-model.number="frequency" type="range" step="1" min="50" max="1000" />
            <input v-model.number="frequency" type="imput" step="1" min="50" max="1000" />
            <br>
            timeUnitMs:
            <input v-model.number="unit" type="range" step="0.001" min="0" max="0.5" /> {{unit}}
        </div>
    </div>
</template>
<script>
    var AudioContext = window.AudioContext || window.webkitAudioContext;
    var ctx = new AudioContext();
    var oscillator;
    
    export default {
        data(){
            return {
                string: 'Welcome to Wikipedia',
                morseCode: [],
                unit: 0.06,
                frequency: 500,
            }
        },

        watch: {
            frequency( f ){
                if (oscillator) {
                    oscillator.frequency.value = f;
                }
            }
        },

        methods: {
            async readString(){
                const morseArray = this.stringToMorse.split('');
                console.log('morseArray', morseArray );

                var t = ctx.currentTime;

                oscillator = ctx.createOscillator();
                oscillator.type = 'triangle';
                oscillator.frequency.value = this.frequency;

                var gainNode = ctx.createGain();
                gainNode.gain.setValueAtTime(0, t);

                morseArray.forEach( (letter) => {
                    switch(letter) {
                        case ".":
                            gainNode.gain.setValueAtTime(1, t);
                            t += this.unit;
                            gainNode.gain.setValueAtTime(0, t);
                            t += this.unit;
                            break;
                        case "-":
                            gainNode.gain.setValueAtTime(1, t);
                            t += 3 * this.unit;
                            gainNode.gain.setValueAtTime(0, t);
                            t += this.unit;
                            break;
                        case " ":
                            t += 7 * this.unit;
                            break;
                    }
                });
                
                oscillator.connect(gainNode);
                gainNode.connect(ctx.destination);
                oscillator.start();

                return false;
            },

            returnNum( b ) {
                return new Promise((resolve) => {
                    let milliseconds = 0;
                    let freq = this.frequency;
                });
            },
        },

        computed: {
            stringToMorse(){
                const morseCode = [];
                
                const chars = this.string
                    .toLowerCase()
                    .replace(/[^a-z0-9\s\n\r]/g, '')
                    .replace(/  +/g, ' ')
                    .split('');
                
                chars.forEach( char => {
                    if( char == ' '){
                        morseCode.push( morse.space.join('') );
                    }
                    else if(char == '\r' || char == '\n'){
                        morseCode.push( morse.return.join('') );
                    } 
                    else {
                        morseCode.push( morse[char].join('') );
                    }
                });
                
                return morseCode.join(' ');
            }, 
        }
    }

    const dot = '.';
    const dash = '-';
    const divide = '_';
    const newline = '\n';
    const morse = {
       a: [dot,dash],
       b: [dash,dot,dot,dot],
       c: [dash,dot,dash,dot],
       d: [dash,dot,dot],
       e: [dot],
       f: [dot,dot,dash,dot],
       g: [dash,dash,dot],
       h: [dot,dot,dot,dot],
       i: [dot,dot],
       j: [dot,dash,dash,dash],
       k: [dash,dot,dash],
       l: [dot,dash,dot,dot],
       m: [dash,dash],
       n: [dash,dot],
       o: [dash,dash,dash],
       p: [dot,dash,dash,dot],
       q: [dash,dash,dot,dash],
       r: [dot,dash,dot],
       s: [dot,dot,dot],
       t: [dash],
       u: [dot,dot,dash],
       v: [dot,dot,dot,dash],
       w: [dot,dash,dash],
       x: [dash,dot,dot,dash],
       y: [dash,dot,dash,dash],
       z: [dash,dash,dot,dot],
       1: [dot,dash,dash,dash,dash],
       2: [dot,dot,dash,dash,dash],
       3: [dot,dot,dot,dash,dash],
       4: [dot,dot,dot,dot,dash],
       5: [dot,dot,dot,dot,dot],
       6: [dash,dot,dot,dot,dot],
       7: [dash,dash,dot,dot,dot],
       8: [dash,dash,dash,dot,dot],
       9: [dash,dash,dash,dash,dot],
       0: [dash,dash,dash,dash,dash],
       space: [divide],
       return: [newline]
    };
    
    // The letters of a word are separated by a space of duration equal to three dots, and the 
    // words are separated by a space equal to seven dots.
</script>