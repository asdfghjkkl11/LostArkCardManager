<script>
    export let info;
    export let cardLeftClickEvent;
    export let cardRightClickEvent;

    $: name = info.name;
    $: rarity = info.rarity;
    $: isHas = info.isHas === 1;
    $: active1 = info.active > 0;
    $: active2 = info.active > 1;
    $: active3 = info.active > 2;
    $: active4 = info.active > 3;
    $: active5 = info.active > 4;

    function addEvent(){
        if(!isHas){
            cardLeftClickEvent(name, 1, 0);
        }else if(active5){
            cardLeftClickEvent(name, 1, 5);
        }else{
            cardLeftClickEvent(name, 1, info.active +1);
        }
    }

    function isHasEvent(){
        cardLeftClickEvent(name, 1, 0);
    }

    function isActiveEvent(active){
        cardLeftClickEvent(name, 1, active);
    }

    function deleteEvent(){
        cardLeftClickEvent(name, 0, 0);
    }
</script>
<div class="card" on:contextmenu|preventDefault={(event) => cardRightClickEvent(name)}>
    <span class="btn name {rarityMap[rarity]}" on:click={(event) => addEvent()}>{name}</span>
    <div class="btn circle is-has" class:active={isHas} on:click={(event) => isHasEvent()}></div>
    <div class="btn hexagon active1" class:active={active1} on:click={(event) => isActiveEvent(1)}></div>
    <div class="btn hexagon active2" class:active={active2} on:click={(event) => isActiveEvent(2)}></div>
    <div class="btn hexagon active3" class:active={active3} on:click={(event) => isActiveEvent(3)}></div>
    <div class="btn hexagon active4" class:active={active4} on:click={(event) => isActiveEvent(4)}></div>
    <div class="btn hexagon active5" class:active={active5} on:click={(event) => isActiveEvent(5)}></div>
    <div class="btn close" on:click={(event) => deleteEvent()}></div>
</div>
<style>
    .card{
        height: 20px;
        margin: 1px;
        padding: 4px 8px;
        display: inline-flex;
        align-items: center;
        gap: 4px;
        font-size: 16px;
        line-height: 16px;
        font-weight: 500;
        border: solid 1px var(--border);
        border-radius: 4px;
        background: linear-gradient(0deg,#4a4c52 0,#53555d);
    }
    .card:hover{
        background: #222327;
    }
    .name{
        min-width: 200px;
        flex: 1;
    }
    .circle {
        width: 16px;
        height: 16px;
        background: var(--inactive);
        border-radius: 50%
    }
    .circle.active{
        background: var(--active);
    }
    .hexagon {
        width: 12px;
        height: 20px;
        background-image: url("/image/hex-inactive.svg");
        background-repeat: no-repeat;
        contain: content;
    }
    .hexagon.active{
        background-image: url("/image/hex-active.svg");
    }

    .close {
        width: 16px;
        height: 16px;
        position: relative;
    }
    .close:before, .close:after {
        width: 3px;
        height: 17px;
        position: absolute;
        left: 6.4px;
        top: -0.4px;
        content:' ';
        background-color: #343434;
    }
    .close:before {
        transform: rotate(45deg);
    }
    .close:after {
        transform: rotate(-45deg);
    }
</style>