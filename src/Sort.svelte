<script>
    export let groupFlag;
    export let gradeFilter;
    export let kindFilter;
    export let groupFlagClickEvent;
    export let gradeFilterClickEvent;
    export let kindFilterClickEvent;
</script>
<div class="sort">
    <div class="layer">
        <div class="label">그룹</div>
        <button class="btn-sort" class:active={groupFlag === 0} on:click={(event) => groupFlagClickEvent(0)}>기본</button>
        <button class="btn-sort" class:active={groupFlag === 1} on:click={(event) => groupFlagClickEvent(1)}>등급별</button>
        <button class="btn-sort" class:active={groupFlag === 2} on:click={(event) => groupFlagClickEvent(2)}>수집세트별</button>
    </div>
    <div class="layer">
        <div class="label">등급</div>
        {#each Object.entries(gradeMap) as [name, info]}
            <button class="btn-sort" class:active={gradeFilter[name] === 1} on:click={(event) => gradeFilterClickEvent(name)}>{name}</button>
        {/each}
    </div>
    <div class="layer">
        <div class="label">세트효과</div>
        {#each Object.entries(collectMap) as [name, info]}
            <button class="btn-sort" class:active={kindFilter[name] === 1} on:click={(event) => kindFilterClickEvent(name)}>{name}</button>
        {/each}
    </div>
</div>
<style>
    .sort{
        padding: 8px;
        display: flex;
        flex-direction: column;
        align-items: start;
        justify-content: center;
        gap: 8px;
    }
    .layer{
        display: flex;
        align-items: center;
        justify-content: start;
        gap: 4px;
        flex-wrap: wrap;
    }
    .label{
        width: 92px;
        height: 24px;
        padding: 4px;
        text-align: center;
        font-weight: bold;
        background: var(--inactive);
        border-radius: 4px;
    }
    .btn-sort{
        width: 100px;
        height: 32px;
        margin: 0;
        padding: 4px;
        font-weight: 500;
        background: var(--inactive);
        border: 1px solid var(--border);
        cursor: pointer;
    }
    .btn-sort.active{
        background: var(--active);
    }
</style>