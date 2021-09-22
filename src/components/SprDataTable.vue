<template>
  <v-container>
    <v-container>
      <!-- <v-row v-if="buttonClicked">
        <v-col cols="12" sm="6" md="2">
          <v-select
            v-model="search"
            :items="items"
            item-text="PSPNAME"
            label="Наименование"
          ></v-select>
        </v-col>
        <v-col cols="12" sm="6" md="2">
          <v-select
            v-model="search"
            :items="filials"
            item-text="NAME"
            item-value="KOD"
            label="Филиал"
          ></v-select>
        </v-col>
        <v-col cols="12" sm="6" md="2">
          <v-select
            v-model="search"
            :items="sikntype"
            item-text='NAME'
            item-value='KOD'
            label="Назначение СИКН"
          ></v-select>
        </v-col>
      </v-row> -->
      <v-row v-if="buttonClicked">
        <v-col cols="12" sm="6" md="2">
          <v-select
            v-model="nameFilterValue"
            :items="items"
            item-text="PSPNAME"
            label="Наименование"
          ></v-select>
        </v-col>
        <v-col cols="12" sm="6" md="2">
          <v-select
            v-model="ownerFilterValue"
            :items="items"
            item-text="PSPOWNER"
            label="Владелец"
          ></v-select>
        </v-col>
        <v-col cols="12" sm="6" md="2">
          <v-select
            v-model="filialFilterValue"
            :items="filials"
            item-text="NAME"
            label="Филиал"
          ></v-select>
        </v-col>
        <v-col cols="12" sm="6" md="2">
          <v-select
            v-model="sikntypeFilterValue"
            :items="sikntype"
            item-text="NAME"
            label="Назначение СИКН"
          ></v-select>
        </v-col>
      </v-row>
      <!-- <v-btn class="primary" @click="loadFilter">Фильтры</v-btn> -->
      <v-btn class="primary" @click="resetFilter">Сбросить фильтр</v-btn>
    </v-container>
    <v-data-table
      :headers="headers"
      :items="sprList"
      :footer-props="{
        'items-per-page-text': 'Кол-во объектов на страницу',
      }"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Объекты</v-toolbar-title>

          <v-divider class="mx-4" inset vertical></v-divider>

          <v-spacer></v-spacer>

          <v-dialog v-model="dialog" max-width="700px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                Добавить
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-select
                        v-model="editedItem.PSPNAME"
                        :items="items"
                        item-text="PSPNAME"
                        label="Наименование"
                      ></v-select>
                    </v-col>

                    <v-col cols="12" sm="6" md="4">
                      <v-select
                        v-model="editedItem.PSPOWNER"
                        :items="items"
                        item-text="PSPOWNER"
                        label="Владелец"
                      ></v-select>
                    </v-col>

                    <v-col cols="12" sm="6" md="4">
                      <v-select
                        v-model="editedItem.NUKOD"
                        :items="filials"
                        item-text="NAME"
                        item-value="KOD"
                        label="Филиал"
                      ></v-select>
                      <!-- <v-text-field label="Введите филиал"></v-text-field>
                      <v-btn text color="primary">Добавить</v-btn> -->
                    </v-col>


                  </v-row>
                  <v-row>
                    <v-col cols="12" sm="6" md="3">
                      <v-text-field
                        v-model="editedItem.NSIKN"
                        label="№ СИКН"
                      ></v-text-field>
                    </v-col>

                    <v-col cols="12" sm="6" md="3">
                      <v-select
                        v-model="editedItem.SIKNTYPEKOD"
                        :items="sikntype"
                        item-text="NAME"
                        item-value="KOD"
                        label="Назначение СИКН"
                      ></v-select>
                    </v-col>

                    <v-col cols="12" sm="6" md="3">
                      <v-text-field
                        v-model="editedItem.KMH_ORDER"
                        label="№ в задаче КМХ"
                      ></v-text-field>
                    </v-col>
                  </v-row>

                  <v-row>
                    <!-- priem -->
                    <v-col cols="12" sm="6" md="3">
                      <v-select
                        v-model="editedItem.PRIEMKOD"
                        :items="priemKod"
                        item-text="NAME"
                        item-value="KOD"
                        label="Прием"
                      ></v-select>
                    </v-col>

                    <!-- meas_subj -->
                    <v-col cols="12" sm="6" md="3">
                      <v-select
                        v-model="editedItem.MEAS_SUBJKOD"
                        :items="measKod"
                        item-text="NAME"
                        item-value="KOD"
                        label="Предмет измерения"
                      ></v-select>
                    </v-col>

                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Отмена
                </v-btn>
                <v-btn color="blue darken-1" text @click="save">
                  Сохранить
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5"
                >Вы уверены, что хотите удалить объект?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete"
                  >Отмена</v-btn
                >
                <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                  >Да</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
        <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize"> Reset </v-btn>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
