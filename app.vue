<script setup lang="ts">
const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';

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
    while (true) {
        const code = generateCode();

        status.value = `Jacking... (Tried ${triedLobbies} lobbies)`;

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

const status = ref('Idle');

async function jack() {
    status.value = 'Jacking...';

    try {
        const lobby = await findWorkingLobby();

        status.value = `Jacked '${lobby.appTag}': ${lobby.code}`;
    } catch (error) {
        status.value = 'Error (you may be IP-banned)';
    }
}
</script>

<template>
    <div>
        <h1>Jackbox Jacker</h1>
        <button v-on:click="jack()">Start Jacking</button>
        <p>{{ status }}</p>
    </div>
</template>

<style module>
html {
    background-color: #121212;
    color: #ffffff;
    font-family: sans-serif;
}
</style>