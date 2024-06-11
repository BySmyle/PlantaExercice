<template>
  <div class="container">
    <div class="navbar">
      <button @click="redirectToGmail">Gmail</button>
      <button @click="toggleExercises">{{ showExercises ? 'Cacher les exercices' : 'Montrer les exercices' }}</button>
      <button @click="toggleAdminLogin">Administration</button>
    </div>
    <div v-if="showAdminLogin" class="admin-login">
      <h2>Admin Login</h2>
      <form @submit.prevent="login">
        <div>
          <label for="username">Username:</label>
          <input type="text" v-model="username" id="username" required />
        </div>
        <div>
          <label for="password">Password:</label>
          <input type="password" v-model="password" id="password" required />
        </div>
        <button class="button-19" type="submit">Login</button>
        <p v-if="loginError" class="error">{{ loginError }}</p>
      </form>
    </div>
    <div class="draw-area">
      <h1>Vue.js 2 with Konva</h1>
      <div class="stage-container" ref="stageContainer"></div>
    </div>
    <div class="shape-buttons">
      <button @click="addRectangle">Add Rectangle</button>
      <button @click="addCircle">Add Circle</button>
      <button @click="addTriangle">Add Triangle</button>
      <button v-if="isAuthenticated" @click="showAddExercise = true">Add Exercise</button>
    </div>
    <div v-if="showAddExercise" class="add-exercise">
      <h2>Add Exercise</h2>
      <form @submit.prevent="submitExercise">
        <div>
          <label for="imageLink">Image Link:</label>
          <input type="text" v-model="newExerciseLink" id="imageLink" required />
        </div>
        <button class="button-19" type="submit">Submit</button>
      </form>
    </div>
    <div class="action-buttons">
      <button @click="resetCanvas">Reset Canvas</button>
      <button @click="downloadCanvas">Download Canvas</button>
    </div>
    <div v-if="showExercises" class="exercise-buttons">
      <ExerciseButtons :images="images" @add-image="addImage" />
    </div>
  </div>
</template>

<script>
import Konva from 'konva';
import ExerciseButtons from '../components/ExerciseButtons.vue';

