<!-- @format -->

 <template>
 <v-main class="list">
 <h3 class="text-h3 font-weight-medium mb-5"> Table karyawan </h3>

 <v-card>
 <v-card-title>

 <v-text-field
 v-model="search"
 append-icon="mdi-magnify"
 label="Search"
 single-line
 hide-details
 ></v-text-field>


 <v-spacer></v-spacer>
 <v-btn color="success" dark @click="dialog = true">
 Tambah
 </v-btn>

 </v-card-title>


 <v-data-table :headers="headers" :items="karyawans" :search="search">
 <template v-slot:[`item.actions`]="{ item }">
 <v-btn small class="mr-2" @click="editHandler(item)">
 edit
 </v-btn>
 <v-btn small @click="deleteHandler(item.id)">
 delete
 </v-btn>
 </template> 
 </v-data-table>
 </v-card>

 <v-dialog v-model="dialog" persistent max-width="600px">
 <v-card>
 <v-card-title>
 <span class="headline">{{ formTitle }} karyawan</span>
 </v-card-title>
 <v-card-text>
 <v-container>
 <v-text-field
 v-model="form.nama_karyawan"
 label="Nama karyawan"
 required
 ></v-text-field>

 <v-text-field
 v-model="form.noTelp_karyawan"
 label="No Telepon"
 required
 ></v-text-field>

