<script lang="ts">
    /**
     * Get current date.
     */
    const monthName: Array<string> = [
        'January', 'February', 'March', 'April', 'May', 'June',
        'July', 'August', 'September', 'October', 'November', 'December'
    ];
    const date: Date = new Date();
</script>

<header class="ui-headline" id="ui-headline">
    <div class="container">
        <div class="ui-editor">
            <h1>Indonesia Darurat<br/><span>Covid-19</span></h1>
            <p>
                Last Update {date.getDate() + ' ' + monthName[date.getMonth()] + ' ' + date.getFullYear()}.<br/>
                Collecting data from <a href="https://data.covid19.go.id">https://data.covid19.go.id</a>
            </p>
        </div>
    </div>
</header>

<style>
    .ui-headline {
        padding: 64px 0 0;
    }
    .ui-headline h1 {
        margin: 0 0 16px;
        font-size: 4.4rem;
        font-weight: bold;
        line-height: 1.1;
    }
    .ui-headline h1 span {
        color: #EA2C2C;
    }
    .ui-headline p {
        margin: 0;
        font-size: 1.45rem;
        color: #555;
        line-height: 1.6;
    }
    .ui-headline p a {
        color: #222;
        text-decoration: none;
        border-bottom: 1px solid #222;
    }
    @media (min-width: 768px) {
        .ui-headline {
            padding: 120px 0 40px;
        }
        .ui-headline h1 {
            font-size: 6.4rem;
        }
    }
</style>