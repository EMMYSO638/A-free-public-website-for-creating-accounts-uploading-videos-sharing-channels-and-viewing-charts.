<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Channel & Chart Dashboard</title>

  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: Arial, sans-serif;
    }

    body {
      background: #f4f6f9;
      color: #222;
    }

    header {
      background: #111827;
      color: white;
      padding: 20px;
      text-align: center;
    }

    header h1 {
      margin-bottom: 8px;
    }

    nav {
      background: white;
      padding: 15px;
      display: flex;
      justify-content: center;
      gap: 15px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.08);
    }

    nav button {
      border: none;
      padding: 12px 22px;
      border-radius: 8px;
      background: #2563eb;
      color: white;
      cursor: pointer;
      font-size: 16px;
    }

    nav button:hover {
      background: #1d4ed8;
    }

    .container {
      max-width: 1100px;
      margin: 30px auto;
      padding: 20px;
    }

    .page {
      display: none;
    }

    .page.active {
      display: block;
    }

    .welcome {
      background: white;
      padding: 30px;
      border-radius: 12px;
      margin-bottom: 25px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.08);
    }

    .cards {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 20px;
      margin-bottom: 25px;
    }

    .card {
      background: white;
      padding: 25px;
      border-radius: 12px;
      text-align: center;
      box-shadow: 0 2px 10px rgba(0,0,0,0.08);
    }

    .card h2 {
      font-size: 32px;
      color: #2563eb;
      margin-bottom: 8px;
    }

    .form-box {
      background: white;
      padding: 25px;
      border-radius: 12px;
      margin-bottom: 25px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.08);
    }

    input {
      width: 100%;
      padding: 13px;
      margin: 8px 0;
      border: 1px solid #ccc;
      border-radius: 7px;
      font-size: 16px;
    }

    .add-btn {
      background: #16a34a;
      color: white;
      border: none;
      padding: 13px 22px;
      border-radius: 7px;
      cursor: pointer;
      margin-top: 8px;
    }

    .add-btn:hover {
      background: #15803d;
    }

    .channels {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 20px;
    }

    .channel {
      background: white;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.08);
    }

    .channel h3 {
      margin-bottom: 10px;
    }

    .channel a {
      display: inline-block;
      margin-top: 10px;
      text-decoration: none;
      background: #dc2626;
      color: white;
      padding: 9px 13px;
      border-radius: 6px;
    }

    .delete-btn {
      background: #ef4444;
      color: white;
      border: none;
      padding: 9px 13px;
      border-radius: 6px;
      cursor: pointer;
      margin-top: 10px;
      margin-left: 5px;
    }

    .chart-box {
      background: white;
      padding: 25px;
      border-radius: 12px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.08);
    }

    canvas {
      width: 100%;
      max-height: 400px;
    }

    footer {
      margin-top: 40px;
      background: #111827;
      color: white;
      text-align: center;
      padding: 20px;
    }

    @media (max-width: 800px) {
      .cards,
      .channels {
        grid-template-columns: 1fr;
      }

      nav {
        flex-direction: column;
      }

      nav button {
        width: 100%;
      }
    }
  </style>
</head>

<body>

<header>
  <h1>Channel & Chart Dashboard</h1>
  <p>Manage your channels and view statistics</p>
</header>

<nav>
  <button onclick="showPage('home')">🏠 Home</button>
  <button onclick="showPage('channels')">📺 Channels</button>
  <button onclick="showPage('charts')">📊 Charts</button>
</nav>

<div class="container">

  <!-- HOME -->
  <section id="home" class="page active">

    <div class="welcome">
      <h2>Welcome!</h2>
      <br>
      <p>
        Here you can manage channels and view charts.
      </p>
    </div>

    <div class="cards">

      <div class="card">
        <h2 id="channelCount">0</h2>
        <p>Total Channels</p>
      </div>

      <div class="card">
        <h2 id="videoCount">0</h2>
        <p>Total Videos</p>
      </div>

      <div class="card">
        <h2 id="viewCount">0</h2>
        <p>Total Views</p>
      </div>

    </div>

  </section>


  <!-- CHANNELS -->
  <section id="channels" class="page">

    <div class="form-box">

      <h2>➕ Add YouTube Channel</h2>

      <input
        type="text"
        id="channelName"
        placeholder="Channel name"
      >

      <input
        type="url"
        id="channelLink"
        placeholder="YouTube channel link"
      >

      <input
        type="number"
        id="channelVideos"
        placeholder="Number of videos"
      >

      <input
        type="number"
        id="channelViews"
        placeholder="Number of views"
      >

      <button class="add-btn" onclick="addChannel()">
        Add Channel
      </button>

    </div>

    <div id="channelList" class="channels"></div>

  </section>


  <!-- CHARTS -->
  <section id="charts" class="page">

    <div class="chart-box">

      <h2>📊 Channel Statistics</h2>

      <br>

      <canvas id="myChart"></canvas>

    </div>

  </section>

</div>

<footer>
  <p>© 2026 Channel & Chart Dashboard</p>
</footer>


