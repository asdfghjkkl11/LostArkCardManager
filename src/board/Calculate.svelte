<script>
    export let info;
    $: name = info.name;
    $: grade = info.grade;
    $: exp = info.exp;
</script>
<div class="calculate">
    <span class="btn name {gradeMap[grade]}">{name}</span>
    <span class="exp">{exp}</span>
</div>
<style>
    .calculate{
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
    .calculate:hover{
        background: #222327;
    }
    .name{
        min-width: 200px;
        flex: 1;
    }
    .exp{
        min-width: 118px;
        flex: 1;
        text-align: right;
        color: white;
    }
</style>