<script>
    import { createEventDispatcher } from "svelte";
    import meetups from "./meetups-srore"
import Button from "../UI/Button.svelte";
import Modal from "../UI/Modal.svelte";
import TextInput from "../UI/TextInput.svelte";
import {isEmpty , isValidEmail} from "../helpers/validation"


export let id =null 
let title = "";
  let subtitle = "";
  let adress = "";
  let email = "";
  let description = "";
  let imageUrl = "";

    if (id){
        const unsubscribe = meetups.subscribe(items => {
      const selectedMeetup = items.find(i => i.id === id);
      title = selectedMeetup.title;
      subtitle = selectedMeetup.subtitle;
      adress = selectedMeetup.adress;
      email = selectedMeetup.contactEmail;
      description = selectedMeetup.description;
      imageUrl = selectedMeetup.imageUrl;
    });

    unsubscribe();
    }





let dispatch = createEventDispatcher();

$: titleValid =!isEmpty(title);
$: subtitleValid=!isEmpty(subtitle)
$: adressValid=!isEmpty(adress)
$: descriptionValid=!isEmpty(description) 
$: imageUrlValid=!isEmpty(imageUrl)
$: emailValid=isValidEmail(email)
$:formisValid=titleValid&& 
subtitleValid &&
adressValid && 
descriptionValid && 
imageUrlValid && 
emailValid;


    function submitForm (){
        const meetupDate= {
            
            title:title,
            subtitle: subtitle,
            description:description,
            imageUrl:imageUrl,
            contactEmail:email,
            adress:adress
        };
        if(id){
            fetch(`https://reffat-964d5-default-rtdb.firebaseio.com/meetup/${id}.json`,{
                method:'PATCH',
                body:JSON.stringify(meetupDate),
                headers:{'Content-Type': 'application/json'}
            }).then(res=>{
                if(!res.ok){
                    throw new Error('Falied')
                } 
                meetups.updateMeetup(id,meetupDate)
            }).catch(err=>{console.log(err)})

        }else{
            fetch('https://reffat-964d5-default-rtdb.firebaseio.com/meetup.json',{
                method:'POST',
                body:JSON.stringify({...meetupDate, isFavorite:false}),
                headers:{'Content-Type': 'application/json'}
            }).then(res=>{
                if(!res.ok){
                    throw new Error('Falied')
                }
                return res.json();
            }).then(data=>{
                meetups.addMeetup({
                    ...meetupDate,
                isFavorite: false,
                id:data.name});
            }).catch(
                err=>console.log(err)
                );
        }
        
        dispatch('save',)
    }
    function cancel() {
        dispatch('cancel');
    }
    function deleteMeetup(event){
        fetch(`https://reffat-964d5-default-rtdb.firebaseio.com/meetup/${id}.json`,{
                method:'DELETE',
            }).then(res=>{
                if(!res.ok){
                    throw new Error('Falied')
                } 
                meetups.removeMeetup(id);
            }).catch(err=>{console.log(err)})
        
        dispatch('save')

    }
</script>

<Modal title='Edit Meetup date' on:cancel>
    <form on:submit|preventDefault={submitForm}>
        <TextInput 
        id="title" 
        label= 'Title ' 
        valid={titleValid}
        validityMessage={"Please enter a valid title"}
        value={title} 
        on:input={event=>title= event.target.value} />
        <TextInput 
        id="subtitle" 
        label= 'Subtitle ' 
        valid={subtitleValid}
        validityMessage={"Please enter a valid subtitle"}
        value={subtitle} 
        on:input={event=>subtitle= event.target.value} />
        <TextInput 
        id="address" 
        label= 'addres ' 
        valid={adressValid}
        validityMessage={"Please enter a valid address"}
        value={adress} 
        on:input={event=> adress= event.target.value} />
        <TextInput 
        id="imageUrl" 
        label= 'image Url '
        valid={imageUrlValid}
        validityMessage={"Please enter a valid image url"}
        value={imageUrl} 
        on:input={event=>imageUrl= event.target.value} />
        <TextInput 
        id="email" 
        label= 'Email ' 
        valid={emailValid}
        validityMessage={"Please enter a valid email address"}
        value={email} 
        type='email'
        on:input={event=>email= event.target.value} />
        <TextInput 
        id="description" 
        valid={descriptionValid}
        validityMessage={"Please enter a valid description"}
        label= 'Description '
        controlType='textarea' 
        value={description} 
        on:input={event=>description= event.target.value} />
        
    </form>
    <div slot="footer">
        <Button type="button" mode="outline" on:click={cancel}>Cancel</Button>
        <Button type="button" on:click={submitForm} disabled={!formisValid}>
            Save
        </Button>
        {#if  id}
            <Button type="button" on:click={deleteMeetup} >Delete</Button>
        {/if}
    </div>
</Modal>

<style>
    form{
        width: 100%;
    }
</style>