<script setup>
import { ref } from 'vue'
import { useUserStore } from '../stores/user.js'

const store = useUserStore()

const userFavoritePlayers = ref([])
const userFavoriteTeams = ref([])

const playerSelected = defineModel('selected-player')
const teamSelected = defineModel('selected-team')
const gameSelected = defineModel('selected-game')
const teamImg = defineModel('team-img')

const searchInput = ref(null)
const searchTerm = ref('')
const resultsContainerValid = ref(false)
const teamContainerValid = ref(false)
const gamesContainerValid = ref(false)
const advancedOption = ref(false)
const isValid = ref(false)

// refs for advanced search

const advancedSeason = ref('')
const advancedConference = ref('')
const advancedStartDate = ref('')
const advancedEndDate = ref('')

const searchPlayerResults = ref([])
const teamResults = ref([])
const gameResults = ref([])

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

let urlEndpoint = ''

const showGameStats = (game) => {
  playerSelected.value = null
  teamSelected.value = null
  gameSelected.value = null

  setTimeout(() => {
    gameSelected.value = game
  }, 300)
}


const pushFavoritePlayers = async () => {
  let url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/favorite-players`


  const options = {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${store.userToken}`,
    },
    body: JSON.stringify({ player_id: playerSelected.value.id}),
  }

  const response = await fetch(url, options)

  if (response.status === 201) {
    console.log(response.status);


    console.log('the player has been pushed ')
  } else {
    console.log(response.status)
  }
}
const pushFavoriteTeams = async () => {
  let url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/favorite-teams`


  const options = {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${store.userToken}`,
    },
    body: JSON.stringify({ team_id: teamSelected.value.id})
  }

  const response = await fetch(url, options)

  if (response.status === 201) {
    console.log(response.status);


    console.log('the team has been pushed ')
  } else {
    console.log(response.status)
  }
}

const addFavoritePlayer = (player) => {
  userFavoritePlayers.value = store.userData.favoritePlayers

  for (let i = 0; i < userFavoritePlayers.value.length; i++) {
    if (player.id === userFavoritePlayers.value[i].id) {
      console.log('cant do it')
      return
    }
  }

  userFavoritePlayers.value.push(player)
  pushFavoritePlayers()
}

// function that controls the endpoint of the search input
function getEndpoint(endpoint) {
  resultsContainerValid.value = false
  teamContainerValid.value = false
  gamesContainerValid.value = false

  switch (endpoint) {
    case 1:
      urlEndpoint = 'players'

      searchInput.value.style.backgroundColor = '#396ecb60'
      searchInput.value.placeholder = 'Enter Player Name'
      searchInput.value.style['pointer-events'] = 'auto'

      break
    case 2:
      urlEndpoint = 'teams'
      searchInput.value.style.backgroundColor = '#17489b60'
      searchInput.value.placeholder = 'Enter Team Name'
      searchInput.value.style['pointer-events'] = 'auto'
      break
    case 3:
      urlEndpoint = 'games'
      searchInput.value.style.backgroundColor = '#2b549c7c'
      searchInput.value.placeholder = 'Enter Game Name'
      searchInput.value.style['pointer-events'] = 'auto'
      break
    default:
      return
  }

  console.log(urlEndpoint)
}

// function to show userstats below there team

const showUserStats = (data) => {
  playerSelected.value = null
  teamSelected.value = null
  gameSelected.value = null
  setTimeout(() => {
    playerSelected.value = data
  }, 100)
}

const triggerIsValid = () => {
  isValid.value = true

  setTimeout(() => {
    isValid.value = false
  }, 1000)
}

const addFavoriteTeam = (team) => {
  userFavoriteTeams.value = store.userData.favoriteTeams

  for (let i = 0; i < userFavoriteTeams.value.length; i++) {
    if (team.id === userFavoriteTeams.value[i].id) {
      console.log('didnt work')
      return
    }
  }

  triggerIsValid()
  userFavoriteTeams.value.push(team)
  pushFavoriteTeams()
}

// 3 functions of searching either teams/ players/ or games/

async function searchPlayers() {
  let url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/players`

  if (advancedConference.value || advancedSeason.value || searchTerm.value) {
    url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/players?name-search=${searchTerm.value}`
  }

  const options = {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
    },
  }

  const response = await fetch(url, options)

  if (response.status === 200) {
    const dataObject = await response.json()
    console.log(dataObject)
    console.log(dataObject.data[0])

    searchPlayerResults.value.push(...dataObject.data)

    searchTerm.value = ''
    resultsContainerValid.value = true
  } else {
    resultsContainerValid.value = false

    console.log(response.status)
  }
}

