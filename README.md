<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PAK vs ENG Live</title>

<style>
body{
    margin:0;
    font-family:Arial, sans-serif;
    background:linear-gradient(135deg,#006400,#002244);
    color:white;
    text-align:center;
}

.container{
    padding:20px;
}

.score-box{
    background:rgba(255,255,255,0.12);
    padding:25px;
    margin-top:20px;
    border-radius:15px;
    backdrop-filter: blur(10px);
    box-shadow:0 0 20px rgba(0,0,0,0.4);
}

.team{
    font-size:22px;
    margin:15px 0;
    transition:all 0.3s ease;
}

.team.updated{
    transform:scale(1.1);
    color:#00ff88;
}

.live{
    color:#00ff00;
    font-weight:bold;
    animation:blink 1s infinite;
}

@keyframes blink{
    0%{opacity:1;}
    50%{opacity:0.4;}
    100%{opacity:1;}
}

.status{
    margin-top:10px;
    font-size:16px;
    color:#ffeb3b;
}

.footer{
    margin-top:15px;
    font-size:13px;
    opacity:0.7;
}
</style>
</head>

<body>
<div class="container">
    <h1>Pakistan 🇵🇰 vs England 🏴</h1>
    <div class="live">LIVE MATCH</div>

    <div class="score-box">
        <div class="team" id="pakScore">PAK: --/-- (-- ov)</div>
        <div class="team" id="engScore">ENG: --/-- (-- ov)</div>
        <div class="status" id="matchStatus">Loading live data...</div>
    </div>

    <div class="footer">
        Auto Refresh: 10 Seconds
    </div>
</div>

<script>

const API_KEY = "3d127321-a353-4baf-b67f-e785b7508c54";

let lastPakScore = "";
let lastEngScore = "";

async function fetchLiveScore() {

    try {

        const response = await fetch(
            "https://api.cricapi.com/v1/currentMatches?apikey=" + API_KEY + "&offset=0"
        );

        const result = await response.json();

        if(result.status !== "success"){
            document.getElementById("matchStatus").innerText =
                "API limit reached or invalid key";
            return;
        }

        // Find live Pakistan vs England match
        const match = result.data.find(m =>
            m.matchStarted === true &&
            m.matchEnded === false &&
            m.name.toLowerCase().includes("pakistan") &&
            m.name.toLowerCase().includes("england")
        );

        if(!match){
            document.getElementById("matchStatus").innerText =
                "Pakistan vs England match not live";
            return;
        }

        document.getElementById("matchStatus").innerText = match.status;

        if(match.score && match.score.length > 0){

            const pak = match.score[0];
            const pakText = "PAK: " + pak.r + "/" + pak.w + " (" + pak.o + " ov)";

            if(pakText !== lastPakScore){
                const el = document.getElementById("pakScore");
                el.innerText = pakText;
                el.classList.add("updated");
                setTimeout(()=>el.classList.remove("updated"),400);
                lastPakScore = pakText;
            }

            if(match.score.length > 1){
                const eng = match.score[1];
                const engText = "ENG: " + eng.r + "/" + eng.w + " (" + eng.o + " ov)";

                if(engText !== lastEngScore){
                    const el2 = document.getElementById("engScore");
                    el2.innerText = engText;
                    el2.classList.add("updated");
                    setTimeout(()=>el2.classList.remove("updated"),400);
                    lastEngScore = engText;
                }
            }
        }

    } catch (error) {
        console.error(error);
        document.getElementById("matchStatus").innerText =
            "Error fetching live data";
    }
}

fetchLiveScore();
setInterval(fetchLiveScore, 10000);

</script>
</body>
</html>