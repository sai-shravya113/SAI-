<script>
  import { userStore } from './stores';
  import { setUser } from './actions';
  import { onMount } from 'svelte';

  let user;

  const unsubscribe = userStore.subscribe((value) => {
    user = value.user;
  });

  onMount(() => {
    return () => {
      unsubscribe();
    };
  });

  const updateUser = () => {
    setUser({ name: 'John Doe', email: 'john@example.com' });
  };
</script>

<div>
  {#if user}
    <h1>Hello, {user.name}</h1>
    <p>Email: {user.email}</p>
  {:else}
    <p>User not logged in.</p>
  {/if}

  <button on:click={updateUser}>Update User</button>
</div>
