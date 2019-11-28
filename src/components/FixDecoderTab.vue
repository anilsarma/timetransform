<template>
   <div  align="center">
    <div id="converter" @submit.prevent="handleDecodeSubmit">
                <br>
                <form>
                    <label>Fix Message</label><br>
                    <textarea name="fixmsg" v-model="fix.input" form="usrform" style="width:90%;">Enter text here...</textarea>
                       <br/>
                        <button>Decode</button>

                        
                    <br/>
                    
                </form>
                <table>
                        <tbody>
                             <tr v-for="entry in fix.fields" v-bind:key="entry.fieldId">
                                 <td align="right"> {{entry.field==null?"": "(" + entry.field.name + ")"}} {{entry.fieldId}}</td>
                                 <td>=</td>
                                 <td> {{entry.value}} {{entry.decodedValue==null?"":"(" + entry.decodedValue + ")"}}</td>
                                 <td> </td>
                             </tr>
                         </tbody>
                </table>
    </div>

    </div>
</template>

<script>
    import fix_data from '@/assets/fix.json'
    var regex = /([+-0-9]+)=([^|;\001]*)/g;
    var //FIELD_CHECKSUM = 10,
            BEGIN_STRING = 8
           // BODY_LENGTH = 9,
           // MSG_TYPE = 35,
            //SENDER_COMP_ID = 49,
            //TARGET_COMP_ID = 56
            ;

    export default {
        name: "timeconvertor",
        
        data() {
            return {
                fix_data: fix_data, 
                
                fix: {
                    input: '8=FIX.4.4\u00019=148\u000135=D\u000134=1080\u000149=TESTBUY1\u000152=20180920-18:14:19.508\u000156=TESTSELL1\u000111=636730640278898634\u000115=USD\u000121=2\u000138=7000\u000140=1\u000154=1\u000155=MSFT\u000160=20180920-18:14:19.492\u000110=092\u0001',
                    fields: []
                },
                
            }
        }, 
        // created () {
        //     this.timeOut();
        //     this.timer = setInterval(this.timeOut, 1000)
        // },
        beforeDestroy () {
            clearInterval(this.timer)
        },
        methods: {
            parse(fix_str) {
                var fields=[];
                var result;
                var fixVersion = 'unknown';
                 while ((result = regex.exec(fix_str))) {
                    var fieldId = parseInt(result[1]),
                    value = result[2],
                    field = fix_data.fieldById[fieldId],
                    decodedValue = field && field.values ? field.values[value] : null;

                    if (fieldId === BEGIN_STRING) {
                        fixVersion = this.parseVersionFromBeginString(value);
                    }
                    var classes = [];

                // if (this.contains(fix_data.systemFieldIds, fieldId)) {
                //     classes.push("system-field");
                // }

                if (field) {
                    if (field.isRequired) {
                        classes.push("required-field");
                    }
                    if (field.deprecatedSince <= fixVersion) {
                        classes.push("deprecated-field");
                    }
                    if (field.isHeaderField) {
                        classes.push("header-field");
                    }
                }

                fields.push({
                    fieldId: fieldId,
                    value: value,
                    field: field,
                    raw: fieldId + "=" + value + "\u0001",
                    decodedValue: decodedValue,
                    classes: classes.join(' ')
                });

                 }
                // console.log(fields)
                 this.fix.fields = fields
                return fields
            },
            handleDecodeSubmit() {
                var fields = this.parse(this.fix.input)
                return fields
            },
            parseVersionFromBeginString(str) {
                 return parseFloat(str.substr("FIX.".length));
            },
           
            clearStatus() {
                this.success = false;
                this.error = false;
            },
            invalidEpoch() {
                return this.time.epoch == "";
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