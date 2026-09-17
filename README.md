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
