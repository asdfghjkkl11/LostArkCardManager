<script>
	import Card from './Card.svelte';
	import CardGroup from './CardGroup.svelte';

	export let renderCardList;
	export let getCollectList;

	let renderList;

	$: {
		renderList = {};

		for (let card in renderCardList) {
			let name = renderCardList[card].name;

			let collectList = getCollectList(name);

			if(collectList) {
				for (let i = 0; i < collectList.length; i++) {
					if (renderList[collectList[i]] === undefined) {
						renderList[collectList[i]] = [];
					}

					renderList[collectList[i]].push(renderCardList[card]);
				}
			}
		}
	}
</script>
<div class="board">
	{#each Object.entries(renderList) as [name, cardList]}
		{#if cardList.length > 0}
			<CardGroup {name}>
				{#each cardList as info}
					<Card {info}/>
				{/each}
			</CardGroup>
		{/if}
	{/each}
</div>
<style>
	.board {
		column-width: 340px;
		overflow: auto;
		flex: 1;
	}
</style>