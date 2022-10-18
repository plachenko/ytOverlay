<script>
    import {onMount} from 'svelte';

    let can;
    let ctx;
    let s = 4;

    onMount(() => {
        
        ctx = can.getContext('2d');
        // console.log(ctx);

        for(let w = 0; w <= can.width; w+=s){
            for(let h = 0; h <= can.width; h+=s){
                ctx.fillRect(h, w, s, s);
            }
        }
    });
</script>
  
<canvas bind:this={can} />
  
<style>
canvas{

}
</style>
  