import axios from "axios";

export default {
  name: "SprDataTable",
  data: () => ({
    dialog: false,
    dialogDelete: false,
    buttonClicked: true,
    editedIndex: -1,
    editedItem: {
      RECID: "-1",
      PSPNAME: "",
      PSPOWNER: "",
      NSIKN: "",
      NU: "",
      NUKOD: "",
      PRIEM: "",
      PRIEMKOD: "",
      MEAS_SUBJ: "",
      ALTFIL: "",
      SIKNTYPE: "",
      SIKNTYPEKOD: "",
      ACTIVE: "1",
      KMH_ORDER: "",
    },
    defaultItem: {
      RECID: "-1",
      PSPNAME: "",
      PSPOWNER: "",
      NSIKN: "",
      NU: "",
      NUKOD: "",
      PRIEM: "",
      PRIEMKOD: "",
      MEAS_SUBJ: "",
      ALTFIL: "",
      SIKNTYPE: "",
      SIKNTYPEKOD: "",
      ACTIVE: "1",
      KMH_ORDER: "",
    },
    items: [],
    filials: [],
    sikntype: [],
    priemKod: [],
    measKod: [],
    row: null,
    sprList: [],
    sprListNuSikntype: {
      RECID: "",
      PSPNAME: "",
      PSPOWNER: "",
      NSIKN: "",
      NU: "",
      PRIEM: "",
      MEAS_SUBJ: "",
      ALTFIL: "",
      SIKNTYPE: "",
      ACTIVE: "",
      KMH_ORDER: "",
    },
    nameFilterValue: null,
    ownerFilterValue: null,
    filialFilterValue: null,
    sikntypeFilterValue: null,
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Новый объект" : "Изменить объект";
    },
    headers() {
      return [
        {
          text: "ID",
          align: "start",
          value: "RECID",
        },
        {
          text: "Наименование",
          value: "PSPNAME",
          filter: this.nameFilter,
        },
        {
          text: "Владелец",
          value: "PSPOWNER",
          filter: this.ownerFilter,
        },
        { text: "№ СИКН", value: "NSIKN" },
        {
          text: "Филиал имя",
          value: "NU",
          filter: this.filialFilter,
        },
        // {
        //   text: "Филиал код",
        //   value: "NUKOD",
        //   // filter: this.filialFilter,
        // },
        { text: "Прием", value: "PRIEM" },
        { text: "Предмет измерения", value: "MEAS_SUBJ" },
        // { text: "Altfil", value: "ALTFIL" },
        {
          text: "Назначение СИКН имя",
          value: "SIKNTYPE",
          filter: this.sikntypeFilter,
        },
        // {
        //   text: "Назначение СИКН код",
        //   value: "SIKNTYPEKOD",
        //   // filter: this.sikntypeFilter,
        // },
        // { text: "Active", value: "ACTIVE" },
        { text: "№ в задаче КМХ", value: "KMH_ORDER" },
        { text: "Изменить", value: "actions", sortable: false },
      ];
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

  async mounted() {
    await this.getSprList();
    await this.getPspnameAndOWner();
    await this.getFilials();
    await this.getSikntype();
    await this.getPriemKod();
    await this.getMeasKod();
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {},

    editItem(item) {
      this.editedIndex = this.sprList.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.sprList.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    async deleteItemConfirm() {
      // this.sprList.splice(this.editedIndex, 1);
      this.deleteSprFromTable();
      this.closeDelete();
      await this.getSprList();
    },

    async close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
      await this.getSprList();
    },

    async closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
      await this.getSprList();
    },

    async save() {
      if (this.editedIndex > -1) {
        Object.assign(this.sprList[this.editedIndex], this.editedItem);
        await this.updateSprInTable();
      } else {
        this.sprList.push(this.editedItem);
        await this.updateSprInTable();
      }

      this.close();
      //this.getSprList();
    },

    // функция для добавления и изменения объекта в БД
    async updateSprInTable() {

      const fd = new FormData();
      fd.append("pRECID", this.editedItem.RECID);
      fd.append("pPSPNAME", this.editedItem.PSPNAME);
      fd.append("pPSPOWNER", this.editedItem.PSPOWNER);
      fd.append("pNSIKN", this.editedItem.NSIKN);
      fd.append("pNU", this.editedItem.NUKOD);
      fd.append("pPRIEM", this.editedItem.PRIEMKOD || "");
      fd.append("pMEAS_SUBJ", this.editedItem.MEAS_SUBJKOD || "");
      fd.append("pALTFIL", this.editedItem.ALTFIL);
      fd.append("pSIKNTYPE", this.editedItem.SIKNTYPEKOD);
      fd.append("pACTIVE", this.editedItem.ACTIVE);
      fd.append("pKMH_ORDER", this.editedItem.KMH_ORDER);

      await axios
        .post(`/ords/remont/PKG_SIKN_SPR.SAVE_SPR_OBJECT`, fd, {
          headers: { "Content-Type": "multipart/form-data" },
        })
        .then(
          (response) => {
            console.log(response);
          },
          (error) => {
            console.log(error);
          }
        );
    },

    // функция для удаления объекта из базы данных
    deleteSprFromTable() {
      const fd = new FormData();
      fd.append("pRECID", this.editedItem.RECID);
      axios
        .post(`/ords/remont/PKG_SIKN_SPR.DELETE_SPR_OBJECT`, fd, {
          headers: { "Content-Type": "multipart/form-data" },
        })
        .then(
          (response) => {
            console.log(response);
          },
          (error) => {
            console.log(error);
          }
        );
    },

    async getSprList() {
      const { data } = await axios.get(
        "/ords/remont/PKG_SIKN_SPR.GET_SIKN_SPR"
      );
      if (data.status) {
        this.sprList = data.result;
        console.log(this.sprList);
      }
    },

    async getPspnameAndOWner() {
      const { data } = await axios.get(
        "/ords/remont/PKG_SIKN_SPR.GET_PSPNAME_AND_OWNER"
      );
      if (data.status) {
        this.items = data.result;
      }
    },

    async getFilials() {
      const { data } = await axios.get("/ords/remont/PKG_SIKN_SPR.GET_FILIALS");
      if (data.status) {
        this.filials = data.result;
      }
    },

    async getSikntype() {
      const { data } = await axios.get(
        "/ords/remont/PKG_SIKN_SPR.GET_SIKNTYPE"
      );
      if (data.status) {
        this.sikntype = data.result;
      }
    },

    async getPriemKod() {
      const { data } = await axios.get(
        "/ords/remont/PKG_SIKN_SPR.GET_SIKN_PRIEMKOD"
      );
      if (data.status) {
        this.priemKod = data.result;
      }
    },

    async getMeasKod() {
      const { data } = await axios.get(
        "/ords/remont/PKG_SIKN_SPR.GET_SIKN_MEASKOD"
      );
      if (data.status) {
        this.measKod = data.result;
      }
    },

    loadFilter() {
      this.buttonClicked = !this.buttonClicked;
    },

    resetFilter() {
      this.nameFilterValue = null;
      this.ownerFilterValue = null;
      this.filialFilterValue = null;
      this.sikntypeFilterValue = null;
    },

    nameFilter(value) {
      if (!this.nameFilterValue) {
        return true;
      }
      return value === this.nameFilterValue;
    },
    ownerFilter(value) {
      if (!this.ownerFilterValue) {
        return true;
      }
      return value === this.ownerFilterValue;
    },
    filialFilter(value) {
      if (!this.filialFilterValue) {
        return true;
      }
      return value === this.filialFilterValue;
    },
    sikntypeFilter(value) {
      if (!this.sikntypeFilterValue) {
        return true;
      }
      return value === this.sikntypeFilterValue;
    },
  },
};
</script>

<style>
</style>