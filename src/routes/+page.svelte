<script context="module">
  export const ssr = false;
</script>

<svelte:head>
  <title>SPKIDE :: terminal</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet" />
</svelte:head>

<script>
  import { onMount } from "svelte";

  let input = "";
  let history = [];
  let typed = "";
  let i = 0;

  let glitch = false;
  let shutdown = false;
  let matrix = true;
  let snow = true;

  let inputEl;
  const focus = () => setTimeout(() => inputEl?.focus(), 0);

  let keySound;
  let beepSound;

  const intro = "booting spkide@linux [OK]";
  const motd = `
🎄 FELIZ NAVIDAD 🎄
de parte de SPKIDE

“No deberías estar aquí”
“El sistema existe antes que tú”
“SPKIDE no se presenta, se descubre”

⚠ SECURITY WARNING
Identity corrupted
`;

  const sections = {
    home: `
SPKIDE — UNKNOWN ENTITY
Linux · Security · Underground

Type: help
`,
    help: `
home | skills | abilities | projects | contact
about | neofetch | whoami
matrix | snow on/off
fortune | hack | spkide
clear | exit
`,
    skills: `
💻 Programming & Development:
  - C/C++, Python, JavaScript
  - Svelte, Node.js, Bash scripting
  - Docker, Proxmox

🛡 Security & Networking:
  - Pentesting, Wireshark, Nmap
  - Linux hardening
  - SSH, VPNs

⚙ Tools & Systems:
  - Linux administration
  - Git, GitHub, GitLab
  - CI/CD pipelines
`,
    abilities: `
🚀 Abilities:
  - Reverse engineering
  - Exploit development
  - System automation
  - Terminal wizardry
  - Ethical hacking
  - Creative coding
`,
    projects: `
terminal-portfolio
ctf-writeups
linux-utils
`,
    contact: `
github.com/spkide
`
  };

  const fortunes = [
    "There is no system, only control.",
    "You are already inside.",
    "Silence is a weapon.",
    "Access denied is a lie.",
    "You were never supposed to find this."
  ];

  onMount(() => {
    document.title = "SPKIDE :: terminal";

    keySound = new Audio("/sounds/keyboard.mp3");
    beepSound = new Audio("/sounds/beep.mp3");
    keySound.volume = 0.15;
    beepSound.volume = 0.4;

    const t = setInterval(() => {
      typed += intro[i];
      i++;
      if (i >= intro.length) {
        clearInterval(t);
        history.push({ cmd: "motd", out: motd });
        history.push({ cmd: "home", out: sections.home });
        focus();
      }
    }, 60);
  });

  function push(cmd, out) {
    history = [...history, { cmd, out }];
    input = "";
    focus();
  }

  function run() {
    const cmd = input.trim().toLowerCase();
    if (!cmd) return;

    keySound.currentTime = 0;
    keySound.play();
    glitch = false;

    if (cmd === "clear") {
      history = [];
      input = "";
      return;
    }

    if (cmd === "exit") {
      push(cmd, "System halted.");
      setTimeout(() => shutdown = true, 800);
      return;
    }

    if (cmd === "help") return push(cmd, sections.help);
    if (cmd === "home") return push(cmd, sections.home);
    if (cmd === "skills") return push(cmd, sections.skills);
    if (cmd === "abilities") return push(cmd, sections.abilities);
    if (cmd === "projects") return push(cmd, sections.projects);
    if (cmd === "contact") return push(cmd, sections.contact);

    if (cmd === "about") {
      glitch = true;
      beepSound.play();
      return push(cmd, "ERROR: identity unknown\nUse: home");
    }

    if (cmd === "whoami") return push(cmd, "unknown");

    if (cmd === "neofetch") {
      return push(cmd, `
spkide@linux
-------------
OS: SPKIDE Linux
Kernel: 6.6.6-ghost
Shell: bash
WM: terminal
Theme: hacker
`);
    }

    if (cmd === "matrix") {
      matrix = !matrix;
      return push(cmd, `Matrix ${matrix ? "enabled" : "disabled"}`);
    }

    if (cmd === "snow on") {
      snow = true;
      return push(cmd, "Snow enabled ❄️");
    }

    if (cmd === "snow off") {
      snow = false;
      return push(cmd, "Snow disabled");
    }

    if (cmd === "fortune") {
      return push(cmd, fortunes[Math.floor(Math.random() * fortunes.length)]);
    }

    if (cmd === "hack") {
      glitch = true;
      beepSound.play();
      return push(cmd, "Injecting payload...\nroot denied");
    }

    if (cmd === "spkide") {
      glitch = true;
      beepSound.play();
      return push(cmd, `
“No deberías estar aquí”
“El sistema existe antes que tú”
“SPKIDE no se presenta, se descubre”
`);
    }

    push(cmd, `command not found: ${cmd}`);
  }
