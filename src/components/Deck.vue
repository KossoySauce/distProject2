<script setup>
import { ref } from 'vue'
import { useUserStore } from '../stores/user.js'

const store = useUserStore()

const emit = defineEmits(['playerChoice', 'teamChoice', 'teamImgChoice', 'gameChoice'])

const favoritePlayers = ref(true)
const favoriteTeams = ref(false)


// all team logo images
const playerShirts = {
  Hawks: '../../public/Players/ATL_icon.png',
  Celtics: '../../public/Players/BOS_icon.png',
  Nets: '../../public/Players/BKN_icon.png',
  Hornets: '../../public/Players/CHA_icon.png',
  Bulls: '../../public/Players/CHI_icon.png',
  Cavaliers: '../../public/Players/CLE_icon.png',
  Mavericks: '../../public/Players/DAL_icon.png',
  Nuggets: '../../public/Players/DEN_icon.png',
  Pistons: '../../public/Players/DET_icon.png',
  Warriors: '../../public/Players/GSW_icon.png',
  Rockets: '../../public/Players/HOU_icon.png',
  Pacers: '../../public/Players/IND_icon.png',
  Clippers: '../../public/Players/LAC_icon.png',
  Lakers: '../../public/Players/LAL_icon.png',
  Grizzlies: '../../public/Players/MEM_icon.png',
  Heat: '../../public/Players/MIA_icon.png',
  Bucks: '../../public/Players/MIL_icon.png',
  Timberwolves: '../../public/Players/MIN_icon.png',
  Pelicans: '../../public/Players/NOP_icon.png',
  Knicks: '../../public/Players/NYK_icon.png',
  Thunder: '../../public/Players/ORL_icon.png',
  Magic: '../../public/Players/PHI_icon.png',
  '76ers': '../../public/Players/PHX_icon.png',
  Suns: '../../public/Players/POR_icon.png',
  'Trail Blazers': '../../public/Players/POR_icon.png',
  Kings: '../../public/Players/SAC_icon.png',
  Spurs: '../../public/Players/SAS_icon.png',
  Raptors: '../../public/Players/TOR_icon.png',
  Jazz: '../../public/Players/UTA_icon.png',
  Wizards: '../../public/Players/WAS_icon.png',
}




// all team logo images
const teamLogos = {
  Hawks: '../../public/hawksLogo.avif',
  Celtics: '../../public/celticsLogo.png',
  Nets: '../../public/netsLogo.png',
  Hornets: '../../public/hornetsLogo.svg',
  Bulls: '../../public/bullsLogo.svg',
  Cavaliers: '../../public/cavsLogo.svg',
  Mavericks: '../../public/mavsLogo.svg',
  Nuggets: '../../public/nuggetsLogo.svg',
  Pistons: '../../public/pistonsLogo.svg',
  Warriors: '../../public/warriorsLogo.svg',
  Rockets: '../../public/rocketsLogo.svg',
  Pacers: '../../public/pacersLogo.svg',
  Clippers: '../../public/clippersLogo.svg',
  Lakers: '../../public/lakersLogo.svg',
  Grizzlies: '../../public/grizzliesLogo.svg',
  Heat: '../../public/heatLogo.svg',
  Bucks: '../../public/bucksLogo.svg',
  Timberwolves: '../../public/timberwolvesLogo.svg',
  Pelicans: '../../public/pelicansLogo.svg',
  Knicks: '../../public/knicksLogo.svg',
  Thunder: '../../public/thunderLogo.svg',
  Magic: '../../public/magicLogo.svg',
  '76ers': '../../public/sixersLogo.svg',
  Suns: '../../public/sunsLogo.png',
  'Trail Blazers': '../../public/trailblazersLogo.svg',
  Kings: '../../public/kingsLogo.svg',
  Spurs: '../../public/spursLogo.svg',
  Raptors: '../../public/raptorsLogo.svg',
  Jazz: '../../public/jazzLogo.svg',
  Wizards: '../../public/wizardsLogo.svg',
}

const userFavoritePlayers = ref([])
const userFavoriteTeams = ref([])

userFavoritePlayers.value = store.userData.favoritePlayers
userFavoriteTeams.value = store.userData.favoriteTeams

const deleteFavoritePlayers = async (player) => {
  let url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/favorite-players/${player.id}`


  const options = {
    method: 'DELETE',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${store.userToken}`,
    },
  }

  const response = await fetch(url, options)

  if (response.status === 200) {
    console.log(response.status);


    console.log('the player has been deleted')
  } else {
    console.log(response.status)
  }
}

const deleteFavoriteTeams = async (team) => {
  let url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/favorite-teams/${team.id}`


  const options = {
    method: 'DELETE',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${store.userToken}`,
    },
  }

  const response = await fetch(url, options)

  if (response.status === 200) {
    console.log(response.status);


    console.log('the team has been deleted')
  } else {
    console.log(response.status)
  }
}

const removeFavoritePlayer = (player) => {
  for (let i = 0; i < userFavoritePlayers.value.length; i++) {
    if (player === userFavoritePlayers.value[i]) {
      userFavoritePlayers.value.splice(i, 1)
      deleteFavoritePlayers(player);
      console.log('removed')
      return
    }
  }



}

