
<script>
  import meetups from "./meetups-srore.js"
  import { createEventDispatcher } from 'svelte';
import Badge from "../UI/Badge.svelte";
import Button from "../UI/Button.svelte";
import LoadingSpinner from "../UI/LoadingSpinner.svelte";

    export let id;
    export let title;
    export let subtitle;
    export let imageUrl;
    export let description;
    export let adress;
    export let isFav;

    let isLoading=false;
    const dispatch = createEventDispatcher();

   function toggleFavorite (){
    isLoading=true;
    fetch(`https://reffat-964d5-default-rtdb.firebaseio.com/meetup/${id}.json`,{
      method:'PATCH',
      body:JSON.stringify({isFavorite: !isFav}),
      headers:{'Content-Type': 'application/json'}
    }).then(res=>{
      if(!res.ok)
      {
        throw new Error('Falied')
      }
      isLoading=false;
      meetups.toggleFavorite(id) 
    }).catch(err=>{
      isLoading=false;
      console.log(err)})
   }
</script>



  <article>
    <header>
      <h1>
        {title}
        {#if isFav }
          <Badge>FAVORITE</Badge> 
        {/if}
      </h1>
      <h2>{subtitle}</h2>
      <p>{adress}</p>
    </header>
    <div class="image">
      <img src={imageUrl} alt={title} />
    </div>
    <div class="content">
      <p>{description}</p>
    </div>
    <footer>
        <Button mode="outlin" type="button" on:click={()=>{dispatch('edit', id)}} >Edit</Button>
        {#if isLoading}
          
        <span>Changing...</span>
        {:else}
       <Button 
        type="button" 
        mode="outline" 
        color={isFav ? null: "success"}
        on:click={toggleFavorite} >
            {isFav ?  'Unfavorite': 'Favorite'}
        </Button>
        {/if} 
        <Button type="button" on:click={() => dispatch('showdetails', id)}  >Show Details</Button>
        
    </footer>
  </article>
  
  
  
  
  <style>
    article {
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
      border-radius: 5px;
      background: white;
      margin: 1rem;
    }
  
    header,
    .content,
    footer {
      padding: 1rem;
    }
  
    .image {
      width: 100%;
      height: 14rem;
    }
  
    .image img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
  
    h1 {
      font-size: 1.25rem;
      margin: 0.5rem 0;
      font-family: "Roboto Slab", sans-serif;
    }
  
  
    h2 {
      font-size: 1rem;
      color: #808080;
      margin: 0.5rem 0;
    }
  
    p {
      font-size: 1.25rem;
      margin: 0;
      word-break:break-all;
    }
  
    div {
      text-align: right;
    }
  </style>