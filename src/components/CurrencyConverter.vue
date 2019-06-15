<template>
    <div id="container">
        <div class="md-layout md-gutter">

            <div class="md-layout-item">
                <md-field>
                    <label>Base amount</label>
                    <md-input :value="currentBaseAmount" @input="updateBaseAmount" type="number"></md-input>
                </md-field>
            </div>

            <div class="md-layout-item">
                <md-field>
                    <label for="baseCurrency">Base currency</label>
                    <md-select v-model="selectedBaseCurrency" @md-selected="selectBasedCurrency" name="baseCurrency"
                               id="baseCurrency"
                               md-dense>
                        <md-option v-for="item of availableBaseCurrencies" :value="item" :key="item">
                            {{item}}
                        </md-option>
                    </md-select>
                </md-field>
            </div>

        </div>

        <span v-if="selectedCounterCurrency" class="md-display-1">1 {{selectedBaseCurrency}} equals
            {{basedConversionRates[selectedCounterCurrency] | formatNumber}}
            {{selectedCounterCurrency}}
        </span>

        <div class="md-layout md-gutter">

            <div class="md-layout-item">
                <md-field>
                    <label>Counter amount</label>
                    <md-input :value="currentCounterAmount" @input="updateCounterAmount" type="number"></md-input>
                </md-field>
            </div>

            <div class="md-layout-item">
                <md-field>
                    <label for="counterCurrency">Counter currency</label>
                    <md-select v-model="selectedCounterCurrency" name="counterCurrency" id="counterCurrency" md-dense>
                        <md-option v-for="item of counterCurrencyNames" :value="item" :key="item">
                            {{item}}
                        </md-option>
                    </md-select>
                </md-field>
            </div>

        </div>

        <CurrencyChart v-if="selectedCounterCurrency" :base="selectedBaseCurrency" :counter="selectedCounterCurrency"/>

    </div>
</template>

<script>
    import {availableCurrencies} from "../sample-data";
    import axios from "axios";
    import CurrencyChart from "./CurrencyChart"

    export default {
        name: 'CurrencyConverter',
        components: {
            CurrencyChart
        },
        data: function () {
            return {
                selectedBaseCurrency: 'EUR',
                currentBaseAmount: 0,

                selectedCounterCurrency: 'USD',
                counterCurrencyNames: [],
                currentCounterAmount: 0,

                basedConversionRates: {},
                availableBaseCurrencies: []
            }
        },
        methods: {
            selectBasedCurrency(value) {
                this.getBasedCurrency(value);
                this.selectedCounterCurrency = this.selectedBaseCurrency === 'EUR' ? 'USD' : 'EUR';
            },
            updateBaseAmount(value) {
                this.currentBaseAmount = value;
                this.currentCounterAmount = this.currentBaseAmount * this.basedConversionRates[this.selectedCounterCurrency];
            },
            updateCounterAmount(value) {
                this.currentCounterAmount = value;
                this.currentBaseAmount = this.currentCounterAmount / this.basedConversionRates[this.selectedCounterCurrency];
            },
            async getBasedCurrency(base) {
                const url = `https://api.exchangeratesapi.io/latest?base=${base}`;
                try {
                    const result = await axios.get(url);
                    this.basedConversionRates = result.data['rates'];
                    this.counterCurrencyNames = Object.keys(result.data['rates']);
                    this.updateBaseAmount(this.currentBaseAmount);
                } catch (e) {
                    throw new Error('Could not retrieve rates');
                }
            }
        },
        mounted() {
            this.availableBaseCurrencies = availableCurrencies;
            this.getBasedCurrency(this.selectedBaseCurrency);
        }
    }
</script>

<style scoped>
    #container {
        width: 1000px;
        margin: 0 auto;
    }

    h3 {
        margin: 40px 0 0;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #42b983;
    }
</style>
