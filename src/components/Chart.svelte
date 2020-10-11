<script lang="ts">
    import { onMount } from 'svelte';
    import { getDayId } from '../Helper.svelte';
    import Chart from 'chart.js';
    import UIBlockHeader from './BlockHeader.svelte';

    // define initial state
    let currentDayId: string = '';
    let responses: any = false;

    /**
     * When document is ready.
     */
    onMount(async () => {
        /**
         * Ini chart.
         */
        function init(ctx: any, labels: any, items: any) {
            let datalabels: any = [];
            let datasets: any = [];
            let start: number = 0;
            let mod: number = 0;

            // 90 4 -> 0, 89, 22.5, 45

            if (window.innerWidth <= 480) {
                start = labels.length - 30;
                mod   = 30;
            } else if (window.innerWidth <= 768) {
                start = labels.length - 60;
                mod   = 60 / 2;
            } else if (window.innerWidth <= 1024) {
                start = labels.length - 90;
                mod   = 90 / 3;
            } else {
                start = 0;
                mod   = Math.ceil(labels.length / 5);
            }

            datalabels = labels.filter((label: string, index: number) => {
                if (index >= start) {
                    return label;
                }
            });
            datasets = items.filter((item: any, index: number) => {
                if (index >= start) {
                    return item;
                }
            });

            new Chart(ctx, {
                type: 'line',
                data: {
                    labels: datalabels,
                    datasets: [{
                        data: datasets,
                        backgroundColor: 'transparent',
                        borderColor: '#ffffff',
                        pointBackgroundColor: '#f3ed2b'
                    }]
                },
                options: {
                    legend: {
                        display: false
                    },
                    scales: {
                        xAxes: [{
                            ticks: {
                                callback: function(value: string, index: number) {
                                    if (index === 0) return value;
                                    if (index === (datalabels.length - 1)) return value;
                                    if ((index + 1) % mod === 0) {
                                        return value;
                                    }
                                    return '';
                                },
                                autoSkip: false,
                                maxRotation: 0,
                                fontColor: '#ffffff'
                            },
                            gridLines: {
                                lineWidth: 0,
                                zeroLineColor: '#236bc3'
                            }
                        }],
                        yAxes: [{
                            ticks: {
                                callback: function(value: string) {
                                    return parseInt(value) === 0 ? value : (parseInt(value) / 1000) + 'K';
                                },
                                fontColor: '#ffffff'
                            },
                            scaleLabel: {
                                display: true,
                                labelString: 'Jumlah Kasus',
                                fontColor: '#ffffff'
                            },
                            gridLines: {
                                color: '#236bc3',
                                zeroLineColor: '#236bc3'
                            }
                        }]
                    }
                }
            });
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
         * Setup chart label and data.
         */
        const labels: any = [];
        const items: any = [];

        // define month name
        const monthName: Array<string> = [
            'Jan', 'Feb', 'Mar', 'Apr', 'Mei', 'Jun',
            'Jul', 'Agu', 'Sep', 'Okt', 'Nov', 'Des'
        ];

        responses.update.harian.forEach((data: any) => {
            // setup label
            const date  = new Date(data.key_as_string);
            const day   = date.getDate();
            const month = monthName[date.getMonth()];
            const year  = date.getFullYear();
            const label = day + ' ' + month + ' ' + year;

            // save labels
            labels.push(label);

            // save items
            items.push(data.jumlah_positif.value);
        });

        /**
         * Init chart.
         */
        const ctx: any = document.getElementById('chart-timeline');
        init(ctx, labels, items);
    });
</script>

<section class="ui-chart ui-container" id="ui-chart">
    <div class="ui-editor">
        <UIBlockHeader title="Perkembangan Covid-19 di Indonesia" />
        <p>Statistik perkembangan kasus Covid-19 di Indonesia dari tanggal 2 Maret 2020 sampai dengan hari ini.</p>
    </div>
    <div class="chart-timeline">
        <canvas id="chart-timeline" height="120"></canvas>
    </div>
</section>

<style>
    .ui-chart {
        padding-left: 20px;
        padding-right: 20px;
        color: #fff;
        background-color: #2f79d4;
    }
    .ui-chart .chart-timeline {
        margin-top: 40px;
    }
    @media (min-width: 1280px) {
        .ui-chart {
            padding-left: 40px;
            padding-right: 40px;
        }
    }
</style>