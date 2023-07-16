<script>
  import { Avatar } from "@material-ui/core";
  import { Link } from "svelte-routing";
  import { auth } from "../firebase";
  import { useEffect, useState } from 'svelte';
  import { v4 as uuidv4 } from 'uuid';

  export let id;
  export let name;
  export let addNewChat;

  let seed = Math.floor(Math.random() * 50000);
  let messages = "";
  let uniqueId = "";

  useEffect(() => {
    setSeed(Math.floor(Math.random() * 50000));
  }, []);

  useEffect(() => {
    let sender = auth.currentUser.email;
    let receiver = id;

    if (receiver !== undefined) {
      if (sender > receiver) {
        [sender, receiver] = [receiver, sender];
      }
      const merge = sender + receiver;
      setUniqueId(merge);
    }
  }, [id]);

  function setUniqueId(value) {
    uniqueId = value;
  }
</script>

{#if !addNewChat && uniqueId}
  <Link to={`/rooms/${uniqueId}/${id}`}>
    <div class="sidebarChat">
      <Avatar src={`https://robohash.org/john${seed}.png`} />
      <div class="sidebarChat__info">
        <h2>{name}</h2>
        <p>
          {messages[0]?.message.substring(0, 15)}
          {messages[0]?.message.length > 15 && "..."}
        </p>
      </div>
    </div>
  </Link>
{:else}
  <div class="a"></div>
{/if}

<style>
  /* Add your styles here */
  .sidebarChat {
  display: flex;
  padding: 20px;
  cursor: pointer;
  border-bottom: 1px solid #f6f6f6;
}

.sidebarChat:hover {
  background-color: #ebebeb;
}
.sidebarChat__info > h2 {
  font-size: 16px;
  margin-bottom: 8px;
}
.sidebarChat__info {
  margin-left: 15px;
}

a {
  text-decoration: none;
  color: black;
}

</style>
