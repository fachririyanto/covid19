<script lang="ts">
    import { onMount } from 'svelte';
    import BlockHeader from './BlockHeader.svelte';

    // define initial state
    let responses: any = false;
    let news: Array<any> = [];
    let totalPlaceholder: number = 0;

    /**
     * When document is ready.
     */
     onMount(async () => {
        responses = await fetch('https://fachririyanto.com/wp-json/covid-19/v1/news');
        responses = await responses.json();

        // define total placeholder
        if (window.innerWidth > 768) {
            totalPlaceholder = 3;
        } else if (window.innerWidth > 480) {
            totalPlaceholder = 2;
        } else {
            totalPlaceholder = 1;
        }

        // set news data
        news = responses;
     });
</script>

<style>
    .ui-news {
        background-color: #f5f5f5;
    }
    .ui-news .ui-editor {
        text-align: center;
    }
    .ui-news .ui-editor a {
        color: #222;
        text-decoration: none;
        border-bottom: 1px solid #222;
    }
    .ui-news .news-list {
        display: flex;
        flex-wrap: wrap;
        margin: 32px -12px 0;
    }
    .ui-news .news {
        padding: 0 0 16px;
        width: 100%;
    }
    .ui-news .news .news-card {
        position: relative;
        overflow: hidden;
        height: 100%;
        background-color: #fff;
        border-radius: 6px;
        box-shadow: 1px 2px 6px rgba(0,0,0,.1);
    }
    .ui-news .news .news-image {
        position: relative;
        margin: 0;
        padding-top: 56.25%;
    }
    .ui-news .news .news-image picture {
        display: flex;
    }
    .ui-news .news .news-image img {
        display: block;
        width: 100%;
        height: 100%;
        object-fit: cover;
    }
    .ui-news .news .news-detail {
        padding: 16px;
    }
    .ui-news .news .news-title {
        margin: 0;
        font-size: 1.55rem;
        font-weight: bold;
        line-height: 1.4;
    }
    .ui-news .news .news-date {
        display: block;
        margin-top: 4px;
        font-size: 1.35rem;
        color: #777;
        line-height: 1.3;
    }
    .ui-news .news .news-permalink {
        z-index: 2;
        font-size: 0;
        line-height: 0;
    }
    .ui-news .news-view-all {
        margin-top: 8px;
        text-align: center;
    }
    @media (min-width: 480px) {
        .ui-news .news {
            padding: 0 12px 24px;
            width: 50%;
        }
    }
    @media (min-width: 768px) {
        .ui-news .news {
            width: 33.33%;
        }
    }
</style>

<section class="ui-news ui-container" id="ui-news">
    <div class="container">
        <div class="ui-editor">
            <BlockHeader title="Berita Terkini" />
            <p>Berita terkait Covid-19 yang terjadi di Indonesia. Sumber data: <a href="https://covid19.go.id/feed/berita">https://covid19.go.id/feed/berita</a></p>
        </div>
        <div class="news-list">
            {#if news.length === 0}
                {#each Array(totalPlaceholder) as _}
                    <article class="news">
                        <div class="news-card">
                            <figure class="news-image ui-placeholder">
                                <picture class="overlay"></picture>
                            </figure>
                            <div class="news-detail">
                                <h3 class="news-title ui-placeholder">&nbsp;</h3>
                                <span class="news-date ui-placeholder"></span>
                            </div>
                        </div>
                    </article>
                {/each}
            {:else}
                {#each news as data}
                    <article class="news">
                        <div class="news-card">
                            <figure class="news-image">
                                <picture class="overlay">
                                    <img src={data.featured} alt={data.title}>
                                </picture>
                            </figure>
                            <div class="news-detail">
                                <h3 class="news-title">{data.title}</h3>
                                <span class="news-date">{data.date}</span>
                            </div>
                            <a class="news-permalink overlay" href={data.permalink} target="_blank">{data.title}</a>
                        </div>
                    </article>
                {/each}
            {/if}
        </div>
        <div class="news-view-all">
            <a href="https://covid19.go.id/p/berita" class="ui-button">Lihat Selengkapnya</a>
        </div>
    </div>
</section>