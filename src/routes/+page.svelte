<script>
    import { text } from "@sveltejs/kit";

    let game = $state([
        -1, -1, -1,
        -1, -1, -1,
        -1, -1, -1
    ]);
    let draw = $state(0);
    let players = $state([
        "player ○", "player x"
    ]);
    let symbols = $state([
        "○", "x"
    ]);

    let win = $state([
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6]
    ])

    let desc = $state("Let the game Begin! (" + players[draw] + ")")

    let gameEnd = $state(false)

    let winSituationIndex = -1;

    const checkWin = (index) => {
        return win.some((situation, i) => {
            if (situation.every(i => game[i] === index)) {
                winSituationIndex = i;
                return true;
            }
            return false;
        });
    };

    const resetGame = () => {
        game = [
            -1, -1, -1,
            -1, -1, -1,
            -1, -1, -1
        ];
        draw = 0;
        gameEnd = false
        winSituationIndex = -1
        desc = "Let the game Begin! (" + players[draw] + ")"
    };

    const click = (index) => {
        if (gameEnd) { return; }

        if (game[index] !== -1) { 
            return;
        }
        game[index] = draw

        if (checkWin(draw)) {
            desc = players[draw] + " has won! (" + winSituationIndex + ")";
            for (const button of win[winSituationIndex]) {
                game[button] = draw + 2
            }
            gameEnd = true
            return;
        }

        if (game.every(cell => cell !== -1)) {
            desc = "No one has won!"
            return;
        }

        draw = (draw + 1) % symbols.length;
        desc = "Its " + players[draw] + " turn"
    }
</script>

<style>
    .button-grid {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        gap: 10px;
    }

    :global(body) {
        @apply bg-zinc-900;
    }

    .animate-button:hover {
        animation: button 1s;
    }

    .animate-reset:hover {
        transition: transform .3s ease-in-out;
        transform: scale(1.1);
    }

    .animation-tik-title {
        animation: tik-title 7s infinite;
    }

    @keyframes tik-title {
        0%, 100% {

        }

        20% {
            transform: rotate(20deg);
        }

        40% {
            transform: scale(1.3);
        }

        60% {
            transform: rotate(-20deg);
        }

        80% {
            transform: scale(0.9);
        }
    }

    .animate-selected-button:hover {
        transition: transform .3s ease-in-out;
        transform: scale(0.9);
    }

    @keyframes button {
        0%, 100% {

        }

        50% {
            transform: rotate(15deg);
            scale: 1.2;
            filter: blur(0.05rem);
        }
    }
</style>

<div class="filed fixed flex w-full h-full flex-col items-center justify-center">
    <div class="info mb-4">
        <h1 class="text-center text-4xl pb-5 font-semibold text-white animation-tik-title">Tik Tak Toe</h1>
        <h2 class="text-center text-lg font-semibold text-white">{desc}</h2>
    </div>
    <div class="grid grid-cols-3 gap-3">
        {#each game as value, index}
        <button id="{index}" class="{value === -1 ? "bg-gray-300" : value === 0 ? "bg-blue-500" : value === 1 ? "bg-green-500": value === 2 ? "bg-blue-900 text-white" : value === 3 ? "bg-green-900 text-white": "bg-red-500"} {!gameEnd ? value !== -1 ? "animate-selected-button" : "animate-button" : "animate-selected-button"} max-h-20 h-16 max-w-20 w-16 rounded-xl" onclick="{() => click(index)}">{value === -1 ? "" : value === 0 || value === 1 ? symbols[value] : value === 2 || value === 3 ? symbols[value - 2] : "e"}</button>
        {/each}
    </div>
    <div class="reset mb-5">
        <button onclick="{resetGame}" class="bg-orange-500 p-3 m-3 rounded-xl animate-reset text-white">Reset</button>
    </div>
</div>