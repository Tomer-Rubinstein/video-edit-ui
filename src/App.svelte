<script>
  import Timestamp from "./Timestamp.svelte";
  import {timestamps} from './store'

  let inputVideoIdBind;
  let inputStartTimeBind;

  /* add YouTube embed API */
  var tag = document.createElement('script');
  tag.src = "https://www.youtube.com/iframe_api";

  var firstScriptTag = document.getElementsByTagName('script')[0];
  firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);

  var player;
  window.onYouTubeIframeAPIReady = () => {
    player = new YT.Player("player", {
      height: "620",
      width: "940",
      videoId: "6RF6nxnJEBw",
      playerVars: {
        rel: 0,
        modestbranding: 1,
        showinfo: 0,
        autostart: 0,
      }
    });
  }

  function markTimestamp(event) {
    if (event.code == "KeyH") {
      const timestamp = player.getCurrentTime();
      addTimestamp(timestamp);
    }
  }

  function loadVideoId() {
    const videoId = inputVideoIdBind.value;
    player.loadVideoById(videoId);
  }

  function setStartTime() {
    const startTime = parseInt(inputStartTimeBind.value);
    player.seekTo(startTime);
  }

  function addTimestamp(timestamp) {
    for (var i=0; i < $timestamps.length; i++) {
      if ($timestamps[i] < timestamp) {
        $timestamps = [
          ...$timestamps.slice(0, i),
          timestamp,
          ...$timestamps.slice(i)
        ]
        break;
      }
    }
  }

  function exportMetadata() {

  }

  addEventListener("keypress", markTimestamp);
</script>


<main>
  <div class="rowContainer">
    <div id="player"></div>

    <ul>
      {#each $timestamps as time}
        <Timestamp timestamp={time} disabled={time==0}/>
      {/each}
    </ul>
  </div>
  
  <div class="inputContainer">
    <input bind:this={inputVideoIdBind} type="text" placeholder="YouTube Video ID"/>
    <button on:click={loadVideoId}>Play</button>
    <br><br>
    <input bind:this={inputStartTimeBind} type="text" placeholder="Start Time"/>
    <button on:click={setStartTime}>Set</button>
    <br><br>
    <button on:click={exportMetadata}>Ok!</button>
  </div>
</main>


<style>
  .rowContainer {
    padding-top: 100px;
    padding-left: 200px;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
  }
  .inputContainer {
    text-align: center;
  }
  ul {
    width: 300px;
    height: 620px;
    overflow: auto;
  }
</style>
