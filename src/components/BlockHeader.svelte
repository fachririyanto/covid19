<script lang="ts">
    export let title: string = '';
</script>

<header class="ui-block-header">
    <h2>{title}</h2>
</header>

<style>
    .ui-block-header {
        margin: 0 0 8px;
    }
    .ui-block-header h2 {
        margin: 0;
        font-size: 2.2rem;
        line-height: 1.1;
    }
    @media (min-width: 768px) {
        .ui-block-header h2 {
            font-size: 3.2rem;
        }
    }
</style>