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
      <v-spacer></v-spacer>
      <v-flex md10> <!-- table begins here-->
        <v-card >
          
          <v-simple-table >
            <template v-slot:default>
              <thead>
                <tr >
                  <th v-for="(item,index) in headers" :key="index" >{{item.text}}</th>
                </tr>
              </thead>
              <tbody v-show="editMode" >
                <tr v-for="(i,keys) in totalrows" :key="keys">
                <td  v-for="(item,key) in headers" :key="key"> 
                  <div  v-if="userSelects[keys]" >
                    
                         <v-text-field v-if="userSelects[keys][item.text]" v-model="userSelects[keys][item.text]" ref="bodyelement" name="bodyelement"   @keydown.f2="dialogue(item.text,key,keys)" @keydown.enter="newTab(key,keys)"></v-text-field>
                      <v-text-field  v-else :label="nullvaluegiver"  ref="bodyelement" name="bodyelement" @keydown.f2="dialogue(item.text,key,keys)" @keydown.enter="newTab(key,keys)"></v-text-field>
                  </div>
                 <div  v-else >
                     <v-text-field  :label="nullvaluegiver" ref="bodyelement"  @keydown.f2="dialogue(item.text,key,keys)" @keydown.enter="newTab(key,keys)"> </v-text-field>
                 </div>
                 
                </td>
                </tr>
              </tbody>
              <tbody v-show="editMode===false" >
                  <tr v-for="(i,keys) in totalrows" :key="keys"  class="pa-2 ma-2">
                    <td  v-for="(item,key) in headers" :key="key" :class="{'active-row-item':tablerowselector === keys}" > 
                      <div  v-if="userSelects[keys]" >
                        <v-text-field v-if="userSelects[keys][item.text]" disabled v-model="userSelects[keys][item.text]" ></v-text-field>
                        <v-text-field  v-else disabled :label="nullvaluegiver" ></v-text-field>
                      </div>
                      <div v-else>
                       <v-text-field  disabled :label="nullvaluegiver" ></v-text-field>
                      </div>
                      
                    </td>
                  </tr>
              </tbody>
            </template>
           </v-simple-table>
        </v-card>
      </v-flex>
       <v-spacer> </v-spacer>
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
      editMode:false,
      totalrows:1,
      deletedList:[],
      dialog:false,
      search:'',
      mycurrentindex:'',
      selectedrow:'',
      userSelects:new Object(),
      currentItem: 0,
      tablerowselector:0,
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
  

    mounted() {
       document.addEventListener('keydown', this.escapeKeyListener); 
          
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
      escapeKeyListener(evt){
          if (evt.keyCode === 38) {
            if(this.editMode ==false){
              if(this.tablerowselector!=0){
                this.tablerowselector--;
              }
            }
               
             // alert("keypre");
            }
            else if (evt.keyCode === 40) {
              if(this.editMode ==false){
                if(this.tablerowselector+1 !== this.totalrows){
                  this.tablerowselector++;
                }
              }
               
            }else if(evt.keyCode ===13){
             if(this.editMode ==false){
               this.editMode=true;
               this.setFocus(this.tablerowselector);
              
             }
              
                //this.$refs.bodyelement[10].focus();
                
              //let focusto= this.tablerowselector*this.headers.length;
              
            }else if(evt.keyCode ===27){
              this.editMode=false;
            }else if(evt.keyCode ===46){
              //alert("del pressed");
               if(this.editMode ==false){
                  this.deleteCurrentRow(this.tablerowselector);
                }
            }
       
      },
      setFocus(row){
         let focusbe = row * this.headers.length;
         this.$nextTick(function(){
          this.$refs.bodyelement[focusbe].focus();
        });
          
         // this.nullvaluegiver+="\n";
      },
      
      newTab(column,row){
        //alert(column+"cols rows"+row);
        if(column+1 === this.headers.length && this.totalrows ==row+1 ){
          this.totalrows++;
        }else{
        let focusbe =row*this.headers.length+column+1;
        this.$refs.bodyelement[focusbe].focus();
        }

      },
      deleteCurrentRow(row){
        let isDeleted =false;
        let focusbe
        for (var i=0;i<=this.totalrows; i++) { 
          
          if(isDeleted == true && this.userSelects[i]){
            this.userSelects[i-1] = this.userSelects[i];
            delete this.userSelects[i];
          }
          if(i ==row){
           delete this.userSelects[i];
           if(i!=0){
              
               if(row+1 === this.totalrows){
                 focusbe  = (row-1)*this.headers.length;
               }else{
                 focusbe  = (row)*this.headers.length;
               }
             this.totalrows --;
            
           }else{
              if(this.totalrows !=1){
               this.totalrows --;
             }
             focusbe=0;
           }
           this.$refs.bodyelement[focusbe].focus();
            this.nullvaluegiver+="\n";
             isDeleted =true;
            
          }
         
      }  
     
        console.log(row);
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
.active-row-item{
  background-color: blue;
}
</style>