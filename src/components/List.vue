<template>
    <v-container>
      <v-row class="cards-title">
        <v-col>
          <h1 class="text-center text-h1">Карточки</h1>
        </v-col>
      </v-row>
      <v-row class="list_cards">
        <v-col md="12" v-for="card in cardsList" :key="card.id" class="card">
          <v-card>
            <v-card-title>
              <h3 class="text-h4"><b>Название:</b> {{ card.title }}</h3>
            </v-card-title>
            <v-card-text>
              <p class="text-body-1"><b>Выполнено:</b> {{ card.description }}</p>
              <p class="text-body-1"><b>Описание:</b> {{ card.isDone }}</p>
              <p class="text-body-1"><b>ID Карточки:</b> {{ card.id }}</p>
            </v-card-text>
            <v-card-actions>
              <v-btn variant="outlined" v-on:click="deleteCard(card.id)">
                <b>Удалить</b>
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>  
    </v-container>
  </template>


  <script>
  export default {
    name: 'List-cards',
    data() {
      return {
        cardsList: [],
      };
    },
    methods:{
      fetchCards() {
        const data = fetch('http://localhost:3000/api/todos', {
          method: 'GET'
        })
            .then((response) => response.json())
            .then((data) => {
              this.cardsList = data;
            });
        console.log(data)
      },
      deleteCard(id){
        const index = this.cardsList.findIndex(card => card.id === id);
        if (index !== -1) {
          this.cardsList.splice(index, 1);
        }
      },
    },
    created() {
        this.fetchCards();
    },
  }
  </script>