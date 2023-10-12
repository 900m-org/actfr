<template>
  <div class="bodyPage">
    <div class="typeAction">
      <div class="filtre">
        <p class="Ok" style="color: white; font-weight: bold;">Type d'action :</p>
        <div class="typeActionBouton" :class="{ 'selected': selectedType === 'Informer' }"
          @click="filterByType('Informer')">S'informer</div>
        <div class="typeActionBouton" :class="{ 'selected': selectedType === 'Participer' }"
          @click="filterByType('Participer')">Participer</div>
        <div class="typeActionBouton" :class="{ 'selected': selectedType === 'Entreprendre' }"
          @click="filterByType('Entreprendre')">Entreprendre</div>
      </div>
      <div class="filtre">
        <p class="Ok" style="color: white; font-weight: bold;">Durée :</p>
        <div class="typeActionBouton" :class="{ 'selected': selectedTemps === '5' }" @click="filterByTemps('5')">5 minutes
        </div>
        <div class="typeActionBouton" :class="{ 'selected': selectedTemps === '20' }" @click="filterByTemps('20')">20
          minutes</div>
        <div class="typeActionBouton" :class="{ 'selected': selectedTemps === '45' }" @click="filterByTemps('45')">45
          minutes et +</div>

      </div>
    </div>

    <div class="resteAFaire">
      <p>Nouvelles actions :</p>
      <div class="resteAFaireBouton" @click="showAllAction">{{ countActionsAFaire }}</div>

      <p>Actions effectuées :</p>
      <div class="resteAFaireBouton" @click="showAllAction">{{ countActionsFaites }}</div>

      <p>Mis de côté: </p>
      <div class="resteAFaireBouton" @click="toggleMisDeCote">{{ misDeCote.length }}</div>
      <div v-if="showMisDeCote" class="misDeCoteContent">
        <div v-for="post in misDeCote" :key="post.id"></div>
      </div>
    </div>

    <div class="row d-flex justify-content-between" style="">
      <div v-for="post in filteredPosts" :key="post.id" class="col-md-4 col-sm-20 mb-5">
        <div class="card">
          <div class="card-body" :style="{ 'background-color': misDeCote.includes(post) ? '#FFCCCC' : '#DDECCB' }">
            <h3 class="card-title" style="font-weight: bold;">{{ post.title }}</h3>
            <div style="display: flex; flex-direction: row;">
              <p>
                <img src="../public/photos/temps(1).png" alt="" style="max-width: 20px; margin-right: 5px;" />
                {{ post.temps }} minutes
              </p>
              <div class="typeActionDescription">
                <p style="font-weight: bold; margin-top: 5%;">{{ post.type }}</p>
              </div>
            </div>
            <p class="card-text">{{ post.description }}</p>

            <!-- Affichage conditionnel du bouton Réinitialiser ou des boutons Fait/Plus tard -->
            <div v-if="misDeCote.includes(post)">
              <button class="btn btn-primary" @click="resetPostStatus(post)">Réinitialiser</button>
            </div>
            <div v-else>
  <a href="#" :class="['btn', post.isDone ? 'fait' : 'a-faire', 'action']" @click="togglePostStatus(post)">
    {{ post.isDone ? 'Fait' : 'A faire' }}
  </a>
  <!-- BOUTON PLUS TARD -->
  <a href="#" class="btn btn-warning" @click="moveToLater(post)" style="margin-left: 10px !important;" v-if="!post.isDone">
    Plus tard
  </a>
</div>

          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Cookies from 'js-cookie';

