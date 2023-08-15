<script lang="ts">
	import AceOfClubs from '$lib/assets/ace_of_clubs.webp';
	import AceOfDiamonds from '$lib/assets/ace_of_diamonds.webp';
	import AceOfHearts from '$lib/assets/ace_of_hearts.webp';
	import AceOfSpades from '$lib/assets/ace_of_spades.webp';
	import KingOfClubs from '$lib/assets/king_of_clubs.webp';
	import KingOfDiamonds from '$lib/assets/king_of_diamonds.webp';
	import KingOfHearts from '$lib/assets/king_of_hearts.webp';
	import KingOfSpades from '$lib/assets/king_of_spades.webp';
	import QueenOfClubs from '$lib/assets/queen_of_clubs.webp';
	import QueenOfDiamonds from '$lib/assets/queen_of_diamonds.webp';
	import QueenOfHearts from '$lib/assets/queen_of_hearts.webp';
	import QueenOfSpades from '$lib/assets/queen_of_spades.webp';
	import JackOfClubs from '$lib/assets/jack_of_clubs.webp';
	import JackOfDiamonds from '$lib/assets/jack_of_diamonds.webp';
	import JackOfHearts from '$lib/assets/jack_of_hearts.webp';
	import JackOfSpades from '$lib/assets/jack_of_spades.webp';

	const TextureMap = new Map<string, string>([
		['11', AceOfClubs],
		['12', AceOfDiamonds],
		['13', AceOfHearts],
		['14', AceOfSpades],
		['21', KingOfClubs],
		['22', KingOfDiamonds],
		['23', KingOfHearts],
		['24', KingOfSpades],
		['31', QueenOfClubs],
		['32', QueenOfDiamonds],
		['33', QueenOfHearts],
		['34', QueenOfSpades],
		['41', JackOfClubs],
		['42', JackOfDiamonds],
		['43', JackOfHearts],
		['44', JackOfSpades]
	]);

	const CardNames = new Map<string, string>([
		['11', 'Ace of clubs'],
		['12', 'Ace of diamonds'],
		['13', 'Ace of hearts'],
		['14', 'Ace of spades'],
		['21', 'King of clubs'],
		['22', 'King of diamonds'],
		['23', 'King of hearts'],
		['24', 'King of spades'],
		['31', 'Queen of clubs'],
		['32', 'Queen of diamonds'],
		['33', 'Queen of hearts'],
		['34', 'Queen of spades'],
		['41', 'Jack of clubs'],
		['42', 'Jack of diamonds'],
		['43', 'Jack of hearts'],
		['44', 'Jack of spades']
	]);

	const texture = (face: number, suit: number) => TextureMap.get(`${face}${suit}`);
	const alt = (face: number, suit: number) => CardNames.get(`${face}${suit}`);

	class Card {
		face: number;
		suit: number;
		id: string;

		constructor(face: number, suit: number) {
			this.face = face;
			this.suit = suit;
			this.id = `${face}${suit}`;
		}
	}

	let cards: Card[] = [
		new Card(1, 1),
		new Card(1, 2),
		new Card(1, 3),
		new Card(1, 4),
		new Card(2, 1),
		new Card(2, 2),
		new Card(2, 3),
		new Card(2, 4),
		new Card(3, 1),
		new Card(3, 2),
		new Card(3, 3),
		new Card(3, 4),
		new Card(4, 1),
		new Card(4, 2),
		new Card(4, 3),
		new Card(4, 4)
	];

	let selectedId: string = '';
	function selectCard(card: Card) {
		if (selectedId === card.id) {
			selectedId = '';
		} else if (selectedId === '') {
			selectedId = card.id;
		} else {
			// swap the cards
			let idx1 = 0;
			let idx2 = 0;
			cards.forEach((c, i) => {
				if (c.id === selectedId) {
					idx1 = i;
				} else if (c.id === card.id) {
					idx2 = i;
				}
			});
			let swap = cards[idx1];
			cards[idx1] = cards[idx2];
			cards[idx2] = swap;
			selectedId = '';
		}
	}

	function selectCardWithKeyboard(e: KeyboardEvent, card: Card) {
		if (e.key === 'Enter' || e.key === ' ') {
			selectCard(card);
			e.preventDefault();
			return;
		}
	}

	function isSolved(cards: Card[]) {
		let rowsFaces = [new Set<number>(), new Set<number>(), new Set<number>(), new Set<number>()];
		let colsFaces = [new Set<number>(), new Set<number>(), new Set<number>(), new Set<number>()];
		let rowsSuits = [new Set<number>(), new Set<number>(), new Set<number>(), new Set<number>()];
		let colsSuits = [new Set<number>(), new Set<number>(), new Set<number>(), new Set<number>()];

		for (let i = 0; i < cards.length; i++) {
			const card = cards[i];
			const row = Math.floor(i / 4);
			const col = i % 4;

			if (rowsFaces[row].has(card.face) || rowsSuits[row].has(card.suit)) {
				return false;
			}
			if (colsFaces[col].has(card.face) || colsSuits[col].has(card.suit)) {
				return false;
			}

			rowsFaces[row].add(card.face);
			rowsSuits[row].add(card.suit);
			colsFaces[col].add(card.face);
			colsSuits[col].add(card.suit);
		}

		return true;
	}

	function resetGame() {
		let newCards = [];
		for (let i = 1; i <= 4; i++) {
			for (let j = 1; j <= 4; j++) {
				newCards.push(new Card(i, j));
			}
		}
		cards = newCards;
	}

	let headerMinimized = false;
	export const snapshot = {
		capture: () => cards,
		restore: (value: Card[]) => (cards = value)
	};

	$: solved = isSolved(cards);
