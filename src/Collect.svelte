<script>
    export let collectedEffect;
    $: collectList = collectedEffect;
</script>
<div class="wrapper">
    <div class="header">
        수집 카드 효과
    </div>
    <ul class="list">
        {#each collectList as collect}
            <li class="item">
                {#if collectMap[collect[0]]}
                    {collect[0]} 계열 피해량 증가 +{collect[1].toFixed(2)}%
                {:else}
                    {collect[0]} +{collect[1]}
                {/if}
            </li>
        {/each}
    </ul>
</div>
<style>
    .wrapper{
        width: 100%;
        background: #eeeeee;
        border-radius: 8px;
    }
    .header{
        padding: 8px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-weight: bold;
    }
    .list{
        padding: 12px 24px;
        display: flex;
        flex-direction: column;
        gap: 2px;
    }
    .item{
        margin-left: 20px;
    }
</style>