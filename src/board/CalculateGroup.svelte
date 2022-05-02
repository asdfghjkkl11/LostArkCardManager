<script>
    import Calculate from "./Calculate.svelte";

    export let calculateList;
    $: collect = calculateList.collect;
    $: expSum = calculateList.expSum;
    $: cardList = calculateList.cardList;
    $: effect1 = calculateList.effect1;
    $: effect2 = calculateList.effect2;
</script>
<div class="card-group">
    <div class="group-header">
        <div class="flex-between">
            <span>{collect}</span>
            <span>{expSum}</span>
        </div>
        <span>
            {effect1} 계열 피해량 증가 +{effect2}%
        </span>
    </div>
    {#each cardList as info}
        <Calculate {info}/>
    {/each}
</div>
<style>
    .group-header{
        width: 324px;
        margin: 4px 0;
        padding: 4px 8px;
        font-weight: bold;
        background: #343434;
        color: #cdcdcd;
    }
    .flex-between{
        display: flex;
        justify-content: space-between;
    }
</style>