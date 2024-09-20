<script>
    import { onMount } from "svelte";

    let cards = [];
    let flippedCards = [];
    let matchedPairs = 0;
    let score = 0;
    let timer = 0;
    let interval;
    let level = 'easy'; // 'easy', 'medium', 'hard'
    let totalPairs;

    const cardImages = [
        'images/imagem1.png',
        'images/imagem2.png',
        'images/imagem3.png',
        'images/imagem4.png',
        'images/imagem5.png',
        'images/imagem6.png',
        'images/imagem7.png',
        'images/imagem8.png'
    ];

    function startGame() {
        resetGame();
        createCards();
        shuffle(cards);
        timer = 0;
        clearInterval(interval);
        interval = setInterval(() => {
            timer++;
        }, 1000);
    }

    function createCards() {
        totalPairs = level === 'easy' ? 4 : level === 'medium' ? 6 : 8;
        cards = cardImages.slice(0, totalPairs).concat(cardImages.slice(0, totalPairs));
        cards = cards.map(value => ({ value, flipped: false }));
    }

    function shuffle(array) {
        for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
        }
    }

    function flipCard(index) {
        if (flippedCards.length < 2 && !cards[index].flipped) {
            cards[index].flipped = true;
            flippedCards.push(index);

            if (flippedCards.length === 2) {
                setTimeout(checkMatch, 1000);
            }
        }
    }

    function checkMatch() {
        const [firstIndex, secondIndex] = flippedCards;
        if (cards[firstIndex].value === cards[secondIndex].value) {
            score++;
            matchedPairs++;
        } else {
            cards[firstIndex].flipped = false;
            cards[secondIndex].flipped = false;
        }
        flippedCards = [];

        if (matchedPairs === totalPairs) {
            clearInterval(interval);
            alert(`Você ganhou! Pontuação: ${score}, Tempo: ${timer} segundos`);
        }
    }

    function resetGame() {
        cards = [];
        flippedCards = [];
        matchedPairs = 0;
        score = 0;
        timer = 0;
    }

    onMount(() => {
        startGame();
    });
</script>

<style>
    body {
        font-family: 'Arial', sans-serif;
        background-color: #e0f7fa;
        margin: 0;
        padding: 20px;
        color: #333;
    }

    .scoreboard {
        text-align: center;
        margin-bottom: 20px;
    }

    h1 {
        color: #00796b;
    }

    .grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
        gap: 10px;
        max-width: 600px;
        margin: auto;
    }

    .card {
        width: 100px;
        height: 100px;
        display: flex;
        align-items: center;
        justify-content: center;
        background-color: #4db6ac;
        border-radius: 8px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        cursor: pointer;
        transition: transform 0.3s, background-color 0.3s;
    }

    .card.flipped {
        background-color: #80deea;
        transform: scale(1.1);
    }

    .card img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        border-radius: 8px;
        transition: opacity 0.3s;
    }

    .card.flipped img {
        opacity: 1;
    }

    .card:not(.flipped) img {
        opacity: 0;
    }

    select {
        margin: 10px 0;
        padding: 5px;
        border-radius: 5px;
        border: 1px solid #00796b;
    }

    button {
        padding: 10px 20px;
        border: none;
        background-color: #00796b;
        color: white;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s;
    }

    button:hover {
        background-color: #004d40;
    }

    @media (max-width: 600px) {
        .grid {
            grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
        }

        .card {
            width: 80px;
            height: 80px;
        }
    }
</style>

<div class="scoreboard">
    <h1>Jogo da Memória</h1>
    <p>Pontuação: {score}</p>
    <p>Tempo: {timer} segundos</p>
    <select bind:value={level} on:change={startGame}>
        <option value="easy">Fácil</option>
        <option value="medium">Médio</option>
        <option value="hard">Difícil</option>
    </select>
</div>

<div class="grid">
    {#each cards as card, index}
        <div class="card {card.flipped ? 'flipped' : ''}" on:click={() => flipCard(index)}>
            {#if card.flipped}
                <img src={card.value} alt="Imagem do cartão">
            {:else}
                ?
            {/if}
        </div>
    {/each}
</div>

