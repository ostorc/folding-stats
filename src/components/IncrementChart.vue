<script>
    import {Bar} from 'vue-chartjs';

    export default {
        extends: Bar,
        props: [
            'name',
            'data',
            'color',
            'type'
        ],
        mounted() {
            this.render()
        },
        watch: {
            type: function () {
                this.render()
            }
        },
        methods: {
            render() {
                const set = this.computeDataSet();

                this.renderChart(
                    {
                        labels: set.labels,
                        datasets: [
                            {
                                label: this.name,
                                data: set.data,
                                fill: true,
                                borderColor: this.color,
                                backgroundColor: this.color,
                            }
                        ]
                    },
                    {
                        legend: {
                            display: false,
                        },
                        tooltips: {
                            callbacks: {
                                label: function (tooltipItem, data) {
                                    const formatter = new Intl.NumberFormat(localStorage.lang);
                                    const teamName = data.datasets[tooltipItem.datasetIndex].label;
                                    const number = formatter.format(parseInt(tooltipItem.yLabel));
                                    return `${teamName}: ${number}`;
                                }
                            }
                        },
                        maintainAspectRatio: false,
                        scales: {
                            yAxes: [
                                {
                                    ticks: {
                                        callback: function (label) {
                                            if (label > 1000000)
                                            {
                                                return label/1000000 + "M";
                                            }

                                            if (label > 1000)
                                            {
                                                return label/1000 + "k";
                                            }

                                            return label;
                                        }
                                    },
                                }
                            ]
                        }

                    }
                )
            },
            formatDate(date) {
                return date.getDate() + ". " +
                    (date.getMonth() + 1) + ". " +
                    date.getHours().toString().padStart(2, "0") + ":" +
                    date.getMinutes().toString().padStart(2, "0")
            }
            ,
            computeDataSet() {
                const labels = [];
                const data = [];

                let previous = null;

                this.data.forEach((function (item) {
                    if (previous !== null) {
                        const date = new Date();
                        const increase = item.score - previous;

                        date.setTime(item.datetime * 1000);

                        labels.push(this.formatDate(date));
                        data.push(increase);
                    }

                    previous = item.score;

                }).bind(this));

                return {data, labels};
            }
        }
    }
</script>
