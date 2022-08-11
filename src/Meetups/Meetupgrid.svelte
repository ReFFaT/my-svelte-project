<script>
import Button from "../UI/Button.svelte";
import MeetupFilter from "./MeetupFilter.svelte";
import MeetupItem from "./MeetupItem.svelte";
import { createEventDispatcher } from 'svelte';



let dispatch =createEventDispatcher();

export let meetups;


let favsOnly
 $: filterMeetups= favsOnly? meetups.filter(m=>m.isFavorite): meetups;
function setFilter(event){
 
  favsOnly=event.detail===1;
}
</script>

<section id="meetup-controls">
    <MeetupFilter on:select={setFilter} />
    <Button   on:click={()=>dispatch('add')} >New Meetup</Button>
</section>
{#if  filterMeetups.lenght===0}
  <p>No meetups found</p>
{/if}
<section id="meetups">
  
    {#each filterMeetups as meetup }
        <MeetupItem 
        id={meetup.id}
        title={meetup.title}
        subtitle={meetup.subtitle}
        description={meetup.description}
        imageUrl={meetup.imageUrl}
        email={meetup.email} 
        adress={meetup.adress}
        isFav={meetup.isFavorite}
        on:showdetails
        on:edit/>
    {/each}
</section>
<style>
#meetups {
    width: 100%;
    display: grid;
    grid-template-columns: 1fr;
    grid-gap: 1rem;
  }

  #meetup-controls {
    margin: 1rem;
    display: flex;
    justify-content: space-between;
  }

  @media (min-width: 768px) {
    #meetups {
      grid-template-columns: repeat(2, 1fr);
    }
  }

</style>