<template>
    <div id="v-journal">
        <header class="v-header">
            <div class="v-header-date">
                <span class="v-date">{{ date }}</span>
                <div>
                    <p class="v-day">{{ dayName }}</p>
                    <p class="v-year">{{ monthName }} {{ year }}</p>
                </div>
            </div>
        </header>
        <main class="row">
            <div class="w-50">
                <section class="v-page v-h-100">
                    <h4>What needs to be done?</h4>
                    <input type="text" v-model="value" @keypress.enter="addTask" class="v-control mt-3" placeholder="New task...">
                    <div class="v-day-content" v-if="content.tasks.length">
                        <ul class="v-tasks-list">
                            <li v-for="(task, index) in content.tasks" v-bind:key="index" :class="{completed: task.completed}" class="v-task">
                                <label class="v-custom-checkbox">{{ task.name }}
                                    <input type="checkbox" v-model="task.completed">
                                    <span class="v-checkbox"></span>
                                </label>
                                <span class="v-task-delete" @click="deleteTask(index)"></span>
                            </li>
                        </ul>
                    </div>
                </section>
            </div>
            <div class="w-50">
                <section class="v-page v-note">
                    <h4>Note</h4>
                    <textarea class="v-control mt-3" v-model="content.note"></textarea>
                </section>
                <section class="v-page">
                    <calendar 
                        @day-changed="dayChanged">
                    </calendar>
                </section>
            </div>
        </main>
        <section class="v-page v-quote">
            <h4>Quote of the day</h4>
            <blockquote>
                <p v-text="quote.content"></p>
               <footer v-text="quote.author"></footer>
            </blockquote>
        </section>
    </div>
</template>

<script>
    import calendar from './Calendar'
    import axios from 'axios'

    export default {
        components: {
            calendar
        },

        data() {
            return {
                now: new Date,
                value: '',
                dayNames: ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
                monthNames: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
                content: {
                    tasks: [],
                    note: ''
                },
                quote: {
                    author: 'Samuel Butler',
                    content: "It is better to have loved and lost than never to have lost at all."
                }
            }
        },
        created() {
            this.fetchContent();
            this.fetchQuote();
        },
        computed: {
            date() {
                return this.now.getDate();
            },
            day() {
                return this.now.getDay();
            },
            dayName() {
                return this.dayNames[this.day];
            },
            month() {
                return this.now.getMonth();
            },
            monthName() {
                return this.monthNames[this.month];
            },
            year() {
                return this.now.getFullYear();
            },
            storageDate() {
                return this.date + ':' + this.month + ':' + this.year;
            }
        },
        watch: {
            content: {
                deep: true,
                handler: function() {
                    this.storeContent();
                },
            },
            now: {
                deep: true,
                handler: function() {
                    this.fetchContent();
                }
            }
        },
        methods: {
            previousDay() {
                this.now = new Date(this.now.setDate(this.now.getDate() - 1));
            },
            nextDay() {
                this.now = new Date(this.now.setDate(this.now.getDate() + 1));
            },
            addTask() {
                if(this.value) {
                    this.content.tasks.push({
                        name: this.value,
                        completed: false
                    });
    
                    this.value = '';
                }
            },
            fetchContent() {
                const storage = JSON.parse(localStorage.getItem('v-calendar'));

                if(storage && storage.hasOwnProperty(this.storageDate)) {
                    this.content = storage[this.storageDate];
                }
                else {
                    this.content = {
                        tasks: [],
                        note: ''
                    }
                }
            },
            fetchQuote() {
                axios.get('https://api.quotable.io/random')
                    .then(response => this.quote = response.data)
                    .catch(error => console.log(error));
            },
            storeContent() {
                const storage = JSON.parse(localStorage.getItem('v-calendar')) || {};

                storage[this.storageDate] = this.content;

                localStorage.setItem('v-calendar', JSON.stringify(storage));
            },
            deleteTask(index) {
                this.content.tasks.splice(index, 1);
            },
            dayChanged(date) {
                this.now = date;
            }
        }
    }
</script>

