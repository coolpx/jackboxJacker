<script setup lang="ts">
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

        status.value = `Jacked '${lobby.appTag}'`;
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
</style>