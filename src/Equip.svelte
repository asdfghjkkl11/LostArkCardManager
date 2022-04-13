<script>
    export let equippedList;
    export let equippedEffect;
    export let removeEquippedEvent;
    $: cardList = equippedList;
    $: effectList = equippedEffect;

    let flag = false;

    function toggle(){
        flag = flag ^ true;
    }
</script>
<div class="wrapper">
    <div class="header">
        장착 카드 효과
        <button class="toggle" on:click={(event) => toggle()}>
            {#if flag}
                활성된 효과만 보기
            {:else }
                비활성 효과 보기
            {/if}
        </button>
    </div>
    <div class="list">
        {#each Object.entries(cardList) as [name, info]}
            <span class="item card btn" on:contextmenu|preventDefault={(event) => removeEquippedEvent(info.name)}>
                <span class="{rarityMap[info.rarity]}">{info.name}</span>
                <span>({info.active})</span>
            </span>
        {/each}
    </div>
    <div class="list">
        {#each effectList as effect}
            {#if effect[4] || flag}
                <span class="item effect" class:active={effect[4]}>
                    <span class="effect-title">
                        {effect[0]} {effect[1]}세트
                        {#if effect[2] !== '0'}
                            ({effect[2]}각성합계)
                        {/if}
                    </span>
                    <span class="effect-text">{effect[3]}</span>
                </span>
            {/if}
        {/each}
    </div>
</div>
<style>
    .wrapper {
        width: 100%;
        background: #eeeeee;
        border-radius: 8px;
    }

    .header {
        padding: 8px;
        position: relative;
        display: flex;
        align-items: center;
        justify-content: center;
        font-weight: bold;
    }

    .toggle{
        margin: 0;
        position: absolute;
        top: 4px;
        right: 4px;
        font-weight: normal;;
        font-size: 12px;
    }

    .list {
        padding: 12px 24px;
        display: flex;
        flex-direction: column;
        gap: 2px;
    }

    .item {
        display: flex;
    }

    .card {
        align-items: center;
        justify-content: space-between;
    }

    .effect {
        flex-direction: column;
        color: #b8b8b8;
    }

    .effect-title {
        font-weight: 500;
    }

    .effect-text {
        margin-left: 20px;
        display: list-item;
    }

    .active .effect-title {
        color: #00b727;
    }

    .active .effect-text {
        color: black;
    }
</style>