<script>
import EditMeetup from "./Meetups/EditMeetup.svelte";
import meetups from "./Meetups/meetups-srore";
import Meetupgrid from "./Meetups/Meetupgrid.svelte";
import Button from "./UI/Button.svelte";


import Header from "./UI/Header.svelte";
import MeetupDetail from "./Meetups/MeetupDetail.svelte";
import LoadingSpinner from "./UI/LoadingSpinner.svelte";
import Error from "./UI/Error.svelte";


let editMode;
  let editedId;
  let page = "overview";
  let pageData = {};
  let isLoading=true;
  let error;

  fetch('https://reffat-964d5-default-rtdb.firebaseio.com/meetup.json')
  .then(res=>{
    if (!res.ok){
      throw new Error('FEtching Meetups failed')
    }
    return res.json()

  })
  .then(data=>{
    const loadedMeetups=[];
    for (const key in data){
      loadedMeetups.push({
        ...data[key],
        id:key
      });
    }
    setTimeout(()=>{
      meetups.setMeetups(loadedMeetups.reverse());
      isLoading=false;
    },1000);
    
  })
  .catch(err=>{
    error=err;
    isLoading=false;
    console.log(err); 
  })
  function savedMeetup(event) {
    editMode = null;
    editedId = null;
  }

  function cancelEdit() {
    editMode = null;
    editedId = null;
  }

  function showDetails(event) {
    page = "details";
    pageData.id = event.detail;
  }

  function closeDetails() {
    page = "overview";
    pageData = {};
  }

  function startEdit(event) {
    editMode = "edit";
    editedId = event.detail;
  }

function clearError (){
  error=null;
}
</script>
{#if error}
  <Error message={error.message} on:cancel={clearError} />
{/if}

<Header />


<main>
    {#if page==='overview'}
    
        {#if editMode==='edit' }
        <EditMeetup id={editedId} on:save={savedMeetup}  on:cancel={cancelEdit}/>
        {/if}
        {#if isLoading}
          <LoadingSpinner />

        {:else}
        <Meetupgrid on:add={()=>{editMode='edit'}} meetups={$meetups} on:showdetails={showDetails} on:edit={ startEdit}/>
        {/if}
         {:else}
     <MeetupDetail id={pageData.id} on:close={closeDetails} />
     {/if}

</main>

<style>
    main{
        margin-top: 5rem;
    }
</style>

