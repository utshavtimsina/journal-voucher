<template>
  <div class="home">
<v-layout row>
  <v-flex md6>
  </v-flex>
   <v-flex md4>

  </v-flex>
  <v-flex md2>
    <v-menu >
     <template v-slot:activator="{on}" >
        <v-text-field
          v-on="on"   label="Date" :value="dateFormatted" 

        >
        </v-text-field>
      </template>
      <v-card>
        <v-date-picker v-model="date" landscape></v-date-picker>
      </v-card>
      </v-menu>
  </v-flex>
  
</v-layout>
    <v-layout row>
      <v-flex md2>
       
      </v-flex>
      <v-flex md6>
        <v-card >
          
          <v-simple-table>
            <template v-slot:default>
              <thead>
                <tr >
                  <th v-for="(item,index) in headers" :key="index" >{{item.text}}</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(i,keys) in totalrows" :key="keys">
                <td  v-for="(item,key) in headers" :key="key"> 
                  <div  v-if="userSelects[keys]" >
                    
                         <v-text-field v-if="userSelects[keys][item.text]" v-model="userSelects[keys][item.text]" ref="bodyelement" name="bodyelement" @keydown.f2="dialogue(item.text,key,keys)" @keydown.enter="newTab(key,keys)"></v-text-field>
                      <v-text-field  v-else :label="nullvaluegiver"  ref="bodyelement" name="bodyelement" @keydown.f2="dialogue(item.text,key,keys)" @keydown.enter="newTab(key,keys)"></v-text-field>
                  </div>
                 <div  v-else>
                     <v-text-field  :label="nullvaluegiver" ref="bodyelement"  @keydown.f2="dialogue(item.text,key,keys)"  @keydown.enter="newTab(key,keys)"> </v-text-field>
                 </div>
                 
                </td>
                </tr>
              </tbody>
            </template>
           </v-simple-table>
        </v-card>
      </v-flex>
       
    </v-layout>
    <v-navigation-drawer app v-model="dialog" right temporary>
      <v-card>
         <v-card-title>
            
            <v-spacer></v-spacer>
           <v-text-field
             v-model="search"
            append-icon="mdi-search-web"
            :label="selectedValue"
            
             single-line
             ref="searchbox"
             @keydown.down="downbuttonClicked"
              @keyup.down="updateClickedValue"
              @keyup.up="updateClickedValue"
             @keydown.up="upbuttonClicked"
             @keydown.enter="enterbuttonClicked"
            hide-details>
            </v-text-field>
          </v-card-title>
         <v-card-text>
           <v-list>
             <v-list-item-group >
               <v-list-item  v-for="(item,index) in searchContent" :key="index" :class='{"active-item": currentItem === index}' > 
                 <v-list-item-content>
                    <v-list-item-title dark >
                      <span v-if="currentItem === index" ref='searchBoxes' >{{item.text}}</span>
                       <span v-else >{{item.text}}</span>
                    </v-list-item-title>
                  </v-list-item-content>
               </v-list-item>
             </v-list-item-group>
           </v-list>
         </v-card-text>
         
       </v-card>
       
    </v-navigation-drawer>
      
           
  </div>
</template>

<script>
// @ is an alias to /src
 //import { ModelSelect } from 'vue-search-select'
export default {
  name: 'home',
  
  data(){
    return{
      date:"",
      totalrows:1,
      dialog:false,
      search:'',
      mycurrentindex:'',
      selectedrow:'',
      userSelects:new Object(),
      currentItem: 0,
      
      nullvaluegiver:'',
      selectedValue:"",
      inputkeypair:'',
      dateFormatted:'',
      dataItems:[
        {
          "Dr/Cr":[
            {
              text:"Dr"
             },
             {
              text:"Cr"
            }
            ],
            "SNO":[
            {
              text:"1"
             },
             {
              text:"2"
            }
            ],
            "Donor":[
            {
              text:"Dr"
             },
             {
              text:"Cr"
            }
            ],
            "GL Code":[
            {
              text:"1"
             },
             {
              text:"2"
            }
            ],
        },
      ],
     searchContent:[],
      headers: [
          {
            text: 'SNO',
            value:'sno',
            
          },
          {
             text: 'Dr/Cr',
             value:'dr/cr',
           },
          { text: 'Donor',
          value:'donor',

          },
          { text: 'GL Code',
            value:'gl code',
          },
          { 
            text: 'SL Code',
            value:'sl code',
          },
          { 
            text: 'TAC 1',
            value:'tac',
             },
          {
            text:'Activity',
            value:'activity',
          },
          {
            text:'Sub Activity',
            value:'sub activity',
          },
          {
            text:'Debit',
            value:'debit',
          },
          {
            text:'Credit',
            value:'credit',
          }
        ],
    }
  },
  
  created:{
       getDate(){
         this.dateFormatted = this.formatDate(new Date());
        
       },
       
      
    },
   
  watch: {
      date () {
       this.dateFormatted = this.formatDate(this.date)
      },
      search (){
        /* if(this.search.length === 0){
         this.searchContent =this.dataItems[0][this.inputkeypair];
         //console.log(this.dataItems[0].SNO);
        }else{ */
          this.searchContent =this.dataItems[0][this.inputkeypair].filter(items =>{
            return items.text.toLowerCase().includes(this.search.toLowerCase());
          });
       // }
      },
    
     
    },
    methods:{
      newTab(column,row){
        //alert(column+"cols rows"+row);
        if(column+1 === this.headers.length && this.totalrows ==row+1 ){
          this.totalrows++;
        }else{
        let focusbe =row*this.headers.length+column+1;
        this.$refs.bodyelement[focusbe].focus();
        }

      },
      dialogue: function(key,index,row){
       
       this.selectedrow =row;
       this.inputkeypair =key;
       
        this.searchContent =this.dataItems[0][this.inputkeypair];
        this.selectedValue = this.dataItems[0][this.inputkeypair][0].text;
        this.dialog =true;
        this.mycurrentindex = index;
        //this.nullvaluegiver='no val';
        this.$refs.searchbox.focus();
        //console.log(this.$refs.searchbox);

      },
      updateClickedValue(){
         this.selectedValue = this.$refs.searchBoxes[0].innerText;
      },
      enterbuttonClicked(){
        this.selectedValue = this.$refs.searchBoxes[0].innerText;
        this.search = this.selectedValue;
        this.nullvaluegiver+='\n';
        
        let result =this.search;
        let mystorevariable = new Object();
       
       
        if(this.userSelects[this.selectedrow]){
          let objselector = this.userSelects[this.selectedrow];
            mystorevariable = objselector;
        }
        mystorevariable[this.inputkeypair]=result;
        this.userSelects[this.selectedrow] =mystorevariable;
        this.dialog =false;
        let focusbe = this.selectedrow*this.headers.length+this.mycurrentindex;
        this.$refs.bodyelement[focusbe].focus();
        //console.log( this.$refs.bodyelement);
      },
      newfocus: function(){
       
      },
      downbuttonClicked(){
        //alert('');
        
        if(this.currentItem<=this.search.length){
          this.currentItem++;
           
        }
      },
      upbuttonClicked(){
        
        if(this.currentItem!=0){
          this.currentItem--;
        }
      },
      formatDate (date) {
        if (!date) return null

        const [year, month, day] = date.split('-')
        return `${day}/${month}/${year}`
      },
    }
}
</script>
<style  scoped>
.active-item {
  background-color: red;
}
</style>