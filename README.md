# Friday-Ai
My personal AI assistant
index.html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="theme-color" content="#050b18">
<title>FRIDAY AI</title>

<style>
*{box-sizing:border-box}
body{
 margin:0;background:#050914;color:#eaf6ff;
 font-family:Arial,sans-serif;min-height:100vh;
}
header{
 padding:20px;display:flex;justify-content:space-between;
 align-items:center;border-bottom:1px solid #12314b;
}
.logo{font-size:23px;font-weight:bold;color:#55caff;letter-spacing:3px}
.status{font-size:12px;color:#43e6bb}
nav{display:flex;justify-content:center;gap:12px;padding:15px}
button{
 background:#10243b;color:#75d9ff;border:1px solid #17608b;
 padding:11px 17px;border-radius:12px;font-size:14px;
}
button:active{transform:scale(.96)}
main{max-width:650px;margin:auto;padding:15px}
.core-area{text-align:center;padding:25px 0 15px}
.core{
 width:190px;height:190px;margin:10px auto 22px;
 border-radius:50%;display:flex;align-items:center;
 justify-content:center;position:relative;
 background:radial-gradient(circle,#164c78,#07172d 65%,#050914);
 border:2px solid #39caff;
 box-shadow:0 0 20px #087dbb,0 0 65px #087dbb55,inset 0 0 35px #159bff66;
 animation:pulse 3s infinite;
}
.core:before,.core:after{
 content:"";position:absolute;border:1px solid #39bfff;
 border-radius:50%;inset:-13px;animation:spin 8s linear infinite;
}
.core:after{inset:-25px;border-style:dashed;animation:spin 15s linear infinite reverse}
.core span{font-size:35px;font-weight:bold;color:#9ceaff;text-shadow:0 0 20px #00bfff}
@keyframes pulse{50%{box-shadow:0 0 35px #159bff,0 0 90px #087dbb88}}
@keyframes spin{to{transform:rotate(360deg)}}
h1{font-size:25px;margin:10px}
.subtitle{color:#80a8c4;font-size:13px}
#chat{
 height:290px;overflow-y:auto;padding:15px;
 background:#091525;border:1px solid #163c5c;
 border-radius:15px;margin-top:15px;
}
.message{
 padding:12px 14px;margin:10px 0;border-radius:13px;
 max-width:88%;font-size:14px;line-height:1.5;
 white-space:pre-wrap;overflow-wrap:anywhere;
}
.bot{background:#102c46;border:1px solid #17608b}
.user{background:#124c70;margin-left:auto}
.input-area{display:flex;gap:8px;margin-top:12px}
input{
 flex:1;min-width:0;background:#0b1a2b;color:white;
 border:1px solid #245274;border-radius:12px;padding:14px;
 font-size:15px;outline:none;
}
.send{background:#087dbb;color:white}
.tools{display:flex;gap:10px;justify-content:center;margin:14px 0}
.tools button{flex:1}
.panel{
 background:#091525;border:1px solid #163c5c;
 border-radius:14px;padding:15px;margin-top:15px;
}
.panel h3{color:#62d0ff}
select{
 background:#10243b;color:white;padding:10px;
 border:1px solid #17608b;border-radius:8px;
}
footer{text-align:center;color:#52758e;font-size:11px;padding:25px}
</style>
</head>

<body>

<header>
 <div class="logo">FRIDAY AI</div>
 <div class="status">● SYSTEM ONLINE</div>
</header>

<nav>
 <button onclick="showPage('home')">HOME</button>
 <button onclick="showPage('history')">HISTORY</button>
 <button onclick="showPage('settings')">SETTINGS</button>
</nav>

<main>

<section id="home">
 <div class="core-area">
  <div class="core"><span>F</span></div>
  <h1>HELLO, I'M FRIDAY</h1>
  <div class="subtitle" id="status">Your personal AI assistant</div>
 </div>

 <div id="chat">
  <div class="message bot">
   Hello! I am FRIDAY AI. How can I help you today?
   <br>হ্যালো! আমি FRIDAY AI। আপনাকে কীভাবে সাহায্য করতে পারি?
  </div>
 </div>

 <div class="input-area">
  <input id="question" placeholder="Ask FRIDAY anything...">
  <button class="send" onclick="sendMessage()">SEND</button>
 </div>

 <div class="tools">
  <button onclick="startVoice()">🎙️ SPEAK</button>
  <button onclick="speakLast()">🔊 VOICE</button>
  <button onclick="clearChat()">CLEAR</button>
 </div>
</section>

<section id="history" style="display:none">
 <div class="panel">
  <h3>CHAT HISTORY</h3>
  <div id="historyList">Your conversations will appear here.</div>
  <button onclick="showPage('home')">BACK TO CHAT</button>
 </div>
</section>

<section id="settings" style="display:none">
 <div class="panel">
  <h3>FRIDAY SETTINGS</h3>
  <p>Language</p>
  <select id="language">
   <option value="en-US">English</option>
   <option value="bn-BD">বাংলা</option>
  </select>
  <p>Voice assistant</p>
  <button onclick="speak('Hello! I am FRIDAY AI.')">TEST VOICE</button>
  <p>Voice output</p>
  <button onclick="toggleVoice()">TOGGLE VOICE</button>
  <p id="voiceStatus">Voice enabled</p>
 </div>
</section>

</main>

<footer>FRIDAY AI • YOUR PERSONAL ASSISTANT</footer>

<script>
let voiceEnabled=true;
let lastAnswer="Hello! I am FRIDAY AI.";
let messages=JSON.parse(localStorage.getItem("fridayHistory")||"[]");

function addMessage(text,type){
 const chat=document.getElementById("chat");
 const div=document.createElement("div");
 div.className="message "+type;
 div.textContent=text;
 chat.appendChild(div);
 chat.scrollTop=chat.scrollHeight;
}

function saveHistory(){
 localStorage.setItem("fridayHistory",JSON.stringify(messages));
}

function sendMessage(){
 const input=document.getElementById("question");
 const text=input.value.trim();
 if(!text)return;

 addMessage(text,"user");
 messages.push({role:"user",text});
 input.value="";
 saveHistory();

 // Temporary demo response.
 // Connect a secure AI backend to enable real AI answers.
 let answer=makeDemoAnswer(text);
 setTimeout(()=>{
  addMessage(answer,"bot");
  messages.push({role:"assistant",text:answer});
  lastAnswer=answer;
  saveHistory();
  if(voiceEnabled)speak(answer);
 },500);
}

function makeDemoAnswer(text){
 const q=text.toLowerCase();

 if(q.includes("hello")||q.includes("hi"))
  return "Hello! I am FRIDAY AI. Nice to meet you!";

 if(q.includes("who are you"))
  return "I am FRIDAY AI, your personal digital assistant.";

 if(q.includes("bangla")||q.includes("বাংলা"))
  return "আমি FRIDAY AI। আপনাকে সাহায্য করতে প্রস্তুত!";

 if(q.includes("time"))
  return "Your device time is "+new Date().toLocaleTimeString();

 if(q.includes("date"))
  return "Today's date is "+new Date().toLocaleDateString();

 return "I received your message: "+text+
 "\n\nMy AI brain is not connected yet. We will connect a real AI service in the next development step.";
}

function speak(text){
 if(!voiceEnabled)return;
 if(!("speechSynthesis" in window)){
  alert("Voice output is not supported in this browser.");
  return;
 }
 speechSynthesis.cancel();
 let utterance=new SpeechSynthesisUtterance(text);
 utterance.lang=document.getElementById("language").value;
 utterance.rate=0.95;

 const voices=speechSynthesis.getVoices();
 const femaleVoice=voices.find(v=>
  /female|samantha|zira|google.*female/i.test(v.name)
 );
 if(femaleVoice)utterance.voice=femaleVoice;
 speechSynthesis.speak(utterance);
}

function speakLast(){speak(lastAnswer)}

function toggleVoice(){
 voiceEnabled=!voiceEnabled;
 document.getElementById("voiceStatus").textContent=
 voiceEnabled?"Voice enabled":"Voice disabled";
 if(!voiceEnabled)speechSynthesis.cancel();
}

function startVoice(){
 const SpeechRecognition=window.SpeechRecognition||
 window.webkitSpeechRecognition;

 if(!SpeechRecognition){
  alert("Speech recognition is not supported in this browser. Try a supported browser.");
  return;
 }

 const recognition=new SpeechRecognition();
 recognition.lang=document.getElementById("language").value;
 recognition.start();

 document.getElementById("status").textContent="Listening...";

 recognition.onresult=function(event){
  document.getElementById("question").value=
  event.results[0][0].transcript;
  document.getElementById("status").textContent="Ready";
  sendMessage();
 };
 recognition.onerror=function(){
  document.getElementById("status").textContent="Voice unavailable";
 };
}

function clearChat(){
 document.getElementById("chat").innerHTML="";
 messages=[];
 saveHistory();
}

function showPage(page){
 ["home","history","settings"].forEach(id=>{
  document.getElementById(id).style.display=
  id===page?"block":"none";
 });

 if(page==="history"){
  const list=document.getElementById("historyList");
  list.innerHTML="";
  if(!messages.length){
   list.textContent="No saved conversations yet.";
  }else{
   messages.forEach(m=>{
    const p=document.createElement("p");
    p.textContent=(m.role==="user"?"You: ":"FRIDAY: ")+m.text;
    list.appendChild(p);
   });
  }
 }
}

document.getElementById("question").addEventListener("keydown",e=>{
 if(e.key==="Enter")sendMessage();
});
</script>

</body>
</html>
