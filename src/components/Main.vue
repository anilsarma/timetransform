<template>
  <div class="hello;table-responsive">
      <h1>{{msg}} </h1>
        <div class="alert alert-success alert-dismissable fade show" role="alert">
            Developer Web tool
        </div>
      <!--b-btn @click="show2=!show2;">Show Second Tab ? {{ show2 }} </b-btn-->
      <keep-alive>
          <b-card>
       <b-tabs  lazy>
            <b-tab title="Time Tools" >            
             Under Development
             <TimeConvert/>
            </b-tab>
            <b-tab title="Fix Decoder" v-if="show2" >Under Construction
                 <img alt="Under Construction" src="../assets/underconstruction.png">
                 </b-tab>
      </b-tabs>
          </b-card>
      </keep-alive>
      <img alt="Under Construction" src="../assets/underconstruction.png">
     
  </div>
  
</template>

<script>
import TimeConvert from '@/components/TimeConvert.vue'
  export default {
      props: {
        msg: String
    },
     components: {
             TimeConvert
    },
    data() {
      return {
        show2: true,
        tabIndex: 0
      }
    },
    methods: {
      linkClass(idx) {
        if (this.tabIndex === idx) {
          return ['bg-primary', 'text-light']
        } else {
          return ['bg-light', 'text-info']
        }
      }
    }
  }
</script>


<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0; 
}
li {
  display: inline-block;
  margin:  100px;
  margin-left:  100px;
  margin-right:  100px;
}
a {
  color: #42b983;
}

button {
  background: #009435;
  border: 1px solid #009435;
}
    .hello {
        margin: 100px;
    }
    .small-container {
        max-width: 680px;
    }
</style>