export default {
  data() {
    return {
      loading: false,
      posts: [],
      misDeCote: [],
      error: null,
      id: null,
      selectedType: '', // Nouvelle propriété pour stocker le type d'action sélectionné
      selectedTemps: '', // Nouvelle propriété pour stocker le temps sélectionné
      showMisDeCote: false,

    };
  },
  methods: {
    resetPostStatus(post) {
      const index = this.misDeCote.indexOf(post);
      if (index !== -1) {
        this.misDeCote.splice(index, 1);
        this.posts.push(post); // Ajouter l'action réinitialisée à this.posts
      }
      Cookies.set('misDeCote', JSON.stringify(this.misDeCote));
    },
    filterByType(type) {
      if (this.selectedType === type) {
        this.selectedType = ''; // Réinitialiser le filtre s'il est déjà sélectionné
      } else {
        this.selectedType = type;
      }
    },
    filterByTemps(temps) {
      if (this.selectedTemps === temps) {
        this.selectedTemps = ''; // Réinitialiser le filtre s'il est déjà sélectionné
      } else {
        this.selectedTemps = temps;
      }
    },
    togglePostStatus(post) {
      post.isDone = !post.isDone;
      Cookies.set(`post_${post.id}`, post.isDone ? 'Fait' : 'A faire');
      this.updateCounters();
    },
    moveToLater(post) {
  // Trouver l'index de l'élément dans le tableau des posts
  const index = this.posts.indexOf(post);
  // Vérifier si l'élément existe dans le tableau des posts
  if (index !== -1) {
    // Retirer l'élément du tableau des posts
    this.posts.splice(index, 1);
    // Ajouter l'élément au tableau des éléments mis de côté
    this.misDeCote.push(post);
    // Mettre à jour le compteur d'actions à faire et d'actions effectuées
    this.updateCounters();

    // Sauvegarder la liste des actions mises de côté dans un cookie
    Cookies.set('misDeCote', JSON.stringify(this.misDeCote));

    // Sauvegarder la liste des actions à faire dans un cookie (après avoir retiré l'action)
    const actionsAFaire = this.posts.filter((post) => !post.isDone);
    Cookies.set('actionsAFaire', JSON.stringify(actionsAFaire));
  }
},
    toggleMisDeCote() {
      this.showMisDeCote = !this.showMisDeCote;
    },
    updateCounters() {
      const actionsAFaire = this.posts.filter((post) => !post.isDone).length;
      const actionsFaites = this.posts.filter((post) => post.isDone).length;
      const countActionsMiseDeCote = this.misDeCote.length;

      // Sauvegarder les compteurs dans des cookies
      Cookies.set('countActionsAFaire', actionsAFaire);
      Cookies.set('countActionsFaites', actionsFaites);
      Cookies.set('countActionsMiseDeCote', countActionsMiseDeCote);
    },
  },
  computed: {
    filteredPosts() {
      if (this.showMisDeCote) {
        return this.misDeCote; // Afficher seulement les posts mis de côté si "Mis de côté" est cliqué
      } else if (!this.selectedType && !this.selectedTemps) {
        return this.posts; // Si aucun type et aucun temps n'est sélectionné, renvoyer tous les posts
      } else {
        return this.posts.filter((post) => {
          if (this.selectedType && this.selectedTemps) {
            return post.type === this.selectedType && post.temps === this.selectedTemps;
          } else if (this.selectedType) {
            return post.type === this.selectedType;
          } else {
            return post.temps === this.selectedTemps;
          }
        });
      }
    },
    countActionsAFaire() {
      return this.posts.filter((post) => !post.isDone).length;
    },
    countActionsFaites() {
      return this.posts.filter((post) => post.isDone).length;
    },
  },
  async created() {
  try {
    this.loading = true;
    const response = await axios.get('https://my-json-server.typicode.com/900m-org/db/posts');
    this.loading = false;

    // Map response data and set the `isDone` property based on the cookies
    this.posts = response.data.map((post) => {
      const cookieValue = Cookies.get(`post_${post.id}`);
      if (cookieValue === 'Fait') {
        post.isDone = true;
      } else {
        post.isDone = false;
      }
      return post;
    });

    // Load misDeCote from cookies
    const misDeCoteCookie = Cookies.get('misDeCote');
    if (misDeCoteCookie) {
      // Filter out any items that are already in this.misDeCote to avoid duplicates
      const misDeCoteFromCookie = JSON.parse(misDeCoteCookie);
      this.misDeCote = misDeCoteFromCookie.filter(
        (item) => !this.misDeCote.some((existingItem) => existingItem.id === item.id)
      );
    }

    // Filter out actions from this.posts that are in this.misDeCote to avoid duplicates
    this.posts = this.posts.filter(
      (post) => !this.misDeCote.some((item) => item.id === post.id)
    );

    // Update counters once data is loaded
    this.updateCounters();
  } catch (error) {
    this.loading = false;
    this.error = `Une erreur s'est produite : ${error.message}`;
  }

  const params = new URLSearchParams(window.location.search);
  this.id = params.get('id');
}

};
</script>


