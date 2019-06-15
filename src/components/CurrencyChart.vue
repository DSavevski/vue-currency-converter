<template>


    <div id="container" style="height: 450px; width: 650px;">
        <div class="separator">
            <md-button :disabled="selectedPeriod === filter" @click="clickAvatar($event.target.outerText)"
                       v-for="filter of filters" :key="filter"
                       class="md-icon-button">
                {{filter}}
            </md-button>
        </div>
        <canvas id="currencyChart" height="350" width="600"></canvas>
    </div>


</template>

<script>
    import axios from "axios";
    import Chart from "chart.js"
    import moment from "moment";

    const apiUrl = 'https://api.exchangeratesapi.io/history';

    export default {
        name: "CurrencyChart",
        props: {
            base: String,
            counter: String
        },
        watch: {
            counter: function (newVal, oldVal) {
                if (oldVal !== newVal) {
                    this.counter = newVal;
                    this.clickAvatar(this.selectedPeriod);
                }
            }
        },
        data: function () {
            return {
                filters: ['5D', '15D', '1M'],
                data: {},
                selectedPeriod: ''
            }
        },
        methods: {
            async getHistory(url) {
                const result = await axios.get(url);
                this.data = result.data['rates'];
                this.displayLineChart();
            },
            displayLineChart() {
                const currencyChart = document.getElementById('currencyChart');
                new Chart(currencyChart, {
                    type: 'line',
                    data: {
                        labels: Object.keys(this.data),
                        datasets: [{
                            label: `${this.counter} rate relative to ${this.base}`,
                            data: Object.values(this.data).map(item => item[this.counter])
                        }]
                    },
                    options: {
                        scales: {
                            yAxes: [{
                                ticks: {
                                    beginAtZero: false
                                }
                            }]
                        }
                    }
                });
            },
            buildUrl(base, counter, start, end) {
                return apiUrl + `?start_at=${end}&end_at=${start}&base=${base}&symbols=${counter}`;
            },
            clickAvatar(value) {
                let startDate = '';
                let endDate = '';
                switch (value) {
                    case '5D': {
                        startDate = moment().format('YYYY-MM-DD');
                        endDate = moment().subtract(5, 'days').format('YYYY-MM-DD');
                        break;
                    }
                    case '15D': {
                        startDate = moment().format('YYYY-MM-DD');
                        endDate = moment().subtract(15, 'days').format('YYYY-MM-DD');
                        break;
                    }
                    default: {
                        startDate = moment().format('YYYY-MM-DD');
                        endDate = moment().subtract(1, 'months').format('YYYY-MM-DD');
                        break;
                    }
                }

                this.getHistory(this.buildUrl(this.base, this.counter, startDate, endDate));
                this.selectedPeriod = value;
            }
        },
        created() {
            // eslint-disable-next-line no-console
            console.log('Currency chart component created.');
        },
        mounted() {
            // eslint-disable-next-line no-console
            console.log('Currency chart component mounted.');
            this.clickAvatar('5D');
        }
    }
</script>

<style scoped>
    #container {
        margin-top: 25px;
    }
</style>