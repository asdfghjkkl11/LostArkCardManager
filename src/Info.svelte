<script>
    export let calculateFlag;
    export let calculateFlagClickEvent;
</script>
<div class="info">
    <div class="flex-column">
        <span class="head">*사용방법*</span>
        <span>카드장착: 장착할 카드 우클릭</span>
        <span>카드장착해제: 장착한 카드 우클릭</span>
        <span>각성단계증가: 카드이름 좌클릭, 해당 각성단계 좌클릭</span>
    </div>
    <div class="btn-area">
        <button class="btn-info" class:active={calculateFlag === 1} on:click={(event) => calculateFlagClickEvent()}>남은 카드 경험치 효율 보기</button>
    </div>
</div>
<style>
    .info{
        padding: 8px;
        display: flex;
        justify-content: space-between;
        font-weight: 500;
        color: #b8b8b8;
    }
    .flex-column{
        display: flex;
        flex-direction: column;
    }
    .head{
        font-weight: bold;
    }
    .btn-info{
        height: 32px;
        margin: 0;
        padding: 4px;
        font-weight: 500;
        background: var(--inactive);
        border: 1px solid var(--border);
        cursor: pointer;
    }
    .btn-info.active{
        background: var(--active);
    }
</style>