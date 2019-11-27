<template>
   <div>
       <table class="table">
           <tbody>
               <tr>
                   <td>
                       <h2>Unix Epoch</h2>
                        <p ><b class="time">Current Time: {{time.current_epoch}} seconds</b></p>
                        <p><b class="time">Current Date: {{time.current_date}}</b></p>
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
    export default {
        name: "timeconvertor",
        
        data() {
            return {
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
                this.time.current_date = date.toString();
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