const removeFavoriteTeam = (team) => {
  for (let i = 0; i < userFavoriteTeams.value.length; i++) {
    if (team === userFavoriteTeams.value[i]) {
      userFavoriteTeams.value.splice(i, 1)
      deleteFavoriteTeams(team)
      return
    }
  }
}

const triggerPlayerRefresh = (player) => {
  emit('playerChoice', null)
  emit('teamChoice', null)
  emit('gameChoice', null)

  setTimeout(() => {
    emit('playerChoice', player)
  }, 100)
}

const triggerTeamRefresh = (team, img) => {
  emit('playerChoice', null)
  emit('teamChoice', null)
  emit('gameChoice', null)

  setTimeout(() => {
    emit('teamChoice', team)
    emit('teamImgChoice', img)
  }, 100)
}

// toggling favorite players or teams
const togglePlayers = () => {
  favoriteTeams.value = false
  favoritePlayers.value = true
}

const toggleTeams = () => {
  favoritePlayers.value = false
  favoriteTeams.value = true
}

const showDetails = (player) => {
  triggerPlayerRefresh(player)
}

const showTeamDetails = (team, img) => {
  triggerTeamRefresh(team, img)
}
</script>

<template>
  <div class="favoriteOptions">
    <button @click="togglePlayers" class="favoriteOptionButton" >Favorite Players</button>
    <button @click="toggleTeams" class="favoriteOptionButton" >Favorite Teams</button>
  </div>
  <div class="team-cards-container" v-if="favoritePlayers">
    <div
      class="player-card"
      tabindex="0"
      v-for="player in userFavoritePlayers"
      :key="player.id"
      @click="showDetails(player)"
    >
      <img :src="playerShirts[player?.team?.name]" alt="" class="player-card-profile-img" />
      <div class="player-card-player-name">{{ player?.first_name + ' ' + player?.last_name }}</div>
      <div class="player-card-player-number">#{{ player?.jersey_number }}</div>
      <button class="removeFavoriteButton" @click="removeFavoritePlayer(player)">-</button>
    </div>
  </div>

  <div class="favorite-teams-container" v-if="favoriteTeams">
    <div class="team-card-container" v-for="team in userFavoriteTeams" :key="team.id">
      <div class="team-card" tabindex="0" @click="showTeamDetails(team, teamLogos[team.name])">
        <div class="team-image-container">
          <img :src="teamLogos[team?.name]" alt="" class="team-logo-img" />
          <button class="removeFavoriteButton" @click="removeFavoriteTeam(team)">-</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.removeFavoriteButton {
  position: absolute;
  top: 0;
  left: 0;
  border-radius: 50%;
  background: rgba(144, 144, 149, 0.513);
  border: none;
  margin: 0.5rem;
  width: 30px;
  height: 30px;
  cursor: pointer;
  font-size: 20px;
  font-family: var(--font-primary);
}

.removeFavoriteButton:hover {
  background: rgb(114, 5, 5);
  color: white;
}

.team-logo-img {
  width: 100px;
}

.team-card-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.team-card {
  width: 230px;
  height: 200px;
  border: 1px solid black;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: 10px;
  cursor: pointer;
  position: relative;
}

.team-card:hover {
  background: rgba(136, 135, 135, 0.538);
}

.team-card:focus {
  background: rgb(187, 150, 150);
}

.player-card-player-name {
  font-size: 15px;
}

.player-card-player-number {
  font-size: 25px;
  position: absolute;
  top: 0;
  right: 10px;
}

.favoriteOptionButton {
  background: rgba(196, 196, 198, 0.556);
  border: 0.125px solid black;

  width: 500px;
  font-family: var(--font-primary);
  font-size: 20px;
  height: 46px;

  cursor: pointer;
}

.favoriteOptionButton:focus {
  background: rgb(50, 50, 57);
  color: white;
}

.favoriteOptionButton:hover {
  background: rgba(135, 135, 135, 0.639);
}

.favoriteOptions {
  display: flex;
  height: 30px;
  width: 1000px;
  margin-bottom: 1rem;
}

/* player card inside the card containers  */
.team-cards-container .player-card {
  width: 150px;
  height: 200px;
  border: 1px solid black;
  margin: 0.25rem;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  font-family: var(--font-primary);
  align-items: center;
  cursor: pointer;
  padding: 0.5rem;
  border-radius: 10px;
  position: relative;
}

/* hover over player card */
.team-cards-container .player-card:hover {
  background-color: rgba(136, 135, 135, 0.538);
}

/* whenever a player is selected inside the card container */
.team-cards-container .player-card:focus {
  background-color: rgb(187, 150, 150);
}

/* profile image inside the player card */
.player-card .player-card-profile-img {
  width: 60px;
  margin-top: 1rem;
}

/* team cards container */
.team-cards-container {
  width: 1000px;
  border: 1px solid black;
  max-height: 455px;
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  grid-template-rows: repeat(5, 210px);
  padding: 1rem;
  overflow-y: auto;
}

.favorite-teams-container {
  width: 100%;
  border: 1px solid black;
  max-height: 455px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(5, 220px);

  overflow-y: auto;
}
</style>
