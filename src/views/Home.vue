<template>
  <div>
    <v-form
      ref="form"
      v-model="valid"
      :lazy-validation="lazy">
      <v-text-field
        v-model="firstname"
        :rules="firstnameRules"
        label="Nome"
        required>
      </v-text-field>

      <v-text-field
        v-model="lastname"
        :rules="lastnameRules"
        label="Sobrenome"
        required>
      </v-text-field>

      <v-text-field
        v-model="email"
        :rules="emailRules"
        label="E-mail"
        @input="findEmailAlreadyRegistred"
        required>
      </v-text-field>

      <v-text-field
        v-model="pis"
        :rules="pisRules"
        label="Pis"
        required>
      </v-text-field>

      <v-btn
        :disabled="!valid"
        color="success"
        class="mr-4"
        >
        Cadastrar
      </v-btn>

      <v-btn
        color="error"
        class="mr-4"
        >
        Resetar
      </v-btn>
    </v-form>

    <v-data-table
      :headers="headers"
      :items="employers"
      :items-per-page="5"
      class="elevation-1">
    </v-data-table>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import axios from 'axios';
import Employer from '../model/employer';
import API from '../model/API';

export default Vue.extend({
  data: () => ({
    employers: [],
    headers: [
        {
          text: 'Nome',
          align: 'left',
          sortable: false,
          value: 'firstname',
        },
        { text: 'Sobrenome', value: 'lastname'},
        { text: 'Email', value: 'email'},
        { text: 'Pis', value: 'pis'},
      ],
    valid: true,
    firstname: '',
    lastname: '',
    firstnameRules: [
      v => !!v || 'Nome é obrigatório',
      v => (v && (v.length > 2 && v.length <= 30)) || 'Nome deve ter entre 2 e 30 caracteres',
    ],
    lastnameRules: [
      v => !!v || 'Sobrenome é obrigatório',
      v => (v && (v.length > 2 && v.length <= 30)) || 'Sobrenome deve ter entre 2 e 30 caracteres',
    ],
    email: '',
    emailRules: [
      v => !!v || 'Email é obrigatório',
      v => /.+@.+\..+/.test(v) || 'E-mail inválido',
    ],
    pis: '',
    pisRules: [
      v => !!v || 'Pis é obrigatório',
      v => /\d/.test(v) || 'Somente números',
    ],
    select: null,
    lazy: false,
  }
  ),
  methods: {
    testEmail(v:any) {
      return /.+@.+\..+/.test(v);
    },
    // cadastrar () {
    //   if (this.$refs.form.validate()) {
    //     this.snackbar = true;
    //   }
    // },
    // reset () {
    //   this.$refs.form.reset();
    // },

    findEmailAlreadyRegistred(event: string){
      if(this.testEmail(event)){
        axios.get(`http://localhost:8085/employers/email-already-registered?query=${event}`)
        .then(resp => console.log(resp));
      }
    }
  },
  beforeCreate(){
    axios.get(API.employers).then(employers => {
      this.$data.employers = employers.data.employers;
    });
  }
  
});
</script>
