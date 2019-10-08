<template>
    <div class="csp-timepicker">
        <input :class="classes"
               class="csp-picker-input"
               type="text"
               :name="name"
               :value="time"
               onpaste="return false;"
               oncopy="return false;"
               onkeypress="return false;"
               @focus="isOpen = true">

        <div v-show="isOpen" class="csp-picker">
            <div class="csp-content">
                <button class="csp-btn-close" @click="isOpen = false">
                    <span class="csp-close"></span>
                </button>

                <div class="csp-hour csp-control">
                    <button @click="hour++">
                        <span class="csp-arrow csp-arrow-up"></span>
                    </button>
                    <span v-text="hour"></span>
                    <button @click="hour--">
                        <span class="csp-arrow csp-arrow-down"></span>
                    </button>
                </div>
                <span>:</span>
                <div class="csp-minute csp-control">
                    <button @click="minute+=step">
                        <span class="csp-arrow csp-arrow-up"></span>
                    </button>
                    <span>{{ minute | addPadding }}</span>
                    <button @click="minute-=step">
                        <span class="csp-arrow csp-arrow-down"></span>
                    </button>
                </div>

                <div class="csp-meridiem csp-control">
                    <button @click="toggleMeridiem">
                        <span class="csp-arrow csp-arrow-up"></span>
                    </button>
                    <span v-text="meridiem"></span>
                    <button @click="toggleMeridiem">
                        <span class="csp-arrow csp-arrow-down"></span>
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props: {
            setTime: String,
            name: String,
            classes: String,
            step: {
                type: Number,
                default: 5
            }
        },
        data () {
            return {
                isOpen: false,
                hour: 12,
                minute: 0,
                meridiem: 'PM'
            }
        },
        mounted () {
            if (this.setTime) {
                let date = this.convertTimeStringToDate(this.setTime);
                if (date instanceof Date && !isNaN(date)) {
                    let time = this.setTime.split(':');
                    let minute = time[1].split(' ');
                    this.hour = time[0];
                    this.minute = minute[0];
                    this.meridiem = minute[1];
                }
            }
        },
        filters: {
            addPadding (value) {
                return value.toString().padStart(2, '0');
            }
        },
        computed: {
            time () {
                return this.hour + ':' + this.$options.filters.addPadding(this.minute) + ' ' + this.meridiem;
            }
        },
        watch: {
            time (time) {
                this.$emit('input', time);
            },
            hour (hour) {
                if (hour > 12)
                    this.hour = 1;
                else if (hour < 1)
                    this.hour = 12;
            },
            minute (minute) {
                if (minute > 59)
                    this.minute = 0;
                else if (minute < 0)
                    this.minute = 55;
            },
            isOpen (isOpen) {
                if (isOpen) {
                    document.addEventListener('click', this.closeIfClickedOutside);
                }
            }
        },
        methods: {
            closeIfClickedOutside (event) {
                if (!event.target.closest('.csp-timepicker')) {
                    this.isOpen = false;
                }
            },
            toggleMeridiem () {
                this.meridiem = this.meridiem === 'PM' ? 'AM' : 'PM';
            },
            convertTimeStringToDate (value) {
                let time = value.split(':');
                let hour = time[0];
                let min = time[1].split(' ')[0];
                if (value.match('PM'))
                    hour = 12 + parseInt(hour, 10);
                else
                    hour = time[0] === '12' ? '00' : hour;

                let timeString = '1970-01-01T' + hour + ':' + min + ':00';
                return new Date(timeString);
            }
        }
    }
</script>

<style scoped>
    header {
        text-align: center;
    }

    .csp-timepicker {
        font-family: Helvetica, Arial, sans-serif;
        font-size: 1.1rem;
    }

    .csp-picker {
        position: absolute;
        width: 200px;
        border: 1px solid #ccc;
    }

    .csp-content {
        display: flex;
        align-items: center;
        justify-content: center;
        padding: 30px 0;
    }

    .csp-control {
        display: flex;
        flex-direction: column;
        margin: 0 4px;
        text-align: center;
    }

    .csp-control > button {
        border: 0;
        text-align: center;
        cursor: pointer;
        outline: none;
    }

    .csp-control > button:first-child {
        margin-bottom: 4px;
    }

    .csp-control > button:last-child {
        margin-top: 4px;
    }

    .csp-arrow {
        display: block;
        border-right: 5px solid #2c2c2c;
        border-bottom: 5px solid #2c2c2c;
        width: 8px;
        height: 8px;
    }

    .csp-arrow-up {
        transform: rotate(-135deg);
    }

    .csp-arrow-down {
        transform: rotate(45deg);
    }

    .csp-btn-close {
        top: 3px;
        right: 3px;
        position: absolute;
        margin: 0;
        padding: 0;
        border: 0;
        outline: none;
        cursor: pointer;
        width: 18px;
        height: 18px;
    }

    .csp-close {
        width: 14px;
        height: 14px;
        display: block;
        position: relative;
    }

    .csp-close:after {
        content: '';
        height: 12px;
        border-left: 2px solid #272727;
        position: absolute;
        transform: rotate(45deg);
    }

    .csp-close:before {
        content: '';
        height: 12px;
        border-left: 2px solid #272727;
        position: absolute;
        transform: rotate(-45deg);
    }
</style>
