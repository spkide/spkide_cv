<script>
  import { goto } from "$app/navigation";

  let user = "";
  let pass = "";

  function login() {
    if (!user || !pass) return;
    goto("/terminal");
  }
</script>

<style>
  body {
    margin: 0;
    background: black;
    color: #00ff9c;
    font-family: monospace;
  }

  .login {
    height: 100vh;
    display: grid;
    place-items: center;
  }

  .box {
    border: 1px solid #00ff9c55;
    padding: 2rem;
    width: min(90%, 320px);
  }

  input, button {
    width: 100%;
    margin-top: 0.5rem;
    padding: 0.5rem;
    background: black;
    border: 1px solid #00ff9c;
    color: #00ff9c;
  }
</style>

<div class="login">
  <div class="box">
    <h3>spkide@linux</h3>
    <input placeholder="user" bind:value={user} />
    <input type="password" placeholder="password" bind:value={pass} />
    <button on:click={login}>enter</button>
  </div>
</div>
