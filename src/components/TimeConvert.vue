<template>
   <div  align="center">
                  
       <table class="table;table-responsive">
           <tbody>
               <tr >
                   <td>
                            <p>
                                <b-btn v-if="showtimezone" @click="showtimezone=!showtimezone;">Hide TimeZone  </b-btn>
                                <b-btn v-if="!showtimezone" @click="showtimezone=!showtimezone;">Show TimeZone </b-btn>
                                &nbsp;&nbsp;&nbsp;
                                <b class="time">{{time.current_epoch}}  sec</b>
                               
                            </p>

                            <b v-if="!showtimezone" class="time">{{time.current_date.toDateString()}} {{time.current_date.toLocaleTimeString()}}</b>
                            <b v-if="showtimezone" class="time">{{time.current_date.toDateString()}} {{time.current_date.toTimeString()}}</b>
                     
                        <p  ></p>
                        <div id="converter" @submit.prevent="handleSubmit">
                                <br>
                                <h2>Epoch Time</h2>
                                <form>
                                    <label>Epoch Time</label>
                                
                                    <input v-model="time.epoch" type="text" :class="{'has-error': submitting && invalidEpoch }" ref="first" @focus="clearStatus"/>
                                      <button>Decode</button>
                                     <br/>
                                    <input type="radio" name="unit" value="seconds" v-model="time.unit" > seconds
                                    <input type="radio" name="unit" value="milliseconds" v-model="time.unit" checked> ms
                                    <input type="radio" name="unit" value="microseconds" v-model="time.unit" > us
                                    <input type="radio" name="unit" value="nanoseconds" v-model="time.unit"> ns
                                    <p v-if="error && submitting" class="error-message">
                                        !epoch not set correctly
                                        </p>
                                        <!--p v-if="success" class="success-message">
                                        all is good
                                        </p-->
                                        <br/>                            
                                </form>
                            
                                 <transition name="slide-fade" mode="out-in">
                                     <div  :key="time.date">
                                        <p><label class="time">{{getFormatedTime(time.date)}}</label></p>
                                    </div>
                                 </transition>
                            </div>
                   </td>
               </tr>
               <hr>
                <tr>
                   <td>
                         <h2>Time Since Midnight Time </h2>
                         <div id="midnight" @submit.prevent="handleMidnightSubmit">
                            <form>
                                    <label>Hour </label>
                                    <input  type="number"   width="2" v-model="midnight.hour" min="0" max="12" @focus="clearMidnightStatus"/>
                                    <label>: </label>
                                    <input  type="number"   v-model="midnight.min" min="0" max="59" @focus="clearMidnightStatus"/>
                                    <label>: </label>
                                    <input  type="number"   v-model="midnight.sec" min="0" max="59" @focus="clearMidnightStatus"/>
                                    <select v-if="midnight.hour <=12 " v-model="midnight.ampm" @focus="clearMidnightStatus">
                                        <option value="AM" >AM</option>
                                        <option value="PM">PM</option>                                
                                    </select >
                                      <br/>
                                    <input type="radio" name="unit" value="seconds" v-model="midnight.unit" > seconds
                                    <input type="radio" name="unit" value="milliseconds" v-model="midnight.unit" checked> ms
                                    <input type="radio" name="unit" value="microseconds" v-model="midnight.unit" > us
                                    <input type="radio" name="unit" value="nanoseconds" v-model="midnight   .unit"> ns
                                    <p v-if="midnight.error" class="error-message">!! {{midnight.error_msg}}</p>
                                    <br/>
                                    <!--button>Convert </button-->
                            </form>
                            <br>
                            <b  class="time">{{getTicksSinceMidnight()}} </b>
                            <b  class="time">{{getFormatedTime(getDateSinceMidnight())}} </b>
                            <br>
                            <br>
                            <form>
                                 <label >Ticks Since Midnight  </label>
                                 <input  class ="text" type="number"   v-model="midnight.ticks"  @focus="clearMidnightStatus"/>
                                   <br/>
                                    <input type="radio" name="unit" value="seconds"      v-model="midnight.unit_since" > seconds
                                    <input type="radio" name="unit" value="milliseconds" v-model="midnight.unit_since" checked> ms
                                    <input type="radio" name="unit" value="microseconds" v-model="midnight.unit_since" > us
                                    <input type="radio" name="unit" value="nanoseconds"  v-model="midnight.unit_since"> ns
                                    <p v-if="midnight.error" class="error-message">!! {{midnight.error_msg}}</p>
                                    <br/>
                            </form>
                            <br/>
                             <transition name="slide-fade" mode="out-in">
                                     <div  :key="midnight.ticks">
                                        <b  class="time">{{getMidightFormatedTimeFromTicks()}} </b>
                                     </div>
                                     <div  :key="midnight.unit_since">
                                        <b  class="time">{{getMidightFormatedTimeFromTicks()}} </b>
                                     </div>
                             </transition>
                            <!--b v-if="!showtimezone" class="time">{{midnight.date.toDateString()}} {{midnight.date.toLocaleTimeString()}}</b>
                            <b v-if="showtimezone" class="time">{{midnight.date.toDateString()}} {{midnight.date.toTimeString()}}</b-->
                          </div>
                   </td>
               </tr>
                <hr>
               <tr>
                   <td>
                         <h2>KDB Time - Work in progress</h2>
                   </td>
               </tr>
           </tbody>
       </table>
    </div>
