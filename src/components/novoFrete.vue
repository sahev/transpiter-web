<template>
  <v-row justify="center">
    <v-dialog
      v-model="dialog"
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-toolbar dark color="primary">
          <v-btn
            icon
            dark
            @click.stop.prevent="emitClick()"
            @click="snackbar = false"
          >
            <v-icon>mdi-close</v-icon>
          </v-btn>
          <v-toolbar-title>Cadastrar novo carregamento</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items>
            <v-btn dark text @click="saveFrete()"> Salvar </v-btn>
          </v-toolbar-items>
        </v-toolbar>
        <v-list three-line subheader>
          <v-subheader>Frete</v-subheader>
        </v-list>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="6" sm="6">
                Carregamento
                <v-autocomplete
                  v-model="estadoCar"
                  :loading="loading"
                  :items="this.estadosCar"
                  cache-items
                  flat
                  hide-no-data
                  hide-details
                  label="Estado"
                  item-text="text"
                  hint="Local de carregamento da carga"
                ></v-autocomplete>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                .
                <v-autocomplete
                  flat
                  :items="this.cidadesCar"
                  hide-no-data
                  hide-details
                  v-model="cidadeCar"
                  label="Cidade"
                  hint="Local de carregamento da carga"
                ></v-autocomplete>
              </v-col>

              <v-col cols="6" sm="6">
                Descarregamento
                <v-autocomplete
                  v-model="estadoDes"
                  :loading="loading"
                  :items="this.estadosDes"
                  cache-items
                  flat
                  label="Estado"
                  item-text="text"
                  hint="Local de descarregamento da carga"
                ></v-autocomplete>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                .
                <v-autocomplete
                  :items="this.cidadesDes"
                  v-model="cidadeDes"
                  flat
                  label="Cidade"
                  hint="Local de descarregamento da carga"
                ></v-autocomplete>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.fre_porc_icms"
                  label="ICMS"
                  hint="Porcentagem de imposto sobre Circulação de Mercadorias e Prestação de Serviços"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.fre_porc_manutencao"
                  label="Manutenção"
                  hint="Porcentagem de manutenção"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.fre_dias"
                  label="Dias"
                  hint="Dias de viagem"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.fre_valorkm"
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

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="6" sm="6">
                <v-text-field
                  v-model="frete.custo.cus_vaziobase"
                  label="Custo Vazio"
                  hint="Custo Vazio Base X Matriz"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.custo.cus_vaziobasekm"
                  label="Kilômetros"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="6" sm="6">
                <v-text-field
                  v-model="frete.custo.cus_carregado"
                  label="Custo Carregado"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.custo.cus_carregadokm"
                  label="Kilômetros"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="6" sm="6">
                <v-text-field
                  v-model="frete.custo.cus_vazioretorno"
                  label="Custo Vazio Retorno"
                  hint="Custo Vazio Retorno X Base"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.custo.cus_vazioretornokm"
                  label="Kilômetros"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.custo.cus_comissao"
                  label="Comissão"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.custo.cus_salariodia"
                  label="Salário p/ dia"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="frete.custo.cus_seguro"
                  label="Seguro /p dia"
                  required
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
import axios from "axios";
import brasil from "../estados/brasil.json";

export default {
  props: {
    dialog: {
      type: Boolean,
    },
  },
  created() {
    console.log(brasil);
  },
  watch: {
    estadoCar: function () {
      this.cidadesCar = brasil[this.estadoCar].cidades;
    },
    estadoDes: function () {
      this.cidadesDes = brasil[this.estadoDes].cidades;
    },
    cidadeCar: function () {
      this.frete.fre_carga = this.cidadeCar + " - " + this.estadoCar;
    },
    cidadeDes: function () {
      this.frete.fre_descarga = this.cidadeDes + " - " + this.estadoDes;
    },
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
    frete: {
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
    estadosCar: [
      { value: "AC", text: "Acre" },
      { value: "AL", text: "Alagoas" },
      { value: "AP", text: "Amapá" },
      { value: "AM", text: "Amazonas" },
      { value: "BA", text: "Bahia" },
      { value: "CE", text: "Ceará" },
      { value: "DF", text: "Distrito Federal" },
      { value: "ES", text: "Espírito Santo" },
      { value: "GO", text: "Goiás" },
      { value: "MA", text: "Maranhão" },
      { value: "MT", text: "Mato Grosso" },
      { value: "MS", text: "Mato Grosso do Sul" },
      { value: "MG", text: "Minas Gerais" },
      { value: "PA", text: "Pará" },
      { value: "PB", text: "Paraíba" },
      { value: "PR", text: "Paraná" },
      { value: "PE", text: "Pernambuco" },
      { value: "PI", text: "Piauí" },
      { value: "RJ", text: "Rio de Janeiro" },
      { value: "RN", text: "Rio Grande do Norte" },
      { value: "RS", text: "Rio Grande do Sul" },
      { value: "RO", text: "Rondônia" },
      { value: "RR", text: "Roraima" },
      { value: "SC", text: "Santa Catarina" },
      { value: "SP", text: "São Paulo" },
      { value: "SE", text: "Sergipe" },
      { value: "TO", text: "Tocantins" },
    ],
    estadosDes: [
      { value: "AC", text: "Acre" },
      { value: "AL", text: "Alagoas" },
      { value: "AP", text: "Amapá" },
      { value: "AM", text: "Amazonas" },
      { value: "BA", text: "Bahia" },
      { value: "CE", text: "Ceará" },
      { value: "DF", text: "Distrito Federal" },
      { value: "ES", text: "Espírito Santo" },
      { value: "GO", text: "Goiás" },
      { value: "MA", text: "Maranhão" },
      { value: "MT", text: "Mato Grosso" },
      { value: "MS", text: "Mato Grosso do Sul" },
      { value: "MG", text: "Minas Gerais" },
      { value: "PA", text: "Pará" },
      { value: "PB", text: "Paraíba" },
      { value: "PR", text: "Paraná" },
      { value: "PE", text: "Pernambuco" },
      { value: "PI", text: "Piauí" },
      { value: "RJ", text: "Rio de Janeiro" },
      { value: "RN", text: "Rio Grande do Norte" },
      { value: "RS", text: "Rio Grande do Sul" },
      { value: "RO", text: "Rondônia" },
      { value: "RR", text: "Roraima" },
      { value: "SC", text: "Santa Catarina" },
      { value: "SP", text: "São Paulo" },
      { value: "SE", text: "Sergipe" },
      { value: "TO", text: "Tocantins" },
    ],
  }),
  methods: {
    emitClick() {
      this.localDialog = !this.localDialog;
      this.$emit("emit-click");
      this.$emit("emit-fretes");
    },
    async saveFrete() {
      await axios.post("frete", this.frete);
      this.snackbar = true;
      this.emitClick();
    },
  },
};
</script>
