<template>
  <v-container>
    <v-row class="cards-title">
      <v-col>
        <h1 class="text-center text-h1">Карточки</h1>
      </v-col>
    </v-row>


    <v-row class="delete-all-cards">
      <v-col>
        <v-btn color="red" @click="deleteAllCards()">Удалить все карточки</v-btn>
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
            <v-btn @click="deleteCard(card.id)">Удалить</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>



    <v-row class="create_or_edit_card">
      <v-col md="6">
        <h2 class="text-h2">Создать новую карточку</h2>
        <v-form ref="form" v-model="valid">
          <v-text-field
            v-model="title"
            :counter="255"
            label="Название"
            required
          ></v-text-field>
          <v-textarea
            v-model="description"
            :counter="255"
            label="Описание"
            required
          ></v-textarea>
          <v-checkbox type="checkbox" v-model="isDone" label="Выполнено"></v-checkbox>
        </v-form>
        <v-btn v-bind:disabled="!title || !description" @click="createCard()">Добавить карточку
        </v-btn>
      </v-col>

      <v-col md="6">
        <h2 class="text-h2">Редактировать карточку по ID</h2>
        <v-form ref="form" v-model="edit_valid">
          <v-text-field
            v-model="edit_id"
            label="ID Карточки"
            required
          ></v-text-field>
          <v-text-field
            v-model="edit_title"
            :counter="255"
            label="Название"
            required
          ></v-text-field>
          <v-textarea
            v-model="edit_description"
            :counter="255"
            label="Описание"
            required
          ></v-textarea>
          <v-checkbox v-model="edit_isDone" label="Выполнено"></v-checkbox>
        </v-form>
        <v-btn v-bind:disabled="!edit_title || !edit_description || !edit_id" @click="editCard(edit_title, edit_description, edit_isDone, edit_id)">Применить
        </v-btn>
      </v-col>
    </v-row>


    <v-row class="edit_card">
      <v-col></v-col>
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
    async editCard(edit_title, edit_description, edit_isDone, edit_id) {
      if (!this.edit_title || !this.edit_description) {
    this.edit_valid = false;
    return;
  }
  try {
    const response = await fetch(`http://localhost:3000/api/todos/${edit_id}`, {
      method: "PATCH",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        title: edit_title,
        description: edit_description,
        isDone: edit_isDone
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