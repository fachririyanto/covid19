<script lang="ts">
    import { onMount } from 'svelte';
    import { getDayId } from '../Helper.svelte';

    // define initial state
    let currentDayId: string = '';
    let responses: any = false;

    /**
     * When document is ready.
     */
    onMount(async () => {
        /**
         * Animate increase number.
         */
        function animate(element: any) {
            let interval: any = setInterval(() => {
                var value: number  = parseInt(element.getAttribute('data-val'));
                var number: number = parseInt(element.getAttribute('data-number'));
                if (value >= number) clearInterval(interval);
                element.setAttribute('data-val', value + 1);
                element.innerText = new Intl.NumberFormat('id-ID').format(value + 1);
            }, 10);
        }

        /**
         * Fetch data from API.
         */
        currentDayId = localStorage.getItem('covid19_last_update');
        responses    = localStorage.getItem('covid19_updates');
        if (responses && getDayId() === currentDayId) {
            responses = JSON.parse(responses);
        } else {
            responses = await fetch('https://fachririyanto.com/wp-json/covid-19/v1/updates');
            responses = await responses.json();

            // save to cache
            localStorage.setItem('covid19_last_update', getDayId());
            localStorage.setItem('covid19_updates', JSON.stringify(responses));
        }

        /**
         * Set data to layout.
         */
        const elements: any = document.querySelectorAll('h2.stats-number');
        elements.forEach((element: any, index: number) => {
            let value: number;
            switch (index) {
                case 0:
                    value = responses.update.total.jumlah_positif;
                    break;
                case 1:
                    value = responses.update.total.jumlah_sembuh;
                    break;
                case 2:
                    value = responses.update.total.jumlah_meninggal;
                    break;
                default:
                    value = 0;
            }
            element.classList.remove('ui-placeholder');
            element.setAttribute('data-number', value);
            element.setAttribute('data-val', value - 30);
            animate(element);
        });
    });
</script>

<section class="ui-bigstats" id="ui-bigstats">
    <div class="container">
        <div class="stats">
            <h2 class="stats-number ui-placeholder" id="stats-confirmed" data-val="0" data-number="0">0</h2>
            <p>Positif</p>
        </div>
        <div class="stats">
            <h2 class="stats-number ui-placeholder" id="stats-recovered" data-val="0" data-number="0">0</h2>
            <p>Sembuh</p>
        </div>
        <div class="stats">
            <h2 class="stats-number stats-danger ui-placeholder" id="stats-deaths" data-val="0" data-number="0">0</h2>
            <p>Meninggal</p>
        </div>
    </div>
</section>

<style>
    .ui-bigstats {
        padding: 40px 0;
    }
    .ui-bigstats .container {
        display: flex;
    }
    .ui-bigstats .stats {
        width: 33.33%;
    }
    .ui-bigstats .stats h2 {
        margin: 0 0 8px;
        font-size: 2rem;
        font-weight: 500;
        line-height: 1;
    }
    .ui-bigstats .stats h2.stats-danger {
        color: #EA2C2C;
    }
    .ui-bigstats .stats p {
        margin: 0;
        font-size: 1.45rem;
        font-weight: 500;
        color: #555;
        line-height: 1.4;
        text-transform: uppercase;
    }
    @media (min-width: 768px) {
        .ui-bigstats .stats {
            text-align: center;
        }
        .ui-bigstats .stats h2 {
            font-size: 4.4rem;
        }
    }
    @media (min-width: 1200px) {
        .ui-bigstats .stats {
            padding: 0 24px;
        }
        .ui-bigstats .stats h2 {
            font-size: 5.4rem;
        }
        .ui-bigstats .stats p {
            font-size: 1.65rem;
        }
    }
</style>