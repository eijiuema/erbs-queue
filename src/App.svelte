<script>
  import { io } from "socket.io-client";
  import { v4 as uuidv4 } from "uuid";
  import Queue from "./Queue.svelte";
  import SvelteTooltip from "svelte-tooltip";

  let userCount = 0;

  let queues = {
    solo: [],
    duo: [],
    squad: [],
  };

  let user = null;

  const socket = io("https://erbs-queue.herokuapp.com");

  const token = localStorage.getItem("token") || uuidv4();
  localStorage.setItem("token", token);

  socket.emit("connected", token);

  socket.on("update user state", (_user) => {
    user = _user;
    updatingUserState = false;
  });

  socket.on("update users count", (_userCount) => {
    userCount = _userCount;
  });

  socket.on("update queues", (_queues) => {
    queues = _queues;
  });

  socket.on("disconnect", () => {
    alert("Desconectado, a página será atualizada para reconectar.");
    location.reload();
  });

  socket.on("error", (error) => {
    alert(error);
  });

  let updatingUserState = false;

  function updateNickname() {
    if (!user.nickname) {
      return;
    }
    updatingUserState = true;
    socket.emit("update user nickname", user.nickname);
  }

  function joinQueue(mode) {
    updatingUserState = true;
    socket.emit("join queue", mode);
  }

  function leaveQueues() {
    updatingUserState = true;
    socket.emit("leave queues");
  }

  function mmrToTier(mmr) {
    return Math.min(Math.floor(mmr / 400), 6) + 1;
  }
</script>

{#if !user || updatingUserState}
  <div class="loader-overlay">
    <div class="loader" />
  </div>
{/if}

{#if user}
  <main>
    <div class="user-info">
      <SvelteTooltip
        tip={user.queue ? "Saia da fila para trocar o nick" : undefined}
        bottom
      >
        <input
          placeholder="Digite seu nick"
          bind:value={user.nickname}
          on:change={updateNickname}
          disabled={updatingUserState || user.queue}
        />
      </SvelteTooltip>

      <div class="tiers">
        <div class="tier">
          <span class="mmr">{user.mmr.solo}</span>
          <img
            src="img/tiers/{mmrToTier(user.mmr.solo)}.png"
            alt="Solo Tier"
          />
        </div>
        <div class="tier">
          <span class="mmr">{user.mmr.duo}</span>
          <img src="img/tiers/{mmrToTier(user.mmr.duo)}.png" alt="Duo Tier" />
        </div>
        <div class="tier">
          <span class="mmr">{user.mmr.squad}</span>
          <img
            src="img/tiers/{mmrToTier(user.mmr.squad)}.png"
            alt="Squad Tier"
          />
        </div>
      </div>
    </div>
    <div class="user-count">
      {userCount}
      {userCount == 1 ? "jogador conectado" : "jogadores conectados"}
    </div>
    <div class="queues">
      <div class="queue">
        <Queue
          name="Ranked Solo"
          img="img/solo.png"
          ranks={queues.solo}
          joined={user.queue == "solo"}
          join={() => joinQueue("solo")}
          leave={() => leaveQueues()}
        />
      </div>
      <div class="queue">
        <Queue
          name="Ranked Duo"
          img="img/duo.png"
          ranks={queues.duo}
          joined={user.queue == "duo"}
          join={() => joinQueue("duo")}
          leave={() => leaveQueues()}
        />
      </div>
      <div class="queue">
        <Queue
          name="Ranked Squad"
          img="img/squad.png"
          ranks={queues.squad}
          joined={user.queue == "squad"}
          join={() => joinQueue("squad")}
          leave={() => leaveQueues()}
        />
      </div>
    </div>
  </main>
{/if}

<style>
  main {
    display: flex;
    flex-direction: column;
    height: 100%;
    padding: 16px;
  }

  .user-info {
    display: flex;
    flex-direction: column;
    margin: 0 auto;
  }

  .user-info input {
    text-align: center;
  }

  .user-count {
    margin: 0 auto;
    text-align: center;
  }

  .loader-overlay {
    position: absolute;
    top: 0;
    left: 0;
    display: flex;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    z-index: 1000;
  }

  .loader {
    margin: auto;
    border: 10px solid #f3f3f3; /* Light grey */
    border-bottom: 10px solid #e79731; /* Blue */
    border-top: 10px solid #e79731; /* Blue */
    border-radius: 50%;
    width: 80px;
    height: 80px;
    animation: spin 2s linear infinite;
    margin: auto;
  }

  @keyframes spin {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }

  .queues {
    max-width: 100%;
    display: flex;
    flex-wrap: wrap;
    margin: 0 auto;
    justify-content: space-between;
  }

  .queue {
    margin: 8px auto;
    width: 900px;
    max-width: 100%;
  }

  .tiers {
    margin: 8px 0;
    display: flex;
    justify-content: space-evenly;
  }

  .tier {
    position: relative;
    width: 48px;
    user-select: none;
    z-index: -1;
  }

  .tier img {
    max-width: 100%;
  }

  .tier .mmr {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-shadow: 2px 0 0 #000, -2px 0 0 #000, 0 2px 0 #000, 0 -2px 0 #000,
      1px 1px #000, -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000;
  }
</style>
