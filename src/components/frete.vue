<template>
  <v-app id="inspire">
    <v-system-bar app>
      <v-spacer></v-spacer>
    </v-system-bar>

    <v-navigation-drawer
      v-model="drawer"
      app
    >
      <v-sheet
        color="grey lighten-4"
        class="pa-4"
      >
        <v-avatar
          class="mb-4"
          color="grey darken-1"
          size="64"
        ></v-avatar>

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
      <v-container
        class="py-8 px-6"
        fluid
      >
        <v-row>
          <v-col
          >
            <v-card>
              <v-subheader>Fretes</v-subheader>

              <v-list two-line>
                <template v-for="(frete, i ) in fretes">
                  <v-list-item

                    :key="frete.fre_frete"
										link
										@click="getFrete(frete.fre_frete); setDiagVisu()"
                  >
                    <v-list-item-avatar color="grey darken-1">
                    </v-list-item-avatar>

                    <v-list-item-content>
                      <v-list-item-title>{{ frete.fre_descarga }}</v-list-item-title>

                      <v-list-item-subtitle>
                        Carregamento: <strong>{{ frete.fre_carga }}</strong> - 
												Dias: <strong>{{ frete.fre_dias }}</strong> - 
												Total KM rodados: <strong>{{ frete.fre_totalkmrodado }}</strong>
                      </v-list-item-subtitle>
                    </v-list-item-content>
                  </v-list-item>

                  <v-divider
                    v-if="i !== countFretes"
                    :key="`divider-${i}`"
                    inset
                  ></v-divider>
                </template>
              </v-list>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
		<v-container>
		<NovoFrete  :dialog="this.dialogNovo"
		@emit-click="setDiagNovo();" @emit-fretes="getFretes()" />
		</v-container>
		<v-container>
		<VisualizarFrete :dialog="this.dialogVisu" :frete="this.frete"
		@emit-click="setDiagVisu();" @emit-fretes="getFretes()" />
		</v-container>		
  </v-app>
</template>

<script>
import axios from 'axios';
import NovoFrete from './novoFrete.vue'
import VisualizarFrete from './visualizarFrete.vue'

  export default {
		components: {
			NovoFrete,
			VisualizarFrete
		},
    data: () => ({
      drawer: null,
			dialogNovo: false,
			dialogVisu: false,
      links: [
        ['mdi-plus', 'Novo Frete'],
        ['mdi-clipboard-search-outline', 'Buscar']
      ],
      fretes: [],
			frete: {},
      countFretes: 0,
    }),
    created() {
        this.getFretes();
    },
    methods: {
			async getFretes() {
				let res = await axios.get('/frete')
				this.fretes = res.data;
				this.countFretes = this.fretes.length - 1;				
			},
			async getFrete(id) {
				let res = await axios.get(`/frete/${id}`)
				this.frete = res.data;
				this.dialogVisu = true;
				console.log(this.frete);
			},
			checkButton(button) {
				switch(button) {
					case 'Novo Frete':
						this.setDiagNovo()
						break;
					
				} 
			},
			setDiagNovo() {
				this.dialogNovo = !this.dialogNovo;
			},
			setDiagVisu() {
				this.dialogVisu = !this.dialogVisu;
				console.log(this.dialogVisu, 'frete');
			}			
    }
  }
</script>