<template>
  <v-app id="inspire">
    <v-system-bar app>
      <v-spacer></v-spacer>
    </v-system-bar>

    <v-navigation-drawer v-model="drawer" app>
      <v-sheet color="grey lighten-4" class="pa-4">
        <v-avatar class="mb-4" color="grey darken-1" size="64"></v-avatar>

        <div>Transpiter</div>
      </v-sheet>

      <v-divider></v-divider>

      <v-list>
        <v-list-item
          v-for="[icon, text] in links"
          :key="icon"
          link
          @click="checkButton(text)"
        >
          <v-list-item-icon>
            <v-icon>{{ icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ text }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-main>
      <v-container class="py-8 px-6" fluid>
        <v-row justify="center">
          <v-subheader>Fretes</v-subheader>
          <v-expansion-panels inset>
            <v-expansion-panel
              v-for="(frete, i) in fretes"
              :key="i"
              @click="getFrete(frete.fre_frete)"
            >
              <v-expansion-panel-header
                >{{ frete.fre_descarga }}
              </v-expansion-panel-header>

              <v-col class="text--secondary">
                <v-fade-transition leave-absolute>
                  <v-row no-gutters style="width: 100%">
                    <v-col align="center" >
                      Carregamento: <strong>{{ frete.fre_carga }}</strong>
                    </v-col>
                    <v-col align="end">
                      Descarregamento: <strong>{{ frete.fre_descarga }}</strong>
                    </v-col>
                    <v-col align="center">
                      Dias: <strong>{{ frete.fre_dias }}</strong>
                    </v-col>

                    <v-col align="center">
                      Total KM rodados:
                      <strong>{{
                        frete.fre_totalkmrodado
                      }}</strong>
                    </v-col>
                  </v-row>
                </v-fade-transition>
              </v-col>

              <v-expansion-panel-content>
                <v-container>
                  <v-card-text>
                    <v-container>
                      <v-row @keyup="setCalculation(frete.fre_frete)">
                        <v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="valor_frete"
                            label="Valor do Frete"
                            v-bind:properties="{
                              prefix: 'R$',
                              readonly: true,
                            }"
                            v-bind:options="{
                              locale: 'pt-BR',
                              length: 20,
                              precision: 2,
                              empty: null,
                            }"
                          />
                        </v-col>

<v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="frete_liquido"
                            label="Frete Líquido"
                            v-bind:properties="{
                              prefix: 'R$',
                              readonly: true,
                              hint: 'Frete líquido com desconto sobre o ICMS',
                            }"
                          ></v-text-field-money>
                        </v-col>


                                                <v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="saldo_total"
                            label="Saldo"
                            v-bind:properties="{
                              prefix: 'R$',
                              readonly: true,
                              hint: 'Saldo com descontos de operação e manutenção',
                            }"
                          ></v-text-field-money>
                        </v-col>


                        <v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="valorkmsemICMS"
                            v-bind:properties="{
                              prefix: 'R$',
                              readonly: true,
                              hint: 'Valor por kilômetro sem ICMS',
                            }"
                            label="Valor bruto p/ KM"
                            
                          ></v-text-field-money>
                        </v-col>