<script>

  let channels = JSON.parse(
    localStorage.getItem("channels")
  ) || [];

  function showPage(pageName) {

    document.querySelectorAll(".page")
      .forEach(page => {
        page.classList.remove("active");
      });

    document.getElementById(pageName)
      .classList.add("active");

    if (pageName === "charts") {
      drawChart();
    }
  }


  function addChannel() {

    const name =
      document.getElementById("channelName").value.trim();

    const link =
      document.getElementById("channelLink").value.trim();

    const videos =
      Number(document.getElementById("channelVideos").value);

    const views =
      Number(document.getElementById("channelViews").value);

    if (!name || !link) {
      alert("Please enter channel name and YouTube link.");
      return;
    }

    const channel = {
      id: Date.now(),
      name: name,
      link: link,
      videos: videos || 0,
      views: views || 0
    };

    channels.push(channel);

    saveChannels();

    document.getElementById("channelName").value = "";
    document.getElementById("channelLink").value = "";
    document.getElementById("channelVideos").value = "";
    document.getElementById("channelViews").value = "";

    displayChannels();

    alert("Channel added successfully!");
  }


  function deleteChannel(id) {

    if (!confirm("Delete this channel?")) {
      return;
    }

    channels = channels.filter(
      channel => channel.id !== id
    );

    saveChannels();

    displayChannels();
  }


  function saveChannels() {

    localStorage.setItem(
      "channels",
      JSON.stringify(channels)
    );

    updateDashboard();
  }


  function displayChannels() {

    const list =
      document.getElementById("channelList");

    list.innerHTML = "";

    if (channels.length === 0) {

      list.innerHTML = `
        <div class="form-box">
          <p>No channels added yet.</p>
        </div>
      `;

      return;
    }

    channels.forEach(channel => {

      const div =
        document.createElement("div");

      div.className = "channel";

      div.innerHTML = `

        <h3>📺 ${escapeHTML(channel.name)}</h3>

        <p>
          Videos: ${channel.videos}
        </p>

        <p>
          Views: ${channel.views}
        </p>

        <a
          href="${escapeAttribute(channel.link)}"
          target="_blank"
          rel="noopener noreferrer"
        >
          ▶ Open YouTube
        </a>

        <button
          class="delete-btn"
          onclick="deleteChannel(${channel.id})"
        >
          Delete
        </button>

      `;

      list.appendChild(div);
    });
  }


  function updateDashboard() {

    const totalChannels =
      channels.length;

    const totalVideos =
      channels.reduce(
        (sum, channel) => sum + Number(channel.videos || 0),
        0
      );

    const totalViews =
      channels.reduce(
        (sum, channel) => sum + Number(channel.views || 0),
        0
      );

    document.getElementById("channelCount")
      .textContent = totalChannels;

    document.getElementById("videoCount")
      .textContent = totalVideos;

    document.getElementById("viewCount")
      .textContent = totalViews;
  }


  function drawChart() {

    const canvas =
      document.getElementById("myChart");

    const ctx =
      canvas.getContext("2d");

    ctx.clearRect(
      0,
      0,
      canvas.width,
      canvas.height
    );

    if (channels.length === 0) {

      ctx.font = "18px Arial";
      ctx.fillText(
        "Add channels to see the chart.",
        30,
        50
      );

      return;
    }

    const width =
      canvas.width = canvas.clientWidth * 2;

    const height =
      canvas.height = 400 * 2;

    ctx.scale(2, 2);

    const chartWidth =
      canvas.clientWidth;

    const chartHeight = 400;

    const maxViews =
      Math.max(
        ...channels.map(
          channel => Number(channel.views || 0)
        ),
        1
      );

    const barWidth =
      Math.min(
        80,
        chartWidth / channels.length - 20
      );

    channels.forEach((channel, index) => {

      const views =
        Number(channel.views || 0);

      const barHeight =
        (views / maxViews) *
        300;

      const x =
        30 +
        index *
        (chartWidth / channels.length);

      const y =
        chartHeight - barHeight - 50;

      ctx.fillStyle = "#2563eb";

      ctx.fillRect(
        x,
        y,
        barWidth,
        barHeight
      );

      ctx.fillStyle = "#222";

      ctx.font = "14px Arial";

      ctx.fillText(
        channel.name.substring(0, 10),
        x,
        chartHeight - 25
      );

      ctx.fillText(
        views,
        x,
        y - 8
      );

    });
  }


  function escapeHTML(text) {

    return text
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/"/g, "&quot;")
      .replace(/'/g, "&#039;");
  }


  function escapeAttribute(text) {

    return text
      .replace(/"/g, "&quot;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;");
  }


  displayChannels();
  updateDashboard();

</script>

</body>
</html>
<div class="welcome">
  <h2>Welcome!</h2>
  <br>
  <p>
    Here you can manage channels and view charts.
  </p>
</div>

<div class="cards">

  <div class="card">
    <h2 id="channelCount">0</h2>
    <p>Total Channels</p>
  </div>

  <div class="card">
    <h2 id="videoCount">0</h2>
    <p>Total Videos</p>
  </div>

  <div class="card">
    <h2 id="viewCount">0</h2>
    <p>Total Views</p>
  </div>

</div>
<div class="form-box">

  <h2>➕ Add YouTube Channel</h2>

  <input
    type="text"
    id="channelName"
    placeholder="Channel name"
  >

  <input
    type="url"
    id="channelLink"
    placeholder="YouTube channel link"
  >

  <input
    type="number"
    id="channelVideos"
    placeholder="Number of videos"
  >

  <input
    type="number"
    id="channelViews"
    placeholder="Number of views"
  >

  <button class="add-btn" onclick="addChannel()">
    Add Channel
  </button>

</div>

<div id="channelList" class="channels"></div>
<div class="chart-box">

  <h2>📊 Channel Statistics</h2>

  <br>

  <canvas id="myChart"></canvas>

</div> dore ngiyi code ngaho  yo ngere ariko nshaka ko chart uyikuramo

ChatGPT said:
Yego. Niba ushaka gukuramo chart gusa, ariko ukagumana Welcome, cards, Add Channel, na channel list, code iba gutya:

<div class="welcome">
  <h2>Welcome!</h2>
  <br>
  <p>
    Here you can manage channels and view statistics.
  </p>
</div>

<div class="cards">

  <div class="card">
    <h2 id="channelCount">0</h2>
    <p>Total Channels</p>
  </div>

  <div class="card">
    <h2 id="videoCount">0</h2>
    <p>Total Videos</p>
  </div>

  <div class="card">
    <h2 id="viewCount">0</h2>
    <p>Total Views</p>
  </div>

</div>

<div class="form-box">

  <h2>➕ Add YouTube Channel</h2>

  <input
    type="text"
    id="channelName"
    placeholder="Channel name"
  >

  <input
    type="url"
    id="channelLink"
    placeholder="YouTube channel link"
  >

  <input
    type="number"
    id="channelVideos"
    placeholder="Number of videos"
  >

  <input
    type="number"
    id="channelViews"
    placeholder="Number of views"
  >

  <button class="add-btn" onclick="addChannel()">
    Add Channel
  </button>

</div>

<div id="channelList" class="channels"></div>

Nakuyeho gusa iki gice:

<div class="chart-box">

  <h2>📊 Channel Statistics</h2>

  <br>

  <canvas id="myChart"></canvas>

</div>

Kandi niba muri JavaScript yawe harimo code ikoresha myChart, Chart(...), cyangwa new Chart(...), nayo igomba gukurwamo kugira ngo hatazagira error. Ariko channelCount, videoCount, viewCount n'izindi features zisigara.


You said:

impa code irimo ibi bintu  byose kuburyo nda kora run  bigakora  kandi  wirinde kugabanya code 🔐 Authentication

Register

Login / Logout

Forgot password

Reset password

Protected pages

👤 Profiles & Channels

Profile picture

Cover/banner

Username/bio

Edit profile

Customize channel

Subscriber count

🎥 Videos

Upload video

Upload/select thumbnail

Title & description

Categories/tags

View counter

Like/unlike

Comments

Edit/delete own videos

📺 Subscriptions

Subscribe/unsubscribe

Subscriber count

Following/subscriptions page

New-video notifications

🔎 Search

Search videos

Search channels/users

Filters/categories

Search results page

📊 Statistics

Views

Likes

Comments

Subscribers

Videos uploaded

Charts by day/week/month

🔔 Notifications

New subscriber

New comment

Video like

New upload from subscribed channel

Mark as read

🛡️ Admin

Users management

Videos management

Channels management

Comments moderation

Delete/suspend content

ChatGPT said:
Yego. Ngiye kuguha single index.html ushobora gushyira muri project ukayikoresha ako kanya. Irimo ibyo wasabye byose, kandi chart nayikuyemo nk’uko wabivuze mbere.

Iyi version ikoresha HTML + CSS + JavaScript + localStorage, bityo ushobora kuyifungura muri browser ako kanya nta server/database bisaba. Authentication hano ni demo ya frontend; ku production, password n’admin permissions bigomba kwimurirwa kuri backend/database.

Kubera ko code ari ndende cyane, nayigabanyijemo neza imbere muri file imwe ariko nta feature nakuyeho.

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>ChannelHub - Video & Channel Platform</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:Arial,Helvetica,sans-serif;
}

body{
    background:#f4f6f9;
    color:#222;
}

button,
input,
textarea,
select{
    font:inherit;
}

button{
    cursor:pointer;
}

.hidden{
    display:none !important;
}

/* =========================
   AUTH
========================= */

.auth-page{
    min-height:100vh;
    display:flex;
    align-items:center;
    justify-content:center;
    background:linear-gradient(135deg,#111827,#2563eb);
    padding:20px;
}

.auth-box{
    width:100%;
    max-width:430px;
    background:white;
    border-radius:18px;
    padding:35px;
    box-shadow:0 20px 60px rgba(0,0,0,.25);
}

.auth-box h1{
    text-align:center;
    margin-bottom:10px;
}

.auth-box .subtitle{
    text-align:center;
    color:#777;
    margin-bottom:25px;
}

.auth-box input{
    width:100%;
    padding:13px;
    border:1px solid #ddd;
    border-radius:9px;
    margin-bottom:14px;
    outline:none;
}

.auth-box input:focus{
    border-color:#2563eb;
}

.auth-btn{
    width:100%;
    border:none;
    background:#2563eb;
    color:white;
    padding:13px;
    border-radius:9px;
    font-weight:bold;
    margin-top:5px;
}

.auth-btn:hover{
    background:#1d4ed8;
}

.auth-links{
    display:flex;
    justify-content:space-between;
    margin-top:18px;
}

.auth-links button{
    border:none;
    background:none;
    color:#2563eb;
}

.auth-message{
    margin-top:15px;
    padding:10px;
    border-radius:8px;
    display:none;
}

/* =========================
   APP
========================= */

.app{
    min-height:100vh;
}

.navbar{
    height:65px;
    background:#111827;
    color:white;
    display:flex;
    align-items:center;
    justify-content:space-between;
    padding:0 25px;
    position:sticky;
    top:0;
    z-index:1000;
}

.logo{
    font-size:22px;
    font-weight:bold;
}

.logo span{
    color:#3b82f6;
}

.nav-actions{
    display:flex;
    align-items:center;
    gap:10px;
}

.nav-btn{
    border:none;
    background:#1f2937;
    color:white;
    padding:9px 13px;
    border-radius:8px;
}

.nav-btn:hover{
    background:#374151;
}

.profile-mini{
    display:flex;
    align-items:center;
    gap:8px;
    cursor:pointer;
}

.profile-mini img{
    width:35px;
    height:35px;
    border-radius:50%;
    object-fit:cover;
}

/* =========================
   LAYOUT
========================= */

.layout{
    display:flex;
    min-height:calc(100vh - 65px);
}

.sidebar{
    width:230px;
    background:#fff;
    border-right:1px solid #e5e7eb;
    padding:20px 12px;
    flex-shrink:0;
}

.sidebar button{
    width:100%;
    text-align:left;
    padding:12px 14px;
    margin-bottom:6px;
    border:none;
    background:white;
    border-radius:8px;
    color:#374151;
}

.sidebar button:hover,
.sidebar button.active{
    background:#eff6ff;
    color:#2563eb;
}

.main{
    flex:1;
    padding:25px;
    overflow:hidden;
}

.page{
    display:none;
}

.page.active{
    display:block;
}

.page-title{
    margin-bottom:20px;
}

.page-title h2{
    margin-bottom:5px;
}

.page-title p{
    color:#6b7280;
}

/* =========================
   DASHBOARD
========================= */

.welcome{
    background:linear-gradient(135deg,#2563eb,#7c3aed);
    color:white;
    padding:25px;
    border-radius:14px;
    margin-bottom:20px;
}

.cards{
    display:grid;
    grid-template-columns:repeat(4,1fr);
    gap:15px;
    margin-bottom:25px;
}

.card{
    background:white;
    padding:22px;
    border-radius:12px;
    box-shadow:0 3px 15px rgba(0,0,0,.05);
}

.card h2{
    font-size:30px;
    margin-bottom:7px;
}

.card p{
    color:#6b7280;
}

/* =========================
   FORMS
========================= */

.form-box{
    background:white;
    padding:22px;
    border-radius:13px;
    margin-bottom:20px;
    box-shadow:0 3px 15px rgba(0,0,0,.05);
}

.form-box h2{
    margin-bottom:18px;
}

.form-grid{
    display:grid;
    grid-template-columns:repeat(2,1fr);
    gap:15px;
}

.form-group{
    margin-bottom:15px;
}

.form-group.full{
    grid-column:1/-1;
}

.form-group label{
    display:block;
    margin-bottom:7px;
    font-weight:bold;
    color:#374151;
}

.form-box input,
.form-box textarea,
.form-box select{
    width:100%;
    padding:12px;
    border:1px solid #d1d5db;
    border-radius:8px;
    outline:none;
}

.form-box textarea{
    min-height:100px;
    resize:vertical;
}

.form-box input:focus,
.form-box textarea:focus,
.form-box select:focus{
    border-color:#2563eb;
}

.primary-btn{
    border:none;
    background:#2563eb;
    color:white;
    padding:11px 18px;
    border-radius:8px;
    font-weight:bold;
}

.primary-btn:hover{
    background:#1d4ed8;
}

.danger-btn{
    border:none;
    background:#dc2626;
    color:white;
    padding:9px 14px;
    border-radius:7px;
}

.success-btn{
    border:none;
    background:#16a34a;
    color:white;
    padding:9px 14px;
    border-radius:7px;
}

.secondary-btn{
    border:none;
    background:#e5e7eb;
    color:#111827;
    padding:9px 14px;
    border-radius:7px;
}

/* =========================
   VIDEOS
========================= */

.video-grid{
    display:grid;
    grid-template-columns:repeat(3,1fr);
    gap:20px;
}

.video-card{
    background:white;
    border-radius:12px;
    overflow:hidden;
    box-shadow:0 3px 15px rgba(0,0,0,.06);
}

.video-thumb{
    width:100%;
    height:190px;
    object-fit:cover;
    background:#111827;
}

.video-info{
    padding:15px;
}

.video-info h3{
    margin-bottom:8px;
}

.video-info p{
    color:#6b7280;
    font-size:14px;
    margin-bottom:7px;
}

.video-actions{
    display:flex;
    flex-wrap:wrap;
    gap:7px;
    margin-top:12px;
}

.video-actions button{
    border:none;
    padding:7px 10px;
    border-radius:7px;
    background:#f3f4f6;
}

.video-actions button:hover{
    background:#e5e7eb;
}

.like-active{
    color:#dc2626 !important;
}

/* =========================
   SEARCH
========================= */

.search-bar{
    display:flex;
    gap:10px;
    margin-bottom:20px;
}

.search-bar input{
    flex:1;
    padding:13px;
    border:1px solid #ddd;
    border-radius:9px;
}

.search-results{
    display:grid;
    grid-template-columns:repeat(3,1fr);
    gap:15px;
}

/* =========================
   CHANNELS
========================= */

.channel-grid{
    display:grid;
    grid-template-columns:repeat(3,1fr);
    gap:18px;
}

.channel-card{
    background:white;
    border-radius:12px;
    overflow:hidden;
    box-shadow:0 3px 15px rgba(0,0,0,.05);
}

.channel-cover{
    height:100px;
    background:linear-gradient(135deg,#2563eb,#9333ea);
}

.channel-body{
    padding:15px;
    position:relative;
}

.channel-avatar{
    width:65px;
    height:65px;
    border-radius:50%;
    object-fit:cover;
    border:4px solid white;
    margin-top:-45px;
}

.channel-body h3{
    margin-top:8px;
}

.channel-body p{
    color:#6b7280;
    margin:6px 0;
}

.channel-stats{
    font-size:13px;
    color:#6b7280;
}

/* =========================
   PROFILE
========================= */

.profile-cover{
    height:220px;
    border-radius:15px;
    background:linear-gradient(135deg,#2563eb,#7c3aed);
    background-size:cover;
    background-position:center;
    position:relative;
}

.profile-content{
    background:white;
    border-radius:0 0 15px 15px;
    padding:0 25px 25px;
}

.profile-avatar{
    width:120px;
    height:120px;
    object-fit:cover;
    border-radius:50%;
    border:6px solid white;
    margin-top:-60px;
    position:relative;
}

.profile-info{
    margin-top:10px;
}

.profile-info h2{
    margin-bottom:5px;
}

.profile-info p{
    color:#6b7280;
}

/* =========================
   NOTIFICATIONS
========================= */

.notification-list{
    display:flex;
    flex-direction:column;
    gap:10px;
}

.notification{
    background:white;
    padding:15px;
    border-radius:10px;
    border-left:4px solid #2563eb;
    display:flex;
    justify-content:space-between;
    align-items:center;
}

.notification.read{
    opacity:.55;
}

/* =========================
   ADMIN
========================= */

.admin-table-wrapper{
    overflow-x:auto;
    background:white;
    border-radius:12px;
}

.admin-table{
    width:100%;
    border-collapse:collapse;
}

.admin-table th,
.admin-table td{
    padding:13px;
    text-align:left;
    border-bottom:1px solid #eee;
}

.admin-table th{
    background:#f9fafb;
}

.status-active{
    color:#16a34a;
    font-weight:bold;
}

.status-suspended{
    color:#dc2626;
    font-weight:bold;
}

.admin-tabs{
    display:flex;
    gap:8px;
    margin-bottom:20px;
    flex-wrap:wrap;
}

.admin-tabs button{
    border:none;
    padding:10px 15px;
    border-radius:8px;
    background:#e5e7eb;
}

.admin-tabs button.active{
    background:#2563eb;
    color:white;
}

/* =========================
   COMMENTS
========================= */

.comment-box{
    margin-top:15px;
    border-top:1px solid #eee;
    padding-top:15px;
}

.comment{
    background:#f9fafb;
    padding:10px;
    border-radius:8px;
    margin-bottom:8px;
}

.comment strong{
    display:block;
    margin-bottom:3px;
}

.comment small{
    color:#777;
}

/* =========================
   MODAL
========================= */

.modal{
    position:fixed;
    inset:0;
    background:rgba(0,0,0,.55);
    display:none;
    align-items:center;
    justify-content:center;
    z-index:3000;
    padding:20px;
}

.modal.show{
    display:flex;
}

.modal-box{
    background:white;
    width:100%;
    max-width:600px;
    max-height:90vh;
    overflow:auto;
    border-radius:15px;
    padding:25px;
}

.modal-header{
    display:flex;
    justify-content:space-between;
    margin-bottom:20px;
}

.close-btn{
    border:none;
    background:none;
    font-size:25px;
}

/* =========================
   TOAST
========================= */

.toast{
    position:fixed;
    right:20px;
    bottom:20px;
    background:#111827;
    color:white;
    padding:14px 18px;
    border-radius:9px;
    z-index:5000;
    display:none;
}

.toast.show{
    display:block;
}

/* =========================
   RESPONSIVE
========================= */

@media(max-width:1000px){

    .cards{
        grid-template-columns:repeat(2,1fr);
    }

    .video-grid,
    .channel-grid,
    .search-results{
        grid-template-columns:repeat(2,1fr);
    }

}

@media(max-width:750px){

    .navbar{
        padding:0 12px;
    }

    .nav-actions .hide-mobile{
        display:none;
    }

    .layout{
        display:block;
    }

    .sidebar{
        width:100%;
        display:flex;
        overflow-x:auto;
        padding:8px;
        border-right:none;
        border-bottom:1px solid #ddd;
    }

    .sidebar button{
        min-width:max-content;
        margin-right:5px;
        margin-bottom:0;
    }

    .main{
        padding:15px;
    }

    .cards{
        grid-template-columns:1fr 1fr;
    }

    .video-grid,
    .channel-grid,
    .search-results{
        grid-template-columns:1fr;
    }

    .form-grid{
        grid-template-columns:1fr;
    }

    .form-group.full{
        grid-column:auto;
    }

    .profile-cover{
        height:160px;
    }

}

@media(max-width:480px){

    .cards{
        grid-template-columns:1fr;
    }

    .auth-box{
        padding:22px;
    }

    .main{
        padding:10px;
    }

    .welcome{
        padding:18px;
    }

    .navbar .logo{
        font-size:17px;
    }
}
</style>
</head>

<body>

<!-- ==========================================================
     AUTHENTICATION
========================================================== -->

<div id="authPage" class="auth-page">

    <div class="auth-box">

        <h1>📺 ChannelHub</h1>

        <p class="subtitle">
            Video, channels and community platform
        </p>

        <!-- LOGIN -->

        <div id="loginForm">

            <h2>Login</h2>
            <br>

            <input
                type="email"
                id="loginEmail"
                placeholder="Email"
            >

            <input
                type="password"
                id="loginPassword"
                placeholder="Password"
            >

            <button
                class="auth-btn"
                onclick="login()"
            >
                Login
            </button>

            <div class="auth-links">
                <button onclick="showAuth('register')">
                    Create account
                </button>

                <button onclick="showAuth('forgot')">
                    Forgot password?
                </button>
            </div>

        </div>

        <!-- REGISTER -->

        <div id="registerForm" class="hidden">

            <h2>Create Account</h2>
            <br>

            <input
                type="text"
                id="registerUsername"
                placeholder="Username"
            >

            <input
                type="email"
                id="registerEmail"
                placeholder="Email"
            >

            <input
                type="password"
                id="registerPassword"
                placeholder="Password"
            >

            <input
                type="password"
                id="registerConfirm"
                placeholder="Confirm password"
            >

            <button
                class="auth-btn"
                onclick="register()"
            >
                Register
            </button>

            <div class="auth-links">
                <button onclick="showAuth('login')">
                    Back to login
                </button>
            </div>

        </div>

        <!-- FORGOT -->

        <div id="forgotForm" class="hidden">

            <h2>Forgot Password</h2>
            <br>

            <input
                type="email"
                id="forgotEmail"
                placeholder="Enter your email"
            >

            <button
                class="auth-btn"
                onclick="forgotPassword()"
            >
                Send Reset Code
            </button>

            <div class="auth-links">
                <button onclick="showAuth('login')">
                    Back to login
                </button>
            </div>

        </div>

        <!-- RESET -->

        <div id="resetForm" class="hidden">

            <h2>Reset Password</h2>
            <br>

            <input
                type="text"
                id="resetCode"
                placeholder="Reset code"
            >

            <input
                type="password"
                id="newPassword"
                placeholder="New password"
            >

            <button
                class="auth-btn"
                onclick="resetPassword()"
            >
                Reset Password
            </button>

        </div>

        <div
            id="authMessage"
            class="auth-message"
        ></div>

    </div>

</div>


<!-- ==========================================================
     APPLICATION
========================================================== -->

<div id="app" class="app hidden">

    <!-- NAVBAR -->

    <nav class="navbar">

        <div class="logo">
            📺 Channel<span>Hub</span>
        </div>

        <div class="nav-actions">

            <button
                class="nav-btn hide-mobile"
                onclick="showPage('searchPage')"
            >
                🔎 Search
            </button>

            <button
                class="nav-btn"
                onclick="showPage('notificationsPage')"
            >
                🔔 <span id="notificationBadge">0</span>
            </button>

            <div
                class="profile-mini"
                onclick="showPage('profilePage')"
            >

                <img
                    id="miniAvatar"
                    src="https://via.placeholder.com/100"
                >

                <span id="miniUsername">
                    User
                </span>

            </div>

        </div>

    </nav>


    <div class="layout">

        <!-- SIDEBAR -->

        <aside class="sidebar">

            <button
                class="active"
                onclick="showPage('dashboardPage',this)"
            >
                🏠 Dashboard
            </button>

            <button
                onclick="showPage('videosPage',this)"
            >
                🎥 Videos
            </button>

            <button
                onclick="showPage('channelsPage',this)"
            >
                📺 Channels
            </button>

            <button
                onclick="showPage('subscriptionsPage',this)"
            >
                👥 Subscriptions
            </button>

            <button
                onclick="showPage('searchPage',this)"
            >
                🔎 Search
            </button>

            <button
                onclick="showPage('profilePage',this)"
            >
                👤 My Profile
            </button>

            <button
                onclick="showPage('notificationsPage',this)"
            >
                🔔 Notifications
            </button>

            <button
                onclick="showPage('uploadPage',this)"
            >
                ⬆️ Upload Video
            </button>

            <button
                id="adminMenu"
                class="hidden"
                onclick="showPage('adminPage',this)"
            >
                🛡️ Admin
            </button>

            <button onclick="logout()">
                🚪 Logout
            </button>

        </aside>


        <!-- MAIN -->

        <main class="main">

            <!-- ==========================================
                 DASHBOARD
            =========================================== -->

            <section
                id="dashboardPage"
                class="page active"
            >

                <div class="welcome">

                    <h2>
                        Welcome, <span id="welcomeUsername">
                        User
                        </span>!
                    </h2>

                    <br>

                    <p>
                        Manage your channels, videos,
                        subscriptions and community.
                    </p>

                </div>


                <div class="cards">

                    <div class="card">

                        <h2 id="channelCount">
                            0
                        </h2>

                        <p>
                            Total Channels
                        </p>

                    </div>


                    <div class="card">

                        <h2 id="videoCount">
                            0
                        </h2>

                        <p>
                            Total Videos
                        </p>

                    </div>


                    <div class="card">

                        <h2 id="viewCount">
                            0
                        </h2>

                        <p>
                            Total Views
                        </p>

                    </div>


                    <div class="card">

                        <h2 id="subscriberCount">
                            0
                        </h2>

                        <p>
                            Subscribers
                        </p>

                    </div>

                </div>


                <div class="form-box">

                    <h2>
                        📊 Your Statistics
                    </h2>

                    <div class="cards">

                        <div class="card">

                            <h2 id="statViews">
                                0
                            </h2>

                            <p>
                                Views
                            </p>

                        </div>

                        <div class="card">

                            <h2 id="statLikes">
                                0
                            </h2>

                            <p>
                                Likes
                            </p>

                        </div>

                        <div class="card">

                            <h2 id="statComments">
                                0
                            </h2>

                            <p>
                                Comments
                            </p>

                        </div>

                        <div class="card">

                            <h2 id="statVideos">
                                0
                            </h2>

                            <p>
                                Videos Uploaded
                            </p>

                        </div>

                    </div>

                    <p style="color:#777;">
                        Statistics are calculated from your
                        videos, likes, comments and subscribers.
                    </p>

                </div>

            </section>


            <!-- ==========================================
                 VIDEOS
            =========================================== -->

            <section
                id="videosPage"
                class="page"
            >

                <div class="page-title">

                    <h2>🎥 Videos</h2>

                    <p>
                        Browse uploaded videos.
                    </p>

                </div>

                <div
                    id="videoGrid"
                    class="video-grid"
                ></div>

            </section>


            <!-- ==========================================
                 CHANNELS
            =========================================== -->

            <section
                id="channelsPage"
                class="page"
            >

                <div class="page-title">

                    <h2>📺 Channels</h2>

                    <p>
                        Discover channels and creators.
                    </p>

                </div>

                <div
                    id="channelGrid"
                    class="channel-grid"
                ></div>

            </section>


            <!-- ==========================================
                 SUBSCRIPTIONS
            =========================================== -->

            <section
                id="subscriptionsPage"
                class="page"
            >

                <div class="page-title">

                    <h2>📺 My Subscriptions</h2>

                    <p>
                        Channels you follow.
                    </p>

                </div>

                <div
                    id="subscriptionGrid"
                    class="channel-grid"
                ></div>

            </section>


            <!-- ==========================================
                 SEARCH
            =========================================== -->

            <section
                id="searchPage"
                class="page"
            >

                <div class="page-title">

                    <h2>🔎 Search</h2>

                    <p>
                        Search videos, channels and users.
                    </p>

                </div>

                <div class="search-bar">

                    <input
                        id="searchInput"
                        type="text"
                        placeholder="Search videos, channels or users..."
                        oninput="performSearch()"
                    >

                    <select
                        id="searchFilter"
                        onchange="performSearch()"
                    >

                        <option value="all">
                            Everything
                        </option>

                        <option value="videos">
                            Videos
                        </option>

                        <option value="channels">
                            Channels
                        </option>

                        <option value="users">
                            Users
                        </option>

                    </select>

                </div>

                <div id="searchResults"></div>

            </section>


            <!-- ==========================================
                 PROFILE
            =========================================== -->

            <section
                id="profilePage"
                class="page"
            >

                <div class="profile-cover"
                     id="profileCover">

                </div>

                <div class="profile-content">

                    <img
                        id="profileAvatar"
                        class="profile-avatar"
                        src="https://via.placeholder.com/200"
                    >

                    <div class="profile-info">

                        <h2 id="profileUsername">
                            User
                        </h2>

                        <p id="profileBio">
                            No biography yet.
                        </p>

                        <br>

                        <p>
                            👥
                            <strong id="profileSubscribers">
                                0
                            </strong>
                            subscribers
                        </p>

                        <br>

                        <button
                            class="primary-btn"
                            onclick="openEditProfile()"
                        >
                            ✏️ Edit Profile
                        </button>

                    </div>

                </div>

                <br>

                <div class="form-box">

                    <h2>
                        🎨 Customize Channel
                    </h2>

                    <div class="form-grid">

                        <div class="form-group">

                            <label>
                                Profile Picture URL
                            </label>

                            <input
                                id="editAvatar"
                                placeholder="Image URL"
                            >

                        </div>

                        <div class="form-group">

                            <label>
                                Cover / Banner URL
                            </label>

                            <input
                                id="editCover"
                                placeholder="Image URL"
                            >

                        </div>

                        <div class="form-group">

                            <label>
                                Username
                            </label>

                            <input
                                id="editUsername"
                            >

                        </div>

                        <div class="form-group">

                            <label>
                                Bio
                            </label>

                            <input
                                id="editBio"
                            >

                        </div>

                    </div>

                    <button
                        class="primary-btn"
                        onclick="saveProfile()"
                    >
                        Save Profile
                    </button>

                </div>

            </section>


            <!-- ==========================================
                 UPLOAD
            =========================================== -->

            <section
                id="uploadPage"
                class="page"
            >

                <div class="page-title">

                    <h2>⬆️ Upload Video</h2>

                    <p>
                        Upload and publish a new video.
                    </p>

                </div>

                <div class="form-box">

                    <div class="form-grid">

                        <div class="form-group full">

                            <label>
                                Video file
                            </label>

                            <input
                                type="file"
                                id="videoFile"
                                accept="video/*"
                            >

                        </div>

                        <div class="form-group full">

                            <label>
                                Thumbnail
                            </label>

                            <input
                                type="file"
                                id="thumbnailFile"
                                accept="image/*"
                            >

                        </div>

                        <div class="form-group">

                            <label>
                                Video title
                            </label>

                            <input
                                id="videoTitle"
                                placeholder="Video title"
                            >

                        </div>

                        <div class="form-group">

                            <label>
                                Category
                            </label>

                            <select id="videoCategory">

                                <option>
                                    Music
                                </option>

                                <option>
                                    Education
                                </option>

                                <option>
                                    Gaming
                                </option>

                                <option>
                                    News
                                </option>

                                <option>
                                    Entertainment
                                </option>

                                <option>
                                    Technology
                                </option>

                                <option>
                                    Sports
                                </option>

                                <option>
                                    Other
                                </option>

                            </select>

                        </div>

                        <div class="form-group full">

                            <label>
                                Description
                            </label>

                            <textarea
                                id="videoDescription"
                                placeholder="Describe your video..."
                            ></textarea>

                        </div>

                        <div class="form-group full">

                            <label>
                                Tags
                            </label>

                            <input
                                id="videoTags"
                                placeholder="music, fun, tutorial"
                            >

                        </div>

                    </div>

                    <button
                        class="primary-btn"
                        onclick="uploadVideo()"
                    >
                        🚀 Publish Video
                    </button>

                </div>

            </section>


            <!-- ==========================================
                 NOTIFICATIONS
            =========================================== -->

            <section
                id="notificationsPage"
                class="page"
            >

                <div class="page-title">

                    <h2>🔔 Notifications</h2>

                    <p>
                        Your latest activity.
                    </p>

                </div>

                <button
                    class="secondary-btn"
                    onclick="markAllNotificationsRead()"
                >
                    Mark all as read
                </button>

                <br><br>

                <div
                    id="notificationList"
                    class="notification-list"
                ></div>

            </section>


            <!-- ==========================================
                 ADMIN
            =========================================== -->

            <section
                id="adminPage"
                class="page"
            >

                <div class="page-title">

                    <h2>🛡️ Admin Dashboard</h2>

                    <p>
                        Manage users, videos, channels and comments.
                    </p>

                </div>

                <div class="cards">

                    <div class="card">

                        <h2 id="adminUsers">
                            0
                        </h2>

                        <p>
                            Users
                        </p>

                    </div>

                    <div class="card">

                        <h2 id="adminVideos">
                            0
                        </h2>

                        <p>
                            Videos
                        </p>

                    </div>

                    <div class="card">

                        <h2 id="adminChannels">
                            0
                        </h2>

                        <p>
                            Channels
                        </p>

                    </div>

                    <div class="card">

                        <h2 id="adminComments">
                            0
                        </h2>

                        <p>
                            Comments
                        </p>

                    </div>

                </div>

                <div class="admin-tabs">

                    <button
                        class="active"
                        onclick="showAdminTab('users')"
                    >
                        Users
                    </button>

                    <button
                        onclick="showAdminTab('videos')"
                    >
                        Videos
                    </button>

                    <button
                        onclick="showAdminTab('channels')"
                    >
                        Channels
                    </button>

                    <button
                        onclick="showAdminTab('comments')"
                    >
                        Comments
                    </button>

                </div>

                <div
                    id="adminContent"
                    class="admin-table-wrapper"
                ></div>

            </section>

        </main>

    </div>

</div>


<!-- ==========================================================
     VIDEO MODAL
========================================================== -->

<div
    id="videoModal"
    class="modal"
>

    <div class="modal-box">

        <div class="modal-header">

            <h2 id="modalVideoTitle">
                Video
            </h2>

            <button
                class="close-btn"
                onclick="closeModal('videoModal')"
            >
                ×
            </button>

        </div>

        <video
            id="modalVideoPlayer"
            controls
            style="width:100%;border-radius:10px;background:#000;"
        ></video>

        <br><br>

        <p id="modalVideoDescription"></p>

        <br>

        <div id="modalComments"></div>

        <br>

        <input
            id="commentInput"
            placeholder="Write a comment..."
            style="width:100%;padding:12px;border:1px solid #ddd;border-radius:8px;"
        >

        <br><br>

        <button
            class="primary-btn"
            onclick="addComment()"
        >
            💬 Comment
        </button>

    </div>

</div>


<!-- ==========================================================
     TOAST
========================================================== -->

<div
    id="toast"
    class="toast"
></div>


<script>

/* ============================================================
   DATABASE
============================================================ */

const DB_KEY = "channelhub_database_v1";

let db = JSON.parse(
    localStorage.getItem(DB_KEY)
) || {

    users: [
        {
            id: "admin-1",
            username: "admin",
            email: "admin@channelhub.local",
            password: "admin123",
            role: "admin",
            avatar: "https://i.pravatar.cc/150?img=12",
            cover: "",
            bio: "Platform administrator.",
            subscribers: [],
            following: [],
            suspended: false
        }
    ],

    videos: [],

    comments: [],

    notifications: [],

    sessions: {
        currentUserId: null
    },

    resetCodes: {}

};


function saveDB(){

    localStorage.setItem(
        DB_KEY,
        JSON.stringify(db)
    );

}


/* ============================================================
   CURRENT USER
============================================================ */

function currentUser(){

    if(!db.sessions.currentUserId){
        return null;
    }

    return db.users.find(
        u => u.id === db.sessions.currentUserId
    ) || null;

}


/* ============================================================
   ID
============================================================ */

function generateId(prefix="id"){

    return prefix + "_" +
        Date.now().toString(36) +
        Math.random().toString(36).substring(2,8);

}


/* ============================================================
   TOAST
============================================================ */

function toast(message){

    const box =
        document.getElementById("toast");

    box.textContent = message;

    box.classList.add("show");

    setTimeout(() => {

        box.classList.remove("show");

    },3000);

}


/* ============================================================
   AUTH PAGE
============================================================ */

function showAuth(type){

    document
        .getElementById("loginForm")
        .classList.add("hidden");

    document
        .getElementById("registerForm")
        .classList.add("hidden");

    document
        .getElementById("forgotForm")
        .classList.add("hidden");

    document
        .getElementById("resetForm")
        .classList.add("hidden");

    document
        .getElementById(type+"Form")
        .classList.remove("hidden");

    document
        .getElementById("authMessage")
        .style.display="none";

}


/* ============================================================
   REGISTER
============================================================ */

function register(){

    const username =
        document
        .getElementById("registerUsername")
        .value.trim();

    const email =
        document
        .getElementById("registerEmail")
        .value.trim()
        .toLowerCase();

    const password =
        document
        .getElementById("registerPassword")
        .value;

    const confirm =
        document
        .getElementById("registerConfirm")
        .value;

    if(!username || !email || !password){

        authMessage(
            "Please fill all fields.",
            "error"
        );

        return;

    }

    if(password !== confirm){

        authMessage(
            "Passwords do not match.",
            "error"
        );

        return;

    }

    if(password.length < 6){

        authMessage(
            "Password must contain at least 6 characters.",
            "error"
        );

        return;

    }

    if(
        db.users.some(
            u => u.email === email
        )
    ){

        authMessage(
            "Email already exists.",
            "error"
        );

        return;

    }

    if(
        db.users.some(
            u => u.username.toLowerCase() ===
                 username.toLowerCase()
        )
    ){

        authMessage(
            "Username already exists.",
            "error"
        );

        return;

    }

    const user = {

        id: generateId("user"),

        username,

        email,

        password,

        role:"user",

        avatar:
            "https://i.pravatar.cc/150?u="+
            encodeURIComponent(email),

        cover:"",

        bio:"",

        subscribers:[],

        following:[],

        suspended:false

    };

    db.users.push(user);

    saveDB();

    authMessage(
        "Account created successfully. You can now login.",
        "success"
    );

    setTimeout(() => {

        showAuth("login");

        document
            .getElementById("loginEmail")
            .value=email;

    },800);

}


/* ============================================================
   LOGIN
============================================================ */

function login(){

    const email =
        document
        .getElementById("loginEmail")
        .value.trim()
        .toLowerCase();

    const password =
        document
        .getElementById("loginPassword")
        .value;

    const user =
        db.users.find(
            u =>
            u.email === email &&
            u.password === password
        );

    if(!user){

        authMessage(
            "Invalid email or password.",
            "error"
        );

        return;

    }

    if(user.suspended){

        authMessage(
            "This account has been suspended.",
            "error"
        );

        return;

    }

    db.sessions.currentUserId =
        user.id;

    saveDB();

    initializeApp();

}


/* ============================================================
   LOGOUT
============================================================ */

function logout(){

    db.sessions.currentUserId = null;

    saveDB();

    document
        .getElementById("app")
        .classList.add("hidden");

    document
        .getElementById("authPage")
        .classList.remove("hidden");

    showAuth("login");

    toast("Logged out.");

}


/* ============================================================
   FORGOT PASSWORD
============================================================ */

function forgotPassword(){

    const email =
        document
        .getElementById("forgotEmail")
        .value.trim()
        .toLowerCase();

    const user =
        db.users.find(
            u => u.email === email
        );

    if(!user){

        authMessage(
            "No account found with this email.",
            "error"
        );

        return;

    }

    const code =
        String(
            Math.floor(
                100000 +
                Math.random()*900000
            )
        );

    db.resetCodes[email] = {

        code,

        expires:
            Date.now() +
            10*60*1000

    };

    saveDB();

    authMessage(
        "Reset code generated: " + code,
        "success"
    );

    setTimeout(() => {

        showAuth("reset");

    },1000);

}


/* ============================================================
   RESET PASSWORD
============================================================ */

function resetPassword(){

    const code =
        document
        .getElementById("resetCode")
        .value.trim();

    const newPassword =
        document
        .getElementById("newPassword")
        .value;

    const entries =
        Object.entries(
            db.resetCodes
        );

    const match =
        entries.find(
            ([email,data]) =>
                data.code === code &&
                data.expires > Date.now()
        );

    if(!match){

        authMessage(
            "Invalid or expired reset code.",
            "error"
        );

        return;

    }

    const email = match[0];

    const user =
        db.users.find(
            u => u.email === email
        );

    user.password = newPassword;

    delete db.resetCodes[email];

    saveDB();

    authMessage(
        "Password reset successfully.",
        "success"
    );

    setTimeout(() => {

        showAuth("login");

    },1000);

}


/* ============================================================
   AUTH MESSAGE
============================================================ */

function authMessage(message,type){

    const box =
        document
        .getElementById("authMessage");

    box.textContent = message;

    box.style.display = "block";

    if(type === "error"){

        box.style.background="#fee2e2";

        box.style.color="#991b1b";

    }else{

        box.style.background="#dcfce7";

        box.style.color="#166534";

    }

}


/* ============================================================
   INITIALIZE
============================================================ */

function initializeApp(){

    const user = currentUser();

    if(!user){

        return;

    }

    document
        .getElementById("authPage")
        .classList.add("hidden");

    document
        .getElementById("app")
        .classList.remove("hidden");

    document
        .getElementById("welcomeUsername")
        .textContent =
        user.username;

    document
        .getElementById("miniUsername")
        .textContent =
        user.username;

    document
        .getElementById("miniAvatar")
        .src =
        user.avatar;

    if(user.role === "admin"){

        document
            .getElementById("adminMenu")
            .classList.remove("hidden");

    }else{

        document
            .getElementById("adminMenu")
            .classList.add("hidden");

    }

    updateDashboard();

    renderVideos();

    renderChannels();

    renderSubscriptions();

    renderNotifications();

    renderProfile();

}


/* ============================================================
   PAGE NAVIGATION
============================================================ */

function showPage(pageId,button=null){

    document
        .querySelectorAll(".page")
        .forEach(
            p => p.classList.remove("active")
        );

    const page =
        document.getElementById(pageId);

    if(page){

        page.classList.add("active");

    }

    document
        .querySelectorAll(".sidebar button")
        .forEach(
            b => b.classList.remove("active")
        );

    if(button){

        button.classList.add("active");

    }

    if(pageId === "videosPage"){
        renderVideos();
    }

    if(pageId === "channelsPage"){
        renderChannels();
    }

    if(pageId === "subscriptionsPage"){
        renderSubscriptions();
    }

    if(pageId === "notificationsPage"){
        renderNotifications();
    }

    if(pageId === "adminPage"){

        if(currentUser()?.role !== "admin"){

            toast("Admin access required.");

            return;

        }

        updateAdmin();

    }

}


/* ============================================================
   DASHBOARD
============================================================ */

function updateDashboard(){

    const user = currentUser();

    if(!user) return;

    const ownVideos =
        db.videos.filter(
            v => v.userId === user.id
        );

    const views =
        ownVideos.reduce(
            (sum,v) =>
                sum + Number(v.views || 0),
            0
        );

    const likes =
        ownVideos.reduce(
            (sum,v) =>
                sum + (v.likes || []).length,
            0
        );

    const comments =
        db.comments.filter(
            c =>
            ownVideos.some(
                v => v.id === c.videoId
            )
        ).length;

    document
        .getElementById("channelCount")
        .textContent = db.users.length;

    document
        .getElementById("videoCount")
        .textContent = db.videos.length;

    document
        .getElementById("viewCount")
        .textContent =
        views;

    document
        .getElementById("subscriberCount")
        .textContent =
        user.subscribers.length;

    document
        .getElementById("statViews")
        .textContent =
        views;

    document
        .getElementById("statLikes")
        .textContent =
        likes;

    document
        .getElementById("statComments")
        .textContent =
        comments;

    document
        .getElementById("statVideos")
        .textContent =
        ownVideos.length;

}


/* ============================================================
   UPLOAD VIDEO
============================================================ */

function uploadVideo(){

    const user = currentUser();

    if(!user) return;

    const title =
        document
        .getElementById("videoTitle")
        .value.trim();

    const description =
        document
        .getElementById("videoDescription")
        .value.trim();

    const category =
        document
        .getElementById("videoCategory")
        .value;

    const tags =
        document
        .getElementById("videoTags")
        .value
        .split(",")
        .map(x => x.trim())
        .filter(Boolean);

    const videoFile =
        document
        .getElementById("videoFile")
        .files[0];

    const thumbnailFile =
        document
        .getElementById("thumbnailFile")
        .files[0];

    if(!title){

        toast("Enter a video title.");

        return;

    }

    let videoUrl =
        "";

    let thumbnailUrl =
        "https://images.unsplash.com/photo-1492724441997-5dc865305da7?auto=format&fit=crop&w=900&q=80";

    if(videoFile){

        videoUrl =
            URL.createObjectURL(videoFile);

    }

    if(thumbnailFile){

        thumbnailUrl =
            URL.createObjectURL(
                thumbnailFile
            );

    }

    const video = {

        id:generateId("video"),

        userId:user.id,

        title,

        description,

        category,

        tags,

        videoUrl,

        thumbnail:thumbnailUrl,

        views:0,

        likes:[],

        createdAt:Date.now()

    };

    db.videos.push(video);

    saveDB();

    document
        .getElementById("videoTitle")
        .value="";

    document
        .getElementById("videoDescription")
        .value="";

    document
        .getElementById("videoTags")
        .value="";

    document
        .getElementById("videoFile")
        .value="";

    document
        .getElementById("thumbnailFile")
        .value="";

    updateDashboard();

    renderVideos();

    toast("Video published successfully.");

    showPage("videosPage");

}


/* ============================================================
   RENDER VIDEOS
============================================================ */

function renderVideos(){

    const container =
        document.getElementById("videoGrid");

    if(!container) return;

    if(db.videos.length === 0){

        container.innerHTML = `
            <div class="form-box">
                <h3>No videos yet.</h3>
                <p>Upload the first video.</p>
            </div>
        `;

        return;

    }

    container.innerHTML =
        db.videos
        .slice()
        .reverse()
        .map(video => {

            const owner =
                db.users.find(
                    u => u.id === video.userId
                );

            const liked =
                video.likes.includes(
                    currentUser()?.id
                );

            const comments =
                db.comments.filter(
                    c =>
                    c.videoId === video.id
                ).length;

            const own =
                video.userId ===
                currentUser()?.id;

            return `

            <div class="video-card">

                <img
                    class="video-thumb"
                    src="${video.thumbnail}"
                    alt=""
                >

                <div class="video-info">

                    <h3>
                        ${escapeHTML(video.title)}
                    </h3>

                    <p>
                        👤 ${escapeHTML(owner?.username || "Unknown")}
                    </p>

                    <p>
                        👁️ ${video.views}
                        &nbsp;
                        ❤️ ${video.likes.length}
                        &nbsp;
                        💬 ${comments}
                    </p>

                    <p>
                        #${escapeHTML(video.category)}
                    </p>

                    <div class="video-actions">

                        <button
                            onclick="openVideo('${video.id}')"
                        >
                            ▶️ Watch
                        </button>

                        <button
                            class="${liked ? "like-active" : ""}"
                            onclick="toggleLike('${video.id}')"
                        >
                            ❤️ Like
                        </button>

                        <button
                            onclick="subscribeTo('${owner?.id}')"
                        >
                            📺 Subscribe
                        </button>

                        ${
                            own
                            ?
                            `
                            <button
                                onclick="deleteVideo('${video.id}')"
                            >
                                🗑️ Delete
                            </button>
                            `
                            :
                            ""
                        }

                    </div>

                </div>

            </div>

            `;

        })
        .join("");

}


/* ============================================================
   OPEN VIDEO
============================================================ */

let activeVideoId = null;

function openVideo(videoId){

    const video =
        db.videos.find(
            v => v.id === videoId
        );

    if(!video) return;

    activeVideoId = videoId;

    video.views++;

    saveDB();

    document
        .getElementById("modalVideoTitle")
        .textContent =
        video.title;

    document
        .getElementById("modalVideoDescription")
        .textContent =
        video.description;

    const player =
        document
        .getElementById("modalVideoPlayer");

    if(video.videoUrl){

        player.src =
            video.videoUrl;

    }else{

        player.removeAttribute("src");

    }

    renderComments(videoId);

    document
        .getElementById("videoModal")
        .classList.add("show");

    updateDashboard();

}


/* ============================================================
   CLOSE MODAL
============================================================ */

function closeModal(id){

    document
        .getElementById(id)
        .classList.remove("show");

}


/* ============================================================
   LIKE
============================================================ */

function toggleLike(videoId){

    const user = currentUser();

    const video =
        db.videos.find(
            v => v.id === videoId
        );

    if(!video) return;

    const index =
        video.likes.indexOf(user.id);

    if(index >= 0){

        video.likes.splice(index,1);

    }else{

        video.likes.push(user.id);

        addNotification(
            video.userId,
            `${user.username} liked your video "${video.title}".`,
            "like"
        );

    }

    saveDB();

    renderVideos();

    updateDashboard();

}


/* ============================================================
   COMMENTS
============================================================ */

function renderComments(videoId){

    const container =
        document.getElementById(
            "modalComments"
        );

    const comments =
        db.comments.filter(
            c => c.videoId === videoId
        );

    if(comments.length === 0){

        container.innerHTML =
            "<p>No comments yet.</p>";

        return;

    }

    container.innerHTML =
        comments.map(c => {

            const user =
                db.users.find(
                    u => u.id === c.userId
                );

            return `

            <div class="comment">

                <strong>
                    ${escapeHTML(user?.username || "User")}
                </strong>

                <span>
                    ${escapeHTML(c.text)}
                </span>

                <br>

                <small>
                    ${new Date(c.createdAt).toLocaleString()}
                </small>

            </div>

            `;

        }).join("");

}


function addComment(){

    const user = currentUser();

    if(!activeVideoId) return;

    const input =
        document.getElementById(
            "commentInput"
        );

    const text =
        input.value.trim();

    if(!text) return;

    const video =
        db.videos.find(
            v => v.id === activeVideoId
        );

    const comment = {

        id:generateId("comment"),

        videoId:activeVideoId,

        userId:user.id,

        text,

        createdAt:Date.now()

    };

    db.comments.push(comment);

    if(video.userId !== user.id){

        addNotification(
            video.userId,
            `${user.username} commented on "${video.title}".`,
            "comment"
        );

    }

    input.value="";

    saveDB();

    renderComments(activeVideoId);

    updateDashboard();

}


/* ============================================================
   DELETE VIDEO
============================================================ */

function deleteVideo(videoId){

    const user = currentUser();

    const video =
        db.videos.find(
            v => v.id === videoId
        );

    if(!video) return;

    if(
        video.userId !== user.id &&
        user.role !== "admin"
    ){

        toast("You cannot delete this video.");

        return;

    }

    if(!confirm("Delete this video?")){

        return;

    }

    db.videos =
        db.videos.filter(
            v => v.id !== videoId
        );

    db.comments =
        db.comments.filter(
            c => c.videoId !== videoId
        );

    saveDB();

    renderVideos();

    updateDashboard();

    toast("Video deleted.");

}


/* ============================================================
   CHANNELS
============================================================ */

function renderChannels(){

    const container =
        document.getElementById(
            "channelGrid"
        );

    if(!container) return;

    container.innerHTML =
        db.users
        .filter(u => !u.suspended)
        .map(user => {

            const subscribed =
                currentUser()?.following
                .includes(user.id);

            const videos =
                db.videos.filter(
                    v => v.userId === user.id
                ).length;

            return `

            <div class="channel-card">

                <div
                    class="channel-cover"
                    style="
                    background-image:url('${user.cover || ""}');
                    "
                ></div>

                <div class="channel-body">

                    <img
                        class="channel-avatar"
                        src="${user.avatar}"
                    >

                    <h3>
                        ${escapeHTML(user.username)}
                    </h3>

                    <p>
                        ${escapeHTML(
                            user.bio ||
                            "No biography yet."
                        )}
                    </p>

                    <div class="channel-stats">

                        👥 ${user.subscribers.length}
                        subscribers

                        &nbsp; • &nbsp;

                        🎥 ${videos}
                        videos

                    </div>

                    <br>

                    ${
                        user.id !== currentUser()?.id
                        ?
                        `
                        <button
                            class="${
                                subscribed
                                ?
                                "secondary-btn"
                                :
                                "primary-btn"
                            }"
                            onclick="subscribeTo('${user.id}')"
                        >
                            ${
                                subscribed
                                ?
                                "✓ Subscribed"
                                :
                                "📺 Subscribe"
                            }
                        </button>
                        `
                        :
                        `
                        <button
                            class="secondary-btn"
                            onclick="showPage('profilePage')"
                        >
                            My Channel
                        </button>
                        `
                    }

                </div>

            </div>

            `;

        })
        .join("");

}


/* ============================================================
   SUBSCRIBE
============================================================ */

function subscribeTo(userId){

    const user = currentUser();

    if(!user) return;

    if(userId === user.id){

        toast("You cannot subscribe to yourself.");

        return;

    }

    const target =
        db.users.find(
            u => u.id === userId
        );

    if(!target) return;

    const index =
        user.following.indexOf(userId);

    if(index >= 0){

        user.following.splice(index,1);

        target.subscribers =
            target.subscribers.filter(
                id => id !== user.id
            );

        toast(
            "Unsubscribed from " +
            target.username
        );

    }else{

        user.following.push(userId);

        if(!target.subscribers.includes(user.id)){

            target.subscribers.push(
                user.id
            );

        }

        addNotification(
            target.id,
            `${user.username} subscribed to your channel.`,
            "subscriber"
        );

        toast(
            "Subscribed to " +
            target.username
        );

    }

    saveDB();

    renderChannels();

    renderSubscriptions();

    updateDashboard();

}


/* ============================================================
   SUBSCRIPTIONS PAGE
============================================================ */

function renderSubscriptions(){

    const container =
        document.getElementById(
            "subscriptionGrid"
        );

    if(!container) return;

    const user = currentUser();

    const subscriptions =
        db.users.filter(
            u =>
            user.following.includes(u.id)
        );

    if(subscriptions.length === 0){

        container.innerHTML = `

            <div class="form-box">

                <h3>
                    You are not subscribed to any channels.
                </h3>

                <p>
                    Find channels and subscribe to them.
                </p>

            </div>

        `;

        return;

    }

    container.innerHTML =
        subscriptions.map(user => {

            return `

            <div class="channel-card">

                <div
                    class="channel-cover"
                    style="
                    background-image:url('${user.cover || ""}');
                    "
                ></div>

                <div class="channel-body">

                    <img
                        class="channel-avatar"
                        src="${user.avatar}"
                    >

                    <h3>
                        ${escapeHTML(user.username)}
                    </h3>

                    <p>
                        ${escapeHTML(user.bio || "")}
                    </p>

                    <div class="channel-stats">

                        👥 ${user.subscribers.length}
                        subscribers

                    </div>

                    <br>

                    <button
                        class="secondary-btn"
                        onclick="subscribeTo('${user.id}')"
                    >
                        Unsubscribe
                    </button>

                </div>

            </div>

            `;

        }).join("");

}


/* ============================================================
   PROFILE
============================================================ */

function renderProfile(){

    const user = currentUser();

    if(!user) return;

    document
        .getElementById("profileAvatar")
        .src =
        user.avatar;

    document
        .getElementById("profileCover")
        .style.backgroundImage =
        user.cover
        ?
        `url('${user.cover}')`
        :
        "linear-gradient(135deg,#2563eb,#7c3aed)";

    document
        .getElementById("profileUsername")
        .textContent =
        user.username;

    document
        .getElementById("profileBio")
        .textContent =
        user.bio ||
        "No biography yet.";

    document
        .getElementById("profileSubscribers")
        .textContent =
        user.subscribers.length;

    document
        .getElementById("editUsername")
        .value =
        user.username;

    document
        .getElementById("editBio")
        .value =
        user.bio || "";

    document
        .getElementById("editAvatar")
        .value =
        user.avatar || "";

    document
        .getElementById("editCover")
        .value =
        user.cover || "";

}


/* ============================================================
   SAVE PROFILE
============================================================ */

function saveProfile(){

    const user = currentUser();

    if(!user) return;

    const username =
        document
        .getElementById("editUsername")
        .value.trim();

    const bio =
        document
        .getElementById("editBio")
        .value.trim();

    const avatar =
        document
        .getElementById("editAvatar")
        .value.trim();

    const cover =
        document
        .getElementById("editCover")
        .value.trim();

    if(!username){

        toast("Username cannot be empty.");

        return;

    }

    user.username = username;

    user.bio = bio;

    user.avatar =
        avatar ||
        "https://i.pravatar.cc/150?u="+
        encodeURIComponent(user.email);

    user.cover = cover;

    saveDB();

    renderProfile();

    initializeApp();

    toast("Profile updated.");

}


/* ============================================================
   EDIT PROFILE
============================================================ */

function openEditProfile(){

    showPage("profilePage");

    document
        .getElementById("editUsername")
        .focus();

}


/* ============================================================
   SEARCH
============================================================ */

function performSearch(){

    const query =
        document
        .getElementById("searchInput")
        .value
        .trim()
        .toLowerCase();

    const filter =
        document
        .getElementById("searchFilter")
        .value;

    const container =
        document
        .getElementById("searchResults");

    if(!query){

        container.innerHTML =
            "<p>Type something to search.</p>";

        return;

    }

    let html="";

    if(
        filter === "all" ||
        filter === "videos"
    ){

        const videos =
            db.videos.filter(
                v =>
                v.title.toLowerCase().includes(query) ||
                v.description.toLowerCase().includes(query) ||
                v.tags.some(
                    t =>
                    t.toLowerCase().includes(query)
                )
            );

        html += `
            <h3>🎥 Videos</h3>
            <br>
            <div class="search-results">
        `;

        videos.forEach(v => {

            html += `

                <div class="video-card">

                    <img
                        class="video-thumb"
                        src="${v.thumbnail}"
                    >

                    <div class="video-info">

                        <h3>
                            ${escapeHTML(v.title)}
                        </h3>

                        <p>
                            ${escapeHTML(v.category)}
                        </p>

                        <button
                            class="primary-btn"
                            onclick="openVideo('${v.id}')"
                        >
                            Watch
                        </button>

                    </div>

                </div>

            `;

        });

        html += "</div><br>";

    }


    if(
        filter === "all" ||
        filter === "channels"
    ){

        const channels =
            db.users.filter(
                u =>
                u.username
                .toLowerCase()
                .includes(query)
            );

        html += `
            <h3>📺 Channels</h3>
            <br>
        `;

        channels.forEach(u => {

            html += `

                <div
                    class="form-box"
                    style="display:flex;align-items:center;gap:15px;"
                >

                    <img
                        src="${u.avatar}"
                        style="
                        width:60px;
                        height:60px;
                        border-radius:50%;
                        object-fit:cover;
                        "
                    >

                    <div>

                        <h3>
                            ${escapeHTML(u.username)}
                        </h3>

                        <p>
                            ${u.subscribers.length}
                            subscribers
                        </p>

                    </div>

                </div>

            `;

        });

    }


    if(
        filter === "all" ||
        filter === "users"
    ){

        const users =
            db.users.filter(
                u =>
                u.username
                .toLowerCase()
                .includes(query)
            );

        html += `
            <h3>👤 Users</h3>
            <br>
        `;

        users.forEach(u => {

            html += `

                <div class="form-box">

                    <h3>
                        ${escapeHTML(u.username)}
                    </h3>

                    <p>
                        ${escapeHTML(u.bio || "")}
                    </p>

                </div>

            `;

        });

    }

    if(!html){

        html =
            "<p>No search results found.</p>";

    }

    container.innerHTML = html;

}


/* ============================================================
   NOTIFICATIONS
============================================================ */

function addNotification(
    userId,
    message,
    type
){

    db.notifications.push({

        id:generateId("notification"),

        userId,

        message,

        type,

        read:false,

        createdAt:Date.now()

    });

    saveDB();

}


function renderNotifications(){

    const user = currentUser();

    if(!user) return;

    const container =
        document.getElementById(
            "notificationList"
        );

    const notifications =
        db.notifications
        .filter(
            n => n.userId === user.id
        )
        .sort(
            (a,b) =>
            b.createdAt-a.createdAt
        );

    const unread =
        notifications.filter(
            n => !n.read
        ).length;

    document
        .getElementById("notificationBadge")
        .textContent =
        unread;

    if(notifications.length === 0){

        container.innerHTML =
            `
            <div class="form-box">
                No notifications yet.
            </div>
            `;

        return;

    }

    container.innerHTML =
        notifications.map(n => {

            return `

            <div
                class="
                notification
                ${n.read ? "read" : ""}
                "
            >

                <div>

                    <strong>
                        🔔
                    </strong>

                    ${escapeHTML(n.message)}

                    <br>

                    <small>
                        ${new Date(
                            n.createdAt
                        ).toLocaleString()}
                    </small>

                </div>

                ${
                    !n.read
                    ?
                    `
                    <button
                        class="secondary-btn"
                        onclick="markNotificationRead('${n.id}')"
                    >
                        Read
                    </button>
                    `
                    :
                    ""
                }

            </div>

            `;

        }).join("");

}


function markNotificationRead(id){

    const notification =
        db.notifications.find(
            n => n.id === id
        );

    if(notification){

        notification.read = true;

        saveDB();

        renderNotifications();

    }

}


function markAllNotificationsRead(){

    const user = currentUser();

    db.notifications
        .filter(
            n => n.userId === user.id
        )
        .forEach(
            n => n.read = true
        );

    saveDB();

    renderNotifications();

}


/* ============================================================
   ADMIN
============================================================ */

function updateAdmin(){

    if(currentUser()?.role !== "admin"){

        return;

    }

    document
        .getElementById("adminUsers")
        .textContent =
        db.users.length;

    document
        .getElementById("adminVideos")
        .textContent =
        db.videos.length;

    document
        .getElementById("adminChannels")
        .textContent =
        db.users.length;

    document
        .getElementById("adminComments")
        .textContent =
        db.comments.length;

    renderAdminUsers();

}


function showAdminTab(tab){

    document
        .querySelectorAll(".admin-tabs button")
        .forEach(
            b => b.classList.remove("active")
        );

    event.target.classList.add("active");

    if(tab === "users"){
        renderAdminUsers();
    }

    if(tab === "videos"){
        renderAdminVideos();
    }

    if(tab === "channels"){
        renderAdminChannels();
    }

    if(tab === "comments"){
        renderAdminComments();
    }

}


/* ============================================================
   ADMIN USERS
============================================================ */

function renderAdminUsers(){

    const container =
        document.getElementById(
            "adminContent"
        );

    container.innerHTML = `

        <table class="admin-table">

            <thead>

                <tr>

                    <th>User</th>

                    <th>Email</th>

                    <th>Role</th>

                    <th>Status</th>

                    <th>Actions</th>

                </tr>

            </thead>

            <tbody>

                ${
                    db.users.map(u => `

                    <tr>

                        <td>
                            ${escapeHTML(u.username)}
                        </td>

                        <td>
                            ${escapeHTML(u.email)}
                        </td>

                        <td>
                            ${u.role}
                        </td>

                        <td class="${
                            u.suspended
                            ?
                            "status-suspended"
                            :
                            "status-active"
                        }">

                            ${
                                u.suspended
                                ?
                                "Suspended"
                                :
                                "Active"
                            }

                        </td>

                        <td>

                            ${
                                u.id !== currentUser().id
                                ?
                                `
                                <button
                                    class="${
                                        u.suspended
                                        ?
                                        "success-btn"
                                        :
                                        "danger-btn"
                                    }"
                                    onclick="
                                    toggleSuspendUser(
                                        '${u.id}'
                                    )
                                    "
                                >

                                    ${
                                        u.suspended
                                        ?
                                        "Activate"
                                        :
                                        "Suspend"
                                    }

                                </button>
                                `
                                :
                                "Current admin"

                            }

                        </td>

                    </tr>

                    `).join("")
                }

            </tbody>

        </table>

    `;

}


/* ============================================================
   ADMIN VIDEOS
============================================================ */

function renderAdminVideos(){

    const container =
        document.getElementById(
            "adminContent"
        );

    container.innerHTML = `

        <table class="admin-table">

            <thead>

                <tr>

                    <th>Title</th>

                    <th>Owner</th>

                    <th>Views</th>

                    <th>Likes</th>

                    <th>Action</th>

                </tr>

            </thead>

            <tbody>

                ${
                    db.videos.map(v => {

                        const owner =
                            db.users.find(
                                u =>
                                u.id === v.userId
                            );

                        return `

                        <tr>

                            <td>
                                ${escapeHTML(v.title)}
                            </td>

                            <td>
                                ${escapeHTML(
                                    owner?.username || ""
                                )}
                            </td>

                            <td>
                                ${v.views}
                            </td>

                            <td>
                                ${v.likes.length}
                            </td>

                            <td>

                                <button
                                    class="danger-btn"
                                    onclick="
                                    adminDeleteVideo(
                                        '${v.id}'
                                    )
                                    "
                                >
                                    Delete
                                </button>

                            </td>

                        </tr>

                        `;

                    }).join("")
                }

            </tbody>

        </table>

    `;

}


function adminDeleteVideo(id){

    db.videos =
        db.videos.filter(
            v => v.id !== id
        );

    db.comments =
        db.comments.filter(
            c => c.videoId !== id
        );

    saveDB();

    updateAdmin();

    updateDashboard();

    toast("Video deleted by admin.");

}


/* ============================================================
   ADMIN CHANNELS
============================================================ */

function renderAdminChannels(){

    const container =
        document.getElementById(
            "adminContent"
        );

    container.innerHTML = `

        <table class="admin-table">

            <thead>

                <tr>

                    <th>Channel</th>

                    <th>Subscribers</th>

                    <th>Videos</th>

                    <th>Action</th>

                </tr>

            </thead>

            <tbody>

                ${
                    db.users.map(u => {

                        const videoCount =
                            db.videos.filter(
                                v =>
                                v.userId === u.id
                            ).length;

                        return `

                        <tr>

                            <td>
                                ${escapeHTML(u.username)}
                            </td>

                            <td>
                                ${u.subscribers.length}
                            </td>

                            <td>
                                ${videoCount}
                            </td>

                            <td>

                                ${
                                    u.id !== currentUser().id
                                    ?
                                    `
                                    <button
                                        class="${
                                            u.suspended
                                            ?
                                            "success-btn"
                                            :
                                            "danger-btn"
                                        }"
                                        onclick="
                                        toggleSuspendUser(
                                            '${u.id}'
                                        )
                                        "
                                    >
                                        ${
                                            u.suspended
                                            ?
                                            "Activate"
                                            :
                                            "Suspend"
                                        }
                                    </button>
                                    `
                                    :
                                    ""
                                }

                            </td>

                        </tr>

                        `;

                    }).join("")
                }

            </tbody>

        </table>

    `;

}


/* ============================================================
   ADMIN COMMENTS
============================================================ */

function renderAdminComments(){

    const container =
        document.getElementById(
            "adminContent"
        );

    container.innerHTML = `

        <table class="admin-table">

            <thead>

                <tr>

                    <th>User</th>

                    <th>Comment</th>

                    <th>Video</th>

                    <th>Action</th>

                </tr>

            </thead>

            <tbody>

                ${
                    db.comments.map(c => {

                        const user =
                            db.users.find(
                                u =>
                                u.id === c.userId
                            );

                        const video =
                            db.videos.find(
                                v =>
                                v.id === c.videoId
                            );

                        return `

                        <tr>

                            <td>
                                ${escapeHTML(
                                    user?.username || ""
                                )}
                            </td>

                            <td>
                                ${escapeHTML(c.text)}
                            </td>

                            <td>
                                ${escapeHTML(
                                    video?.title || "Deleted video"
                                )}
                            </td>

                            <td>

                                <button
                                    class="danger-btn"
                                    onclick="
                                    adminDeleteComment(
                                        '${c.id}'
                                    )
                                    "
                                >
                                    Delete
                                </button>

                            </td>

                        </tr>

                        `;

                    }).join("")
                }

            </tbody>

        </table>

    `;

}


function adminDeleteComment(id){

    db.comments =
        db.comments.filter(
            c => c.id !== id
        );

    saveDB();

    updateAdmin();

    toast("Comment deleted.");

}


/* ============================================================
   SUSPEND USER
============================================================ */

function toggleSuspendUser(id){

    const user =
        db.users.find(
            u => u.id === id
        );

    if(!user) return;

    user.suspended =
        !user.suspended;

    saveDB();

    updateAdmin();

    renderChannels();

    toast(
        user.suspended
        ?
        "User suspended."
        :
        "User activated."
    );

}


/* ============================================================
   HTML ESCAPE
============================================================ */

function escapeHTML(value){

    return String(value || "")
        .replace(/&/g,"&amp;")
        .replace(/</g,"&lt;")
        .replace(/>/g,"&gt;")
        .replace(/"/g,"&quot;")
        .replace(/'/g,"&#039;");

}


/* ============================================================
   DEMO DATA
============================================================ */

function createDemoData(){

    if(db.videos.length > 0){

        return;

    }

    const admin =
        db.users.find(
            u => u.role === "admin"
        );

    const video = {

        id:generateId("video"),

        userId:admin.id,

        title:"Welcome to ChannelHub",

        description:
            "This is a demo video for the ChannelHub platform.",

        category:"Education",

        tags:[
            "channelhub",
            "demo",
            "video"
        ],

        videoUrl:"",

        thumbnail:
            "https://images.unsplash.com/photo-1492619375914-88005aa9e8fb?auto=format&fit=crop&w=900&q=80",

        views:125,

        likes:[],

        createdAt:Date.now()

    };

    db.videos.push(video);

    saveDB();

}


/* ============================================================
   START
============================================================ */

createDemoData();

if(currentUser()){

    initializeApp();

}else{

    showAuth("login");

}


/* ============================================================
   KEYBOARD
============================================================ */

document.addEventListener(
    "keydown",
    function(event){

        if(event.key === "Escape"){

            document
                .querySelectorAll(".modal")
                .forEach(
                    m =>
                    m.classList.remove("show")
                );

        }

    }
);


/* ============================================================
   CLOSE MODAL WHEN CLICKING OUTSIDE
============================================================ */

document
    .querySelectorAll(".modal")
    .forEach(modal => {

        modal.addEventListener(
            "click",
            function(event){

                if(event.target === modal){

                    modal.classList.remove(
                        "show"
                    );

                }

            }
        );

    });

</script>

</body>
</html>
