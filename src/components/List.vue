<template>
  <v-container>
    <v-row class="cards-title">
      <v-col>
        <h1 class="text-center text-h1">Карточки</h1>
      </v-col>
    </v-row>
    <v-row class="delete-all-cards">
      <v-col>
        <v-btn color="red" @click="deleteAllCards()">Удалить все</v-btn>
      </v-col>
    </v-row>
    <v-row class="list_cards">
      <v-col md="12" v-for="card in cardsList" :key="card.id" class="card">
        <v-card>
          <v-card-title>
            <h3 class="text-h4"><b>Название:</b> {{ card.title }}</h3>
          </v-card-title>
          <v-card-text>
            <p class="text-body-1"><b>Выполнено:</b> {{ card.isDone }}</p>
            <p class="text-body-1"><b>Описание:</b> {{ card.description }}</p>
            <p class="text-body-1"><b>ID Карточки:</b> {{ card.id }}</p>
          </v-card-text>
          <v-card-actions>
            <v-btn v-on:click="deleteCard(card.id)">
              <b>Удалить</b>
            </v-btn>
            <v-btn v-on:click="editCard(card.id)">
              <b>Редактировать</b>
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
    <v-row class="create_card">
      <v-col md="4">
        <h2 class="text-h2">Создать новую карточку</h2>
        <v-form ref="form" v-model="valid">
          <v-text-field
            v-model="title"
            :counter="30"
            label="Название"
            required
          ></v-text-field>
          <v-textarea
            v-model="description"
            :counter="300"
            label="Описание"
            required
          ></v-textarea>
          <v-checkbox v-model="isDone" label="Выполнено"></v-checkbox>
        </v-form>
        <v-btn v-bind:disabled="!title || !description" @click="createCard()">Добавить карточку
        </v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "CardList",
  data() {
    return {
      cardsList: [],
      title: "",
      description: "",
      isDone: false,
      valid: true,
    };
  },
  mounted() {
    this.getCards();
  },
  methods: {
    async getCards() {
      try {
        const response = await fetch("http://localhost:3000/api/todos");
        const data = await response.json();
        this.cardsList = data;
      } catch (error) {
        console.log(error);
      }
    },
    async deleteCard(id) {
      const index = this.cardsList.findIndex((card) => card.id === id);
      try {
        await fetch(`http://localhost:3000/api/todos/${id}`, {
          method: "DELETE",
        });
        this.cardsList.splice(index, 1);
        this.getCards();
      } catch (error) {
        console.log(error);
      }
    },
    async deleteAllCards() {
      try {
        await fetch("http://localhost:3000/api/todos", {
          method: "DELETE",
        });
        this.cardsList = [];
        this.getCards();
      } catch (error) {
        console.log(error);
      }
    },
    async editCard(card) {
  try {
    const response = await fetch(`http://localhost:3000/api/todos/${card.id}`, {
      method: "PATCH",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        title: card.title,
        description: card.description,
        isDone: card.isDone
      })
    });
    const updatedCard = await response.json();
    const index = this.cardsList.findIndex(c => c.id === updatedCard.id);
    this.cardsList.splice(index, 1, updatedCard);
  } catch (error) {
    console.log(error);
  }
},
async createCard() {
  if (!this.title || !this.description) {
    this.valid = false;
    return;
  }
  try {
    await fetch("http://localhost:3000/api/todos", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        title: this.title,
        description: this.description,
        isDone: this.isDone,
      }),
    });
    this.title = "";
    this.description = "";
    this.isDone = false;
    this.valid = true;
    this.getCards();
  } catch (error) {
    console.log(error);
  }
},
  },
};
</script>