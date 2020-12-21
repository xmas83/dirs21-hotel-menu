<template>
  <v-container>
    <v-data-table
            :headers="headers"
            :items="dishesData"
            sort-by="calories"
            class="elevation-1"
            style="border:2px solid antiquewhite;"
    >
      <template v-slot:top>
        <v-toolbar
                color="#363654"
                class="white--text"
                flat
        >
          <v-toolbar-title>Dishes</v-toolbar-title>
          <v-divider
                  class="mx-4"
                  inset
                  vertical
          ></v-divider>
          <v-spacer></v-spacer>
          <v-dialog
                  v-model="dialog"
                  max-width="800px"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                      color="white"
                      class="mb-2 ml-2 #363654--text"
                      @click="resetAll"
              >
                Reset
              </v-btn>
              <v-btn
                      color="white"
                      class="mb-2 #363654--text"
                      v-bind="attrs"
                      v-on="on"
              >
                <v-icon left>
                  mdi-plus
                </v-icon>
                Add New Dish
              </v-btn>
            </template>
            <v-card>
              <v-card-title class="blue-grey #363654--text">
                <span class="headline">Add Dish</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-form
                          ref="form"
                          v-model="valid"
                          lazy-validation
                  >
                    <v-row>
                      <v-col
                              cols="12"
                              sm="12"
                              md="12"
                      >
                        <v-text-field
                                v-model="currentItem.name"
                                outlined dense
                                color="red"
                                :rules="[v => !!v || 'Name is required']"
                                label="Dish name"
                        ></v-text-field>
                      </v-col>
                      <v-col
                              cols="12"
                              sm="12"
                              md="12"
                      >
                        <v-text-field
                                v-model="currentItem.description"
                                outlined dense
                                color="blue-grey"
                                label="Short Description"
                        ></v-text-field>
                      </v-col>
                      <v-col
                              cols="12"
                              sm="6"
                              md="4"
                      >
                        <v-text-field
                                v-model="currentItem.price"
                                outlined dense
                                color="pink"
                                :rules="[v => !!v || 'Price is required',v => (/^[0-9]*$/).test(v) || 'Must be in digits']"
                                label="Price ($)"
                        ></v-text-field>
                      </v-col>
                      <v-col
                              cols="12"
                              sm="6"
                              md="4"
                      >
                        <v-select
                                :items="categories"
                                outlined dense
                                color="pink"
                                v-model="currentItem.category"
                                :rules="[v => !!v || 'Category is required']"
                                label="Category"
                        ></v-select>
                      </v-col>
                      <v-col
                              cols="12"
                              sm="6"
                              md="4"
                      >
                        <v-select
                                :items="availability"
                                outlined dense
                                color="pink"
                                v-model="currentItem.availability"
                                :rules="[v => !!v || 'Availability is required']"
                                label="When it available"
                        ></v-select>
                      </v-col>
                      <v-col
                              cols="12"
                              sm="6"
                              md="6"
                      >
                        <v-switch
                                v-model="currentItem.soldout"
                                outlined dense
                                color="blue-grey"
                                label="Sold Out"
                        ></v-switch>
                      </v-col>
                      <v-col
                              cols="12"
                              sm="6"
                              md="6"
                      >
                        <v-text-field
                                v-model="currentItem.timetowait"
                                outlined dense
                                color="#363654"
                                :rules="[v => !!v || 'Waiting time is required', v => (/^[0-9]*$/).test(v) || 'Must be in digits']"
                                label="Waiting Time (Minutes)"
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-form>
                </v-container>
              </v-card-text>
              <v-card-actions class="justify-center">
                <v-btn
                        color="blue-grey"
                        outlined
                        @click="close"
                >
                  Cancel
                </v-btn>
                <v-btn
                        color="blue-grey"
                        dark
                        @click="save"
                >
                  Save
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="headline">Are you sure you want to delete this item?</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="#363654" outlined @click="dialogDelete = false">Cancel</v-btn>
                <v-btn color="#363654" dark @click="deleteItemConfirm">OK</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="responseMessageDialog" max-width="500px">
            <v-card>
              <v-card-title class="headline justify-center">{{responseMessage}}</v-card-title>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.name="{ item }">
        <v-chip
                class="title"
                color="#363654"
                label
                text-color="white"
        >
          {{item.name}}
        </v-chip>
      </template>
      <template v-slot:item.price="{ item }">
        <span>
          {{item.price}} $
        </span>
      </template>
      <template v-slot:item.timetowait="{ item }">
        <span>
          {{item.timetowait}} min
        </span>
      </template>
      <template v-slot:item.soldout="{ item }">
        <v-chip
                color="red"
                v-if="item.soldout"
                dark
        >
          Sold Out
        </v-chip>
        <v-chip
                color="green"
                v-else
                dark
        >
          Available
        </v-chip>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon
                class="mr-2"
                color="#363654"
                @click="editItem(item)"
        >
          mdi-pencil
        </v-icon>
        <v-icon
                color="#363654"
                @click="deleteItem(item)"
        >
          mdi-delete
        </v-icon>
      </template>
    </v-data-table>
  </v-container>
