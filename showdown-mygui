// This sample application only interacts with showdown.js in
// two places:
//
//  In startGui():
//
//      converter = new Showdown.converter();
//
//  In convertText():
//
//      text = converter.makeHtml(text);
//
// The rest of this file is user interface stuff.
//


//
// Register for onload
//
window.onload = startGui;


//
// Globals
//

var converter;
var convertTextTimer,processingTime;
var lastText,lastOutput,lastRoomLeft;
var convertTextSetting, convertTextButton;
var inputPane,previewPane,outputPane,syntaxPane;
var maxDelay = 3000; // longest update pause (in ms)


//
//  Initialization
//

function startGui() {
  // settings
  convertTextSetting = "continuous";

  inputPane = document.getElementById("inputPane");
  previewPane = document.getElementById("previewPane");
  outputPane = document.getElementById("outputPane");
  syntaxPane = document.getElementById("syntaxPane");


  // First, try registering for keyup events
  // (There's no harm in calling onInput() repeatedly)
  window.onkeyup = inputPane.onkeyup = onInput;

  // In case we can't capture paste events, poll for them
  var pollingFallback = window.setInterval(function(){
    if(inputPane.value != lastText)
      onInput();
  },1000);

  // Try registering for paste events
  inputPane.onpaste = function() {
    // It worked! Cancel paste polling.
    if (pollingFallback!=undefined) {
      window.clearInterval(pollingFallback);
      pollingFallback = undefined;
    }
    onInput();
  }

  // Try registering for input events (the best solution)
  if (inputPane.addEventListener) {
    // Let's assume input also fires on paste.
    // No need to cancel our keyup handlers;
    // they're basically free.
    inputPane.addEventListener("input",inputPane.onpaste,false);
  }

  // start with blank page?
  if (top.document.location.href.match(/\?blank=1$/))
    inputPane.value = "";

  // build the converter
  converter = new Showdown.converter();

  // do an initial conversion to avoid a hiccup
  convertText();

  // give the input pane focus
  inputPane.focus();

  // start the other panes at the top
  // (our smart scrolling moved them to the bottom)
  previewPane.scrollTop = 0;
  outputPane.scrollTop = 0;
}


//
//  Conversion
//

function convertText() {
  // get input text
  var text = inputPane.value;
  
  // if there's no change to input, cancel conversion
  if (text && text == lastText) {
    return;
  } else {
    lastText = text;
  }

  var startTime = new Date().getTime();

  // Do the conversion
  text = converter.makeHtml(text);

  // display processing time
  var endTime = new Date().getTime(); 
  processingTime = endTime - startTime;
  document.getElementById("processingTime").innerHTML = processingTime+" ms";
  
  // update preview pane
  previewPane.innerHTML = text;

  lastOutput = text;
  
};

function onInput() {
// In "delayed" mode, we do the conversion at pauses in input.
// The pause is equal to the last runtime, so that slow
// updates happen less frequently.
//
// Use a timer to schedule updates.  Each keystroke
// resets the timer.

  // if we already have convertText scheduled, cancel it
  
  if (convertTextTimer) {
    window.clearTimeout(convertTextTimer);
    convertTextTimer = undefined;
  }

  if (convertTextSetting.value != "manual") {
    var timeUntilConvertText = 0;
    if (convertTextSetting.value == "delayed") {
      // make timer adaptive
      timeUntilConvertText = processingTime;
    }

    if (timeUntilConvertText > maxDelay)
      timeUntilConvertText = maxDelay;

    // Schedule convertText().
    // Even if we're updating every keystroke, use a timer at 0.
    // This gives the browser time to handle other events.
    convertTextTimer = window.setTimeout(convertText,timeUntilConvertText);
  }

}