</script>

<style>
  :global(html), :global(body) {
    margin: 0;
    padding: 0;
    background: black !important;
    font-family: "JetBrains Mono", monospace;
    overflow: hidden;
  }

  .bg {
    position: fixed;
    inset: -50%;
    background:
      radial-gradient(circle at 20% 30%, #00ff9c33, transparent 40%),
      radial-gradient(circle at 70% 80%, #00ff9c22, transparent 40%);
    animation: spin 30s linear infinite;
    z-index: 0;
  }

  @keyframes spin { to { transform: rotate(360deg); } }

  .crt::before {
    content: "";
    position: fixed;
    inset: 0;
    background: repeating-linear-gradient(
      to bottom,
      rgba(255,255,255,0.04),
      rgba(0,0,0,0.04) 2px
    );
    pointer-events: none;
  }

  .matrix {
    position: fixed;
    inset: 0;
    background:
      repeating-linear-gradient(
        90deg,
        rgba(0,255,0,0.05),
        rgba(0,0,0,0.05) 2px
      );
    pointer-events: none;
  }

  .snow span {
    position: fixed;
    top: -10px;
    color: white;
    animation: fall linear infinite;
  }

  @keyframes fall {
    to { transform: translateY(110vh); }
  }

  .layout {
    display: grid;
    grid-template-columns: 1fr 260px;
    min-height: 100vh;
    position: relative;
    z-index: 2;
    gap: 10px;
    padding: 10px;
  }

  .terminal {
    max-width: 100%;
    margin: auto;
    padding: 1rem;
    border: 1px solid #00ff9c55;
    box-shadow: 0 0 40px #00ff9c33;
    background: black;
    white-space: pre-wrap;
    color: #00ff9c;
    min-height: 80vh;
  }

  .terminal div {
    color: #00ff9c;
  }

  input {
    background: transparent;
    border: none;
    color: #00ff9c;
    outline: none;
    width: 80%;
    font-size: 1rem;
  }

  .panel {
    border-left: 1px solid #00ff9c33;
    padding: 1rem;
    font-size: 0.85rem;
    color: #00ff9c;
  }

  .shutdown {
    position: fixed;
    inset: 0;
    background: black;
    color: red;
    display: grid;
    place-items: center;
    z-index: 50;
  }

  /* Responsive */
  @media (max-width: 768px) {
    .layout {
      grid-template-columns: 1fr;
    }
    .panel {
      border-left: none;
      border-top: 1px solid #00ff9c33;
      font-size: 0.8rem;
    }
    input {
      width: 100%;
      font-size: 0.9rem;
    }
  }

  @media (max-width: 480px) {
    .terminal {
      font-size: 0.8rem;
      min-height: 70vh;
    }
  }
</style>

<div class="bg"></div>

{#if matrix}
  <div class="matrix"></div>
{/if}

{#if snow}
  <div class="snow">
    {#each Array.from({ length: 80 }) as _, i}
      <span style="left:{Math.random()*100}%;animation-duration:{5+Math.random()*5}s">❄</span>
    {/each}
  </div>
{/if}

{#if shutdown}
  <div class="shutdown">SYSTEM HALTED</div>
{/if}

<div class="layout crt">
  <div class="terminal">
    <div>spkide@linux:~$ {typed}</div>

    {#each history as h}
      <div>spkide@linux:~$ {h.cmd}</div>
      <div>{h.out}</div>
    {/each}

    <div>
      spkide@linux:~$
      <input
        bind:this={inputEl}
        bind:value={input}
        on:keydown={(e)=>e.key==="Enter"&&run()}
        autofocus
      />
    </div>
  </div>

  <aside class="panel">
    <b>COMMANDS</b><br/>
    help<br/>
    about<br/>
    neofetch<br/>
    whoami<br/>
    matrix<br/>
    snow on/off<br/>
    fortune<br/>
    hack<br/>
    spkide<br/>
    clear · exit
  </aside>
</div>
