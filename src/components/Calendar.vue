<template>
    <div id="v-calendar">
        <header class="v-calendar-header">
            <div class="v-arrow" @click="previousMonth">
                <i class="v-arrow-left"></i>
            </div>            
            <h4>{{ monthName + ' ' + year }}</h4>
            <div class="v-arrow" @click="nextMonth">
                <i class="v-arrow-right"></i>
            </div>
        </header>
        <main class="v-calendar-content">
            <div class="v-flex cl-t-header">
                <span v-for="dayName in dayNames" v-bind:key="dayName" class="cell cell-header">{{ dayName }}</span>
            </div>
            <div class="v-flex">
                <div v-for="prev in firstDay" class="cell"></div>
                <div v-for="day in daysInMonth" class="cell cell-day" v-bind:key="day" @click="selectDay(day)">
                    <span :class="isCurrentDay(day)">{{ day }}</span>
                </div>
            </div>
        </main>
    </div>
</template>

<script>

    export default {
        name: 'calendar',

        data() {
            return {
                now: new Date,
                dayNames: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
                monthNames: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']

            }
        },
        computed: {
            monthName() {
                return this.monthNames[this.month];
            },
            date() {
                return this.now.getDate();
            },
            day() {
                return this.now.getDay();
            },
            month() {
                return this.now.getMonth();
            },
            year() {
                return this.now.getFullYear();
            },
            daysInMonth() {
                return new Date(this.year, this.month + 1, 0).getDate();
            },
            firstDay() {
                return new Date(this.year, this.month, 1).getDay();
            }
        },
        methods: {
            nextMonth() {
                this.now = new Date(this.now.setMonth(this.now.getMonth() + 1));
            },
            previousMonth() {
                this.now = new Date(this.now.setMonth(this.now.getMonth() - 1));
            },
            isCurrentDay(day) {
                const now = new Date;

                return (this.month == now.getMonth() && this.year == now.getFullYear() && day == now.getDate()) ? 'active' : '';
            },
            selectDay(day) {
                this.$emit('day-changed', new Date(this.year, this.month, day));
            }
        }
    }

</script>

<style scoped lang="scss">

    #v-calendar {

        .v-calendar-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: .75rem;
            text-transform: uppercase;
            font-weight: bold;
            align-items: center;

            h4 {
                margin: 0;
                font-size: 1.1em;
            }
        }

        .v-calendar-content {

            .cell {
                width: 14.28%;
                text-align: center;
                padding: .2rem 0;

                &.cell-header {
                    padding: .5rem 0;
                }

                &.cell-day {
                    cursor: pointer;

                    span {
                        display: inline-block;
                        width: 18px;
                        height: 18px;
                        line-height: 18px;
                        border-radius: 50%;
                    }

                    span:hover {
                        background-color: #d7d7d7;
                    }

                    .active {
                        background-color: #5f5f5f;
                        color: #FFF;
                        font-weight: bold;
                    }
                }
            }
        }


        .v-arrow {
            display: inline-block;
            align-items: center;
            justify-content: center;
            cursor: pointer;

            i {
                display: inline-block;
                width: 0;
                height: 0;
            }

            .v-arrow-left {
                border-top: 4px solid transparent;
                border-bottom: 4px solid transparent;
                border-right: 4px solid #5f5f5f;
            }

            .v-arrow-right {
                border-top: 4px solid transparent;
                border-bottom: 4px solid transparent;
                border-left: 4px solid #5f5f5f;
            }
        }

        .v-flex {
            display: flex;
            flex-wrap: wrap;    
        }
    }

</style>