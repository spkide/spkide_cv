<script context="module">
  export const ssr = false;
</script>

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

  /* 🔊 AUDIO */
  let keySound;
  let beepSound;

  /* TEXT */
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
home | skills | projects | contact
about | neofetch | whoami
matrix | snow on/off
fortune | hack
clear | exit
`,
    skills: `
Linux · Bash · C/C++
Pentesting · Docker
Svelte · Proxmox
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
      return push(cmd, "You were never supposed to type this.");
    }

    push(cmd, `command not found: ${cmd}`);
  }
</script>

<style>
  :global(body) {
    background: black;
    color: #00ff9c;
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
  }

  .terminal {
    max-width: 900px;
    margin: auto;
    padding: 1rem;
    border: 1px solid #00ff9c55;
    box-shadow: 0 0 40px #00ff9c33;
    background: black;
    white-space: pre-wrap;
  }

  input {
    background: transparent;
    border: none;
    color: #00ff9c;
    outline: none;
    width: 80%;
  }

  .panel {
    border-left: 1px solid #00ff9c33;
    padding: 1rem;
    font-size: 0.85rem;
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
</style>

<div class="bg"></div>

{#if matrix}
  <div class="matrix"></div>
{/if}

{#if snow}
  <div class="snow">
    {#each Array.from({ length: 80 }) as _, i}
      <span
        style="left:{Math.random()*100}%;animation-duration:{5+Math.random()*5}s"
      >❄</span>
    {/each}
  </div>
{/if}

{#if shutdown}
  <div class="shutdown">SYSTEM HALTED</div>
{/if}

<div class="layout">
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
    clear · exit
  </aside>
</div>
