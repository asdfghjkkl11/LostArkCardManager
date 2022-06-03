<script>
    export let collectedEffect;
    export let wholeCollectedEffect;
</script>
<div class="wrapper">
    <div class="header">
        수집 카드 효과
    </div>
    <ul class="list">
        {#each collectedEffect as collect}
            <li class="item">
                <span class="effect">
                    <span>
                        {#if collectMap[collect[0]]}
                            {collect[0]} 계열 피해량 증가 +{collect[1]}%
                        {:else}
                            {collect[0]} +{collect[1]}
                        {/if}
                    </span>
                    <span>
                        {#if collectMap[collect[0]]}
                            (+{wholeCollectedEffect[collect[0]]}%)
                        {:else}
                            (+{wholeCollectedEffect[collect[0]]})
                        {/if}
                    </span>
                </span>
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
    .effect{
        display: flex;
        justify-content: space-between;
    }
</style>