<v-col cols="6" sm="4" md="4">
          
                          <v-text-field-money
                            v-model="valor_liquido"
                             
                            v-bind:properties="{
                              prefix: 'R$',
                              readonly: true,
                              hint: 'Valor líquido por kilômetro com desconto sobre ICMS',
                            }"
                            
                            label="Valor Líquido p/ KM"
                          ></v-text-field-money>

                        </v-col>

                        <v-col cols="12" sm="4" md="4">
                          <v-text-field-money
                            v-model="valor_liquido_desc"
                            label="Líquido c/ desconto p/ KM"
                            v-bind:properties="{
                              prefix: 'R$',
                              readonly: true,
                              hint: 'Valor líquido por km com desconto de manutenção e operação',
                            }"
                          ></v-text-field-money>
                        </v-col>




                        <v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="custo.cus_comissao"
                            label="Comissão"
                            v-bind:properties="{
                              prefix: 'R$',
                              readonly: true,
                            }"
                          ></v-text-field-money>
                        </v-col>

                        <v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="salario"
                            label="Salário"
                            v-bind:properties="{
                              prefix: 'R$',
                              readonly: true,
                              hint: 'Valor diário multiplicado pelos dias viajados',
                            }"
                          ></v-text-field-money>
                        </v-col>

                        <v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="seguro"
                            label="Seguro"
                            v-bind:properties="{
                              prefix: 'R$',
                              readonly: true,
                              hint: 'Valor diário multiplicado pelos dias viajados',
                            }"
                          ></v-text-field-money>
                        </v-col>

                        

                        <v-col cols="12" sm="6" md="6">
                          <v-text-field-money
                            v-model="freteUnico.fre_valorkm"
                            label="Valor p/ Km"
                            v-bind:properties="{
                              prefix: 'R$',
                              disabled: !edit,
                              hint: 'Valor por kilômetro rodado',
                            }"
                          ></v-text-field-money>
                        </v-col>

                        <v-col cols="12" sm="6" md="6">
                          <v-text-field-percent
                            v-model="freteUnico.fre_porc_manutencao"
                            label="Manutenção"
                            v-bind:properties="{
                              suffix: '%',
                              disabled: !edit,
                              hint: 'Porcentagem de manutenção',
                            }"
                          ></v-text-field-percent>
                        </v-col>
                        <v-col cols="6" sm="6" md="6">
                          <v-text-field-percent
                            v-model="freteUnico.fre_porc_icms"
                            label="ICMS"
                            v-bind:properties="{
                              suffix: '%',
                              disabled: !edit,
                            }"
                          ></v-text-field-percent>
                        </v-col>
                        <v-col cols="12" sm="6" md="6">
                          <v-text-field
                            v-model="freteUnico.fre_dias"
                            label="Dias"
                            hint="Dias de viagem"
                            :disabled="!edit"
                          ></v-text-field>
                        </v-col>




                      </v-row>
                    </v-container>
                  </v-card-text>

                  <v-divider></v-divider>
                  <v-list three-line subheader>
                    <v-subheader>Custos</v-subheader>
                  </v-list>
                  <v-container>
                    <v-card-text>
                      <v-row @keyup="setCalculation(frete.fre_frete)">
                        <v-col cols="4" sm="6" md="6">
                          <v-text-field-money
                            v-model="custo.cus_vaziobase"
                            label="Custo Vazio - Base X Matriz"
                            v-bind:properties="{
                              prefix: 'R$',
                              disabled: !edit,
                            }"
                          ></v-text-field-money>
                        </v-col>

                        <v-col cols="6" sm="6" md="6">
                          <v-text-field
                            v-model="custo.cus_vaziobasekm"
                            label="Kilômetros"
                            :disabled="!edit"
                          ></v-text-field>
                        </v-col>

                        <v-col cols="6" sm="6" md="6">
                          <v-text-field-money
                            v-model="custo.cus_carregado"
                            label="Custo Carregado"
                            v-bind:properties="{
                              prefix: 'R$',
                              disabled: !edit,
                            }"
                          ></v-text-field-money>
                        </v-col>

                        <v-col cols="6" sm="6" md="6">
                          <v-text-field
                            v-model="custo.cus_carregadokm"
                            label="Kilômetros"
                            :disabled="!edit"
                          ></v-text-field>
                        </v-col>

                        <v-col cols="6" sm="6" md="6">
                          <v-text-field-money
                            v-model="custo.cus_vazioretorno"
                            label="Custo Vazio - Retorno X Base"
                            v-bind:properties="{
                              prefix: 'R$',
                              disabled: !edit,
                            }"
                          ></v-text-field-money>
                        </v-col>

                        <v-col cols="6" sm="6" md="6">
                          <v-text-field
                            v-model="custo.cus_vazioretornokm"
                            label="Kilômetros"
                            :disabled="!edit"
                          ></v-text-field>
                        </v-col>

                        <v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="custo.cus_comissao"
                            label="Comissão"
                            v-bind:properties="{
                              prefix: 'R$',
                              disabled: !edit,
                            }"
                          ></v-text-field-money>
                        </v-col>

                        <v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="custo.cus_salariodia"
                            label="Salário"
                            v-bind:properties="{
                              prefix: 'R$',
                              disabled: !edit,
                            }"
                          ></v-text-field-money>
                        </v-col>

                        <v-col cols="6" sm="4" md="4">
                          <v-text-field-money
                            v-model="custo.cus_seguro"
                            label="Seguro"
                            v-bind:properties="{
                              prefix: 'R$',
                              disabled: !edit,
                            }"
                          ></v-text-field-money>
                        </v-col>
                      </v-row>
                    </v-card-text>
                  </v-container>

                  <v-spacer></v-spacer>

                  <v-row align="center" justify="space-around">
                    <v-btn
                      v-show="!edit"
                      @click="edit = true"
                      color="blue darken-1"
                      text
                    >
                      Editar
                    </v-btn>
                    <v-btn
                      v-show="edit"
                      @click="
                        edit = false;
                        updateFrete();
                      "
                      color="green darken-1"
                      outlined
                      text
                    >
                      Salvar
                    </v-btn>
                    <v-btn
                      v-show="edit"
                      @click="edit = false"
                      color="red darken-1"
                      outlined
                      text
                    >
                      Cancelar
                    </v-btn>

                    <v-btn fab dark small color="red" @click="dialog = true">
                      <v-icon>mdi-delete</v-icon>
                    </v-btn>
                    <template v-slot:activator="{ on, attrs }">
                      <v-btn color="primary" dark v-bind="attrs" v-on="on">
                      </v-btn>
                    </template>
                  </v-row>

                  <v-row justify="center">
                    <v-dialog v-model="dialog" persistent max-width="290">
                      <v-card>
                        <v-card-title> Deseja excluir o frete? </v-card-title>

                        <v-card-actions>
                          <v-spacer></v-spacer>
                          <v-btn
                            color="blue darken-1"
                            text
                            @click="dialog = false"
                          >
                            Não
                          </v-btn>
                          <v-btn
                            color="blue darken-1"
                            text
                            @click="
                              dialog = false;
                              deleteFrete(frete.fre_frete);
                            "
                          >
                            Sim
                          </v-btn>
                        </v-card-actions>
                      </v-card>
                    </v-dialog>
                  </v-row>
                </v-container>
              </v-expansion-panel-content>
            </v-expansion-panel>
          </v-expansion-panels>
        </v-row>
      </v-container>
    </v-main>

      <NovoFrete
        :dialog="this.dialogNovo"
        @emit-click="setDiagNovo()"
        @emit-fretes="getFretes()"
      />
  </v-app>