const showTeamStats = (team) => {
  playerSelected.value = null

  teamSelected.value = null
  gameSelected.value = null

  teamImg.value = teamLogos[team.name]

  setTimeout(() => {
    teamSelected.value = team
  }, 300)
}

async function searchTeams() {
  let url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/teams`

  if (searchTerm.value) {
    url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/teams?team-search=${searchTerm.value}`
  }

  const options = {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
    },
  }

  const response = await fetch(url, options)

  if (response.status === 200) {
    const data = await response.json()

    if (url === `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/teams`) {
      data.data.splice(30, 15)
    }

    teamResults.value = data.data
    resultsContainerValid.value = false
    teamContainerValid.value = true

    console.log(data)
  } else {
    console.log('didnt work')
    return
  }
}

async function searchGames() {
  gamesContainerValid.value = false
  gameResults.value = []
  let url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/games?seasons=2024`

  if (advancedSeason.value) {
    url = `https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/games?seasons=${advancedSeason.value}`
  }

  if (advancedStartDate.value && advancedEndDate.value) {
    url =`https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/games?start_date=${advancedStartDate.value}&end_date=${advancedEndDate.value}`
    console.log(url)
  }


  if(advancedSeason.value && advancedStartDate.value && advancedEndDate.value) {
    url =`https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/games?seasons=${advancedSeason.value}&start_date=${advancedStartDate.value}&end_date=${advancedEndDate.value}`
  }




  const options = {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
    },
  }

  const response = await fetch(url, options)

  if (response.status === 200) {
    const dataObject = await response.json()

    gameResults.value.push(...dataObject.data)

    console.log(...gameResults.value)

    gamesContainerValid.value = true

    console.log(dataObject)
  } else {
    console.log(response.status)
  }
}

function toggleAdvanced() {
  advancedOption.value = !advancedOption.value
}

// function that fetches what the user input searched

const searchBasketball = async () => {
  resultsContainerValid.value = false
  searchPlayerResults.value = []
  teamResults.value = []

  if (!searchInput.value || !urlEndpoint) {
    return
  }

  switch (urlEndpoint) {
    case 'players':
      searchPlayers()
      break
    case 'teams':
      searchTeams()
      break
    case 'games':
      searchGames()
      break
    default:
      return
  }
}
</script>
<template>
  <div class="blue-team-members-container">
    <div class="top-of-search-container">
      <div class="blue-team-header">
        <button class="player-button-filter" @click="getEndpoint(1)">Players</button>
        <button class="player-button-filter" @click="getEndpoint(2)">Teams</button>
        <button class="player-button-filter" @click="getEndpoint(3)">Games</button>
      </div>

      <div class="input-container">
        <img src="../../public/search-interface-symbol.png" alt="" class="search-img" />
        <input
          ref="searchInput"
          type="text"
          v-model="searchTerm"
          class="searchInputContainer"
          placeholder="Choose a Option Above"
        />
        <button class="searchButton" @click="searchBasketball">Search →</button>
      </div>
      <div class="advanced-button-container">
        <div class="advanced-inputs-container" v-if="advancedOption">
          <div class="advanced-option">
            <div class="label">Season:</div>
            <input
              type="number"
              min="2000"
              max="2100"
              placeholder="Choose Season"
              class="season-year"
              v-model="advancedSeason"
            />
          </div>
          <div class="advanced-option">
            <div class="label">Start Date:</div>
            <input
              type="date"
              placeholder="Choose Season"
              class="season-year"
              v-model="advancedStartDate"
            />
          </div>
          <div class="advanced-option">
            <div class="label">End Date:</div>
            <input
              type="date"
              placeholder="Choose Season"
              class="season-year"
              v-model="advancedEndDate"
            />
          </div>
          <div class="advanced-option">
            <div class="label">Conference:</div>
            <select name="conference" class="conferenceSelect">
              <option value="west">West</option>
              <option value="east">East</option>
            </select>
          </div>
        </div>
        <button class="advancedSearchButton" @click="toggleAdvanced">Advanced Search</button>
      </div>
    </div>
    <div class="blue-team-members" v-if="resultsContainerValid">
      <div
        class="user-cell"
        v-for="result in searchPlayerResults"
        :key="result.id"
        tabindex="0"
        @click="showUserStats(result)"
      >
        <button class="addFavoriteButton" @click="addFavoritePlayer(result)">+</button>
        <div class="user-info-box">
          <span class="label-info-player">First Name: </span>
          <span class="stat-number">{{ result.first_name }}</span>
        </div>
        <div class="user-info-box">
          <span class="label-info-player">Last Name: </span>
          <span class="stat-number">{{ result.last_name }}</span>
        </div>
        <div class="user-info-box">
          <span class="label-info-player">Jersey: </span>
          <span class="stat-number">{{ result.jersey_number }}</span>
        </div>
        <div class="user-info-box">
          <span class="label-info-player">Height: </span>
          <span class="stat-number">{{ result.height }}</span>
        </div>
        <div class="user-info-box">
          <span class="label-info-player">Id: </span>
          <span class="stat-number">{{ result.id }}</span>
        </div>
        <div class="user-info-box">
          <span class="label-info-player">Team: </span>
          <span class="stat-number">{{ result.team.name }}</span>
        </div>
      </div>
    </div>
    <div class="team-container" v-if="teamContainerValid">
      <div
        class="team-card"
        tabindex="0"
        v-for="team in teamResults"
        :key="team.id"
        @click="showTeamStats(team)"
      >
        <button class="addFavoriteTeamButton" @click="addFavoriteTeam(team)">+</button>
        <img :src="teamLogos[team.name]" alt="" class="teamImg" />
      </div>
    </div>
    <div class="games-container" v-if="gamesContainerValid">
      <div
        class="games-card"
        v-for="game in gameResults"
        :key="game.id"
        tabindex="0"
        @click="showGameStats(game)"
      >
        <div class="header-game-card">
          <span class="vsTeams"> {{ game.home_team.name }} vs {{ game.visitor_team.name }}</span>
          <span class="seasonGame">{{ game.season }}</span>
        </div>

        <div class="teams-player-container">
          <img :src="teamLogos[game.home_team.name]" alt="" class="vsTeamsLogo" />
          <span class="vsLetters">vs</span>
          <img :src="teamLogos[game.visitor_team.name]" alt="" class="vsTeamsLogo" />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.seasonGame {
  position: absolute;
  right: 10px;
  font-size: 25px;
  color: rgb(47, 48, 49);
}

.header-game-card {
  display: flex;
  position: relative;
  width: 500px;
  justify-content: center;
}

.vsTeamsLogo {
  max-width: 100px;
  max-height: 100px;
  margin: 0.5rem;
}

.vsTeams {
  font-size: 15px;
  margin-block: 0.5rem;
  font-weight: 700;
}

.vsLetters {
  font-size: 40px;
  margin-inline: 3.5rem;
}

.games-card {
  display: flex;
  flex-direction: column;
  border: 2px solid black;
  border-radius: 10px;
  width: 500px;
  height: 170px;
  align-items: center;
  cursor: pointer;
  background: rgb(142, 140, 140);
  margin-block: 1rem;
}

.games-card:hover {
  background: rgba(23, 125, 193, 0.206);
}

.games-card:focus {
  background: rgba(39, 71, 134, 0.389);
}

.games-container {
  height: 500px;
  display: flex;
  flex-direction: column;
  font-family: var(--font-primary);
  overflow-y: auto;
  border: none;
  outline: none;
}

.teams-player-container {
  width: 500px;
  display: flex;
  height: 120px;
  align-items: center;

  justify-content: center;
  padding-bottom: 1rem;
}

.addFavoriteTeamButton {
  position: absolute;
  width: 40px;
  height: 40px;
  border: none;
  border-radius: 50%;
  font-size: 25px;
  font-family: var(--font-primary);
  background: rgba(129, 129, 132, 0.495);
  cursor: pointer;
  top: 15px;
  right: 15px;
}

.addFavoriteTeamButton:hover {
  background: rgb(7, 77, 29);
  color: white;
}

.addFavoriteButton {
  position: absolute;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  border: none;
  bottom: 15px;
  right: 15px;
  font-family: var(--font-primary);
  font-size: 30px;
  background: rgba(149, 149, 152, 0.495);
  cursor: pointer;
}

.addFavoriteButton:hover {
  background: rgb(7, 77, 29);
  color: white;
}

.team-container {
  width: 500px;
  height: 500px;

  overflow-y: auto;
  overflow-x: hidden;
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: repeat(15, 1fr);
}

.team-card {
  width: 230px;
  height: 230px;
  margin-bottom: 1rem;
  margin-inline: 0.5rem;

  border-radius: 30px;
  border: 2px solid black;
  position: relative;

  background: rgba(203, 203, 203, 0.393);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.team-card:hover {
  background: rgba(23, 125, 193, 0.206);
}

.team-card:focus {
  background: rgba(39, 71, 134, 0.389);
}

.teamImg {
  max-width: 120px;
}

.label-info-player {
  font-size: 12px;
  color: rgb(111, 19, 19);
}
.result-stats {
  color: red;
}

.stat-number {
  font-size: 15px;
}

.top-of-search-container {
  display: flex;
  flex-direction: column;
  width: 500px;
  height: 200px;
  justify-content: center;
  align-items: center;
  margin-top: 1rem;
  border-bottom: 0.5px solid rgb(78, 69, 69);
}

.label {
  font-size: 13px;
  color: rgb(80, 1, 1);
}

.conferenceSelect {
  width: 70px;
  background: rgb(119, 119, 119);
  border: 1px solid black;
  font-family: var(--font-primary);
}
.season-year {
  width: 70px;
  background: rgb(119, 119, 119);
  border: 1px solid black;
}

.advanced-option {
  width: 100px;
}

.advanced-inputs-container {
  display: flex;

  width: 380px;
  gap: 0.5rem;

  align-items: center;
}

.user-info-box {
  width: 100px;
  display: flex;
  flex-direction: column;
  font-family: var(--font-primary);
  padding: 1rem;
}

.advanced-button-container {
  display: flex;
  justify-content: right;
  width: 500px;
  min-height: 50px;
  margin: 10px;
}

.advancedSearchButton {
  width: 120px;
  height: 30px;
  font-size: 12px;
  cursor: pointer;
  border-radius: 30px;
  border: none;
  background: rgb(146, 144, 144);
}

.advancedSearchButton:hover {
  background: rgb(96, 96, 96);
}

/* searching player/team/games button*/
.searchButton {
  position: absolute;
  right: 0;
  cursor: pointer;
  width: 100px;
  height: 50px;
  border: none;
  border-radius: 10px;
  background: rgba(183, 183, 183, 0.187);
  font-size: 17px;
  font-family: var(--font-primary);
  color: black;
}
.searchButton:hover {
  background: grey;
}

/* nav logout container */

.logout-style {
  margin-inline: 2.5rem;
  color: rgb(138, 39, 39);
}

/* latest feed container. the two cards that have recent news about games and stats and things like that */
.latest-feed-container .feed-card {
  width: 500px;
  height: 450px;
  background-color: rgba(0, 0, 0, 0.031);
  border: 1px solid black;
}

/* top half container  right side latest updates */

.top-half-container .latest-feed-container {
  background-color: rgba(65, 62, 62, 5%);
  width: 1300px;
  display: flex;
  justify-content: space-evenly;
}

/* blue team members box that has the input and all the other blue team users inside it
/* top half container blue team members container */

.top-half-container .blue-team-members-container {
  width: 500px;
  background-color: rgb(121, 121, 121);
  flex: auto;
  display: flex;
  flex-direction: column;
  padding: 0rem 2rem 2rem 2rem;
  align-items: center;
  font-family: var(--font-primary);
}

/* input container inside the blue team members box */
.blue-team-members-container .input-container {
  width: 500px;
  display: flex;
  justify-content: center;
  position: relative;
}

/* input in the input container in the blue team member box */
.input-container input {
  width: 500px;
  height: 40px;
  padding-left: 3rem;
  font-size: 20px;
  pointer-events: none;
}

/* search image */
.input-container .search-img {
  position: absolute;
  width: 25px;
  left: 0;
  margin-top: 10px;
  margin-left: 10px;
}

.searchInputContainer {
  border: none;
  border-radius: 8px;
  min-height: 50px;
}

.searchInputContainer:focus {
  border: 2px solid blue;
  outline: none;
}

.searchInputContainer::placeholder {
  color: rgb(94, 93, 93);
}

/* blue team header over the input */
.blue-team-header {
  min-height: 100px;
  gap: 1rem;

  display: flex;
  justify-content: center;
  align-items: center;
}

/* buttons in blue team header */

.blue-team-header button {
  box-shadow: 1px 1px 5px 1px black;
  background: rgba(163, 163, 166, 0.646);
  border-radius: 40px;
  border: none;
  font-family: var(--font-primary);

  color: rgb(41, 40, 40);
  font-size: 25px;
  width: 150px;
  height: 40px;
  cursor: pointer;
}

.player-button-filter:hover {
  background: rgba(180, 178, 178, 0.74);
  border: 1px solid rgb(4, 18, 122);
}

.player-button-filter:focus {
  border: 2px solid rgb(19, 19, 144);
  background: rgba(2, 37, 124, 0.531);
  color: grey;
}

/* blue members cell container holding each blue team member */
.blue-team-members {
  gap: 1rem;
  display: flex;
  flex-direction: column;
  overflow-y: auto;
  width: 500px;
}

.blue-team-members .user-cell {
  height: 150px;
  border: 2px solid black;
  border-radius: 10px;
  width: 500px;
  flex: 0 0 auto;
  display: flex;
  cursor: pointer;
  backdrop-filter: blur(10px);
  background: rgba(195, 192, 192, 0.426);
  position: relative;
}

.user-cell:hover {
  background: rgba(23, 125, 193, 0.206);
}

.user-cell:focus {
  background: rgba(39, 71, 134, 0.389);
}
</style>
