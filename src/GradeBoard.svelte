<script>
	import Card from './Card.svelte';
	import CardGroup from './CardGroup.svelte';
	export let renderCardList;
	let renderList;
	$: {
		renderList = {
			"일반": [],
			"고급": [],
			"희귀": [],
			"영웅": [],
			"전설": []
		};

		for(let card in renderCardList){
			let grade = renderCardList[card].grade;
			renderList[grade].push(renderCardList[card]);
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
	.board{
		column-width: 340px;
		overflow: auto;
	}
</style>