</template>

<script>
import axios from "axios";
import NovoFrete from "./novoFrete.vue";

export default {
  components: {
    NovoFrete,
  },
  data: () => ({
    confirmDelete: false,
    dialog: false,
    fab: false,
    edit: false,
    open: false,
    drawer: null,
    dialogNovo: false,
    dialogVisu: false,
    valor_frete: null,
    valor_liquido: null,
    valorkmsemICMS: null,
    frete_liquido: null,
    valor_liquido_desc: null,
    total_liquido: null,
    saldo_total: null,
    totalkmrodado: null,
    salario: null,
    seguro: null,
    custo: {},
    links: [
      ["mdi-plus", "Novo Frete"],
      ["mdi-clipboard-search-outline", "Buscar"],
    ],
    fretes: [],
    freteUnico: {},
    countFretes: 0,
  }),
  created() {
    this.getFretes();
  },
  methods: {
    async getFretes() {
      let res = await axios.get("/frete");
      this.fretes = res.data;
      this.countFretes = this.fretes.length - 1;
      this.setCalculation();
    },
    async updateFrete() {
      await axios.put("/frete/", this.freteUnico);
      location.reload();
    },
    async deleteFrete(id) {
      await axios.delete(`/frete/${id}`);
      location.reload();
    },
    async getFrete(id) {
      let res = await axios.get(`/frete/${id}`);
      this.freteUnico = res.data;
      this.custo = res.data.custo;
      this.dialogVisu = true;
      this.setCalculation(id);
    },
    checkButton(button) {
      switch (button) {
        case "Novo Frete":
          this.setDiagNovo();
          break;
      }
    },
    setDiagNovo() {
      this.dialogNovo = !this.dialogNovo;
    },
    setDiagVisu() {
      this.dialogVisu = !this.dialogVisu;
    },
    setCalculation(id) {
      this.fretes.map((fre) => {
        fre.fre_totalkmrodado =
          fre.custo.cus_carregadokm +
          fre.custo.cus_vaziobasekm +
          fre.custo.cus_vazioretornokm;
        if (fre.fre_frete === id) {
          this.totalkmrodado = fre.fre_totalkmrodado;
        }
      });

      this.salario = this.custo.cus_salariodia * this.freteUnico.fre_dias;
      this.seguro = this.custo.cus_seguro * this.freteUnico.fre_dias;

      this.valor_frete = this.totalkmrodado * this.freteUnico.fre_valorkm;
      this.frete_liquido =
        this.valor_frete -
        (this.valor_frete * this.freteUnico.fre_porc_icms) / 100;
      this.total_liquido =
        this.frete_liquido -
        this.custo.cus_vaziobase -
        this.custo.cus_carregado -
        this.custo.cus_vazioretorno -
        this.custo.cus_comissao -
        this.salario -
        this.seguro;

      this.valor_liquido = this.frete_liquido / this.totalkmrodado;
      this.valor_liquido_desc = this.total_liquido / this.totalkmrodado;

      this.valorkmsemICMS = this.valor_frete / this.totalkmrodado;

      this.saldo_total =
        this.total_liquido -
        (this.total_liquido * this.freteUnico.fre_porc_manutencao) / 100;
    },
  },
};
</script>