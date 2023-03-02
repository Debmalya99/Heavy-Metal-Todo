<template>
    <div class="card card-body timer">
        
        <h4>Pomodoro Timer</h4>
        
        <!-- This will be the pomodoro timer in the future -->
        <div v-if="timerOver" class="card-body" id="cardbody">
            <!-- {{timeDist}} -->
            {{displayMinutes}}:{{displaySeconds}}
            
            <svg viewBox="0 0 70 70" xmlns="http://www.w3.org/2000/svg">
                <linearGradient id="gradient-fill" x1="0" y1="0" x2="800" y2="0" gradientUnits="userSpaceOnUse">
						
                        <stop offset="0" stop-color="#5200f3" />
                    
                        <stop offset="0.14285714285714285" stop-color="#d100ca" />
                    
                        <!-- <stop offset="0.2857142857142857" stop-color="#ff009a" />
                    
                        <stop offset="0.42857142857142855" stop-color="#ff006c" />
                    
                        <stop offset="0.5714285714285714" stop-color="#ff4944" />
                    
                        <stop offset="0.7142857142857142" stop-color="#ff8e1e" /> -->
                    
                        <stop offset="0.14285714285714285" stop-color="#ffc207" />
                    
                        <stop offset="1" stop-color="#ffee3c" />
                    
                </linearGradient>
                 <circle cx="35" cy="35" r="30" :style="circleStyle" stroke-linecap="round"/>
            </svg>
            
        </div>
        <div v-else class="card-body">Timer not running. Press the button to start the timer</div>
        <button v-if="timerOver" class="btn btn-primary" @click="processTimerStop()">Stop Timer</button>
        <button v-else class="btn btn-primary" @click="processTimerStart()">Start Timer</button>
    </div>
</template>

<script>
import alarm_audio_src from '../assets/alarm-clock-short-6402.mp3';


    export default {
        name:'TimerCard',
        components:'',
        data(){
            return {
                // timeDist:0,
                displayMinutes: 0,
                displaySeconds: 0,
                targetTime: new Date(),
                timerDuration: 25,
                timerOver: false,
                circleStyle:{
                    'fill':'none',
                    // 'stroke':'rgb(240,17,115)',
                    'stroke':'url(#gradient-fill)',
                    // 'background':'linear-gradient(126deg, rgba(240,17,115,1) 0%, rgba(9,49,121,1) 35%, rgba(0,212,255,1) 100%)',
                    'stroke-width':5,
                    'stroke-dasharray':189,
                    'stroke-dashoffset':0,
                    
                },
                alarm_audio:new Audio(alarm_audio_src),
                document_title:document.title,
            }
        },
        mounted(){
            // this.processTimerReset()
            this.alarm_audio.loop = true;
        },
        computed: {
            _seconds: () => 1000,
            _minutes: () => {
                return this._seconds * 60;
            },

        },
        methods:{
            doCountDown(){
                const timer = setInterval(()=>{
                    const startTime = new Date();
                    let distance = this.targetTime.getTime() - startTime.getTime();
                    if(distance <= 0){
                        this.timerOver = true;
                        clearInterval(timer);
                        
                        this.alarm_audio.play();
                        this.document_title = document.title;
                        document.title = "Timer over";
                        
                        return;
                    }

                    this.displayMinutes = Math.floor(distance/60000);
                    this.displaySeconds = Math.floor((distance/60000 - this.displayMinutes)*60);
                    
                    // The following two lines are for displaying the correct format of time
                    this.displayMinutes = this.displayMinutes < 10 ? "0" + this.displayMinutes : this.displayMinutes;
                    this.displaySeconds = this.displaySeconds < 10 ? "0" + this.displaySeconds : this.displaySeconds;
                    // this.timeDist = distance;
                    let v = (distance/60000)/this.timerDuration;
                    this.circleStyle['stroke-dashoffset'] = (189 - v*189);
                    // console.log(v)
                },0.2)
            },
            processTimerStop(){
                this.timerOver = !this.timerOver
                // this.targetTime = new Date()
                // this.targetTime.setMinutes(this.targetTime.getMinutes()+this.timerDuration);
                // this.doCountDown();
                this.alarm_audio.pause();
                document.title = this.document_title;
            },
            processTimerStart(){
                this.timerOver = !this.timerOver;
                this.targetTime = new Date()
                this.targetTime.setMinutes(this.targetTime.getMinutes()+this.timerDuration);
                this.doCountDown();
            }
        }
    }

    
</script>

<style>
.timer{
    box-shadow: 13px 29px 11px -25px rgba(0,0,0,0.75);

}
svg{
    position: absolute;
    top: 200;
    left: 0;
}

#cardbody{
    height: 450px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size:xx-large;
}
</style>