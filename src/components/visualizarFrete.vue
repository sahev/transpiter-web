<template>
  <v-row justify="center">
    <v-dialog
      v-model="localDialog"
      max-width="1200px"
    >
      <v-card>
        <v-card-title>
          <span class="text-h5">Título Carregamento</span>
        </v-card-title>
        <v-card-text>
          <v-container>

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.fre_carga"
                  label="Local de carregamento da carga"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.fre_descarga"
                  label="Local de descarregamento da carga"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.fre_porc_manutencao"
                  label="Manutenção"
                  hint="Porcentagem de manutenção"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.fre_dias"
                  label="Dias"
                  hint="Dias de viagem"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.fre_valorkm"
                  label="Valor p/ Km"
                  hint="Valor por kilômetro rodado"
                  required
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-divider></v-divider>
        <v-list three-line subheader>
          <v-subheader>Custos</v-subheader>
        </v-list>

            <v-row>
              <v-col cols="6" sm="6">
                <v-text-field
                  v-model="localFrete.custo.cus_vaziobase"
                  label="Custo Vazio"
                  hint="Custo Vazio Base X Matriz"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.custo.cus_vaziobasekm"
                  label="Kilômetros"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="6" sm="6">
                <v-text-field
                  v-model="localFrete.custo.cus_carregado"
                  label="Custo Carregado"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.custo.cus_carregadokm"
                  label="Kilômetros"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="6" sm="6">
                <v-text-field
                  v-model="localFrete.custo.cus_vazioretorno"
                  label="Custo Vazio Retorno"
                  hint="Custo Vazio Retorno X Base"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.custo.cus_vazioretornokm"
                  label="Kilômetros"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.custo.cus_comissao"
                  label="Comissão"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.custo.cus_salariodia"
                  label="Salário p/ dia"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="localFrete.custo.cus_seguro"
                  label="Seguro /p dia"
                  required
                ></v-text-field>
              </v-col>
            </v-row>

          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="localDialog = false"
          >
            Close
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="localDialog = false; updateFrete()"
          >
            Save
          </v-btn>

          </v-container>
        </v-card-text>

      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
// import axios from "axios";
import brasil from "../estados/brasil.json";

export default {
  props: {
    dialog: {
      type: Boolean,
    },
    frete: {
        type: Object,
    },
  },
  created() {
  },
  watch: {
    frete: function () {
        this.localFrete = this.frete
    }, 
    dialog: function() {
        this.localDialog = this.dialog
    }
  },
  data: () => ({
    loading: false,
    snackbar: false,
    localDialog: null,
    alerttime: true,
    cidadesCar: [],
    cidadesDes: [],
    estadoCar: null,
    cidadeCar: null,
    estadoDes: null,
    cidadeDes: null,
    carga: null,
    descarga: null,
    brasil,
    localFrete: {
      fre_carga: null,
      fre_descarga: null,
      fre_dias: null,
      fre_valorkm: null,
      fre_porc_icms: null,
      fre_porc_manutencao: null,
      custo: {
        cus_vaziobase: null,
        cus_vaziobasekm: null,
        cus_carregado: null,
        cus_carregadokm: null,
        cus_vazioretorno: null,
        cus_vazioretornokm: null,
        cus_comissao: null,
        cus_salariodia: null,
        cus_seguro: null,
      },
    },
  }),
  methods: {
    spli() {
        let str = this.localFrete.fre_carga;
    },
    emitClick() {
      this.localDialog = !this.localDialog;
      this.$emit("emit-click");
      this.$emit("emit-fretes");
    },
    async updateFrete() {
      // update frete
      this.snackbar = true;
      this.emitClick();
    },
  },
};
</script>