<style scoped lang="scss">

    #v-journal {
        color: #5f5f5f;
        text-align: left;
        margin-top: 2rem;

        .v-header {
            display: flex;
            position: relative;
            justify-content: space-between;
            margin-bottom: .75rem;
            align-items: center;

            .v-header-date {
                display: flex;
                align-items: center;
                text-transform: uppercase;
                font-weight: bold;

                .v-date {
                    font-size: 2.5rem;
                    margin-right: .75rem;
                }

                .v-day {
                    font-size: 1.5rem;
                }

                .v-year {
                    text-align: justify;
                }
            }

            p {
                margin: 0;
            }
        }

        .v-page {
            padding: 1rem;
            width: 100%;
            -webkit-box-shadow: 0px 0px 6px 1px rgba(0,0,0,0.15);
            -moz-box-shadow: 0px 0px 6px 1px rgba(0,0,0,0.15);
            box-shadow: 0px 0px 6px 1px rgba(0,0,0,0.15);
            background-color: #fff;
            margin: .5rem 0;

            &.v-h-100 {
                min-height: calc(100% - 1rem);
            }

            h4 {
                margin: 0;
                border-bottom: 1px dashed #00000038;
                padding-bottom: .5rem;
                font-weight: normal;
                font-size: 1.1em;
            }
        }

        .v-tasks-list {
            display: flex;
            flex-direction: column;
            list-style-type: none;
            padding: 0;
            margin: .5rem 0 0 0;

            .v-task {
                position: relative;
                display: block;
                padding: .5rem 0;

                &.completed label {
                    text-decoration: line-through;
                    color: #D6DED4;
                }

                &:hover .v-task-delete { 
                    display: block;
                }

                .v-task-delete {
                    position: absolute;
                    display: none;
                    right: 0;
                    top: 50%;
                    transform: translate(0, -50%);
                    cursor: pointer;
                }

                .v-task-delete:after {
                    content: '\2715';
                }
            }
        }

        .v-note {
            background-color: #f8ffc4;

            textarea {
                resize: vertical;
                min-height: 10rem;
                padding: 0;
                background-color: #f8ffc4;;
                background-clip: padding-box;
                border: none;                
            }
        }

        .v-quote {
            background: url('~@/assets/sheeps.jpg') no-repeat top;

            blockquote {
                margin: 0;
                font-style: italic;

                footer:before {
                    content: '\2796';
                    margin-right: .25rem;
                } 
            }            
        }

        .row {
            display: flex;
            flex-wrap: wrap;
            margin-right: -8px;
            margin-left: -8px;

            .w-50 {
                position: relative;
                width: 100%;
                padding-right: 8px;
                padding-left: 8px;
                flex: 0 0 50%;
                max-width: 50%;
            }
        }

        .mt-3 {
            margin-top: .75rem;
        }

        .v-control {
            font-family: 'Raleway', Arial, Helvetica, sans-serif;
            display: block;
            width: 100%;
            padding: 0.3rem .6rem;
            font-weight: 400;
            line-height: 1.5;
            color: #5f5f5f;
            background-color: #fff;
            background-clip: padding-box;
            border: 1px solid #ececec;
            font-size: 1em;

            &:focus {
                outline: none;
            }
        }

        ::placeholder {
            color: #b8b6b6;
            font-style: italic;
            opacity: 1;
        }

        ::-ms-input-placeholder {
            font-style: italic;
            color: red;
        }

        ::-ms-input-placeholder {
            font-style: italic;
            color: red;
        }

        .v-custom-checkbox {
            display: block;
            position: relative;
            padding-left: 30px;
            line-height: 20px;
            cursor: pointer;
            user-select: none;

            input {
                position: absolute;
                opacity: 0;
                cursor: pointer;
                height: 0;
                width: 0;

                &:checked ~ .v-checkbox {
                    border: 1px solid #D6DED4;

                    &:after {
                        display: block;
                    }
                }
            }

            &:hover input ~ .v-checkbox {
                border-color: #cacaca;
            }

            .v-checkbox:after {
                content: '\2714';
                left: 5px;
                top: 0;
            }    
        }

        .v-checkbox {
            position: absolute;
            top: 0;
            left: 0;
            height: 20px;
            width: 20px;
            background-color: #FFF;
            border: 1px solid #ececec;
            border-radius: 50%;

            &:after {
                content: "";
                position: absolute;
                display: none;
            }
        }
    }

</style>