<template>
  <div id="app">
    <div class="container">
      <h1 v-if="errMsg">{{ errMsg }}</h1>
      <div class="form-group">
        <label for>Имя</label>
        <input type="text" class="form-control" v-model="name" />
        <label for>Фамилия</label>
        <input type="text" class="form-control" v-model="surname" />
        <label for>Телефон</label>
        <input type="text" class="form-control" v-model="phone" />
        <button v-if="isEdit" class="btn btn-primary mt-2" @click="onSaveEdit(id)">Сохранить</button>
        <button v-if="isEdit" class="btn btn-primary mt-2 ml-2" @click="EditModOff">Отмена</button>
        <button v-else class="btn btn-primary mt-2" @click="onSave">Сохранить</button>
        <hr />
      </div>
      <div class="row mt-2" v-for="(note, index) in notes" :key="note.id">
        <div class="col">{{note.name}}</div>
        <div class="col">{{note.surname}}</div>
        <div class="col">{{note.phone}}</div>

        <div class="col">
          <button class="btn btn-danger" @click="onDelete(note.id)">Удалить</button>
        </div>
        <div class="col">
          <button class="btn btn-primary" @click="EditModOn(index)">Редактировать</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data: function() {
    return {
      notes: [],
      name: "",
      surname: "",
      phone: "",
      id: 0,
      errMsg: "",
      isEdit: false
    };
  },
  components: {},
  methods: {
    async updateData() {
      try {
        let res = await this.$http.get("http://localhost:3000/notes");
        this.notes = await res.json();
        this.errMsg = "";
      } catch (e) {
        console.error(e);
      }
    },
    async onSave() {
      let note = {
        name: this.name,
        surname: this.surname,
        phone: this.phone
      };
      let checkPhone = this.notes.filter(i => i.phone == note.phone);
      if (checkPhone != false) {
        this.errMsg = "Такой номер уже существует";
        return;
      }
      try {
        await this.$http.post("http://localhost:3000/notes", note);
        this.updateData();
      } catch (e) {
        console.error(e);
      }
    },
    async onDelete(id) {
      try {
        await this.$http.delete("http://localhost:3000/notes/" + id);
        this.updateData();
      } catch (e) {
        console.log(e);
      }
    },
    EditModOn(index) {
      this.name = this.notes[index].name;
      this.surname = this.notes[index].surname;
      this.phone = this.notes[index].phone;
      this.id = this.notes[index].id;
      this.isEdit = true;
    },
    EditModOff() {
      this.name = "";
      this.surname = "";
      this.phone = "";
      this.id = 0;
      this.isEdit = false;
    },
    async onSaveEdit(id) {
      let note = {
        name: this.name,
        surname: this.surname,
        phone: this.phone,
        id: this.id
      };
      let checkPhone = this.notes.filter(i => i.phone == note.phone);
      if (checkPhone != false) {
        this.errMsg = "Такой номер уже существует";
        return;
      }
      try {
        await this.$http.put("http://localhost:3000/notes/" + id, note);
        this.updateData();
      } catch (e) {
        console.log(e);
      }
    }
  },
  created() {
    this.updateData();
  }
};
</script>