</template>

<script>
    var debug = false; 
    export default {
        name: "timeconvertor",
        
        data() {
            return {
                showtimezone: false,
                submitting: false,
                error:false,
                success: false,
                list: [],
                timer: '',
                time: {
                    epoch: new Date().getTime(),
                    date: new Date(),
                    unit: 'milliseconds',
                  
                    current_epoch: 0,
                    current_date: ''
                },
                midnight: {
                    hour: new Date().getHours(),
                    min: new Date().getMinutes(),
                    sec: 0,
                    date: new Date(),
                    unit: "seconds",
                    unit_since: 'seconds',
                    ampm: new Date().getHours()>11?"PM":"AM",
                    error: false,
                    error_msg:"some thing",
                    ticks: 0
                   
                }
            }
        }, 
        created () {
            this.timeOut();
            this.timer = setInterval(this.timeOut, 1000)
        },
        beforeDestroy () {
            clearInterval(this.timer)
        },
        methods: {
            timeOut() {
                var date = new Date();
                
                this.time.current_date = date;
                this.time.current_epoch =parseInt(date.getTime()/1000);
                // this.$http.get('events', (events) => {
                //     this.list = events;
                // }).bind(this);
            },
            
            handleSubmit() {
               
                this.submitting = true;
                this.clearStatus();
                if(this.invalidEpoch() ) {
                    this.error = true;
                    return;
                }
                var t = this.time.epoch // convert to milliseconds
                if( this.time.unit == "seconds") {
                    t = this.time.epoch*1000;
                } else if( this.time.unit == "microseconds") {
                    t = this.time.epoch/1000;
                } else if( this.time.unit == "nanoseconds") {
                    t = this.time.epoch/1000000;
                }
                this.time.date = new Date(parseInt(t))
                //this.$emit("add:convert", this.epoch);
                this.$refs.first.focus()
                //this.time  = { epoch: 0} // clear
                this.error = false;
                this.success = true;
                this.submitting = false;
            }, 
            clearStatus() {
                this.success = false;
                this.error = false;
            },
            invalidEpoch() {
                return this.time.epoch == "";
            },
        

            handleMidnightSubmit() {
                var date = new Date();
                date.setHours(this.midnight.hour + this.getAdjustMent(this.midnight.hour, this.midnight.ampm))
                //date.setHours(this.midnight.hour + 12)
                date.setMinutes(this.midnight.min)
                date.setSeconds(this.midnight.sec)
                date.setMilliseconds(0)
                
                this.midnight.date = date
            },
            getAdjustMent(hr, ampm) {
                if(hr>12) {
                    return 0;
                }
                if(ampm==="PM") {
                    if(hr!=12) {
                        return 12;
                    }
                    return 0;
                }
                return 0;
            },
            getDateSinceMidnight() {
                var date = new Date();
                date.setHours(parseInt(this.midnight.hour) + this.getAdjustMent(this.midnight.hour, this.midnight.ampm))
                //date.setHours(this.midnight.hour + 12)
                date.setMinutes(this.midnight.min)
                date.setSeconds(this.midnight.sec)
                date.setMilliseconds(0)
                
                //this.midnight.date = date
                return date
            
            },
            getTicksSinceMidnight() {
                var hr = parseInt(this.midnight.hour)
                var min = parseInt(this.midnight.min)
                var sec  = parseInt(this.midnight.sec)
                if(hr>23) {
                    this.midnight_error("error hour cannot more than 23");
                    return -1;
                }
                 if(min>59) {
                    this.midnight_error("error minutues cannot more than 59");
                    return -1;
                }
                if(sec>59) {
                    this.midnight_error("error seconds cannot more than 59");
                    return -1;
                }
                this.clearMidnightStatus()
                hr = hr +  this.getAdjustMent(hr, this.midnight.ampm)
                var total_sec =  hr * 60* 60  + min*60 + sec;
                if(this.midnight.unit == "milliseconds") {
                    total_sec *= 1000;
                } else if(this.midnight.unit == "microseconds") {
                    total_sec *= 1000000;
                } else if(this.midnight.unit == "nanoseconds") {
                    total_sec *= 1000000000;
                }
                var s=  this.midnight.hour + "=>" +  hr + ":" + min + ":" + sec + " ";
                return debug? s + " " + total_sec:total_sec;
            
            }, 
            getFormatedTime(date) {
                //var date = this.getDateSinceMidnight();
                var s = date.toDateString() + " "
                
                s += this.showtimezone?date.toTimeString():date.toLocaleTimeString()
                return s
            
            },
            getMidightFormatedTimeFromTicks() {
                var millis = parseInt(this.midnight.ticks)
                if(this.midnight.unit_since==="seconds") {
                    millis = millis*1000;
                } else  if(this.midnight.unit_since==="microseconds") {
                    millis = millis/1000;
                } else  if(this.midnight.unit_since==="nanoseconds") {
                    millis = millis/1000000;
                }
                var millis_num = Math.floor(millis%1000)
                var sec_num = Math.floor(millis/1000)
                var hours   = Math.floor(sec_num / 3600);
                var minutes = Math.floor((sec_num - (hours * 3600)) / 60);
                var seconds = sec_num - (hours * 3600) - (minutes * 60);

                var date = new Date();
                date.setHours(hours)
                date.setMinutes(minutes)
                date.setSeconds(seconds);
                date.setMilliseconds(millis_num)

                if (hours   < 10) {hours   = "0"+hours;}
                if (minutes < 10) {minutes = "0"+minutes;}
                if (seconds < 10) {seconds = "0"+seconds;}
               
                return debug?(hours+':'+minutes+':'+seconds + " " + date.toLocaleString()) :date.toLocaleString();

              
            },
            clearMidnightStatus() {
                this.midnight.error = false;
                this.midnight.error_msg=""
            },

            midnight_error(msg) {
                this.midnight.error = true
                this.midnight.error_msg = msg
            }

        
        },

    }
</script>
<style>

.time {
  padding: 8px;
  border: 1px solid #7d817f;
   border-radius: 5px;
   background: #d0cbae;
}

.text, label {
  padding: 8px;
  padding-right: 12px;
  /*border: 1px solid #7d817f;*/
   border-radius: 5px;
  /* background: #d0cbae;*/
}

.round {
     border-radius: 25px;
  background: #8080ff;
  padding: 20px; 
  width: 200px;
  height: 300px;  
}
.error-message {
    color: #9c203b;
}

.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active for <2.1.8 */ {
  transform: translateX(10px);
  opacity: 0;
}
</style>