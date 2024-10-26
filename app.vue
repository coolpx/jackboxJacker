<script setup lang="ts">
const gameNames = {
    prototype: 'Prototype',
    'ecast-test-client': 'EcastTestClient',
    'quiplash2-international': 'Quiplash 2 InterLASHional',
    'guesspionage-crowdplay': 'Guesspionage Crowdplay',
    drawful2: 'Drawful 2',
    drawful2international: 'Drawful 2',
    'acquisitions-inc': 'Acquisitions, Inc.',
    bigsurvey: 'The Jackbox Survey Scramble',
    ydkj2015: 'You Don\'t Know Jack 2015',
    drawful: 'Drawful',
    wordspud: 'Word Spud',
    lieswatter: 'Lie Swatter',
    fibbage: 'Fibbage',
    fibbage2: 'Fibbage 2',
    earwax: 'Earwax',
    auction: 'Bidiots',
    bombintern: 'Bomb Corp',
    quiplash: 'Quiplash',
    fakinit: 'Fakin\' It',
    awshirt: 'Tee K.O.',
    quiplash2: 'Quiplash 2',
    triviadeath: 'Trivia Murder Party',
    pollposition: 'Guesspionage',
    fibbage3: 'Fibbage 3',
    survivetheinternet: 'Survive the Internet',
    monstermingle: 'Monster Seeking Monster',
    bracketeering: 'Bracketeering',
    overdrawn: 'Civic Doodle',
    ydkj2018: 'You Don\'t Know Jack: Full Stream',
    splittheroom: 'Split the Room',
    rapbattle: 'Mad Verse City',
    slingshoot: 'Zeeple Dome',
    patentlystupid: 'Patently Stupid',
    triviadeath2: 'Trivia Murder Party 2',
    rolemodels: 'Role Models',
    jokeboat: 'Joke Boat',
    ridictionary: 'Dictionarium',
    pushthebutton: 'Push the Button',
    'jackbox-talks': 'Talking Points',
    quiplash3: 'Quiplash 3',
    everyday: 'The Devils and the Details',
    worldchamps: 'Champ\'d Up',
    'blanky-blank': 'Blather \'Round',
    'apply-yourself': 'Job Job',
    'drawful-animate': 'Drawful Animate',
    'the-wheel': 'The Wheel of Enormous Proportions',
    'survey-bomb': 'The Poll Mine',
    'murder-detectives': 'Weapons Drawn',
    'quiplash3-tjsp': 'Quiplash 3',
    'awshirt-tjsp': 'Tee K.O.',
    'triviadeath2-tjsp': 'Trivia Murder Party 2',
    fourbage: 'Fibbage 4',
    htmf: 'Roomerang',
    'antique-freak': 'Junktopia',
    'range-game': 'Nonsensory',
    lineup: 'Quixort',
    awshirt2: 'Tee K.O. 2',
    'nopus-opus': 'Dodo Re Mi',
    'risky-text': 'FixyText',
    'time-trivia': 'Timejinx',
    'us-them': 'Hypnotorious',
    fakinit2: 'Fakin\' It All Night Long',
    drawful3: 'Dirty Drawful',
    captcha: 'Let Me Finish'
} as { [tag: string]: string };

const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';

const status = ref('By clicking Play, you agree to our Terms of Service');
const codeRef = ref('');
const done = ref(false);

function generateCode() {
    const codeCharacters = [];
    for (let i = 0; i < 4; i++) {
        codeCharacters.push(alphabet[Math.floor(Math.random() * alphabet.length)]);
    }

    return codeCharacters.join('');
}

let triedLobbies = 0;

async function findWorkingLobby(): Promise<{ code: string, appTag: string }> {
    triedLobbies = 0;
    done.value = false;
    while (true) {
        const code = generateCode();

        status.value = `Jacking... (Tried ${triedLobbies} lobbies)`;
        codeRef.value = code;

        const response = await fetch(`https://ecast.jackboxgames.com/api/v2/rooms/${code}`);
        const data = await response.json();

        triedLobbies++;

        if (response.status === 200) {
            console.log('Room found:', data.body.appTag);
            return { code, appTag: data.body.appTag };
        } else {
            console.log('Error', response.status);
        }
    }
}

async function jack() {
    status.value = 'Jacking...';

    try {
        const lobby = await findWorkingLobby();

        const gameName = gameNames[lobby.appTag] || 'Unknown';
        const useAn = ['A', 'E', 'I', 'O', 'U'].includes(gameName[0].toUpperCase());
        status.value = `Jacked ${useAn ? 'an' : 'a'} ${gameName} lobby!`;
        done.value = true;
    } catch (error) {
        status.value = 'Error (you may be IP-banned)';
    }
}

function play() {
    window.location.href = `https://jackbox.tv/?code=${codeRef.value}`;
}
</script>

<template>
    <div>
        <div class="header">
            <div class="logo">
                <span style="font-family: 'SupriaSansHeavyItalic'; margin-right: -1px">coolpixels.</span>
                <span style="font-family: 'SupriaSansHeavyOblique'">net</span>
            </div>
        </div>
        <div class="container">
            <div class="content">
                <p>ROOM CODE</p>
                <input type="text" placeholder="JACK 4-LETTER CODE" :value="codeRef" readonly />
                <button v-if="done" @click="play">PLAY</button>
                <button v-else @click="jack">JACK</button>
                <p class="status">{{ status }}</p>
            </div>
        </div>
    </div>
</template>

<style>
.status {
    font-size: 12px;
    color: #8c939c;
    text-transform: none;
    width: 100%;
    text-align: center;
}

.header {
    background-color: #4254f4;
    height: 50px;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
}

.container {
    background-color: #0a1420;
    padding: 20px 0;
    width: 100%;
    text-transform: uppercase;
    display: flex;
    align-items: center;
    justify-content: center;
    border-bottom: 1px solid #353f4d;
}

.content {
    max-width: 420px;
    padding-right: 12px;
    width: 100%;
}

input, button {
    width: 100%;
    height: 34px;
    margin-bottom: 15px;
    padding: 21px 12px 19px;
    background-color: #1f2937;
    appearance: none;
    -webkit-appearance: none;
    -moz-appearance: none;
    border: 1px solid #353f4d;
    outline: none;
    border-radius: 12px;
    color: inherit;
    font-size: 18px;
    font-weight: 700;
    box-sizing: border-box;
}

button {
    border: none;
    height: 48px;
    width: 370px;
    margin: 0 25px 15px;
    padding: 1px 4px;
    cursor: pointer;
}

input::placeholder {
    color: #8c939c;
}

.logo {
    font-size: 28px;
    letter-spacing: -1px;
}

.logo > span {
    color: #ffffff;
    font-weight: normal;
}
</style>