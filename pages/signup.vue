<template>
  <v-row align-content="center" justify="center" align="center">
    <v-col cols="10">
      <h2 class="title">Compila il modulo per iscriverti</h2>
      <v-card outlined shaped>
        <v-form ref="form">
          <v-container>
            <v-row>
              <v-col cols="12">
                <h3 class="title">Anagrafica</h3>
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-text-field
                  v-model="userData.anagrafica.nome"
                  label="Nome"
                  :rules="[rules.required]"
                  outlined
                />
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-text-field
                  v-model="userData.anagrafica.cognome"
                  label="Cognome"
                  :rules="[rules.required]"
                  outlined
                />
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-menu
                  ref="menu"
                  v-model="menu"
                  :close-on-content-click="false"
                  transition="scale-transition"
                  offset-y
                  max-width="290px"
                  min-width="290px"
                >
                  <template #activator="{ on, attrs }">
                    <v-text-field
                      v-model="dataNascitaFormattata"
                      label="Nato il"
                      readonly
                      outlined
                      v-bind="attrs"
                      :rules="[rules.required]"
                      v-on="on"
                    />
                  </template>
                  <v-date-picker
                    ref="bday"
                    v-model="otherData.data"
                    first-day-of-week="1"
                    locale="it"
                    min="1900-1-1"
                    :max="maxDate"
                    :show-current="maxDate"
                    @input="menu = false"
                  />
                </v-menu>
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-text-field
                  v-model="userData.anagrafica.codice_fiscale"
                  label="Codice fiscale"
                  :rules="[rules.required, rules.codiceFiscaleLunghezza]"
                  outlined
                />
              </v-col>
              <v-col cols="4" sm="4" md="4" lg="4" xl="4">
                <v-select
                  v-model="userData.anagrafica.sesso"
                  outlined
                  label="Sesso"
                  item-text="title"
                  item-value="value"
                  :items="sexValues"
                  :rules="[rules.required]"
                />
              </v-col>
              <v-col
                cols="8"
                sm="8"
                md="8"
                lg="6"
                offset-lg="2"
                xl="6"
                offset-xl="2"
              >
                <v-text-field
                  v-model="userData.telefono"
                  label="Telefono"
                  outlined
                />
              </v-col>
            </v-row>
            <v-divider />
            <v-row>
              <v-col cols="12">
                <h3 class="title">Residenza</h3>
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-text-field
                  v-model="userData.anagrafica.residenza.via"
                  outlined
                  label="Via"
                  :rules="[rules.required]"
                />
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-text-field
                  v-model="userData.anagrafica.residenza.citta"
                  outlined
                  label="Città"
                  :rules="[rules.required]"
                />
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-select
                  v-model="userData.anagrafica.residenza.provincia"
                  outlined
                  label="Provincia"
                  :items="provValues"
                  item-text="nome"
                  item-value="sigla"
                  :rules="[rules.required]"
                />
              </v-col>
            </v-row>
            <v-divider />
            <v-row>
              <v-col cols="12">
                <h3 class="title">Dati account</h3>
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-text-field
                  v-model="userData.email"
                  label="Email"
                  :rules="[rules.required, rules.email]"
                  outlined
                  type="email"
                />
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-text-field
                  v-model="otherData.email"
                  label="Conferma Email"
                  :rules="[rules.required, rules.email, rules.emailMatch]"
                  outlined
                  type="email"
                />
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-text-field
                  v-model="userData.password"
                  label="Password"
                  :rules="[rules.required]"
                  outlined
                  type="password"
                />
              </v-col>
              <v-col cols="12" sm="12" md="12" lg="6" xl="6">
                <v-text-field
                  v-model="otherData.password"
                  label="Conferma Password"
                  :rules="[rules.required, rules.passwordMatch]"
                  outlined
                  type="password"
                />
              </v-col>
              <v-col cols="6">
                <v-checkbox
                  v-model="privacy"
                  label="Accetto la privacy"
                  :rules="[rules.privacy]"
                />
              </v-col>
            </v-row>
          </v-container>
        </v-form>
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" outlined @click="submit()"> Iscriviti </v-btn>
          <v-spacer />
        </v-card-actions>
      </v-card>
    </v-col>
    <v-snackbar
      v-model="snackbar"
      timeout="5000"
      centered
      rounded
      color="error"
    >
      {{ errorMessage }}
    </v-snackbar>
    >
  </v-row>
</template>

