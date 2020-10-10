<script lang="ts">
    import { onMount } from 'svelte';
    import { getDayId, hasClass } from '../Helper.svelte';
    import BlockHeader from './BlockHeader.svelte';

    // define initial state
    let currentDayId: string = '';
    let responses: any = false;
    let provinces: Array<any> = [];

    /**
     * Toggle table.
     */
    function toggle(e: any) {
        e.preventDefault();

        const element: any = document.getElementById('table-province');
        if (hasClass(element, 'table-collapse')) {
            element.classList.remove('table-collapse');
            this.innerText = 'Lihat Lebih Sedikit';
        } else {
            element.classList.add('table-collapse');
            this.innerText = 'Lihat Lebih Banyak';
        }
    }

    /**
     * When document is ready.
     */
    onMount(async () => {
        /**
         * Fetch data from API.
         */
        currentDayId = localStorage.getItem('covid19_last_update');
        responses    = localStorage.getItem('covid19_updates_by_province');
        if (responses && getDayId() === currentDayId) {
            responses = JSON.parse(responses);
        } else {
            responses = await fetch('https://fachririyanto.com/wp-json/covid-19/v1/updates-by-province');
            responses = await responses.json();

            // save to cache
            localStorage.setItem('covid19_last_update', getDayId());
            localStorage.setItem('covid19_updates_by_province', JSON.stringify(responses));
        }
        provinces = responses.list_data;
    });
</script>

<section class="ui-province ui-container" id="ui-province">
    <div class="container">
        <div class="ui-editor">
            <BlockHeader title="Kasus Berdasarkan Provinsi" />
            <p>Semua kasus di 34 provinsi Indonesia.</p>
        </div>
        <div class="province-table table-collapse" id="table-province">
            <table>
                <thead>
                    <tr>
                        <th>Provinsi</th>
                        <th width="130">Kasus</th>
                        <th width="130">Sembuh</th>
                        <th width="130">Meninggal</th>
                    </tr>
                </thead>
                <tbody>
                    {#each provinces as data}
                        <tr>
                            <td>{data.key}</td>
                            <td>{data.jumlah_kasus}</td>
                            <td>{data.jumlah_sembuh}</td>
                            <td>{data.jumlah_meninggal}</td>
                        </tr>
                    {:else}
                        <tr>
                            <td colspan="4">Loading...</td>
                        </tr>
                    {/each}
                </tbody>
            </table>
        </div>
        <div class="province-action">
            <button type="button" class="ui-button" on:click={toggle}>Lihat Lebih Banyak</button>
        </div>
    </div>
</section>

<style>
    .ui-province {
        background-color: #f9f9f9;
    }
    .ui-province .province-table {
        margin: 40px auto 0;
        max-width: 960px;
        overflow: hidden;
        background-color: #fff;
        border-radius: 6px;
        box-shadow: 1px 2px 6px rgba(0,0,0,.12);
    }
    .ui-province .province-table.table-collapse {
        height: 474px;
    }
    .ui-province .province-table table {
        width: 100%;
        border-collapse: collapse;
    }
    .ui-province .province-table table thead th {
        padding: 12px;
        font-size: 1.45rem;
        font-weight: 500;
        line-height: 1.3;
        text-align: left;
        background-color: #f1f1f1;
    }
    .ui-province .province-table table tbody td {
        padding: 12px;
        font-size: 1.45rem;
        line-height: 1.3;
        border: 1px solid rgba(0,0,0,.06);
    }
    .ui-province .province-action {
        margin-top: 32px;
        text-align: center;
    }
    @media (min-width: 768px) {
        .ui-province .province-table table thead th,
        .ui-province .province-table table tbody td {
            padding: 12px 16px;
        }
    }
</style>