<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no" />
<title>Buddy — Chat & Calls</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js" type="module"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth.js" type="module"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database.js" type="module"></script>
<!-- Cloudinary -->
<script src="https://upload-widget.cloudinary.com/global/all.js" type="text/javascript"></script>

<style>
/* All CSS is the same */
*{box-sizing:border-box;margin:0;padding:0;font-family:"Poppins",sans-serif;-webkit-tap-highlight-color:transparent;}
html,body{height:100%;background:#f3f5fa;color:#222}
#loading-screen{height:100vh;display:flex;flex-direction:column;align-items:center;justify-content:center;background:linear-gradient(135deg,#7b2ff7,#f107a3);color:#fff}
#loading-screen h1{font-size:32px;margin-bottom:8px}
#loading-screen p{font-size:12px;opacity:.9;position:fixed;bottom:14px}
#auth-view{display:none;align-items:center;justify-content:center;height:100vh}
.auth-box{width:92%;max-width:360px;background:#fff;padding:20px;border-radius:14px;box-shadow:0 6px 20px rgba(0,0,0,.08)}
.auth-box input{width:100%;padding:10px;margin-top:10px;border-radius:8px;border:1px solid #ddd}
.auth-box button{width:100%;padding:10px;margin-top:14px;border-radius:8px;border:0;background:linear-gradient(135deg,#7b2ff7,#f107a3);color:white; cursor:pointer;}
.switch-link{margin-top:10px;text-align:center;color:#7b2ff7;cursor:pointer;font-size:14px}
.top-bar{height:56px;display:flex;align-items:center;justify-content:space-between;padding:8px 12px;background:linear-gradient(135deg,#7b2ff7,#f107a3);color:#fff;position:relative}
.top-left,.top-right{display:flex;align-items:center}
.top-bar h1{font-size:18px}
.icon-btn{background:none;border:0;color:#fff;font-size:22px;cursor:pointer;padding:6px}
.profile-pic { width: 36px; height: 36px; border-radius: 50%; object-fit: cover; cursor: pointer; border: 2px solid white;}
.tab-bar{display:flex;background:#fff;border-bottom:1px solid #e6e6e6}
.tab{flex:1;padding:12px 0;text-align:center;font-weight:600;cursor:pointer}
.tab.active{color:#7b2ff7;border-bottom:3px solid #7b2ff7}
.main-content{display:flex;flex-direction:column;height:calc(100vh - 112px);background:#f3f5fa}
#friends-panel, #update-panel, #call-panel, #chat-window { width:100%; flex:1; overflow:auto; display: none; }
#friends-panel { background:#fff; }
.friend-item{padding:12px;border-bottom:1px solid #f0f0f0;display:flex;align-items:center;justify-content:space-between;cursor:pointer}
.friend-item .profile-pic { width: 48px; height: 48px; margin-right: 12px; border: none;}
.friend-item .left{display:flex;flex:1;flex-direction:column}
.friend-item .username{font-weight:600}
.friend-item .preview{font-size:12px;color:#888;margin-top:4px}
.unread-badge{background:#ef3b2d;color:#fff;padding:2px 7px;border-radius:999px;font-size:11px;display:none;margin-left:8px}
#add-friend-btn{width:90%;margin:10px auto;padding:10px;border-radius:22px;border:0;background:#7b2ff7;color:#fff; cursor: pointer; display: block;}
.placeholder-text { display: flex; align-items: center; justify-content: center; height: 100%; color: #888; }
#chat-window{display:none;flex-direction:column;background:#e5ddd5; position: relative;}
.chat-header{background:#fff;padding:12px;border-bottom:1px solid #ddd;display:flex;flex-direction:column}
.chat-header .icon-btn { color: #555; }
.chat-top{display:flex;align-items:center;justify-content:space-between}
.chat-top .left-items { display: flex; align-items: center; gap: 8px;}
.chat-top .left-items .profile-pic { width: 40px; height: 40px; border: none; }
#back-btn{background:#7b2ff7;color:#fff;border:0;padding:6px 10px;border-radius:8px; cursor: pointer;}
#chat-friend-name{font-weight:600}
#typing-indicator{font-size:12px;color:#666;margin-top:6px;display:none}
#messages{flex:1;padding:12px;overflow:auto;display:flex;flex-direction:column;gap:8px}
.message{padding:8px 12px;border-radius:18px;max-width:76%;word-break:break-word; position: relative; user-select: none;}
.my-message{background:#dcf8c6;align-self:flex-end}
.friend-message{background:#fff;align-self:flex-start}
.timestamp{font-size:11px;color:#666;margin-top:6px;text-align:right}
#message-input-container{display:flex;align-items: center; padding:8px;border-top:1px solid #ddd;background:#fff; flex-direction: column;}
#reply-preview { display: none; width: 100%; background: #f0f0f0; padding: 8px; border-radius: 8px; margin-bottom: 8px; font-size: 12px; color: #555; position: relative; }
#reply-preview p { margin: 0; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
#close-reply-btn { position: absolute; top: 5px; right: 5px; background: none; border: none; font-size: 16px; cursor: pointer; }
#message-input-bar { display: flex; width: 100%; align-items: center; }
#message-input{flex:1;padding:10px;border-radius:24px;border:1px solid #ddd}
#send-btn{background:#128c7e;color:#fff;border:0;padding:10px 12px;border-radius:999px;margin-left:8px;cursor:pointer}
#attach-btn { background:none; border: none; font-size: 24px; color: #555; cursor: pointer; margin-right: 8px;}
.message img, .message video { max-width: 100%; border-radius: 12px; margin-top: 5px; }
.read-receipt { font-size: 14px; margin-left: 5px; color: grey; }
.read-receipt.read { color: #34b7f1; }
.reply-quote { background: rgba(0,0,0,0.05); padding: 6px; border-left: 3px solid #7b2ff7; border-radius: 4px; margin-bottom: 5px; font-size: 12px; }
.deleted-message { font-style: italic; color: #888; }
#full-menu-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #fff; z-index: 1000; display: none; flex-direction: column; transform: translateX(-100%); transition: transform 0.3s ease-in-out; }
#full-menu-overlay.open { transform: translateX(0); }
.full-menu-header { display: flex; align-items: center; padding: 12px; border-bottom: 1px solid #eee; }
.full-menu-header h2 { font-size: 18px; margin-left: 10px; }
.close-btn { font-size: 24px; background: none; border: none; cursor: pointer; color: #333; }
.full-menu-content { padding: 10px; }
.menu-item { display: block; width: 100%; text-align: left; padding: 15px; font-size: 16px; border: none; background: none; border-bottom: 1px solid #f0f0f0; cursor: pointer; }
.menu-item.danger { color: #e74c3c; }
#chat-menu-dropdown, #message-context-menu { position: absolute; width: 200px; background: #fff; border-radius: 8px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); display: none; z-index: 50; overflow: hidden; }
#chat-menu-dropdown { top: 50px; right: 10px; }
.menu-action { display: block; width: 100%; padding: 12px; font-size: 15px; border: none; background: none; text-align: left; border-bottom: 1px solid #f0f0f0; cursor: pointer; }
.menu-action:last-child { border-bottom: none; }
.menu-action.danger { color: #e74c3c; }
@keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(255, 255, 255, 0.4); } 70% { box-shadow: 0 0 0 20px rgba(255, 255, 255, 0); } 100% { box-shadow: 0 0 0 0 rgba(255, 255, 255, 0); } }
#call-view { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: linear-gradient(to top, #141e30, #243b55); color: white; z-index: 100; flex-direction: column; align-items: center; justify-content: center; }
#videos-container { position: absolute; top: 0; left: 0; width: 100%; height: 100%; display: none; }
#remote-video { width: 100%; height: 100%; object-fit: cover; background: #000; }
#local-video { position: absolute; bottom: 100px; right: 20px; width: 100px; height: 140px; object-fit: cover; border-radius: 10px; border: 2px solid rgba(255,255,255,0.5); }
#audio-call-interface { display: none; flex-direction: column; align-items: center; justify-content: center; gap: 20px; }
#audio-call-avatar { width: 160px; height: 160px; border-radius: 50%; background: rgba(255,255,255,0.1); object-fit: cover; }
.pulsing-avatar { animation: pulse 2s infinite; }
#call-info { text-align: center; z-index: 5; position: relative; margin-bottom: 80px; }
#call-friend-name { font-size: 28px; font-weight: 600; }
#call-status { font-size: 18px; opacity: 0.8; margin-top: 5px; }
.call-controls { position: absolute; bottom: 30px; left: 50%; transform: translateX(-50%); display: flex; gap: 25px; z-index: 5; }
.call-controls button { font-size: 28px; width: 65px; height: 65px; border-radius: 50%; border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; background: rgba(255,255,255,0.15); color: white; transition: background-color 0.2s; }
.call-controls button:hover { background: rgba(255,255,255,0.3); }
.call-controls .end-call-btn { background: #e74c3c; }
.modal-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); z-index: 200; align-items: center; justify-content: center; }
.modal-box { background: white; color: #222; padding: 24px; border-radius: 16px; text-align: center; box-shadow: 0 10px 30px rgba(0,0,0,0.2); width: 90%; max-width: 340px; }
.modal-box h3 { margin-bottom: 8px; font-size: 22px; }
.modal-box p { margin-bottom: 20px; font-size: 16px; color: #666; }
.modal-actions { display: flex; gap: 15px; justify-content: center; }
.modal-actions button { padding: 12px 24px; border-radius: 24px; border: none; font-size: 16px; font-weight: 600; cursor: pointer; }
.accept-call-btn { background: #2ecc71; color: white; }
.reject-call-btn, .close-modal-btn { background: #e74c3c; color: white; }
#blocked-users-list, #friend-requests-list { max-height: 300px; overflow-y: auto; margin-top: 15px; text-align: left; }
.blocked-user-item { display: flex; justify-content: space-between; align-items: center; padding: 10px; border-bottom: 1px solid #f0f0f0; }
.blocked-user-item button { font-size: 12px; padding: 5px 10px; background: #3498db; color: white; border: none; border-radius: 5px; cursor: pointer; }
@media(min-width:900px){
  .main-content{flex-direction:row;height:calc(100vh - 112px)}
  #friends-panel, #update-panel, #call-panel {width:320px;flex:0 0 320px;border-right:1px solid #e6e6e6}
  #chat-window{display:flex}
  #back-btn { display: none; }
}
</style>
</head>
<body>

<div id="loading-screen"><h1>Buddy</h1><p>By Mukul Yadav</p></div>
<div id="auth-view" style="display:none"><div class="auth-box"><h2 id="auth-title">Login</h2><input id="email" placeholder="Email" type="email" /><input id="password" placeholder="Password" type="password" /><input id="username" placeholder="Username (for Sign Up)" type="text" style="display:none" /><button id="auth-action">Login</button><div class="switch-link" id="switch-mode">Don't have an account? Sign up</div><div class="switch-link" id="reset-password">Forgot password?</div></div></div>
<div id="main-view" style="display:none"><div class="top-bar"><div class="top-left"><img id="menu-btn" class="profile-pic" src="https://via.placeholder.com/36" alt="My Profile"/></div><h1>Buddy</h1></div><div id="full-menu-overlay"><div class="full-menu-header"><button id="close-menu-btn" class="close-btn">✖</button><h2>Menu</h2></div><div class="full-menu-content"><input type="file" id="pfp-input" accept="image/*" style="display: none;"/><button id="change-pfp-btn" class="menu-item">Change Profile Picture</button><button id="profile-btn" class="menu-item">View Profile</button><button id="blocked-users-btn" class="menu-item">Blocked Users</button><button id="friend-requests-btn" class="menu-item">Friend Requests</button><button id="logout-btn" class="menu-item danger">Logout</button></div></div><div class="tab-bar"><div class="tab active" data-tab="chat">Chat</div><div class="tab" data-tab="update">Update</div><div class="tab" data-tab="call">Call</div></div><div class="main-content"><div id="friends-panel"><button id="add-friend-btn">Add Friend</button><div id="friends-list"></div></div><div id="update-panel"><div class="placeholder-text">Updates will appear here.</div></div><div id="call-panel"><div class="placeholder-text">Call history will be shown here.</div></div><div id="chat-window"><div class="chat-header"><div class="chat-top"><div class="left-items"><button id="back-btn">Back</button><img id="chat-header-pic" class="profile-pic" src="https://via.placeholder.com/40"/><div id="chat-friend-name">Friend</div></div><div style="display:flex;gap:4px;align-items:center"><button id="audio-call-btn" class="icon-btn" title="Audio call">📞</button><button id="video-call-btn" class="icon-btn" title="Video call">🎥</button><button id="chat-menu-btn" class="icon-btn" title="More options">⋮</button></div></div><div id="typing-indicator">Typing...</div></div><div id="messages"></div><div id="message-input-container"><div id="reply-preview"><p></p><button id="close-reply-btn">✖</button></div><div id="message-input-bar"><button id="attach-btn">📎</button><input id="message-input" placeholder="Type a message..." /><button id="send-btn">➤</button></div></div><div id="chat-menu-dropdown"><button id="clear-chat-btn" class="menu-action">Clear Chat</button><button id="unfriend-btn" class="menu-action danger">Remove Friend</button><button id="block-user-btn" class="menu-action danger">Block User</button></div></div></div></div>
<div id="call-view"><div id="videos-container"><video id="remote-video" autoplay playsinline></video></div><div id="audio-call-interface"><img id="audio-call-avatar" src="https://via.placeholder.com/150"/><div id="call-info"><h2 id="call-friend-name"></h2><p id="call-status">Connecting...</p></div></div><video id="local-video" autoplay playsinline muted></video><div class="call-controls"><button id="toggle-mic-btn">🎙️</button><button id="end-call-btn" class="end-call-btn">📞</button></div></div>
<div id="incoming-call-modal" class="modal-overlay"><div class="modal-box"><h3 id="incoming-call-name">Incoming Call</h3><p id="incoming-call-type"></p><div class="modal-actions"><button id="reject-call-btn" class="reject-call-btn">Reject</button><button id="accept-call-btn" class="accept-call-btn">Accept</button></div></div></div>
<div id="blocked-users-modal" class="modal-overlay"><div class="modal-box"><h3>Blocked Users</h3><div id="blocked-users-list"></div><div class="modal-actions"><button id="close-blocked-modal-btn" class="close-modal-btn">Close</button></div></div></div>
<div id="friend-requests-modal" class="modal-overlay"><div class="modal-box"><h3>Friend Requests</h3><div id="friend-requests-list"></div><div class="modal-actions"><button id="close-requests-modal-btn" class="close-modal-btn">Close</button></div></div></div>
<div id="message-context-menu"><button class="menu-action" id="reply-msg-btn">Reply</button><button class="menu-action danger" id="delete-msg-btn">Delete for Everyone</button></div>

<script type="module">

// --- Firebase and Cloudinary Imports ---
import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js";
import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, sendPasswordResetEmail, signOut, onAuthStateChanged, updateProfile } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-auth.js";
import { getDatabase, ref, set, push, get, remove, onValue, update, off, onDisconnect } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "__FIREBASE_API_KEY__",
  authDomain: "__FIREBASE_AUTH_DOMAIN__",
  databaseURL: "__FIREBASE_DATABASE_URL__",
  projectId: "__FIREBASE_PROJECT_ID__",
  storageBucket: "__FIREBASE_STORAGE_BUCKET__",
  messagingSenderId: "__FIREBASE_MESSAGING_SENDER_ID__",
  appId: "__FIREBASE_APP_ID__"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getDatabase(app);

const ui = {
    loadingScreen: document.getElementById("loading-screen"), authView: document.getElementById("auth-view"), mainView: document.getElementById("main-view"),
    emailInput: document.getElementById("email"), passwordInput: document.getElementById("password"), usernameInput: document.getElementById("username"), authTitle: document.getElementById("auth-title"), authAction: document.getElementById("auth-action"), switchMode: document.getElementById("switch-mode"), resetPassword: document.getElementById("reset-password"),
    menuBtn: document.getElementById("menu-btn"), fullMenuOverlay: document.getElementById("full-menu-overlay"), closeMenuBtn: document.getElementById("close-menu-btn"),
    profileBtn: document.getElementById("profile-btn"), logoutBtn: document.getElementById("logout-btn"), friendRequestsBtn: document.getElementById('friend-requests-btn'),
    changePfpBtn: document.getElementById('change-pfp-btn'),
pfpInput: document.getElementById('pfp-input'),
    tabs: document.querySelectorAll(".tab"), friendsPanel: document.getElementById("friends-panel"), updatePanel: document.getElementById("update-panel"), callPanel: document.getElementById("call-panel"), friendsListDiv: document.getElementById("friends-list"), addFriendBtn: document.getElementById("add-friend-btn"),
    chatWindow: document.getElementById("chat-window"), chatFriendName: document.getElementById("chat-friend-name"), chatHeaderPic: document.getElementById('chat-header-pic'), backBtn: document.getElementById("back-btn"), typingIndicator: document.getElementById("typing-indicator"), messagesList: document.getElementById("messages"), messageInput: document.getElementById("message-input"), sendBtn: document.getElementById("send-btn"),
    attachBtn: document.getElementById('attach-btn'),
    replyPreview: document.getElementById('reply-preview'), closeReplyBtn: document.getElementById('close-reply-btn'),
    audioCallBtn: document.getElementById("audio-call-btn"), videoCallBtn: document.getElementById("video-call-btn"),
    chatMenuBtn: document.getElementById('chat-menu-btn'), chatMenuDropdown: document.getElementById('chat-menu-dropdown'), clearChatBtn: document.getElementById('clear-chat-btn'), unfriendBtn: document.getElementById('unfriend-btn'), blockUserBtn: document.getElementById('block-user-btn'),
    callView: document.getElementById('call-view'), videosContainer: document.getElementById('videos-container'), audioCallInterface: document.getElementById('audio-call-interface'), localVideo: document.getElementById('local-video'), remoteVideo: document.getElementById('remote-video'), callFriendName: document.getElementById('call-friend-name'), callStatus: document.getElementById('call-status'), endCallBtn: document.getElementById('end-call-btn'), toggleMicBtn: document.getElementById('toggle-mic-btn'), audioCallAvatar: document.getElementById('audio-call-avatar'),
    incomingCallModal: document.getElementById('incoming-call-modal'), incomingCallName: document.getElementById('incoming-call-name'), incomingCallType: document.getElementById('incoming-call-type'), acceptCallBtn: document.getElementById('accept-call-btn'), rejectCallBtn: document.getElementById('reject-call-btn'),
    blockedUsersBtn: document.getElementById('blocked-users-btn'), blockedUsersModal: document.getElementById('blocked-users-modal'), blockedUsersList: document.getElementById('blocked-users-list'), closeBlockedModalBtn: document.getElementById('close-blocked-modal-btn'),
    friendRequestsModal: document.getElementById('friend-requests-modal'), friendRequestsList: document.getElementById('friend-requests-list'), closeRequestsModalBtn: document.getElementById('close-requests-modal-btn'),
    messageContextMenu: document.getElementById('message-context-menu'), replyMsgBtn: document.getElementById('reply-msg-btn'), deleteMsgBtn: document.getElementById('delete-msg-btn'),
};

let isSignUp = false, currentUser = null, selectedFriendUID = null, callListenerUnsub = null;
let pc, localStream, remoteStream, incomingCallData = null;
let replyToMessageId = null;
let contextMenuMessageId = null, contextMenuIsMyMessage = false;
let currentChatIdForTyping = null;
let typingRefForDisconnect = null;
const DEFAULT_PFP_URL = 'https://via.placeholder.com/50';

const showLoading = v => ui.loadingScreen.style.display = v ? "flex" : "none";
const showAuth = v => ui.authView.style.display = v ? "flex" : "none";
const showMain = v => ui.mainView.style.display = v ? "block" : "none";

// Menu Logic
ui.menuBtn.addEventListener("click", () => { ui.fullMenuOverlay.style.display = 'flex'; setTimeout(() => ui.fullMenuOverlay.classList.add('open'), 10); });
ui.closeMenuBtn.addEventListener("click", () => { ui.fullMenuOverlay.classList.remove('open'); setTimeout(() => ui.fullMenuOverlay.style.display = 'none', 300); });
ui.chatMenuBtn.addEventListener('click', () => { ui.chatMenuDropdown.style.display = ui.chatMenuDropdown.style.display === 'block' ? 'none' : 'block'; });
document.addEventListener("click", (e)=> {
  if(!ui.chatMenuDropdown.contains(e.target) && e.target !== ui.chatMenuBtn){ ui.chatMenuDropdown.style.display = "none"; }
  if (!ui.messageContextMenu.contains(e.target)) { ui.messageContextMenu.style.display = 'none'; }
});
// --- Profile Picture Logic (Corrected for Cloudinary) ---
ui.changePfpBtn.addEventListener('click', () => {
    // This line makes the "Change Profile Picture" button work by triggering the hidden file input
    ui.pfpInput.click(); 
});

ui.pfpInput.addEventListener('change', async (e) => {
    const file = e.target.files[0];
    if (!file || !currentUser) return;
    ui.closeMenuBtn.click(); // Close the menu after selection
    showLoading(true);

    // This uses the Cloudinary Upload Widget you already have configured
    const myWidget = cloudinary.createUploadWidget({
    cloudName: '__CLOUDINARY_CLOUD_NAME__', 
    uploadPreset: '__CLOUDINARY_UPLOAD_PRESET__'
}, (error, result) => { 
// ...
        sources: ['local', 'camera'],
        multiple: false,
        cropping: true, // Allows user to crop their image to a square
        croppingAspectRatio: 1,
        showSkipCropButton: false
    }, (error, result) => { 
        if (!error && result && result.event === "success") { 
            const downloadURL = result.info.secure_url;
            updateUserProfilePicture(downloadURL);
        } else if (error) {
            console.error("Cloudinary PFP Upload failed:", error);
            alert("Upload failed. Please try again.");
            showLoading(false);
        }
    });
    
    myPfpWidget.open(); // Open the widget to start the upload process
});

async function updateUserProfilePicture(downloadURL) {
    try {
        if (!downloadURL) throw new Error("Upload failed, no URL returned.");

        await updateProfile(auth.currentUser, { photoURL: downloadURL });
        await update(ref(db, `users/${currentUser.uid}`), { profilePicUrl: downloadURL });
        
        ui.menuBtn.src = downloadURL; // Update the menu icon immediately
        alert("Profile picture updated!");
    } catch (error) {
        console.error("PFP Update failed:", error);
        alert("Update failed. Please try again.");
    } finally {
        showLoading(false);
    }
}

// Auth UI logic
ui.switchMode.addEventListener("click", ()=>{
  isSignUp = !isSignUp; ui.authTitle.innerText = isSignUp ? "Sign Up" : "Login"; ui.authAction.innerText = isSignUp ? "Sign Up" : "Login";
  ui.usernameInput.style.display = isSignUp ? "block" : "none"; ui.switchMode.innerText = isSignUp ? "Already have an account? Login" : "Don't have an account? Sign up";
});
ui.authAction.addEventListener("click", async ()=>{
  const email = ui.emailInput.value.trim(); const pwd = ui.passwordInput.value; const username = ui.usernameInput.value.trim();
  if(!email || !pwd) return alert("Enter email & password");
  try{
    if(isSignUp){
      if(!username) return alert("Enter a username");
      const userRef = ref(db, `usernames/${username}`);
      const userSnap = await get(userRef);
      if(userSnap.exists()) return alert("Username already taken.");
      const cred = await createUserWithEmailAndPassword(auth, email, pwd);
      await updateProfile(cred.user, { displayName: username, photoURL: DEFAULT_PFP_URL });
      const updates = {};
      updates[`/users/${cred.user.uid}`] = { username, email, profilePicUrl: DEFAULT_PFP_URL };
      updates[`/usernames/${username}`] = cred.user.uid;
      await update(ref(db), updates);
    } else { await signInWithEmailAndPassword(auth, email, pwd); }
  } catch(err){ alert(err.message || err); }
});
ui.resetPassword.addEventListener("click", async ()=>{
  const email = ui.emailInput.value.trim(); if(!email) return alert("Enter your email");
  try{ await sendPasswordResetEmail(auth, email); alert("Password reset email sent"); }
  catch(err){ alert(err.message || err); }
});
ui.logoutBtn.addEventListener("click", async ()=> { try{ await signOut(auth); } catch(e){ console.error(e); } });
ui.profileBtn.addEventListener("click", async ()=>{
  if(!currentUser) return alert("Not signed in");
  const u = await get(ref(db, `users/${currentUser.uid}`));
  alert(`Username: ${u.val()?.username || currentUser.displayName}\nEmail: ${currentUser.email}`);
  ui.closeMenuBtn.click();
});

// Tabs Logic
ui.tabs.forEach(tab => {
    tab.addEventListener("click", () => {
        ui.tabs.forEach(t => t.classList.remove("active"));
        tab.classList.add("active");
        ui.friendsPanel.style.display = "none"; ui.updatePanel.style.display = "none"; ui.callPanel.style.display = "none";
        if (window.innerWidth < 900) { ui.chatWindow.style.display = "none"; }
        const tabName = tab.dataset.tab;
        if (tabName === 'chat') { ui.friendsPanel.style.display = 'block'; }
        else if (tabName === 'update') { ui.updatePanel.style.display = 'block'; }
        else if (tabName === 'call') { ui.callPanel.style.display = 'block'; }
    });
});

// Friends, Chat, and Block Logic
async function loadFriendRequests(){
  if(!currentUser) return;
  const refReq = ref(db, `friendRequests/${currentUser.uid}`);
  onValue(refReq, snap=>{
    ui.friendRequestsList.innerHTML = ""; const data = snap.val() || {}; let count = 0;
    for(const key in data){
      count++; const r = data[key]; const div = document.createElement("div"); div.className = "blocked-user-item"; const name = r.username || "Unknown";
      div.innerHTML = `<span>${name}</span><button data-from="${r.from}" class="accept-btn" style="background: #2ecc71">Accept</button>`;
      ui.friendRequestsList.appendChild(div);
    }
    if (count === 0) ui.friendRequestsList.innerHTML = '<p style="padding: 10px; text-align: center; color: #888;">No new requests.</p>';
    document.querySelectorAll(".accept-btn").forEach(btn=>{
      btn.onclick = async ()=>{
        const fromUid = btn.dataset.from; if(!fromUid) return;
        const fromUserSnap = await get(ref(db, `users/${fromUid}`));
        const fromUsername = fromUserSnap.val()?.username || "Friend";
        await set(ref(db, `friends/${currentUser.uid}/${fromUid}`), { username: fromUsername });
        await set(ref(db, `friends/${fromUid}/${currentUser.uid}`), { username: currentUser.displayName || "You" });
        await remove(ref(db, `friendRequests/${currentUser.uid}/${fromUid}`));
      };
    });
  });
}
function attachUnreadAndPreview(friendUid, badgeEl, previewEl, chatId){
    onValue(ref(db, `chats/${chatId}/messages`), snap=>{
        const msgs = snap.val() || {}; let lastMsgContent = ""; let unreadCount = 0;
        const messageKeys = Object.keys(msgs).sort((a, b) => msgs[a].timestamp - msgs[b].timestamp);
        if (messageKeys.length > 0) {
            const lastMsg = msgs[messageKeys[messageKeys.length - 1]];
            if (lastMsg.deleted) { lastMsgContent = '🚫 This message was deleted'; }
            else { lastMsgContent = lastMsg.type === 'text' ? lastMsg.text : (lastMsg.type === 'image' ? '📷 Photo' : '📹 Video'); }
        }
        messageKeys.forEach(key => {
            const msg = msgs[key];
            if (msg.from !== currentUser.uid && !msg.read) { unreadCount++; }
        });
        previewEl.textContent = lastMsgContent; badgeEl.textContent = unreadCount;
        badgeEl.style.display = unreadCount > 0 ? 'inline-block' : 'none';
    });
}
async function loadFriends(){
  if(!currentUser) return;
  const myBlockedSnap = await get(ref(db, `blockedUsers/${currentUser.uid}`));
  const myBlockedList = myBlockedSnap.val() || {};
  
  onValue(ref(db, `friends/${currentUser.uid}`), async (snap) => {
    ui.friendsListDiv.innerHTML = ""; const data = snap.val() || {};
    for(const uid in data){
        if (myBlockedList[uid]) continue;
        const theyBlockedMeSnap = await get(ref(db, `blockedUsers/${uid}/${currentUser.uid}`));
        if (theyBlockedMeSnap.exists()) continue;
        const friendName = data[uid].username || "Friend";
        const userSnap = await get(ref(db, `users/${uid}`));
        const friendPfp = userSnap.val()?.profilePicUrl || DEFAULT_PFP_URL;

        const holder = document.createElement("div"); holder.className = "friend-item";
        holder.innerHTML = `<img src="${friendPfp}" class="profile-pic"><div class="left"><div class="username">${friendName}</div><div class="preview" id="preview-${uid}"></div></div><div><span class="unread-badge" id="badge-${uid}">0</span></div>`;
        holder.onclick = ()=> openChat(uid, friendName, friendPfp); ui.friendsListDiv.appendChild(holder);
        const chatId = [currentUser.uid, uid].sort().join("_");
        attachUnreadAndPreview(uid, document.getElementById(`badge-${uid}`), document.getElementById(`preview-${uid}`), chatId);
    }
  });
}
ui.addFriendBtn.addEventListener("click", async () => {
    if (!currentUser) return alert("Sign in first");
    const friendName = prompt("Enter friend's username");
    if (!friendName || friendName.trim() === "") return;
    if (friendName === currentUser.displayName) return alert("You can't add yourself as a friend.");
    const usernameRef = ref(db, `usernames/${friendName.trim()}`);
    const usernameSnap = await get(usernameRef);
    if (!usernameSnap.exists()) { return alert("User not found."); }
    const foundUid = usernameSnap.val();
    const myBlockedSnap = await get(ref(db, `blockedUsers/${currentUser.uid}/${foundUid}`));
    if (myBlockedSnap.exists()) return alert("You have blocked this user. Unblock them to add as a friend.");
    const theyBlockedMeSnap = await get(ref(db, `blockedUsers/${foundUid}/${currentUser.uid}`));
    if (theyBlockedMeSnap.exists()) return alert("You cannot send a friend request to this user.");
    await set(ref(db, `friendRequests/${foundUid}/${currentUser.uid}`), { from: currentUser.uid, username: currentUser.displayName, timestamp: Date.now() });
    alert("Friend request sent");
});
async function openChat(friendUid, friendName, friendPfp){
  if(!currentUser) return alert("Sign in first");
  selectedFriendUID = friendUid; ui.chatFriendName.textContent = friendName; ui.chatHeaderPic.src = friendPfp;
  if (window.innerWidth < 900) { ui.friendsPanel.style.display = "none"; }
  ui.chatWindow.style.display = "flex"; ui.messagesList.innerHTML = ""; ui.typingIndicator.style.display = "none";
  const chatId = [currentUser.uid, friendUid].sort().join("_");
  setupTypingPresence(chatId);
  onValue(ref(db, `chats/${chatId}/messages`), snap=>{
    ui.messagesList.innerHTML = ""; const msgs = snap.val() || {};
    const keys = Object.keys(msgs || {}).sort((a,b)=>(msgs[a].timestamp||0)-(msgs[b].timestamp||0));
    for(const k of keys){
      const m = msgs[k]; const div = document.createElement("div");
      div.className = `message ${m.from === currentUser.uid ? "my-message" : "friend-message"}`;
      div.dataset.messageId = k;
      let content = '';
      if (m.deleted) { content = '<div class="deleted-message">This message was deleted</div>'; }
      else {
          if (m.replyTo) {
              const repliedMsg = msgs[m.replyTo];
              if (repliedMsg && !repliedMsg.deleted) {
                  let repliedContent = repliedMsg.type === 'text' ? repliedMsg.text : (repliedMsg.type === 'image' ? 'Photo' : 'Video');
                  content += `<div class="reply-quote">${repliedContent}</div>`;
              }
          }
          if (m.type === 'image') { content += `<img src="${m.mediaUrl}" alt="Sent image">`; }
          else if (m.type === 'video') { content += `<video src="${m.mediaUrl}" controls></video>`; }
          else { content += `<div>${m.text}</div>`; }
      }
      let readReceipt = '';
      if (m.from === currentUser.uid && !m.deleted) {
          if (m.read) { readReceipt = '<span class="read-receipt read">✓✓</span>'; }
          else if (m.delivered) { readReceipt = '<span class="read-receipt">✓✓</span>'; }
          else { readReceipt = '<span class="read-receipt">✓</span>'; }
      }
      div.innerHTML = `${content}<div class="timestamp">${new Date(m.timestamp).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}${readReceipt}</div>`;
      ui.messagesList.appendChild(div);
      if (!m.deleted) {
          div.addEventListener('contextmenu', (e) => { e.preventDefault();
              contextMenuMessageId = k; contextMenuIsMyMessage = m.from === currentUser.uid;
              ui.deleteMsgBtn.style.display = contextMenuIsMyMessage ? 'block' : 'none';
              ui.messageContextMenu.style.display = 'block'; ui.messageContextMenu.style.top = `${e.pageY}px`; ui.messageContextMenu.style.left = `${e.pageX}px`;
          });
      }
      if (m.from !== currentUser.uid && !m.read) { update(ref(db, `chats/${chatId}/messages/${k}`), { read: true }); }
    }
    ui.messagesList.scrollTop = ui.messagesList.scrollHeight;
  });
  onValue(ref(db, `chats/${chatId}`), snap => {
      if (snap.exists()) {
          const chatData = snap.val();
          Object.keys(chatData.messages || {}).forEach(msgId => {
              if (chatData.messages[msgId].from === currentUser.uid && !chatData.messages[msgId].delivered) {
                  update(ref(db, `chats/${chatId}/messages/${msgId}`), { delivered: true });
              }
          });
      }
  });
  onValue(ref(db, `typing/${chatId}`), snap=>{ const val = snap.val() || {}; ui.typingIndicator.style.display = val[friendUid] ? "block" : "none"; });
}
ui.sendBtn.addEventListener("click", async ()=>{
    if(!currentUser || !selectedFriendUID) return; 
    const text = ui.messageInput.value.trim(); 
    if(!text) return;
    const chatId = [currentUser.uid, selectedFriendUID].sort().join("_");
    const messageData = { from: currentUser.uid, text, type: 'text', timestamp: Date.now(), read: false, delivered: false };
    if (replyToMessageId) { messageData.replyTo = replyToMessageId; }
    await push(ref(db, `chats/${chatId}/messages`), messageData);
    ui.messageInput.value = ""; 
    await set(ref(db, `typing/${chatId}/${currentUser.uid}`), false);
    cancelReply();
});
ui.messageInput.addEventListener("input", async ()=>{
  if(!currentUser || !selectedFriendUID) return; const chatId = [currentUser.uid, selectedFriendUID].sort().join("_");
  await set(ref(db, `typing/${chatId}/${currentUser.uid}`), ui.messageInput.value.trim() !== "");
});
ui.backBtn.addEventListener("click", ()=>{
  ui.chatWindow.style.display = "none"; ui.friendsPanel.style.display = "block"; selectedFriendUID = null; cancelReply();
});
ui.unfriendBtn.addEventListener('click', async () => {
    if (!selectedFriendUID || !currentUser) return;
    if (confirm(`Are you sure you want to remove ${ui.chatFriendName.textContent} as a friend?`)) {
        await remove(ref(db, `friends/${currentUser.uid}/${selectedFriendUID}`));
        await remove(ref(db, `friends/${selectedFriendUID}/${currentUser.uid}`));
        ui.backBtn.click(); alert("Friend removed.");
    }
});
ui.blockUserBtn.addEventListener('click', async () => {
    if (!selectedFriendUID || !currentUser) return;
    if (confirm(`Are you sure you want to block ${ui.chatFriendName.textContent}? They will be removed as a friend and you will no longer see each other.`)) {
        await set(ref(db, `blockedUsers/${currentUser.uid}/${selectedFriendUID}`), true);
        await remove(ref(db, `friends/${currentUser.uid}/${selectedFriendUID}`));
        await remove(ref(db, `friends/${selectedFriendUID}/${currentUser.uid}`));
        ui.backBtn.click(); alert("User blocked and removed from friends.");
    }
});
async function showBlockedUsers() {
    const blockedSnap = await get(ref(db, `blockedUsers/${currentUser.uid}`));
    ui.blockedUsersList.innerHTML = '';
    const blocked = blockedSnap.val() || {};
    if (Object.keys(blocked).length === 0) {
        ui.blockedUsersList.innerHTML = '<p style="padding: 10px; text-align: center; color: #888;">You have not blocked any users.</p>';
        return;
    }
    for (const uid in blocked) {
        const userSnap = await get(ref(db, `users/${uid}`));
        const username = userSnap.val()?.username || 'Unknown User';
        const item = document.createElement('div');
        item.className = 'blocked-user-item';
        item.innerHTML = `<span>${username}</span><button data-uid="${uid}" class="unblock-btn">Unblock</button>`;
        ui.blockedUsersList.appendChild(item);
    }
    document.querySelectorAll('.unblock-btn').forEach(btn => {
        btn.onclick = async (e) => {
            const uidToUnblock = e.target.dataset.uid;
            await remove(ref(db, `blockedUsers/${currentUser.uid}/${uidToUnblock}`));
            alert('User unblocked.'); showBlockedUsers();
        };
    });
}
ui.blockedUsersBtn.addEventListener('click', () => { showBlockedUsers(); ui.blockedUsersModal.style.display = 'flex'; ui.closeMenuBtn.click(); });
ui.closeBlockedModalBtn.addEventListener('click', () => { ui.blockedUsersModal.style.display = 'none'; });
ui.friendRequestsBtn.addEventListener('click', () => { loadFriendRequests(); ui.friendRequestsModal.style.display = 'flex'; ui.closeMenuBtn.click(); });
ui.closeRequestsModalBtn.addEventListener('click', () => { ui.friendRequestsModal.style.display = 'none'; });
ui.clearChatBtn.addEventListener('click', async () => {
    if (!selectedFriendUID || !currentUser) return;
    if (confirm("Are you sure you want to clear this chat? This will only delete messages for you.")) {
        const chatId = [currentUser.uid, selectedFriendUID].sort().join("_");
        const messagesRef = ref(db, `chats/${chatId}/messages`);
        const messagesSnap = await get(messagesRef);
        const updates = {};
        messagesSnap.forEach(child => {
            updates[`${child.key}/deletedFor/${currentUser.uid}`] = true;
        });
        await update(messagesRef, updates);
        alert("Chat cleared.");
    }
});
function setupReply(messageId, messageText) {
    replyToMessageId = messageId;
    ui.replyPreview.querySelector('p').textContent = messageText;
    ui.replyPreview.style.display = 'block';
    ui.messageInput.focus();
}
function cancelReply() {
    replyToMessageId = null;
    ui.replyPreview.style.display = 'none';
}
ui.closeReplyBtn.addEventListener('click', cancelReply);
ui.replyMsgBtn.addEventListener('click', async () => {
    if (!contextMenuMessageId) return;
    const chatId = [currentUser.uid, selectedFriendUID].sort().join("_");
    const msgRef = ref(db, `chats/${chatId}/messages/${contextMenuMessageId}`);
    const msgSnap = await get(msgRef);
    if (msgSnap.exists()) {
        const msg = msgSnap.val();
        let content = msg.type === 'text' ? msg.text : (msg.type === 'image' ? 'Photo' : 'Video');
        setupReply(contextMenuMessageId, content);
    }
    ui.messageContextMenu.style.display = 'none';
});
ui.deleteMsgBtn.addEventListener('click', async () => {
    if (!contextMenuMessageId || !contextMenuIsMyMessage) return;
    if (confirm("Delete this message for everyone?")) {
        const chatId = [currentUser.uid, selectedFriendUID].sort().join("_");
        const msgRef = ref(db, `chats/${chatId}/messages/${contextMenuMessageId}`);
        await update(msgRef, { deleted: true, text: null, type: 'text', mediaUrl: null, replyTo: null });
    }
    ui.messageContextMenu.style.display = 'none';
});

// Cloudinary Media Upload
const myWidget = cloudinary.createUploadWidget({
    cloudName: 'dfmguxgue', 
    uploadPreset: 'gtkkcavn'
}, (error, result) => { 
    if (!error && result && result.event === "success") { 
        const fileType = result.info.resource_type;
        const url = result.info.secure_url;
        sendMediaMessage(fileType, url);
    }
});
ui.attachBtn.addEventListener('click', () => myWidget.open());
async function sendMediaMessage(fileType, url) {
    if (!currentUser || !selectedFriendUID) return;
    const chatId = [currentUser.uid, selectedFriendUID].sort().join("_");
    const messageData = { from: currentUser.uid, type: fileType, mediaUrl: url, timestamp: Date.now(), read: false, delivered: false };
    if (replyToMessageId) {
      messageData.replyTo = replyToMessageId;
    }
    await push(ref(db, `chats/${chatId}/messages`), messageData);
    cancelReply();
}

// Typing Presence
function setupTypingPresence(chatId) {
    if (typingRefForDisconnect) { onDisconnect(typingRefForDisconnect).cancel(); }
    currentChatIdForTyping = chatId;
    typingRefForDisconnect = ref(db, `typing/${chatId}/${currentUser.uid}`);
    onDisconnect(typingRefForDisconnect).set(false);
}
document.addEventListener("visibilitychange", () => {
    if (document.visibilityState === 'hidden' && currentUser && currentChatIdForTyping) {
        const typingRef = ref(db, `typing/${currentChatIdForTyping}/${currentUser.uid}`);
        set(typingRef, false);
    }
});

// WebRTC Calling Logic
async function setupPeerConnection(friendUid, isVideo) {
    const configuration = { iceServers: [{ urls: 'stun:stun.l.google.com:19302' }, { urls: 'stun:stun1.l.google.com:19302' }] };
    pc = new RTCPeerConnection(configuration);
    remoteStream = new MediaStream(); ui.remoteVideo.srcObject = remoteStream;
    try { localStream = await navigator.mediaDevices.getUserMedia({ video: isVideo, audio: true });
    } catch (e) { alert("You must allow camera/microphone access for calls."); return false; }
    ui.localVideo.srcObject = localStream; 
    if (isVideo) {
        ui.videosContainer.style.display = 'block'; ui.audioCallInterface.style.display = 'none'; ui.localVideo.style.display = 'block';
    } else {
        ui.videosContainer.style.display = 'none'; ui.audioCallInterface.style.display = 'flex'; ui.localVideo.style.display = 'none';
    }
    localStream.getTracks().forEach(track => pc.addTrack(track, localStream));
    pc.ontrack = (event) => { event.streams[0].getTracks().forEach(track => remoteStream.addTrack(track)); };
    const calleeCandidateRef = ref(db, `calls/${friendUid}/candidates`);
    pc.onicecandidate = (event) => { if (event.candidate) { push(calleeCandidateRef, event.candidate.toJSON()); } };
    const myCandidateRef = ref(db, `calls/${currentUser.uid}/candidates`);
    onValue(myCandidateRef, (snapshot) => { snapshot.forEach((childSnapshot) => { if (childSnapshot.exists()) { pc.addIceCandidate(new RTCIceCandidate(childSnapshot.val())).catch(e => {}); } }); });
    ui.callStatus.textContent = 'Ringing...';
    ui.audioCallAvatar.classList.add('pulsing-avatar');
    return true;
}
async function startCall(friendUid, friendName, isVideo) {
    const friendSnap = await get(ref(db, `users/${friendUid}`));
    ui.audioCallAvatar.src = friendSnap.val()?.profilePicUrl || DEFAULT_PFP_URL;
    ui.callFriendName.textContent = friendName; ui.callView.style.display = 'flex';
    if (!await setupPeerConnection(friendUid, isVideo)) { ui.callView.style.display = 'none'; return; }
    const callOfferRef = ref(db, `calls/${friendUid}`);
    const offerDescription = await pc.createOffer();
    await pc.setLocalDescription(offerDescription);
    const offer = { sdp: offerDescription.sdp, type: offerDescription.type };
    await set(callOfferRef, { offer, from: currentUser.uid, fromName: currentUser.displayName, type: isVideo ? 'video' : 'audio' });
    const myCallRef = ref(db, `calls/${currentUser.uid}`);
    onValue(myCallRef, async (snapshot) => {
        const data = snapshot.val();
        if (data?.answer && pc && !pc.currentRemoteDescription) {
            await pc.setRemoteDescription(new RTCSessionDescription(data.answer));
            ui.callStatus.textContent = 'Connected';
            ui.audioCallAvatar.classList.remove('pulsing-avatar');
        }
        if (data?.rejected) { alert(`${friendName} rejected the call.`); endCall(); }
    });
}
async function answerCall() {
    const { from: friendUid, fromName: friendName, type, offer } = incomingCallData;
    const friendSnap = await get(ref(db, `users/${friendUid}`));
    ui.audioCallAvatar.src = friendSnap.val()?.profilePicUrl || DEFAULT_PFP_URL;
    ui.incomingCallModal.style.display = 'none'; ui.callFriendName.textContent = friendName; ui.callView.style.display = 'flex';
    if (!await setupPeerConnection(friendUid, type === 'video')) { ui.callView.style.display = 'none'; return; }
    await pc.setRemoteDescription(new RTCSessionDescription(offer));
    const answerDescription = await pc.createAnswer();
    await pc.setLocalDescription(answerDescription);
    const answer = { type: answerDescription.type, sdp: answerDescription.sdp };
    const callerCallRef = ref(db, `calls/${friendUid}`);
    await update(callerCallRef, { answer });
    ui.callStatus.textContent = 'Connected';
    ui.audioCallAvatar.classList.remove('pulsing-avatar');
    await remove(ref(db, `calls/${currentUser.uid}`));
    incomingCallData = null;
}
async function endCall() {
    const friendInCall = selectedFriendUID || incomingCallData?.from;
    if (localStream) { localStream.getTracks().forEach(track => track.stop()); }
    if (pc) { pc.close(); pc = null; }
    ui.callView.style.display = 'none'; localStream = null; remoteStream = null;
    if (currentUser?.uid) await remove(ref(db, `calls/${currentUser.uid}`));
    if (friendInCall) await remove(ref(db, `calls/${friendInCall}`));
    selectedFriendUID = null;
}
function listenForIncomingCalls() { // THIS FUNCTION WAS MISSING
    if (callListenerUnsub) callListenerUnsub();
    const callRef = ref(db, `calls/${currentUser.uid}`);
    callListenerUnsub = onValue(callRef, (snapshot) => {
        const data = snapshot.val();
        if (data?.offer) {
            incomingCallData = data;
            ui.incomingCallName.textContent = `${data.fromName}`;
            ui.incomingCallType.textContent = `Incoming ${data.type} call...`;
            ui.incomingCallModal.style.display = 'flex';
        }
    });
}
ui.audioCallBtn.addEventListener('click', () => { if (selectedFriendUID) startCall(selectedFriendUID, ui.chatFriendName.textContent, false); });
ui.videoCallBtn.addEventListener('click', () => { if (selectedFriendUID) startCall(selectedFriendUID, ui.chatFriendName.textContent, true); });
ui.endCallBtn.addEventListener('click', endCall);
ui.toggleMicBtn.addEventListener('click', () => {
    if (localStream) {
        const audioTrack = localStream.getAudioTracks()[0];
        if (audioTrack) { audioTrack.enabled = !audioTrack.enabled; ui.toggleMicBtn.textContent = audioTrack.enabled ? '🎙️' : '🔇'; }
    }
});
ui.acceptCallBtn.addEventListener('click', answerCall);
ui.rejectCallBtn.addEventListener('click', async () => {
    ui.incomingCallModal.style.display = 'none';
    if(incomingCallData) { await update(ref(db, `calls/${incomingCallData.from}`), { rejected: true }); }
    await remove(ref(db, `calls/${currentUser.uid}`)); 
    incomingCallData = null;
});

// Main Auth State Logic
onAuthStateChanged(auth, async (user) => {
  currentUser = user; 
  showLoading(false);
  if(user){
    showMain(true); 
    showAuth(false);
    ui.emailInput.value = ""; 
    ui.passwordInput.value = ""; 
    ui.usernameInput.value = "";
    
    const userSnap = await get(ref(db, `users/${user.uid}`));
    const userData = userSnap.val();
    ui.menuBtn.src = userData?.profilePicUrl || user.photoURL || DEFAULT_PFP_URL;

    loadFriends();
    listenForIncomingCalls();
    document.querySelector('.tab[data-tab="chat"]').click();
  } else {
    showMain(false); 
    showAuth(true);
    if(callListenerUnsub) { 
        off(ref(db, `calls/${currentUser?.uid}`)); 
        callListenerUnsub = null; 
    }
  }
});

// Initial UI state
showLoading(true);
showAuth(false);
showMain(false);

</script>
</body>
</html>
