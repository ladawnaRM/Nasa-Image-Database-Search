<template>
    <div>
    <div id="search">	
      <!-- General search options -->
      <div name="general_search" id="general_search">
        <h5>GENERAL SEARCH</h5>
        <input type="text" v-model="query" v-on:keyup="populate()">
        <br>
        <br>
        <hr>
      </div>

      <!-- Advanced search options -->
      <div name="advanced_search" class="form-group" id="advanced_search">
        <h5>ADVANCED SEARCH</h5>
        <p>Keywords:</p>
        <input type="text" v-model="keywords" v-on:keyup="checkAdvanced()" placeholder="i.e. stars">
	    <br>
		
	    <p>Location:</p>
	    <input type="text" v-model="location" v-on:keyup="checkAdvanced()" placeholder="i.e. Greenbelt, MD">
	    <br>
		
		<div>
		  <div id="date_left">
	        <p>Year Start:</p>
	        <input type="number" v-model="year_start" v-on:keyup="checkAdvanced()">
	        <br>
		  </div>
		
		  <div id="date_right">
	        <p>Year End:</p>
	        <input type="number" v-model="year_end" v-on:keyup="checkAdvanced()">
	        <br>
		  </div>
		</div>
	    <p>Data Type Returned:</p>
	    <input type="radio" id="image" value="image" name="data-type" v-model="type" checked>
	    <label for="image">Image</label>
	    <br>
	    <!--input type="radio" id="video" value="video" name="data-type" v-model="type">
	    <label for="video">Video</label-->
	  </div>
	  	  
    </div>
	   <!--The code for the popup modal with additional information-->
	   <div class="backdrop" id="modal">
        <div class="modal">
          <div name="body" class="modal-body">
			<button type="button" class="btn_top" @click="close">x</button>
			<h5 id="title"></h5>
			<img id="nasa_image" src=''>
		    <p id="description"><br><br><b>Welcome to an interface for the NASA image database!</b><br><br>Type in search terms to browse the database of images. <br> Click on an image to enlarge it and get more information.<br><br></p>
			<p id="date_created"></p>
			<p id="keywords"></p>
			<button type="button" class="btn_end" @click="close">Close</button>
          </div>
        </div>
      </div>

	  <!--The images resultant from the search-->
      <div name="results" v-for="result in results" class="container" id="results">
	    <div class="flex_container">
		  <!--Loops through the image results and populates them in the program-->
          <img class="img-fluid" id = "res_image" v-bind:src="result.links[0].href" v-on:click="moreInfo(result.links[0].href, result.data[0])"/> 
          <h5>{{result.data[0].title.substring(0,40)}}</h5>
		</div>
      </div>
    </div>
</template>

<script>
import axios from 'axios'
import styles from './Bootstrap/bootstrap.css'
export default {
  name: 'HelloWorld',
  data () {
    //Values to be returned from the HTML form
    return {
      query: '',
      keywords: '',
      year_start: '',
      year_end: '',
      location: '',	  
	  num_images: '',
      type: 'image',
      results: '',
	  added: '',
    }
  },
  methods: {
    //Checks to see if advanced search terms have been entered
    checkAdvanced() {
      var outString = ''
      if (this.location !== '') {
        outString = outString + `&location=${this.location}`
      }
	  if (this.keywords !== '') {
        outString = outString + `&keywords=${this.keywords}`
      }
	  if (this.year_start !== '') {
        outString = outString + `&year_start=${this.year_start}`
      }
	  if (this.year_end !== '') {
        outString = outString + `&year_end=${this.year_end}`
      }
	  this.added=outString
	  
	  //Runs the populate function
	  var link = `https://images-api.nasa.gov/search?q=${this.query}&media_type=${this.type}` + this.added
      axios.get(link).then((response) => { this.results = response.data.collection.items; })
      .catch((error) => { console.log(error) });
    },
	
	//Retrieves the images based on the search terms
    populate() {
	  //Accesses the api
	  var link = `https://images-api.nasa.gov/search?q=${this.query}&media_type=${this.type}` + this.added
	  //Deals with the response to return images or ignore errors
      axios.get(link).then((response) => { this.results = response.data.collection.items; })
      .catch((error) => { console.log(error) });
    },
	
	//Populates the additional information in the modal for the selected image
    moreInfo(href, obj) {
	  document.getElementById('title').innerHTML=obj.title
	  document.getElementById('description').innerHTML=obj.description
	  document.getElementById('date_created').innerHTML="<b>Date: </b>" + obj.date_created.substring(0, 10)
	  document.getElementById('keywords').innerHTML="<b>Keywords: </b>" + obj.keywords
	  document.getElementById('nasa_image').src=href
	  document.getElementById('modal').style.visibility='visible'
	  document.getElementById('modal').setAttribute(z-index, '1')
	},
	
	//Hides the modal with the extra information 
	close() {
	  document.getElementById('modal').style.visibility='hidden'
	  populate()
	}
  }
}
</script>

