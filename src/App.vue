<template>
    <div class="app">
        <div class="help">
            <div class="help-item t-meeting"><u>M</u>eeting</div>
            <div class="help-item t-break"><u>B</u>reak</div>
            <div class="help-item t-lunch"><u>L</u>unch</div>
            <div class="help-item t-work"><u>W</u>ork</div>
            <div class="help-item t-social"><u>C</u>omms</div>
            <div class="help-item t-support"><u>S</u>upport</div>
            <div class="help-item t-event"><u>ESC</u>:Start/stop</div>
            <div class="help-item t-event"><u>BSP</u>:Delete</div>
        </div>
        <hr>
        <div class="top-bar">
            <div class="time">{{time}}</div>
        </div>
        <div class="top-bar">
            <div class="small-time" v-if="events.length > 0">{{events[events.length-1]['time_f']}}</div>
            <div class="small-time">{{totalTime_f}}</div>
        </div>
        <hr>
        <div class="event-table">
            <div class="event" v-bind:class="event.class" v-for="event in events">
                <div class="event-col">[{{event.start}}]</div>
                <div class="event-col">{{event.name}}</div>
                <div class="event-elapsed">{{event.time_f}}</div>
            </div>
        </div>
    </div>
</template>

<script>
import date from 'date-and-time';
export default {
    data() {
        return {
            'test': 'Hello world!',
            'time': 0,
            'eventCount': 0,
            'totalTime': 0,
            'totalTime_f': '_',
            'running': false,
            'events': []
        }
    },
    methods: {
        addEvent(eventName, className) {
            var self = this;
            self.events.push({
                'start': date.format(new Date(), 'hh:mm'),
                'name': eventName,
                'class': className,
                'time': 0,
                'time_f': ''
            });
            self.running = true;
        },
        HMSfromSecs(seconds) {
            var self = this;
            var t = self.conv(seconds);
            if (t.hours < 10) t.hours = '0' + t.hours;
            if (t.minutes < 10) t.minutes = '0' + t.minutes;
            if (t.seconds < 10) t.seconds = '0' + t.seconds;
            return t.hours + ":" + t.minutes + ":" + t.seconds;
        }
    },
    mounted() {
        var self = this;
        if(self.events.length == 0) {
            self.addEvent('START', 't-event');
            self.running = true;
        }
        self.conv = require('convert-seconds');
        var curTime = setInterval(() => {
            const now = new Date();
            self.time = date.format(now, 'hh:mm:ss');
            if(self.running === true) {
                self.totalTime++;
                self.totalTime_f = self.HMSfromSecs(self.totalTime);
            }
            self.events[self.events.length-1]['time']++;
            self.events[self.events.length-1]['time_f'] = self.HMSfromSecs(self.events[self.events.length-1]['time']);
        }, 1000);

        self.$nextTick(function() {
            window.addEventListener('keydown', function(e) {
                if(e.key == "m") { self.addEvent('Meetings', 't-meeting')}
                if(e.key == "b") { self.addEvent('Break / Distractions', 't-break')}
                if(e.key == "l") { 
                    self.addEvent('Lunchtime', 't-lunch')
                    self.running = false;
                }                
                if(e.key == "w") { self.addEvent('Work / Code', 't-work')}
                if(e.key == "c") { self.addEvent('Social / Comms', 't-social')}
                if(e.key == "s") { self.addEvent('Support / Firefighting', 't-support')}
                if(e.key == "Escape" ) { 
                    e.preventDefault();
                    if(self.running) {
                        self.addEvent('STOP', 't-event');
                        self.running = false;
                    } else {
                        self.running = true;
                        self.addEvent('START', 't-event');
                    }
                }
                if(e.key == "Backspace") {
                    e.preventDefault();
                    self.running = false;
                    self.totalTime = 0;
                    self.events = [];
                    self.addEvent('START', 't-event');
                    self.running = true;
                }
            });
        });
    }
}
</script>

<style>
.top-bar {
    display: flex;
}
.help {
    display: flex;
    flex-wrap: wrap;
}
.help-item {
    padding-right: 2em;
}
body {
    background: #000;
    color: #0dd;
    font-family: 'braciola';
}

.time {
    margin: 0 auto;
    font-size: 4em;
    display: block;
}

.small-time {
    margin: 0 auto;
    font-size: 2em;
    display: block;
}

.event {
    display: flex;
}

.event-col {
    padding-right: 3em;
}

.event-elapsed {
    margin-left: auto;
}

.t-meeting {color: #f39}
.t-break {color: #f85}
.t-lunch {color: #fd0}
.t-work {color: #9d0}
.t-social {color: #b8f}
.t-event {color: #fff}
.t-support {color: #f11}

@font-face {
    font-family: 'braciola';
    src: url('braciola.ttf');
}
</style>