<script>
export default {
  layout: 'outside',

  data() {
    return {
      menu: false,
      snackbar: false,
      errorMessage: null,
      privacy: false,
      // saving datas
      userData: {
        email: null,
        password: null,
        telefono: null,
        anagrafica: {
          nome: null,
          cognome: null,
          data_nascita: null,
          sesso: null,
          codice_fiscale: null,
          residenza: {
            via: null,
            citta: null,
            provincia: null,
          },
        },
      },
      otherData: {
        email: null,
        password: null,
        data: null,
      },
      // Option values
      sexValues: [
        { title: 'Maschio', value: 'M' },
        { title: 'Femmina', value: 'F' },
        { title: 'Altro', value: 'A' },
        { title: 'Preferisco non rispondere', value: 'N' },
      ],
      provValues: [
        {
          nome: 'Agrigento',
          sigla: 'AG',
        },
        {
          nome: 'Alessandria',
          sigla: 'AL',
        },
        {
          nome: 'Ancona',
          sigla: 'AN',
        },
        {
          nome: 'Arezzo',
          sigla: 'AR',
        },
        {
          nome: 'Ascoli Piceno',
          sigla: 'AP',
        },
        {
          nome: 'Asti',
          sigla: 'AT',
        },
        {
          nome: 'Avellino',
          sigla: 'AV',
        },
        {
          nome: 'Bari',
          sigla: 'BA',
        },
        {
          nome: 'Barletta-Andria-Trani',
          sigla: 'BT',
        },
        {
          nome: 'Belluno',
          sigla: 'BL',
        },
        {
          nome: 'Benevento',
          sigla: 'BN',
        },
        {
          nome: 'Bergamo',
          sigla: 'BG',
        },
        {
          nome: 'Biella',
          sigla: 'BI',
        },
        {
          nome: 'Bologna',
          sigla: 'BO',
        },
        {
          nome: 'Bolzano/Bozen',
          sigla: 'BZ',
        },
        {
          nome: 'Brescia',
          sigla: 'BS',
        },
        {
          nome: 'Brindisi',
          sigla: 'BR',
        },
        {
          nome: 'Cagliari',
          sigla: 'CA',
        },
        {
          nome: 'Caltanissetta',
          sigla: 'CL',
        },
        {
          nome: 'Campobasso',
          sigla: 'CB',
        },
        {
          nome: 'Carbonia-Iglesias',
          sigla: 'CI',
        },
        {
          nome: 'Caserta',
          sigla: 'CE',
        },
        {
          nome: 'Catania',
          sigla: 'CT',
        },
        {
          nome: 'Catanzaro',
          sigla: 'CZ',
        },
        {
          nome: 'Chieti',
          sigla: 'CH',
        },
        {
          nome: 'Como',
          sigla: 'CO',
        },
        {
          nome: 'Cosenza',
          sigla: 'CS',
        },
        {
          nome: 'Cremona',
          sigla: 'CR',
        },
        {
          nome: 'Crotone',
          sigla: 'KR',
        },
        {
          nome: 'Cuneo',
          sigla: 'CN',
        },
        {
          nome: 'Enna',
          sigla: 'EN',
        },
        {
          nome: 'Fermo',
          sigla: 'FM',
        },
        {
          nome: 'Ferrara',
          sigla: 'FE',
        },
        {
          nome: 'Firenze',
          sigla: 'FI',
        },
        {
          nome: 'Foggia',
          sigla: 'FG',
        },
        {
          nome: 'Forlì-Cesena',
          sigla: 'FC',
        },
        {
          nome: 'Frosinone',
          sigla: 'FR',
        },
        {
          nome: 'Genova',
          sigla: 'GE',
        },
        {
          nome: 'Gorizia',
          sigla: 'GO',
        },
        {
          nome: 'Grosseto',
          sigla: 'GR',
        },
        {
          nome: 'Imperia',
          sigla: 'IM',
        },
        {
          nome: 'Isernia',
          sigla: 'IS',
        },
        {
          nome: "L'Aquila",
          sigla: 'AQ',
        },
        {
          nome: 'La Spezia',
          sigla: 'SP',
        },
        {
          nome: 'Latina',
          sigla: 'LT',
        },
        {
          nome: 'Lecce',
          sigla: 'LE',
        },
        {
          nome: 'Lecco',
          sigla: 'LC',
        },
        {
          nome: 'Livorno',
          sigla: 'LI',
        },
        {
          nome: 'Lodi',
          sigla: 'LO',
        },
        {
          nome: 'Lucca',
          sigla: 'LU',
        },
        {
          nome: 'Macerata',
          sigla: 'MC',
        },
        {
          nome: 'Mantova',
          sigla: 'MN',
        },
        {
          nome: 'Massa-Carrara',
          sigla: 'MS',
        },
        {
          nome: 'Matera',
          sigla: 'MT',
        },
        {
          nome: 'Medio Campidano',
          sigla: 'VS',
        },
        {
          nome: 'Messina',
          sigla: 'ME',
        },
        {
          nome: 'Milano',
          sigla: 'MI',
        },
        {
          nome: 'Modena',
          sigla: 'MO',
        },
        {
          nome: 'Monza e della Brianza',
          sigla: 'MB',
        },
        {
          nome: 'Napoli',
          sigla: 'NA',
        },
        {
          nome: 'Novara',
          sigla: 'NO',
        },
        {
          nome: 'Nuoro',
          sigla: 'NU',
        },
        {
          nome: 'Ogliastra',
          sigla: 'OG',
        },
        {
          nome: 'Olbia-Tempio',
          sigla: 'OT',
        },
        {
          nome: 'Oristano',
          sigla: 'OR',
        },
        {
          nome: 'Padova',
          sigla: 'PD',
        },
        {
          nome: 'Palermo',
          sigla: 'PA',
        },
        {
          nome: 'Parma',
          sigla: 'PR',
        },
        {
          nome: 'Pavia',
          sigla: 'PV',
        },
        {
          nome: 'Perugia',
          sigla: 'PG',
        },
        {
          nome: 'Pesaro e Urbino',
          sigla: 'PU',
        },
        {
          nome: 'Pescara',
          sigla: 'PE',
        },
        {
          nome: 'Piacenza',
          sigla: 'PC',
        },
        {
          nome: 'Pisa',
          sigla: 'PI',
        },
        {
          nome: 'Pistoia',
          sigla: 'PT',
        },
        {
          nome: 'Pordenone',
          sigla: 'PN',
        },
        {
          nome: 'Potenza',
          sigla: 'PZ',
        },
        {
          nome: 'Prato',
          sigla: 'PO',
        },
        {
          nome: 'Ragusa',
          sigla: 'RG',
        },
        {
          nome: 'Ravenna',
          sigla: 'RA',
        },
        {
          nome: 'Reggio di Calabria',
          sigla: 'RC',
        },
        {
          nome: "Reggio nell'Emilia",
          sigla: 'RE',
        },
        {
          nome: 'Rieti',
          sigla: 'RI',
        },
        {
          nome: 'Rimini',
          sigla: 'RN',
        },
        {
          nome: 'Roma',
          sigla: 'RM',
        },
        {
          nome: 'Rovigo',
          sigla: 'RO',
        },
        {
          nome: 'Salerno',
          sigla: 'SA',
        },
        {
          nome: 'Sassari',
          sigla: 'SS',
        },
        {
          nome: 'Savona',
          sigla: 'SV',
        },
        {
          nome: 'Siena',
          sigla: 'SI',
        },
        {
          nome: 'Siracusa',
          sigla: 'SR',
        },
        {
          nome: 'Sondrio',
          sigla: 'SO',
        },
        {
          nome: 'Taranto',
          sigla: 'TA',
        },
        {
          nome: 'Teramo',
          sigla: 'TE',
        },
        {
          nome: 'Terni',
          sigla: 'TR',
        },
        {
          nome: 'Torino',
          sigla: 'TO',
        },
        {
          nome: 'Trapani',
          sigla: 'TP',
        },
        {
          nome: 'Trento',
          sigla: 'TN',
        },
        {
          nome: 'Treviso',
          sigla: 'TV',
        },
        {
          nome: 'Trieste',
          sigla: 'TS',
        },
        {
          nome: 'Udine',
          sigla: 'UD',
        },
        {
          nome: "Valle d'Aosta/Vallée d'Aoste",
          sigla: 'AO',
        },
        {
          nome: 'Varese',
          sigla: 'VA',
        },
        {
          nome: 'Venezia',
          sigla: 'VE',
        },
        {
          nome: 'Verbano-Cusio-Ossola',
          sigla: 'VB',
        },
        {
          nome: 'Vercelli',
          sigla: 'VC',
        },
        {
          nome: 'Verona',
          sigla: 'VR',
        },
        {
          nome: 'Vibo Valentia',
          sigla: 'VV',
        },
        {
          nome: 'Vicenza',
          sigla: 'VI',
        },
        {
          nome: 'Viterbo',
          sigla: 'VT',
        },
      ],
      // validation rules
      rules: {
        required: (value) => !!value || 'Campo obbligatorio',
        codiceFiscaleLunghezza: (value) =>
          (value || '').length === 16 || 'Campo incorretto',
        email: (value) => {
          const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
          return pattern.test(value) || 'Email non valida'
        },
        emailMatch: (value) =>
          value === this.userData.email || 'Le email non corrispondono',
        passwordMatch: (value) =>
          value === this.userData.password || 'Le password non corrispondono',
        privacy: (value) => value || 'Devi accettare i termini',
      },
    }
  },

  head() {
    return {
      title: 'Iscriviti',
    }
  },

  computed: {
    dataNascitaFormattata() {
      if (!this.otherData.data) return
      return this.$moment(this.otherData.data).format('DD/MM/YYYY')
    },

    maxDate() {
      return this.$moment().subtract(16, 'years').format('YYYY-MM-DD')
    },
  },

  watch: {
    menu(val) {
      val && setTimeout(() => (this.$refs.bday.activePicker = 'YEAR'))
    },

    'otherData.data': function (val) {
      if (val)
        this.userData.anagrafica.data_nascita = this.$moment(
          this.otherData.data,
          'YYYY-MM-DD'
        ).toDate()
    },
  },

  methods: {
    save(date) {
      this.$refs.menu.save(date)
    },

    submit() {
      if (this.$refs.form.validate()) {
        this.$axios.post('/api/auth/signup', this.userData).then((res) => {
          let data = res.data

          if (data.error) {
            if (data.code === 11000) this.errorMessage = data.error
            else this.errorMessage = data.error.message
            console.error(data.error)
            this.snackbar = true
          }
        })
      }
    },
  },
}
</script>

<style></style>
