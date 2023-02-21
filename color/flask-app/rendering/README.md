# Rendering pages

In this project, we render the page statically using `application/templates/index.html`:

**`application/templates/index.html`**
```html
<!DOCTYPE html>
<head>
    <title>The Palette Generator</title>
    <link rel="stylesheet" href="/static/css/main.css">
    <link rel="stylesheet" href="/static/css/meta.css">
    <link rel="stylesheet" href="/static/css/imagePicker.css">
    <link rel="stylesheet" href="/static/css/paramSetter.css">
    <link rel="stylesheet" href="/static/css/console.css">
</head>

<body>  
    
    <div class="meta" id="meta">
      <div class="title" id="title">
        {{% include "includes/title.html" %}} 
      </div>

      <div class="discription">
        {{% include "includes/description.html" %}}
      </div>
    </div>
    
    <div class="main">
      <div class="imagePicker">
        {{% include "includes/imagePicker.html" %}}
      </div>

      <div class="paramSetter">
        {{% include "includes/paramSetter.html" %}}
      </div>

      <div class="console">
        {{% include "includes/console.html" %}}
      </div>
    </div>
    
    <!-- JQUERY FOR SLIDER -->     
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
    <script src= "/static/javascript/slider.js"></script>
    <!-- JQUERY FOR THE REST -->      
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src= "/static/javascript/index.js"></script>

</body>
</html>
```
**`title.html`**
```html
<h1>The Palette Generator</h1>
```
**`description.html`**
```html
<p>
    A web app deployed on Heroku that uses k-means algorithm to pick out a color palette from an uploaded image.
</p>
```
**`imagePicker.html`**
```html
<div class="file-upload">
  <button for="file-input" class="file-upload-btn" onclick="$('.file-upload-input').trigger( 'click' )">Add Image</button>

  <div class="image-upload-wrap">
    <input id="file-input" class="file-upload-input" type="file" onchange="readURL(this);" accept="image/*" />
    <div class="drag-text">
      <h3>Drag and drop a file</h3>
    </div>
  </div>
  <div class="file-upload-content">
    <img id="selected-image" class="file-upload-image" src="" alt="your image" />
    <div class="image-title-wrap">
      <button id="remove-image" onclick="removeUpload()" class="remove-image">Remove <span class="image-title">Uploaded Image</span></button>
    </div>
  </div>
</div>
```
**`paramSetter.html`**
```html
<div class="container">
    <div class="card">
      <h2 id="color-number-text">Choose the number of colors</h2>
      <div class="form-group">
        <label for="cluster-number">Number of Colors:</label>
        <div class="input-group">
          <input id="cluster-number" type="number" min="1" max="9" value="1">
        </div>
      </div>
      <div class="form-group">
        <label for="slider">Slider:</label>
        <input id="slider" type="range" min="1" max="9" value="1" class="slider">
      </div>
    </div>
  </div>
```
**`console.html`**
```html
<div id="palette-text-div">
  <p id="palette-text">The Color Palette</p>
</div>

<div class="submit-group">
  <button id="submit-button" class="submit-button">Submit</button>
</div>

<div id="color-container">
  <div class="color-group">
    <div class="color-item">
      <span class="color-rectangle" id="color_1">Color 1</span>
      <span class="color-circle" id="circle_1"></span>
    </div>
    <div class="color-item">
      <span class="color-rectangle" id="color_2">Color 2</span>
      <span class="color-circle" id="circle_2"></span>
    </div>
    <div class="color-item">
      <span class="color-rectangle" id="color_3">Color 3</span>
      <span class="color-circle" id="circle_3"></span>
    </div>
  </div>

  <div class="color-group">
    <div class="color-item">
      <span class="color-rectangle" id="color_4">Color 4</span>
      <span class="color-circle" id="circle_4"></span>
    </div>
    <div class="color-item">
      <span class="color-rectangle" id="color_5">Color 5</span>
      <span class="color-circle" id="circle_5"></span>
    </div>
    <div class="color-item">
      <span class="color-rectangle" id="color_6">Color 6</span>
      <span class="color-circle" id="circle_6"></span>
    </div>
  </div>

  <div class="color-group">
    <div class="color-item">
      <span class="color-rectangle" id="color_7">Color 7</span>
      <span class="color-circle" id="circle_7"></span>
    </div>
    <div class="color-item">
      <span class="color-rectangle" id="color_8">Color 8</span>
      <span class="color-circle" id="circle_8"></span>
    </div>
    <div class="color-item">
      <span class="color-rectangle" id="color_9">Color 9</span>
      <span class="color-circle" id="circle_9"></span>
    </div>
  </div>
</div>
```
And the pages are linked with css files in `application/static/css/` directory and JS files in `application/static/JS/`. While tedious css is basically self-explained, here's the `application/static/javascript/index.js`