</script>

<main>
	<header>
		<nav>
			<h1 class:hidden={headerMinimized}>Card puzzle game</h1>
			<button type="button" on:click={() => resetGame()} class="reset"> Reset Game </button>
			<button type="button" on:click={() => (headerMinimized = !headerMinimized)} class="minimize">
				{#if headerMinimized}
					<span>Expand</span>
				{:else}
					<span>Full screen</span>
				{/if}
			</button>
		</nav>
		<section class:hidden={headerMinimized}>
			<p>
				Arranged in the four by four grid below are the cards ace, king, queen and jack, with one
				card from each of the four suits. To solve the puzzle, arrange the cards such that each row
				and column contain one of each card and suit. Click a card to select it, and then click
				another card to swap.
			</p>
		</section>
		{#if solved}
			<section>
				<h2 class="winner">Congratulations, you've solved the puzzle!</h2>
			</section>
		{/if}
	</header>

	<section class="game">
		{#each cards as card (card.id)}
			<div class="card" role="none">
				<button
					tabindex="0"
					on:keyup={(e) => selectCardWithKeyboard(e, card)}
					on:click={() => selectCard(card)}
				>
					<img
						class:selected={selectedId === card.id}
						src={texture(card.face, card.suit)}
						alt={alt(card.face, card.suit)}
						aria-label={alt(card.face, card.suit)}
					/>
				</button>
			</div>
		{/each}
	</section>
</main>

<style type="text/scss" lang="scss">
	@import url('https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@10..48,400;10..48,600&family=Open+Sans&family=Playfair+Display:wght@600&display=swap');
	:global(html) {
		background-color: #0182b5;
		background: linear-gradient(#579db8 10px, #0182b5 99%);
		height: 100%;
		background-repeat: no-repeat;
		background-attachment: fixed;
	}

	:global(body) {
		padding: 0;
		margin: 0;
	}

	:global(h1) {
		margin: 0;
		padding: 0;
		font-family: 'Playfair Display';
		font-weight: 600;
		font-size: 2em;
		line-height: 2em;
		border-bottom: thin solid #63ccff;
	}

	:global(h2) {
		margin: 0;
		padding: 0;
		font-family: 'Bricolage Grotesque';
		font-weight: 600;
		font-size: 1.8em;
	}

	:global(p) {
		font-size: 1.1em;
		font-family: 'Open sans';
		font-weight: 400;
		margin-top: 0.5em;
	}

	nav {
		display: inline-flex;
		align-items: center;
	}

	header {
		max-width: 1024px;
		display: flex;
		flex-direction: column;
		margin: auto auto;
	}

	.hidden {
		visibility: hidden;
		height: 0;
	}

	.reset {
		margin-left: auto;
		margin-right: 20px;
		background: none;
		font-family: 'Bricolage Grotesque';
		font-size: 16px;
		font-weight: 600;
		border: none;
		cursor: pointer;
		border-bottom: thin solid #63ccff;
		height: 40px;
		&:hover {
			color: #ffcd36;
		}
	}

	.minimize {
		background: none;
		font-family: 'Bricolage Grotesque';
		font-size: 16px;
		font-weight: 600;
		border: none;
		cursor: pointer;
		border-bottom: thin solid #63ccff;
		height: 40px;
		&:hover {
			color: #ffcd36;
		}
	}

	main {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		margin: auto auto;
		max-width: 1024px;
		width: 95%;
		gap: 18px;
	}

	.game {
		display: grid;
		grid-template-columns: 1fr 1fr 1fr 1fr;
		grid-template-rows: 1fr 1fr 1fr 1fr;
		gap: 15px;
		align-content: stretch;
		max-width: 60vh;
		background-color: rgba(228, 228, 228, 0.25);
		box-shadow: 0px 0px 10px 2px rgba(48, 48, 48, 0.3);
		border-radius: 12px;
		padding: 8px;
	}

	.selected {
		scale: 1.025;
		border: 2px solid #ffcd36;
		border-radius: 12px;
		box-shadow: 2px 2px 3px 5px rgba(48, 48, 48, 0.3);
	}

	.winner {
		color: #ffcd36;
	}

	.card {
		display: flex;
		scale: 1;
		transition: scale 0.1s;

		&:hover {
			border-radius: 12px;
			box-shadow: 2px 2px 3px 3px rgba(48, 48, 48, 0.2);
			scale: 1.02;
			transition: scale 0.2s;
		}

		&:focus-within {
			border-radius: 12px;
			box-shadow: 2px 2px 3px 3px rgba(48, 48, 48, 0.2);
			scale: 1.02;
			transition: scale 0.2s;
		}

		& button {
			outline: none;
			border: 0;
			border-radius: 12px;
			padding: 0;
			cursor: pointer;
		}

		& img {
			max-width: 100%;
			height: 100%;
		}
	}
</style>