export default {
  name: 'HomeView',
  components: {
    ExerciseButtons
  },
  data() {
    return {
      images: [],
      showExercises: true,
      showAdminLogin: false,
      isAuthenticated: false,
      showAddExercise: false,
      username: '',
      password: '',
      loginError: '',
      newExerciseLink: ''
    };
  },
  methods: {
    addRectangle() {
      const stage = this.stage;
      const layer = stage.findOne('.layer');
      const rect = new Konva.Rect({
        x: 50,
        y: 50,
        width: 100,
        height: 50,
        fill: 'red',
        draggable: true,
      });
      layer.add(rect);
      layer.batchDraw();
    },
    addCircle() {
      const stage = this.stage;
      const layer = stage.findOne('.layer');
      const circle = new Konva.Circle({
        x: 200,
        y: 100,
        radius: 50,
        fill: 'blue',
        draggable: true,
      });
      layer.add(circle);
      layer.batchDraw();
    },
    addTriangle() {
      const stage = this.stage;
      const layer = stage.findOne('.layer');
      const triangle = new Konva.RegularPolygon({
        x: 300,
        y: 150,
        sides: 3,
        radius: 50,
        fill: 'green',
        draggable: true,
      });
      layer.add(triangle);
      layer.batchDraw();
    },
    addImage(imagePath) {
      const stage = this.stage;
      const layer = stage.findOne('.layer');
      const imageObj = new Image();
      imageObj.src = imagePath;
      imageObj.onload = () => {
        const konvaImage = new Konva.Image({
          x: 50,
          y: 50,
          image: imageObj,
          width: 200,
          height: 200,
          draggable: true,
        });
        layer.add(konvaImage);
        layer.batchDraw();
      };
    },
    resetCanvas() {
      const stage = this.stage;
      const layer = stage.findOne('.layer');
      layer.destroyChildren();
      stage.batchDraw();
    },
    downloadCanvas() {
      const stage = this.stage;
      const dataURL = stage.toDataURL();
      const downloadLink = document.createElement('a');
      downloadLink.href = dataURL;
      downloadLink.download = 'canvas.png';
      document.body.appendChild(downloadLink);
      downloadLink.click();
      document.body.removeChild(downloadLink);
    },
    redirectToGmail() {
      window.location.href = 'https://mail.google.com';
    },
    toggleExercises() {
      this.showExercises = !this.showExercises;
    },
    toggleAdminLogin() {
      this.showAdminLogin = !this.showAdminLogin;
    },
    login() {
      if (this.username === 'FlorexAdmin' && this.password === 'E5Mdp020') {
        this.isAuthenticated = true;
        this.showAdminLogin = false;
        this.loginError = '';
      } else {
        this.loginError = 'Invalid username or password';
      }
    },
    submitExercise() {
      this.images.push({ path: this.newExerciseLink, name: `Exercice ${this.images.length + 1}` });
      this.showAddExercise = false;
      this.newExerciseLink = '';
    }
  },
  mounted() {
    const stage = new Konva.Stage({
      container: this.$refs.stageContainer,
      width: 500,
      height: 500,
    });
    this.stage = stage;
    const layer = new Konva.Layer({ name: 'layer' });
    stage.add(layer);
    const canvasElement = this.$refs.stageContainer.querySelector('canvas');
    canvasElement.style.border = '2px solid #ccc';

    const images = require.context('@/assets/images', false, /\.(png|jpe?g|gif|svg)$/);
    this.images = images.keys().map((key, index) => ({
      path: images(key),
      name: `Exercice ${index + 1}`
    }));
  },
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.navbar {
  display: flex;
  justify-content: space-between;
  width: 100%;
  padding: 10px;
  background-color: #42b983;
}

.draw-area {
  margin-top: 20px;
}

.shape-buttons {
  margin-top: 20px;
}

.action-buttons {
  margin-top: 20px;
}

.exercise-buttons {
  margin-top: 20px;
}

.admin-login {
  margin-top: 20px;
}

.admin-login form {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.admin-login label,
.admin-login input {
  margin-bottom: 10px;
}

.add-exercise {
  margin-top: 20px;
}

.add-exercise form {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.add-exercise label,
.add-exercise input {
  margin-bottom: 10px;
}

.error {
  color: red;
  margin-top: 10px;
}

button {
  appearance: button;
  background-color: #1899D6;
  border: solid transparent;
  border-radius: 16px;
  border-width: 0 0 4px;
  box-sizing: border-box;
  color: #FFFFFF;
  cursor: pointer;
  display: inline-block;
  font-family: din-round, sans-serif;
  font-size: 15px;
  font-weight: 700;
  letter-spacing: .8px;
  line-height: 20px;
  margin: 0;
  outline: none;
  overflow: visible;
  padding: 13px 16px;
  text-align: center;
  text-transform: uppercase;
  touch-action: manipulation;
  transform: translateZ(0);
  transition: filter .2s;
  user-select: none;
  -webkit-user-select: none;
  vertical-align: middle;
  white-space: nowrap;
  max-width: 70%;
}

button:after {
  background-clip: padding-box;
  background-color: #1CB0F6;
  border: solid transparent;
  border-radius: 16px;
  border-width: 0 0 4px;
  bottom: -4px;
  content: "";
  left: 0;
  position: absolute;
  right: 0;
  top: 0;
  z-index: -1;
}

button:main,
button:focus {
  user-select: auto;
}

button:hover:not(:disabled) {
  filter: brightness(1.1);
  -webkit-filter: brightness(1.1);
}

button:disabled {
  cursor: auto;
}

button:active {
  border-width: 4px 0 0;
  background: none;
}
</style>
