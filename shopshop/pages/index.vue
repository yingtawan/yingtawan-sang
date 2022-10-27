<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="8">
      <v-data-table
        :headers="headers"
        :items="shops"
        sort-by="calories"
        class="elevation-1"
      >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Shops</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="info" dark class="mb-2" v-bind="attrs" v-on="on"> เพิ่มร้านค้าใหม่ </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.name" label="ชื่อร้าน" ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" >
                    <v-text-field v-model="editedItem.description" label="รายละเอียด"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.phone" label="เบอร์โทร" ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.address" label="ที่อยู่" ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog> &nbsp;


        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="info" dark class="mb-2" v-bind="attrs" v-on="on"> คำสั่งซื้อ </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.nameproduct" label="ชื่อสินค้า" ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" >
                    <v-text-field v-model="editedItem.description" label="รายละเอียด"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.unit" label="หน่วย" ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.price" label="ราคา" ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="save">Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>


        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="red darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <Product :shop="item"/>
      <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
      <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize"> Reset </v-btn>
    </template>
  </v-data-table>
    </v-col>
  </v-row>
</template>

<script>
import Product from '../components/Product.vue'
  export default {
    data: () => ({
        dialog: false,
        dialogDelete: false,
        headers: [
            {
                text: "ชื่อร้าน",
                align: "start",
                sortable: false,
                value: "name",
            },
            { text: "id", value: "id" },
            { text: "รายละเอียด", value: "description" },
            { text: "เบอร์โทร", value: "phone" },
            { text: "ที่อยู่", value: "address" },
            { text: "รูป", value: "photo" },
            { text: "Actions", value: "actions", sortable: false },
        ],
        shops: [],
        editedIndex: -1,
        editedItem: {
            name: "",
            description: 0,
            phone: 0,
            address: 0,
            photo: 0,
        },
        defaultItem: {
            name: "",
            description: 0,
            phone: 0,
            address: 0,
            photo: 0,
        },
    }),
    computed: {
        formTitle() {
            return this.editedIndex === -1 ? "ร้านค้าใหม่" : "Edit Item";
        },
    },
    watch: {
        dialog(val) {
            val || this.close();
        },
        dialogDelete(val) {
            val || this.closeDelete();
        },
    },
    created() {
        this.initialize();
    },
    methods: {
        initialize() {
            this.shops = [
                {
                    id: Date.now().toString(36),
                    name: "Little JJ",
                    description: 159,
                    phone: 6,
                    address: 24,
                },
            ];
        },
        editItem(item) {
            this.editedIndex = this.shops.indexOf(item);
            this.editedItem = Object.assign({}, item);
            this.dialog = true;
        },
        deleteItem(item) {
            this.editedIndex = this.shops.indexOf(item);
            this.editedItem = Object.assign({}, item);
            this.dialogDelete = true;
        },
        deleteItemConfirm() {
            this.shops.splice(this.editedIndex, 1);
            this.closeDelete();
        },
        close() {
            this.dialog = false;
            this.$nextTick(() => {
                this.editedItem = Object.assign({}, this.defaultItem);
                this.editedIndex = -1;
            });
        },
        closeDelete() {
            this.dialogDelete = false;
            this.$nextTick(() => {
                this.editedItem = Object.assign({}, this.defaultItem);
                this.editedIndex = -1;
            });
        },
        save() {
            if (this.editedIndex > -1) {
                Object.assign(this.shops[this.editedIndex], this.editedItem);
            }
            else {
                this.editedItem.id = Date.now().toString(36);
                this.shops.push(this.editedItem);
            }
            this.close();
        },
    },
    components: { Product }
}
</script>