**`application/static/javascript/index.js`**
```js
$('#slider').on('input change',function(){
  $('#cluster-number').val($(this).val());
  });
  
  $('#cluster-number').keyup(function(e){
    if (e.keyCode==13) {   //only activates after pressing Enter key
    var val = $(this).val().replace(/\D/g,'');   // check only for digits
    $('#slider').val(val);
    }
  });

function resetColors() {
  $(".color-rectangle").each(function(index) {
    $(this).css("background-color", "white");
  });
  $(".color-circle").each(function(index) {
    $(this).css("background-color", "white");
  });
  $("#color_1").text("Color 1");
  $("#color_2").text("Color 2");
  $("#color_3").text("Color 3");
  $("#color_4").text("Color 4");
  $("#color_5").text("Color 5");
  $("#color_6").text("Color 6");
  $("#color_7").text("Color 7");
  $("#color_8").text("Color 8");
  $("#color_9").text("Color 9");
}


// READING IMAGE //   
   
let base64Image;
$(".file-upload-input").change(function() {
    let reader = new FileReader();
    reader.onload = function(e) {
    
        let dataURL = reader.result;
        $('.file-upload-image').attr("src", dataURL);
        base64Image = dataURL.replace("data:image\/png;base64,","");  //stripping off the metadata from the image if in png format
        
        if (base64Image == dataURL){
         base64Image = dataURL.replace("data:image\/jpeg;base64,","");} //stripping off the metadata from the image if in jpg format
        
        console.log(base64Image) // just to check if things are working fine
    }
      reader.readAsDataURL($(".file-upload-input")[0].files[0]);   // TO READ EMPTY WHEN NO IMAGE OR TEXT //
      // Reset color items
      resetColors();

});
    
    
// WHEN PREDICT BUTTON IS CLICKED //    

$("#submit-button").click(function(){
    resetColors();
    $("#submit-button").text( "Loading"); 
    
    // hide normally!
    let message = { // COLLECT THE INPUTS FROM USER AS JSON
        image: base64Image,
        name: $("#cluster-number").val()
    }
    
    console.log(message);

        
    // POPULATE THE COLOR NAMES AND CIRCLES 
        
    $.post("http://127.0.0.1:5000/", JSON.stringify(message), function(response){
            
       $.each( response, function( i, val ) {
        $("#color_" + i).text(val);
        $("#circle_" + i).css("background-color", val);
       
            
       });
            
       console.log(response);
       $("#submit-button").text( "Submit"); 
       });       
          
    }); 
	
function readURL(input) {
  if (input.files && input.files[0]) {
    let reader = new FileReader();
    reader.onload = function(e) {
      $('.image-upload-wrap').hide();
      $('.file-upload-image').attr('src', e.target.result);
      $('.file-upload-content').show();
      $('.image-title').html(input.files[0].name);
    };
    reader.readAsDataURL(input.files[0]);
  } else {
    removeUpload();
  }
}

function removeUpload() {
  $('.file-upload-input').replaceWith(function() {
    return $(this).val('');
  });
  $('.file-upload-content').hide();
  $('.image-upload-wrap').show();
}

$('.image-upload-wrap').on('dragover', function(e) {
  e.preventDefault();
  $(this).addClass('image-dropping');
});

$('.image-upload-wrap').on('dragleave', function(e) {
  e.preventDefault();
  $(this).removeClass('image-dropping');
});
```