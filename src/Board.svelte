<script>
	import Card from './Card.svelte';
	export let renderCardList;
</script>
<div class="board">
	{#each Object.entries(renderCardList) as [name, info]}
		<Card {info}/>
	{/each}
</div>
<style>
	.board{
		column-width: 340px;
		overflow: auto;
	}
</style>