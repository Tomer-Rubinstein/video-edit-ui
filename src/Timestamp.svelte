<script>
    import { timestamps } from './store'
    export let timestamp;
    export let disabled;

    function removeTimestamp() {
        $timestamps = $timestamps.filter(item => item != timestamp); // trigger re-render
        console.log("timestamps: ", $timestamps);
    }
</script>


<div class="containerDiv">
  <p style="padding-right: 10px;">{timestamp}</p>
  <button  on:click={removeTimestamp} disabled={disabled}>delete</button>
</div>


<style>
.containerDiv {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
}
</style>
