<script>
    export let name;
</script>
<div class="card-group">
    <div class="group-header">{name}</div>
    <slot></slot>
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
</style>