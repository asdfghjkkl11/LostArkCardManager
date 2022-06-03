<script>
	import CalculateGroup from "./CalculateGroup.svelte";

	export let renderCardList;
	export let collectedObject;
	export let gradeList;
	export let getCollectList;
	export let getCollect;

	let renderList = [];

	$: {
		renderList = [];
		let renderObject = {};

		for (let card in renderCardList) {
			let name = renderCardList[card].name;

			let collectList = getCollectList(name);

			if(collectList) {
				for (let i = 0; i < collectList.length; i++) {
					if (renderObject[collectList[i]] === undefined) {
						renderObject[collectList[i]] = [];
					}

					let active = renderCardList[card].active;
					let grade = renderCardList[card].grade;

					for(let j = active+1; j < 6; j++){
						let exp = gradeList[grade][j];
						renderObject[collectList[i]].push({
							"name": name,
							"grade": grade,
							"exp": exp,
							"active": j
						});
					}
				}
			}
		}

		for(let render in renderObject){
			if(collectedObject[render] !== undefined) {
				renderObject[render].sort(function (a, b) {
					return a.exp - b.exp;
				});

				let [collect, isHas, active] = collectedObject[render];
				let effect = getCollect(collect);
				let index = 0;
				let expSum = 0;
				let tempList = [];

				for(let i = 0; i < effect.length; i++) {
					if(isHas >= effect[i][1] && active < effect[i][2]) {
						while(index + active < effect[i][2]) {
							expSum += renderObject[render][index].exp;
							tempList.push(renderObject[render][index]);
							index++;
						}
						renderList.push({
							"collect": collect,
							"expSum": expSum,
							"cardList": tempList,
							"effect1": effect[i][3],
							"effect2": effect[i][4]
						});
						expSum = 0;
						tempList = [];
					}
				}
			}
		}
		renderList.sort(function (a,b){
			return (b.effect2*a.expSum) -(a.effect2*b.expSum);
		});
	}
</script>
{#if renderList.length > 0}
	<div class="board">
		{#each renderList as calculateList}
			<CalculateGroup {calculateList}/>
		{/each}
	</div>
{:else}
	<div class="empty">
		<div class="info">
			<span>남은 카드가 없습니다.</span>
		</div>
	</div>
{/if}
<style>
	.board {
		column-width: 340px;
		overflow: auto;
		flex: 1;
	}
	.empty{
		flex: 1;
	}
	.info{
		height: 32px;
		padding: 4px 8px;
		font-size: 18px;
		font-weight: bold;
		background: #343434;
		color: #cdcdcd;
		border-radius: 4px;
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>