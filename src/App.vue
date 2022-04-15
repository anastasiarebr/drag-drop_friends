<template>
  <div id="app">
    <b-container>
      <b-row >
        <b-button
        class="mx-auto mt-5"
        variant="primary"
        @click="loadData()"
        >
        <div class="login">
        <img class="login__img" src="./assets/vk.svg" alt="vk">
        
        <span>Log In</span>
        </div>
        </b-button>
      </b-row>

      <b-row>
        <b-col>
          <div class="column border border-secondary p-1">
            <h2 class="title">Friend List</h2>
            <div
              class="board pb-5"
              @drop="onDrop($event, 1)"
              @dragenter.prevent
              @dragover.prevent
              >
              <div
                v-for="person in getList(1)"
                :id='person.id'
                class="d-flex align-items-center border border-secondary p-2 m-1"
                :draggable="true"
                @dragstart="dragStart"
                @dragover.stop
              >
                <b-avatar variant="info" :src="person.photo" class="mr-3"></b-avatar>
                <span class="mr-auto">{{person.first_name + ' ' + person.last_name }}</span>
              </div>
            </div>
          </div>
        </b-col>
        <b-col>
          <div class="column border border-secondary p-1">
            <h2 class="title">Selected List</h2>
            <div
              class="board_selected pb-5"
              @drop="onDrop($event, 2)"
              @dragenter.prevent
              @dragover.prevent
              >
              <div
                v-for="person in getList(2)"
                :id='person.id'
                class="d-flex align-items-center border border-secondary p-2 m-1"
                :draggable='true'
                @dragstart="dragStart"
                @dragover.stop
              >
                <b-avatar variant="info" :src="person.photo" class="mr-3"></b-avatar>
                <span class="mr-auto">{{person.first_name + ' ' + person.last_name }}</span>
              </div>
            </div>
          </div>
        </b-col>
      </b-row>
      <b-row>
        <b-button
        class="mx-auto mt-2 mb-5"
        variant="success"
        @click="submit"

        >Export to console</b-button>
      </b-row>
    </b-container>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      people: [],
    }
  },
  created() {
    const href = window.location.href

    if (href.includes('access_token')) {
      let arr = href.split('=');
      let token = arr[1].split('&')[0];

      fetch(`https://api.vk.com/method/friends.search?count=100&fields=photo_100&access_token=${token}&v=5.81`)
      .then((response) => {
        return response.json();
      })
      .then((data) => {
        this.people = data.response.items.map((item) => {
          return {
            id: item.id,
            first_name: item.first_name,
            last_name: item.last_name,
            photo: item.photo_100,
            list: 1,
            }
        });
      });
    }

    },
  methods: {
    loadData() {
      document.location = "https://oauth.vk.com/authorize?client_id=8137313&display=page&scope=friends&redirect_uri=http://localhost:8080&response_type=token&v=5.52"
    },
    getList(list) {
      return this.people.filter((item) => item.list === list)
    },
    dragStart(e) {
      const target = e.target;
      e.dataTransfer.dropEffect = 'move';
      e.dataTransfer.effectAllowed = 'move';
      e.dataTransfer.setData('card_id', target.id);
    },
    onDrop(e, list) {
      const card_id = e.dataTransfer.getData('card_id');
      const item = this.people.find((item) => item.id == card_id);
      item.list = list;
      let card = document.getElementById(card_id);
    },
    submit() {
      console.log(this.getList(2));
    },
  }
}
</script>

<style>
@import './style/style.css';
</style>