</template>


<script>
  import axios from 'axios';

  export default {
    name: 'Dishes',
    data: () => ({
      dialog: false,
      dialogDelete: false,
      headers: [
        {
          text: 'Name',
          align: 'start',
          sortable: false,
          value: 'name',
        },
        { text: 'Price ($)', value: 'price' },
        { text: 'Description', value: 'description' },
        { text: 'Category', value: 'category' },
        { text: 'Availability', value: 'availability' },
        { text: 'Waiting Time(Minutes)', value: 'timetowait' },
        { text: 'Sold Out/Available', value: 'soldout' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      categories: [
        {'text':'Starter','value':'Starter'},
        {'text':'Main Course','value':'Main Course'},
        {'text':'Dessert','value':'Dessert'},
        {'text':'Beverage','value':'Beverage'}
      ],
      availability:[
        {'text':'Breakfast','value':'Breakfast'},
        {'text':'Dinner','value':'Dinner'},
        {'text':'Lunch','value':'Lunch'},
        {'text':'Weekdays','value':'Weekdays'},
        {'text':'Weekends','value':'Weekends'},
      ],
      editedIndex: -1,
      currentItem: {
        name: '',
        description: '',
        price: '',
        availability: '',
        soldout: false,
        timetowait: ''
      },
      dishesData:[],
      selectedDeleteItem : null,
      valid:true
    }),
    async created(){
      // To get data on initial load
      this.fetchData();
    },
    methods:{
      // Get data from api and render it in table
      async fetchData(){
        let resData = await axios.get('http://localhost:9000/dishes');
        this.dishesData = resData.data.data;
      },
      // Save new or edited data to storage
      async save(){
        // Validate few validation rules before save
        if(this.$refs.form.validate()){
          await axios.put('http://localhost:9000/dishes',this.currentItem);
          // After save or edit reload table
          this.fetchData();
          this.dialog = false;
          // Empty all fields
          this.currentItem = {
            name: '',
            description: '',
            price: '',
            availability: '',
            soldout: false,
            timetowait: ''
          }
        }
      },
      // To open edit box and assign item to current edit item
      async editItem(item){
        this.currentItem = item;
        this.dialog = true;
      },
      // To open delete confirmation box and assign item to selectedDeleteItem
      deleteItem(item){
        this.dialogDelete = true;
        this.selectedDeleteItem = item;
      },
      // Delete item after confirmation
      async deleteItemConfirm(){
        let res = await axios.delete('http://localhost:9000/dishes/'+this.selectedDeleteItem._id);
        if(res.data.status == 'OK'){
          this.dialogDelete = false;
          // Message showing
          this.responseMessageDialog = true;
          this.responseMessage = res.data.message;
          this.selectedDeleteItem = null;
          // Reload data after delete
          this.fetchData();
        }
      },
      async resetAll(){
        await axios.get('http://localhost:9000/dishes/clear');
        // After reset reload table
        this.fetchData();
      },
      // To close open dialog and make all fields empty
      close(){
        this.dialog = false;
        this.currentItem = {
          name: '',
          description: '',
          price: '',
          availability: '',
          soldout: false,
          timetowait: ''
        }
      }
    }
  }
</script>
