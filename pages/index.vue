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
      <div class="boutonreste">
        <p :class="{ 'bold-text': showMisDeCote }" class=" action2">Nouvelles actions :</p>
        <div class="resteAFaireBouton" @click="showAllAction"> {{
          countActionsAFaire }}</div>
      </div>

      <div class="boutonreste">
        <p :class="{
  'bold-text': showMisDeCote && !(countActionsFaites >= 1),
  'bold-text2': countActionsFaites >= 1 && !showMisDeCote
}" class="action">Actions effectuées :</p>
  <div class="resteAFaireBouton" @click="showAllAction">{{ countActionsFaites }}</div>
</div>

      <div class="boutonreste" :class="{ 'active': showMisDeCote }">
        <p>Mis de côté: </p>
        <div class="resteAFaireBouton" @click="toggleMisDeCote">{{ misDeCote.length }}</div>

        <div v-if="showMisDeCote" class="misDeCoteContent">
          <div v-for="post in misDeCote" :key="post.id"></div>
        </div>
      </div>

    </div>


    <div class="row d-flex justify-content-between" style="margin: auto; width: 80%;">
      <div v-for="post in filteredPosts" :key="post.id"
        class="col-md-4 col-sm-20 mb-5 d-flex align-items-space-around justify-content-space-around" style="margin-top: -25px ;">
        <div class="card" @click="cardClicked = post">
          <div class="card-body" :style="{ 'background-color': misDeCote.includes(post) ? '#FFCCCC' : '#DDECCB' }">
            <h3 class="card-title" style="font-weight: bold;">{{ post.title }}</h3>
            <div style="display: flex; flex-direction: row;">
              <p>
                <img src="../public/photos/temps(1).png" alt="" style="max-width: 20px; margin-right: 5px;" />
                {{ post.temps }} minutes
              </p>
              <div class="typeActionDescription">
                <p style="font-weight: bold; margin :auto">{{ post.type }}</p>
              </div>
            </div>
            <p class="card-text">{{ post.description }}</p>

            <!-- Affichage conditionnel du bouton Réinitialiser ou des boutons Fait/Plus tard -->
            <div v-if="misDeCote.includes(post)">
              <button class="btn btn-primary" @click.stop="resetPostStatus(post)">Réinitialiser</button>
            </div>
            <div v-else>
              <a href="#" :class="['btn', post.isDone ? 'fait' : 'a-faire', 'action3']"
                @click.stop="togglePostStatus(post)">
                {{ post.isDone ? 'A faire' : 'Fait' }}
              </a>
              <!-- BOUTON PLUS TARD -->
              <a href="#" class="btn btn-warning" @click.stop="moveToLater(post)"
                style="margin-left: 10px !important; font-weight: bold; color: white;" v-if="!post.isDone">
                Plus tard
              </a>
            </div>

          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="modal" v-if="cardClicked">
    <div class="modal-content">
      <!-- Contenu agrandi de la carte -->
      <h3 class="card-title" style="font-weight: bold;">{{ cardClicked.title }}</h3>
      <div style="display: flex; flex-direction: row;">
        <p>
          <img src="../public/photos/temps(1).png" alt="" style="max-width: 20px; margin-right: 5px;" />
          {{ cardClicked.temps }} minutes
        </p>
        <div class="typeActionDescription">
          <p style="font-weight: bold; margin-top: 5%;">{{ cardClicked.type }}</p>
        </div>
      </div>
      <p style="font-weight: bold; color:black">Pour quoi Faire :</p>
      <p class="card-text">{{ cardClicked.description }}</p>
      <!-- CONDITION 1  -->
      <div v-if="cardClicked.comment != ''" style="">
        <p style="font-weight: bold; color:black; margin-bottom: 0px;">Comment Faire :</p>
        <p style="margin-bottom: 10px" class="card-text">{{ cardClicked.comment }}</p>
      </div>
      <div v-else>
      </div>

      <!-- CONDITION 2 -->
      <div v-if="cardClicked.lien != ''">
        <p style="font-weight: bold; color:black; margin-bottom: 0px;">Lien :</p>
        <a  :href="cardClicked.lien" target="_blank" rel="noopener noreferrer">{{ cardClicked.lien }}</a>
      </div>
      <div v-else>
      </div>
      <button style="margin-top: 10px;" class="btn btn-primary" @click="cardClicked = null">Fermer</button>
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
      isMisDeCoteActive: false, 
      posts: [],
      misDeCote: [],
      error: null,
      id: null,
      selectedType: '', // Nouvelle propriété pour stocker le type d'action sélectionné
      selectedTemps: '', // Nouvelle propriété pour stocker le temps sélectionné
      showMisDeCote: false,
      cardClicked: null,

    };
  },
  methods: {
    showAllAction() {
      this.showMisDeCote = false;

    },
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
      this.showMisDeCote = !this.showMisDeCote;},

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
      const response = await axios.get('https://my-json-server.typicode.com/900m-org/actdbfr/posts');
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
  .modal {
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.5);
  }

  .modal-content {
    background-color: rgb(221, 236, 203);
    padding: 20px;
    max-width: 40%;
    max-height: 80%;
    overflow: auto;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  }


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
    cursor: pointer
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
    width: 70vw;
    height: 75px;
    display: flex;
    flex-direction: row;
    justify-content: space-around;
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
    cursor: pointer !important;
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
    cursor: pointer !important;
  }

  /* Styles pour le bouton "A faire" */
  .btn.a-faire {
    background-color: rgb(49, 104, 54);
    color: white;
    cursor: pointer !important;
  }

  .btn.a-faire:hover {
    background-color: rgba(0, 255, 0, 0.5);
    color: white;
  }

  .card {
    min-height: 300px; /* Ajustez la valeur à la hauteur minimale souhaitée */
    max-width: 300px !important;
    border: white;
  }

  h3 {
    font-size: 23px;
  }

  .card-body:hover {
    background-color: white !important;
    transition: 1s;
  }

  .btn-warning:hover {
    background-color: rgb(255, 165, 0);
    color: white;
  }


  .card-body {
    width: 300px !important;
    cursor: pointer !important;
    display: flex;
    flex-direction: column;
    justify-content: space-between;


  }

  .boutonreste {
    display: flex;
    flex-direction: row;
    width: 250px;
    height: auto;
  }


  .active {
    color: rgb(253, 237, 9);
    /* Changer la couleur en fonction de votre préférence */
    font-weight: bold;
  }

  .action2 {
    color: green !important;
    font-weight: 900 !important;
    cursor: default !important;
  }

  .action3 {
    color: white !important;

  }

  .action {
    color: black !important;

  }
  .bold-text {
    color: rgb(0, 0, 0) !important;
    font-weight: 400 !important;
  }

  .bold-text2 {
    color: rgb(0, 0, 0) !important;
    font-weight: 400 !important;
  }
}

