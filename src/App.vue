<template>
  <div class="container">
    <form class="form" @submit.prevent="save">
      <input type="text" v-model="form.title" placeholder="Title" class="input"/><br />
      <textarea v-model="form.content" placeholder="Content" class="textarea"></textarea><br />

      <button type="submit" class="button">
        <div class="svg-wrapper-1">
          <div class="svg-wrapper">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="30" height="30" class="icon">
              <path d="M22,15.04C22,17.23 20.24,19 18.07,19H5.93C3.76,19 2,17.23 2,15.04C2,13.07 3.43,11.44 5.31,11.14C5.28,11 5.27,10.86 5.27,10.71C5.27,9.33 6.38,8.2 7.76,8.2C8.37,8.2 8.94,8.43 9.37,8.8C10.14,7.05 11.13,5.44 13.91,5.44C17.28,5.44 18.87,8.06 18.87,10.83C18.87,10.94 18.87,11.06 18.86,11.17C20.65,11.54 22,13.13 22,15.04Z"></path>
            </svg>
          </div>
        </div>
        <span>Save</span>
      </button>
    </form>

    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <strong>{{ article.title }}</strong><br />
        {{ article.content }}<br />

        <div class="button-container">
      <button @click="edit(article)" class="button2">Edit</button>
      <button @click="deleteArticle(article.id)" class="button2">Delete</button>
    </div>

      </li>
    </ul>

    <button @click="load" class="button1 load-button">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: '',
      title: '',
      content: '',
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id ? `http://localhost:3000/articles/${form.id}` : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';
        const response = await axios[method](url, { title: form.title, content: form.content });

        if (form.id) {
          articles.value = articles.value.map(article => article.id === response.data.id ? response.data : article);
        } else {
          articles.value.push(response.data);
        }

        resetForm();
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    function resetForm() {
      form.id = '';
      form.title = '';
      form.content = '';
    }

    async function deleteArticle(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, deleteArticle, load };
  }
};
</script>

<style scoped>
.container {
  max-width: 420px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
  background-color: rgba(192, 192, 192, 0.195);
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form {
  margin-bottom: 20px;
}

.input, .textarea {
  width: 90%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.button {
  padding: 10px 20px;
  background-color: #007BFF;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}

.button:hover {
  background-color: #0056b3;
}

.article-list {
  list-style-type: none;
  padding: 0;
}

.article-item {
  background-color: white;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 4px;
  margin-bottom: 10px;
}

.article-item strong {
  display: block;
  font-size: 18px;
  margin-bottom: 5px;
}

.load-button {
  display: block;
  width: 100%;
  margin-top: 20px;
  background-color: #28a745;
}

.load-button:hover {
  background-color: #218838;
}

button {
  font-family: inherit;
  font-size: 20px;
  background: #212121;
  color: white;
  fill: rgb(155, 153, 153);
  padding: 0.7em 1em;
  padding-left: 0.9em;
  display: flex;
  align-items: center;
  cursor: pointer;
  border: none;
  border-radius: 15px;
  font-weight: 1000;
}

button span {
  display: block;
  margin-left: 0.3em;
  transition: all 0.3s ease-in-out;
}

button svg {
  display: block;
  transform-origin: center center;
  transition: transform 0.3s ease-in-out;
}

button:hover {
  background: #000;
}

button:hover .svg-wrapper {
  transform: scale(1.25);
  transition: 0.5s linear;
}

button:hover svg {
  transform: translateX(1.2em) scale(1.1);
  fill: #fff;
}

button:hover span {
  opacity: 0;
  transition: 0.5s linear;
}

button:active {
  transform: scale(0.95);
}

.button1, .button1::after  {
  padding: 10px 50px;
  font-size: 20px;
  border: none;
  border-radius: 5px;
  color: white;
  background-color: rgb(0, 255, 179);
  position: relative;
}

.button1::after {
  --move1: inset(50% 50% 50% 50%);
  --move2: inset(31% 0 40% 0);
  --move3: inset(39% 0 15% 0);
  --move4: inset(45% 0 40% 0);
  --move5: inset(45% 0 6% 0);
  --move6: inset(14% 0 61% 0);
  clip-path: var(--move1);
  content: 'GLITCH';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: block;
}

.button1:hover::after {
  animation: glitch_4011 1s;
  text-shadow: 10 10px 10px black;
  animation-timing-function: steps(2, end);
  text-shadow: -3px -3px 0px #1df2f0, 3px 3px 0px #E94BE8;
  background-color: transparent;
  border: 3px solid rgb(0, 255, 213);
}

.button1:hover {
  text-shadow: -1px -1px 0px #1df2f0, 1px 1px 0px #E94BE8;
}

.button1:hover {
  background-color: transparent;
  border: 1px solid rgb(0, 255, 213);
  box-shadow: 0px 10px 10px -10px rgb(0, 255, 213);
}

@keyframes glitch_4011 {
  0% {
    clip-path: var(--move1);
    transform: translate(0px,-10px);
  }

  10% {
    clip-path: var(--move2);
    transform: translate(-10px,10px);
  }

  20% {
    clip-path: var(--move3);
    transform: translate(10px,0px);
  }

  30% {
    clip-path: var(--move4);
    transform: translate(-10px,10px);
  }

  40% {
    clip-path: var(--move5);
    transform: translate(10px,-10px);
  }

  50% {
    clip-path: var(--move6);
    transform: translate(-10px,10px);
  }

  60% {
    clip-path: var(--move1);
    transform: translate(10px,-10px);
  }

  70% {
    clip-path: var(--move3);
    transform: translate(-10px,10px);
  }

  80% {
    clip-path: var(--move2);
    transform: translate(10px,-10px);
  }

  90% {
    clip-path: var(--move4);
    transform: translate(-10px,10px);
  }

  100% {
    clip-path: var(--move1);
    transform: translate(0);
  }
}

/*==================================================================*/

.button2-container {
  display: flex;
  gap: 1px; /* Menambahkan jarak antara tombol */
}

.button2 {
  padding: 5px 10px;
  background-color: #007BFF;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  display: flex;
  align-items: center;
  display: inline-block;
  margin: 7px;
  margin-top: 30px;
}

.button2:hover {
  background-color: #0056b3;
}
</style>
