<script>
    import {getContext} from 'svelte';
    export let info;
    let cardLeftClickEvent = getContext("cardLeftClickEvent");
    let cardRightClickEvent = getContext("cardRightClickEvent");

    $: name = info.name;
    $: grade = info.grade;
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
<div class="card" on:click={(event) => addEvent()} on:contextmenu|preventDefault={(event) => cardRightClickEvent(name)}>
    <span class="btn name {gradeMap[grade]}">{name}</span>
    <div class="btn circle is-has" class:active={isHas} on:click|stopPropagation={(event) => isHasEvent()}></div>
    <div class="btn hexagon active1" class:active={active1} on:click|stopPropagation={(event) => isActiveEvent(1)}></div>
    <div class="btn hexagon active2" class:active={active2} on:click|stopPropagation={(event) => isActiveEvent(2)}></div>
    <div class="btn hexagon active3" class:active={active3} on:click|stopPropagation={(event) => isActiveEvent(3)}></div>
    <div class="btn hexagon active4" class:active={active4} on:click|stopPropagation={(event) => isActiveEvent(4)}></div>
    <div class="btn hexagon active5" class:active={active5} on:click|stopPropagation={(event) => isActiveEvent(5)}></div>
    <div class="btn close" on:click|stopPropagation={(event) => deleteEvent()}></div>
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
        -ms-user-select: none;
        -moz-user-select: -moz-none;
        -khtml-user-select: none;
        -webkit-user-select: none;
        user-select: none;
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
        background-image: url("/image/close.svg");
        background-repeat: no-repeat;
        contain: content;
    }
</style>