@media only screen and (max-width: 767px) {
  .modal {
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    margin-top: -60%;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.5);
  }

  .modal-content {
    background-color: rgb(221, 236, 203);
    padding: 20px;
    max-width: 90%;
    max-height: 100%;
    overflow: auto;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  }

  .bodyPage {
    width: 100vw;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }

  .typeAction {
    width: auto;
    height: auto;
    padding: 5%;
    margin-top: 10px;
    background-color: rgb(49, 104, 54);
    display: flex;
    justify-content: center;
    flex-direction: row;
    align-items: center;
    color: black;
    font-weight: bold;
    text-align: center;
  }

  .filtre {
    margin-left: 10px;
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

  .boutonreste {
    display: flex;
    flex-direction: row;
    justify-content: center;
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
    width: 90vw;
    height: 10vh;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    font-size: smaller;
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
    margin-left: -5%;
    margin-right: 10%;
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

  .card {
    width: 90% !important;
    margin: auto;
  }


  h3 {
    font-size: 23px;
  }
  .active {
    color: rgb(253, 237, 9);
    /* Changer la couleur en fonction de votre préférence */
    font-weight: bold;
  }

  .action2 {
    color: green !important;
    font-weight: 900 !important;
    cursor: default !important;
  }

  .action3 {
    color: white !important;

  }

  .action {
    color: black !important;

  }
  .bold-text {
    color: rgb(0, 0, 0) !important;
    font-weight: 400 !important;
  }

  .bold-text2 {
    color: rgb(0, 0, 0) !important;
    font-weight: 400 !important;
  }

}
</style>