<v-text-field
 v-model="form.alamat_karyawan"
 label="Alamat karyawan"
 required
 ></v-text-field>

 <v-text-field
 v-model="form.jenisKelamin_karyawan"
 label="Jenis Kelamin karyawan"
 required
 ></v-text-field>

 <v-text-field
 v-model="form.status_karyawan"
 label="status karyawan"
 required
 ></v-text-field>




 </v-container>
 </v-card-text>
 <v-card-actions>
 <v-spacer></v-spacer>
 <v-btn color="blue darken-1" text @click="cancel">
 Cancel
 </v-btn>
 <v-btn color="blue darken-1" text @click="setForm">
 Save
 </v-btn>
 </v-card-actions>
 </v-card>
 </v-dialog>

 <v-dialog v-model="dialogConfirm" persistent max-width="400px">
 <v-card>
 <v-card-title>
 <span class="headline">warning!</span>
 </v-card-title>
 <v-card-text>
 Anda yakin ingin menghapus karyawan ini?
 </v-card-text>
 <v-card-actions>
 <v-spacer></v-spacer>
 <v-btn color="blue darken-1" text @click="dialogConfirm = false">
 Cancel
 </v-btn>
 <v-btn color="blue darken-1" text @click="deleteData"> 
 Delete
 </v-btn>
 </v-card-actions>
 </v-card>
 </v-dialog>

 <v-snackbar v-model="snackbar" :color="color" timeout="2000" bottom>
 {{error_message}}
 </v-snackbar>
 </v-main>
 </template>
 <script>
 export default {
 name: "List",
 data() {
 return {
 inputType: 'Tambah',
 load: false,
 snackbar: false,
 error_message: '',
 color: '',
 search: null,
 dialog: false,
 dialogConfirm: false,
 headers: [
 { text: "Nama karyawan",
 align: "start",
 sortable: true,
 value: "nama_karyawan" },
 { text: "Nomor karyawan", value: "noTelp_karyawan" },
  { text: "Alamat karyawan", value: "alamat_karyawan" },
   { text: "status karyawan", value: "status_karyawan" },
    { text: "Jenis Kelamin karyawan", value: "jenisKelamin_karyawan" },
 { text: "Actions", value: "actions" },
 ],


 karyawan: new FormData,
 karyawans: [],
 form: {
 nama_karyawan: null,
 noTelp_karyawan: null,
  alamat_karyawan: null,
  jenisKelamin_karyawan: null,
  status_karyawan: null,
 },
 deleteId: '',
 editId: ''
 };
 },
 methods: {
 setForm() {
 if (this.inputType === 'Tambah') {
 this.save()
 } else {
 this.update()
 }
 },
 //read data product
 readData() {
 var url = this.$api + '/karyawan'
 this.$http.get(url, {
 headers: {
 'Authorization': 'Bearer ' + localStorage.getItem('token')
 } 
 }).then(response => {
 this.karyawans = response.data.data
 })
 },
 //simpan data produk
 save() {
 this.karyawan.append('nama_karyawan', this.form.nama_karyawan);
 this.karyawan.append('noTelp_karyawan', this.form.noTelp_karyawan);
  this.karyawan.append(' alamat_karyawan', this.form.alamat_karyawan);
   this.karyawan.append(' jenisKelamin_karyawan', this.form.jenisKelamin_karyawan);
    this.karyawan.append(' status_karyawan', this.form.status_karyawan);

 var url = this.$api + '/karyawan/'
 this.load = true
 this.$http.post(url, this.karyawan, {
 headers: {
 'Authorization': 'Bearer ' + localStorage.getItem('token')
 }
 }).then(response => {
 this.error_message=response.data.message;
 this.color="green"
 this.snackbar=true;
 this.load = false;
 this.close();
 this.readData(); //mengambil data
 this.resetForm();
 }).catch(error => {
 this.error_message=error.response.data.message;
 this.color="red"
 this.snackbar=true;
 this.load = false;
 })
 },
 //ubah data produk
 update() {
 let newData = {
 nama_karyawan: this.form.nama_karyawan,
  noTelp_karyawan: this.form.noTelp_karyawan,
 alamat_karyawan: this.form.alamat_karyawan,
 jenisKelamin_karyawan: this.form.jenisKelamin_karyawan,
 status_karyawan: this.form.status_karyawan,


 }
 var url = this.$api + '/karyawan/' + this.editId;
 this.load = true
 this.$http.put(url, newData, {
 headers: {
 'Authorization': 'Bearer ' + localStorage.getItem('token')
 }
 }).then(response => {
 this.error_message=response.data.message;
 this.color="green"
 this.snackbar=true;
 this.load = false;
 this.close();
 this.readData(); //mengambil data
 this.resetForm();
 this.inputType = 'Tambah';
 }).catch(error => {
 this.error_message=error.response.data.message;
 this.color="red"
 this.snackbar=true;
 this.load = false;
 }) 
 },
 //hapus data produk
 deleteData() {

 //mengahapus data
 var url = this.$api + '/karyawan/' + this.deleteId;
 //data dihapus berdasarkan id
 this.$http.delete(url, {
 headers: {
 'Authorization': 'Bearer ' + localStorage.getItem('token')
 }
 }).then(response => {
 this.error_message=response.data.message;
 this.color="green"
 this.snackbar=true;
 this.load = false;
 this.close();
 this.readData(); //mengambil data
 this.resetForm();
 this.inputType = 'Tambah';
 }).catch(error => {
 this.error_message=error.response.data.message;
 this.color="red"
 this.snackbar=true;
 this.load = false;
 })
 },
 editHandler(item){
 this.inputType = 'Ubah';
 this.editId = item.id;
 this.form.nama_karyawan = item.nama_karyawan;
 this.form.noTelp_karyawan = item.noTelp_karyawan;
 this.form.alamat_karyawan = item.alamat_karyawan;
 this.form.jenisKelamin_karyawan = item.jenisKelamin_karyawan;
 this.form.status_karyawan = item.status_karyawan;
 this.dialog = true;
 },
 deleteHandler(id){
 this.deleteId = id;
 this.dialogConfirm = true;
 },
 close() {
 this.dialog = false
 this.inputType = 'Tambah';
 },
 cancel() {
 this.resetForm();
 this.readData();
 this.dialog = false;
 this.inputType = 'Tambah';
 },
 resetForm() {
 this.form = {
 nama_karyawan: null,
 noTelp_karyawan: null,
 stok: null,
 };
 },
 },
 computed: {
 formTitle() {
 return this.inputType 
 },
 },
 mounted() {
 this.readData();
 },
 };
 </script> 