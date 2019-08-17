<template>
  <div id="container">
    <h3>Cadastrar Funcionário</h3>
    <div id="content">
      <v-form
        ref="form"
        v-model="valid"
        :lazy-validation="lazy">
        <v-text-field
          v-model="employer.firstname"
          :rules="firstnameRules"
          label="Nome"
          required>
        </v-text-field>

        <v-text-field
          v-model="employer.lastname"
          :rules="lastnameRules"
          label="Sobrenome"
          required>
        </v-text-field>

        <v-text-field
          v-model="employer.email"
          :rules="emailRules"
          label="E-mail"
          @input="findEmailAlreadyRegistred"
          required>
        </v-text-field>

        <v-text-field
          v-model="employer.pis"
          :rules="pisRules"
          label="Pis"
          required>
        </v-text-field>

        <v-btn
          :disabled="!valid"
          color="success"
          class="mr-4"
          @click="cadastrar">
          Cadastrar
        </v-btn>

        <v-btn
          color="error"
          class="mr-4"
          >
          Resetar
        </v-btn>
      </v-form>
    </div>  

    <v-data-table id="table"
      :headers="headers"
      :items="employers"
      :items-per-page="5"
      class="elevation-1">

      <template v-slot:item.acao="{ item }">
        <v-icon
          small
          class="mr-2"
          @click="edit(item)">
          edit
        </v-icon>
        <v-icon
          small
          >
          delete
        </v-icon>
      </template>

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
    employer: new Employer(),
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
        { text: 'Ações', value: 'acao'}
      ],
    valid: true,
    firstnameRules: [
      v => !!v || 'Nome é obrigatório',
      v => (v && (v.length > 2 && v.length <= 30)) || 'Nome deve ter entre 2 e 30 caracteres',
    ],
    lastnameRules: [
      v => !!v || 'Sobrenome é obrigatório',
      v => (v && (v.length > 2 && v.length <= 30)) || 'Sobrenome deve ter entre 2 e 30 caracteres',
    ],
    emailRules: [
      v => !!v || 'Email é obrigatório',
      v => /.+@.+\..+/.test(v) || 'E-mail inválido',
    ],
    pisRules: [
      v => !!v || 'Pis é obrigatório',
      v => /\d/.test(v) || 'Somente números',
      v => !!v && v.length === 11 || 'Deve conter 11 números' 
    ],
    select: null,
    lazy: false,
  }
  ),
  methods: {
    testEmail(v:any) {
      return /.+@.+\..+/.test(v);
    },
    cadastrar() {
      axios.post(API.employers, this.employer).then(employers => this.employers = employers.data.employers);
    },
    reset() {
      this.$refs.form.reset();
    },
    edit(item:Employer){
      this.employer = item;
    },
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
  },
  watch: {
    employers(){
      const fn = (a:Employer , b:Employer ) =>  a.firstname < b.firstname ? -1 : a.firstname > b.firstname ? 1 : 0;
      const copyEmployers = <Array<Employer>> Object.assign([],this.employers);
      return copyEmployers.sort(fn);
    }
  }
  
});
</script>

<style scoped>
  #container{
    margin: 0 10px;
    padding: 40px 0px;
  }
  #content{
    display: flex;
    flex-wrap: wrap;
  }
  #table{
    margin: 20px 0px;
  }
  h3 {
    text-align: center;
  }

  form{
    width: 80%;
    margin: 0px auto;
    text-align: center
  }
</style>