<style>
@media only screen and (min-width: 767px) {

  .bodyPage {
    width: 100vw;
    margin-top: 5px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    font-size: 2vh;
  }

  .typeAction {
    width: 90vw;
    height: 20vh;
    background-color: rgb(49, 104, 54);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    color: black;
    font-weight: bold;
    border-radius: 20px;
    box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.13);
  }

  .filtre {
    width: 90vw;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    margin-top: 1%;
  }

  .typeActionBouton {
    width: 150px;
    height: 40px;
    background-color: rgb(221, 236, 203);
    display: flex;
    justify-content: center;
    align-items: center;
    color: black;
    font-weight: bold;
    border-radius: 10px;
    margin-left: 5%;
    box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.13);
  }

  .typeActionBouton:hover {
    background-color: rgb(53, 119, 58);
    color: white;
  }

  .selected {
    background-color: #4caf50;
    /* Nouvelle couleur de fond */
    color: white;
    /* Nouvelle couleur du texte */
  }

  .resteAFaire {
    margin-top: 2%;
    width: 90vw;
    height: 20vh;
    display: flex;
    flex-direction: row;

  }

  .resteAFaireBouton {
    width: 30px;
    height: 30px;
    background-color: rgb(221, 236, 203);
    display: flex;
    justify-content: center;
    align-items: center;
    color: black;
    font-weight: bold;
    border-radius: 10px;
    margin-left: 2%;
    margin-right: 2%;
    box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.13);
  }

  .typeActionDescription {
    width: 150px;
    height: 40px;
    background-color: rgb(49, 104, 54);
    display: flex;
    justify-content: center;
    align-items: center;
    color: white;
    font-weight: bold;
    border-radius: 10px;
    margin-left: 5%;
    box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.13);
  }


  .btn.fait {
    background-color: rgba(0, 128, 0, 0.50);
    color: white;
  }

  /* Styles pour le bouton "A faire" */
  .btn.a-faire {
    background-color: rgb(49, 104, 54);
    color: white;
  }

  .card-body {
    width: 300px !important;


  }

}

@media only screen and (max-width: 767px) {
  .bodyPage {
    width: 100vw;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }

  .typeAction {
    width: 50vw;
    height: 60vh;
    margin-top: 10px;
    background-color: rgb(49, 104, 54);
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
    color: black;
    font-weight: bold;
  }

  .typeActionBouton {
    width: 40vw;
    height: 40px;
    background-color: rgb(221, 236, 203);
    display: flex;
    justify-content: center;
    align-items: center;
    color: black;
    font-weight: bold;
    margin-top: 5%;
    border-radius: 10px;
    margin-left: 5%;
  }

  .typeActionBouton:hover {
    background-color: rgb(49, 104, 54);
    color: white;
  }

  .selected {
    background-color: #4caf50;
    /* Nouvelle couleur de fond */
    color: white;
    /* Nouvelle couleur du texte */
  }

  .resteAFaire {
    margin-top: 2%;
    width: 70vw;
    height: 20vh;
    display: flex;
    flex-direction: row;

  }

  .resteAFaireBouton {
    width: 50px;
    height: 30px;
    background-color: rgb(221, 236, 203);
    display: flex;
    justify-content: center;
    align-items: center;
    color: black;
    font-weight: bold;
    border-radius: 10px;
    margin-left: 2%;
    margin-right: 2%;
  }

  .typeActionDescription {
    width: 150px;
    height: 40px;
    background-color: rgb(49, 104, 54);
    display: flex;
    justify-content: center;
    align-items: center;
    color: white;
    font-weight: bold;
    border-radius: 10px;
    margin-left: 5%;
  }

  .btn.fait {
    background-color: rgba(0, 128, 0, 0.50);
    color: white;
  }

  /* Styles pour le bouton "A faire" */
  .btn.a-faire {
    background-color: rgb(49, 104, 54);
    color: white;
  }
}</style>