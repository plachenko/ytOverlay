<script>
  import {onMount} from 'svelte';
  import YoutubePlayer from 'youtube-player';
  import DrawCanvas from './DrawCanvas.svelte';

  let frame;
  let ws;
  let player;
  let videoEl;
  let allBlockEl;

  function createPlayer(){
    const params = new URLSearchParams(location.search);
    
    player = YoutubePlayer('video-player', {
      videoId: params.get('id'),
      playerVars: {
        'controls': 0,
        'autoplay': 1,
        'modestlogo': 1,
        'end': 0,
        'rel': 0,
      }
    });

    player.mute();
    player.seekTo(0);
    player.width = window.innerWidth;
    player.height = window.innerHeight;

    player.on('ready', (e) => {
      frame = document.getElementsByTagName('iframe')[0];
    });

    player.on('stateChange', e => playerState(e));
  }

  function playerState(e){
    if(e.data == 1){
        // playing.

        // check video duration.
        player.getDuration().then(e => {

          // if duration is greater than 20 seconds
          if(e > 20){
            // stop the player
            // player.stopVideo();
            const obj = {
              type: 'vidLength',
              payload: e
            }

            ws.send(JSON.stringify(obj));
            return;
          } else {
            allBlockEl.style.display = "none";
          }

          allBlockEl.style.display = "none";

        });

      }

      if(e.data == 0){
        // Ended
        console.log('ended.');
        allBlockEl.style.display = "block";
      }
  }

  onMount(() => {
    ws = new WebSocket('ws://localhost:6979');
    
    createPlayer();
    // player.setVolume(80);

    // player.pause();

   
  });
</script>

<main>
    <!-- <DrawCanvas /> -->
    <div class="blocker" id="top" />
    <div class="blocker" id="bot" />
    <div bind:this={allBlockEl} class="blocker" id="all" />
    <div bind:this={videoEl} id="video-player"></div>
</main>

<style>
  main{
    background: none !important;
  }
  *{
    width: 100%;
    height: 100%;
    background: none !important;
  }
  .blocker{
    position: absolute;
    height: 55px;
    width: 100%;
    background-color:#0F0 !important;
  }
  #all{
    width: 100%;
    height: 100%;
  }
  #top{
    top: 0px;
  }
  #bot{
    bottom: 0px;
  }
</style>
