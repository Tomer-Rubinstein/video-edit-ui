<script>
  import Timestamp from "./Timestamp.svelte";
  import {timestamps} from './store';

  var defaultVideoId = "6RF6nxnJEBw";
  var defaultStartTime = 21;

  let inputVideoIdBind;
  let inputStartTimeBind;
  let isSubmitClicked = false;

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
      videoId: defaultVideoId,
      playerVars: {
        rel: 0,
        modestbranding: 1,
        showinfo: 0,
        autostart: 0,
        start: defaultStartTime,
      }
    });
  }

  function loadVideoId() {
    const videoId = inputVideoIdBind.value;
    player.loadVideoById(videoId);
  }

  function setStartTime() {
    const startTime = parseFloat(inputStartTimeBind.value);
    player.seekTo(startTime);
    setInitialTimestamp(startTime);
  }

  function setInitialTimestamp(timestamp) {
    $timestamps[$timestamps.length-1] = timestamp;
  }

  function addTimestamp(timestamp) {
    for (var i=0; i < $timestamps.length; i++) {
      if ($timestamps[i] < timestamp) {
        $timestamps = [
          ...$timestamps.slice(0, i),
          timestamp,
          ...$timestamps.slice(i)
        ];
        break;
      }
    }
  }

  function exportMetadata() {
    isSubmitClicked = true;

    fetch("http://127.0.0.1:5000/start", {
      method: "POST",
      mode: "cors",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        "timestamps": JSON.stringify($timestamps.reverse()),
        "yt_vid_id": inputVideoIdBind.value,
      })
    }).then(resp => {
      console.log("resp:", resp);
    })
  }

  /* */
  addEventListener("keypress", (event) => {
    if (event.code == "KeyH") {
      const timestamp = player.getCurrentTime();
      addTimestamp(timestamp);
    }
  });
  setInitialTimestamp(defaultStartTime);
</script>


<main>
  <div class="rowContainer">
    <div id="player"></div>

    <ul>
      {#each $timestamps as time}
        <Timestamp timestamp={time} disabled={$timestamps[$timestamps.length-1]==time}/>
      {/each}
    </ul>
  </div>
  
  <div class="inputContainer">
    <input bind:this={inputVideoIdBind} type="text" placeholder="YouTube Video ID" value={defaultVideoId}/>
    <button on:click={loadVideoId}>Play</button>
    <br><br>
    <input bind:this={inputStartTimeBind} type="text" placeholder="Start Time" value={defaultStartTime}/>
    <button on:click={setStartTime}>Set</button>
    <br><br>
    <button on:click={exportMetadata} disabled={isSubmitClicked}>Ok!</button>
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
