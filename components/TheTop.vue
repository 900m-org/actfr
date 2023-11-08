<template>
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
</template>