<!-- The CSS stylesheet -->
<style scoped>
  /*Styling for the search sidebar*/
  #search {
    float: left;
	float: bottom;
	position: fixed;
	top:0px;
	align: left;
	width: 13%;
	min-width: 212px;
	overflow-y: hidden;
	height: 12%;
	min-height: 100px;
	border: 5px solid #e8e8e8;
	opacity: .75;
	background-color: #eaeaea;
	box-shadow: 0px 0px 10px 1px grey;
	padding: 5px;
	border-radius:8px;
  }
  
  /*Styling for hovering over the search sidebar*/
  #search:hover {
    opacity: 1;
	height:100%;
	z-index: 1;
  }
  
  /*Styling for image container*/
  #results {
	height: auto;
	width: auto;
	float: right;
	display: flex;
	align-items: flex-end;
	justify-content: center;
	border: 1px;
	margin: 5px;
  }
  
  /*Styling for images*/
  img {
    border-radius: 8px;
	box-shadow: 0px 0px 15px 1px grey;
    margin-top: 5px;
	opacity: 1;
	margin-bottom: 5px;
  }
  
  /*The height and width of the images in the search*/
  #res_image { 
	height: 350px;
	width: 400px;
  }

  /*Styling for text*/
  p, input, label, button {
    font-family: "Agency FB";
	font-size: 20px;
  }
  
  /*Styling for captions and titles*/
  h5 {
    font-family: "Agency FB";
	font-weight: bold;
	font-size: 22px;
  }
  
  /*Styling for hovering over images*/
  img:hover {
    opacity: .65;
  }
  
  /*Styling for the background of the modal*/
  .backdrop {
    position: fixed;
    background-color: white;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  /*Styling for the body of the modal*/
  .modal {
    background: white;
	opacity: .98;
    display: flex;
    flex-direction: column;
	overflow-y: scroll;
	z-index: 1;
  }
  
  /*Styling for the top right close button*/
  .btn_top {
    border: none;
    font-size: 20px;
	float: right;
    padding: 20px;
    font-weight: bold;
    color: grey;
    background: transparent;
	z-index: 1;
  }
  
  /*Styling for the bottom right close button*/
  .btn_end {
    background: light grey;
    border: 1px solid grey;
	padding: 5px;
	margin: 5px;
	float: right;
    border-radius: 2px;
	z-index: 1;
  }
  
  /*Styling for the scrollbar*/
  ::-webkit-scrollbar {
    width: 10px;
  }
  
  /*Styling for the scrollbar*/
  ::-webkit-scrollbar-track {
    box-shadow: inset 0 0 2px grey;
  }
 
  /*Styling for the scrollbar*/
  ::-webkit-scrollbar-thumb {
    background: #dbdbdb; 
    border-radius: 10px;
  }

  /*Styling for the scrollbar*/
  ::-webkit-scrollbar-thumb:hover {
    background: #969696; 
  }

  
</style>
