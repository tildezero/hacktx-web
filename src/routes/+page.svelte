<script lang="ts">
  import Counter from "./Counter.svelte";
  import welcome from "$lib/images/svelte-welcome.webp";
  import welcomeFallback from "$lib/images/svelte-welcome.png";

  import { HandLandmarker, FilesetResolver } from "@mediapipe/tasks-vision";

  let videoSource: HTMLVideoElement | null = null;
  let loading = false;
  let handLandmarker;
  const obtainCamera = async () => {
    try {
      loading = true;
      const stream = await navigator.mediaDevices.getUserMedia({
        video: true,
      });
      videoSource.srcObject = stream;
      videoSource?.play();
      loading = false;
    } catch (error) {
      console.log(error);
    }
  };

  const createHandLandmarker = async () => {
    const vision = await FilesetResolver.forVisionTasks(
      "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@0.10.0/wasm"
    );

    handLandmarker = await HandLandmarker.createFromOptions(vision, {
      baseOptions: {
        modelAssetPath: `https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/1/hand_landmarker.task`,
        delegate: "GPU",
      },
      runningMode: "VIDEO",
      numHands: 2,
    });
  };

  createHandLandmarker();

  const video = document.getElementById("webcam") as HTMLVideoElement;
  const canvasElement = document.getElementById("output_canvas") as HTMLCanvasElement;
  const canvasCtx = canvasElement.getContext("2d");
</script>

<svelte:head>
  <title>Home</title>
  <meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
  <h1>
    <span class="welcome">
      <picture>
        <source srcset={welcome} type="image/webp" />
        <img src={welcomeFallback} alt="Welcome" />
      </picture>
    </span>

    to your new<br />SvelteKit app
  </h1>

  <h2>
    try editing <strong>src/routes/+page.svelte</strong>
  </h2>

  <!-- {#if loading} -->
  <!-- <h1>LOADING</h1> -->
  <!-- {/if} -->
  <!-- svelte-ignore a11y-media-has-caption -->
  <!-- <video bind:this={videoSource} /> -->
  <!-- <button on:click={obtenerVideoCamara}>CLICK</button> -->

  <video bind:this={videoSource} id="webcam"></video>
  <button on:click={obtainCamera}>get video</button>

  <Counter />
</section>

<style>
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    flex: 0.6;
  }

  h1 {
    width: 100%;
  }

  .welcome {
    display: block;
    position: relative;
    width: 100%;
    height: 0;
    padding: 0 0 calc(100% * 495 / 2048) 0;
  }

  .welcome img {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    display: block;
  }
</style>
