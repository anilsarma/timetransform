<template>
   <div>
       <table class="table;table-responsive">
           <tbody>
               <tr>
                   <td>
                       <h2>Unix Epoch</h2>
                         <b-btn v-if="showtimezone" @click="showtimezone=!showtimezone;">Hide TimeZone  </b-btn>
                         <b-btn v-if="!showtimezone" @click="showtimezone=!showtimezone;">Show TimeZone </b-btn>
                         <p/>
                        <p ><b class="time">Now: {{time.current_epoch}} (s)</b></p>
                        <p>
                            <b v-if="!showtimezone" class="time">{{time.current_date.toDateString()}} {{time.current_date.toLocaleTimeString()}}</b>
                            <b v-if="showtimezone" class="time">{{time.current_date.toDateString()}} {{time.current_date.toTimeString()}}</b>
                        </p>
                        <p  ></p>
                        <div id="converter" @submit.prevent="handleSubmit">
                                <form>
                                    <label>Epoch Time</label>
                                    <input v-model="time.epoch" type="text" :class="{'has-error': submitting && invalidEpoch }" ref="first" @focus="clearStatus"/>
                                    <p v-if="error && submitting" class="error-message">
                                        !epoch not set correctly
                                        </p>
                                        <!--p v-if="success" class="success-message">
                                        all is good
                                        </p-->
                                        <br/>
                                        <button>Decode</button>
                                </form>
                                <label>{{time.date}}</label>
                            </div>
                   </td>
               </tr>
                <tr>
                   <td>
                         <h2>Epoch Time Since Midnight Time - Work in progress </h2>
                   </td>
               </tr>
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
    var  d = new Date();
    d.toLocaleTimeString
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
                    date: new Date().toString(),
                    unit: 'seconds',
                    current_epoch: 0,
                    current_date: ''
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

    
                this.time.date = new Date(parseInt(this.time.epoch))
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
            }
        }
    }
</script>
<style>
.time {
  padding: 8px;
  border: 1px solid #7d817f;
}
</style>