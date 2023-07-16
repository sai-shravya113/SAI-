<script>
  import {
    Avatar,
    ClickAwayListener,
    IconButton,
  } from "@material-ui/core";
  import DonutLargeIcon from "@material-ui/icons/DonutLarge";
  import ChatIcon from "@material-ui/icons/Chat";
  import MoreVertIcon from "@material-ui/icons/MoreVert";
  import { SearchOutlined } from "@material-ui/icons";
  import { Link } from "svelte-routing";
  import db, { auth } from "../firebase";
  import { useState, useEffect } from 'svelte';
  import { useStateValue } from "../StateProvider";
  import { actionTypes } from "../reducer";
  import "@fortawesome/fontawesome-free/css/all.css";
  import ConnectWithoutContactIcon from "@mui/icons-material/ConnectWithoutContact";
  import SidebarChat from "./SidebarChat.svelte";

  let users = [];
  let search = '';
  const currentUser = auth.currentUser.email;
  const [{ user }, dispatch] = useStateValue();
  let showdropdown = false;

  useEffect(() => {
    const unsubscribe = db.collection("Users").onSnapshot((snapshot) => {
      users = snapshot.docs.map((doc) => ({
        id: doc.id,
        data: doc.data(),
      }));
    });

    return () => {
      unsubscribe();
    };
  }, []);

  const signOut = () => {
    auth
      .signOut()
      .then(() => {
        dispatch({
          type: actionTypes.SET_USER,
          user: null,
        });
        setDropdown(!showdropdown);
      })
      .catch((err) => {
        alert(err.message);
      });
  };

  const createChat = () => {
    setDropdown(!showdropdown);
    const roomName = prompt("Please enter name for Room");
    if (roomName) {
      db.collection("Rooms").add({
        name: roomName,
      });
      console.log("executed");
    }
  };

  let setDropdown = (value) => {
    showdropdown = value;
  };
</script>

<div class:class={hide ? "sidebar side__bar" : "sidebar"}>
  <div class="sidebar__header">
    <div class="text">
      <h2>Resolver</h2>
    </div>
    <div class="sidebar__headerRight">
      <IconButton class="icon">
        <DonutLargeIcon style="color: white" />
      </IconButton>
      <IconButton>
        <ChatIcon style="color: white" />
      </IconButton>
      <ClickAwayListener on:click={() => setDropdown(false)}>
        <div class="dropdown">
          <IconButton style="color: white" on:click={() => setDropdown(!showdropdown)}>
            <MoreVertIcon />
          </IconButton>
          <div class:class={showdropdown ? "dropdown__list" : "dropdown__list hide"}>
            <ul>
              <li on:click={createChat}>Add Room</li>
              <li on:click={signOut}>Log Out</li>
              <li on:click={() => alert("Not added this functionality.\nTry Logout and add Room options")}>Help ?</li>
            </ul>
          </div>
        </div>
      </ClickAwayListener>
    </div>
  </div>
  <div class="navbar">
    <Link to={"/user"}>
      <div class="navbar__contacts">
        <i class="fa fa-user"></i>
        <span>Chats</span>
      </div>
    </Link>
    <Link to={"/rooms/BroadCast/random@gmail.com"}>
      <div class="navbar__status">
        <ConnectWithoutContactIcon />
        <span>BroadCast</span>
      </div>
    </Link>
  </div>
  <div class="sidebar__search">
    <div class="sidebar__searchContainer">
      <SearchOutlined />
      <input
        placeholder="Search a room"
        type="text"
        bind:value="{search}"
      />
    </div>
  </div>
  <div class="sidebar__chats">
    <SidebarChat addNewChat={true} />
    {#each users as user_temp}
      {#if (user_temp.data.name.toLowerCase().includes(search.toLowerCase()) || search === '') && user_temp.data.email !== currentUser}
        <SidebarChat
          key="{user_temp.id}"
          id="{user_temp.id}"
          name="{user_temp.data.name}"
        />
      {/if}
    {/each}
  </div>
</div>

<style>
  /* Add your styles here */
  .sidebar {
  display: flex;
  flex-direction: column;
  flex: 0.35;
}
.sidebar__search {
  display: flex;
  align-items: center;
  background-color: #f6f6f6;
  height: 50px;
  padding: 10px;
}
.sidebar__searchContainer {
  display: flex;
  align-items: center;
  background-color: white;
  width: 100%;
  height: 35px;
  border-radius: 20px;
}
.sidebar__searchContainer > .MuiSvgIcon-root {
  color: gray;
  padding: 10px;
}
.sidebar__searchContainer > input {
  border: none;
  margin-left: 10px;
  box-sizing: border-box;
  min-width: 0;
  /* font-weight: 400;
  font-size: 15px;*/
  outline: none;
  will-change: width;
}
.sidebar__header {
  .text{
    color: white;
  }
  background-color: rgba(0, 128, 105, 1);
  display: flex;
  justify-content: space-between;
  padding: 20px;
  border-right: 1px solid lightgray;
}

.sidebar__headerRight {
  
  display: flex;
  align-items: center;
  justify-content: space-between;
  min-width: 10vw;
}
.sidebar__headerRight > .MuiSvgIcon-root {
  
  margin-right: 2vw;
  font-size: 24px !important;
}
.sidebar__chats {
  background-color: white;
  flex: 1;
  overflow: scroll;
  -ms-overflow-style: none;
  scrollbar-width: none;
}

.sidebar__chats::-webkit-scrollbar {
  display: none;
}

.dropdown {
  position: relative;
}

.icon{
  background-color: white;
}

.dropdown__list {
  display: block;
  position: absolute;
  top: 55px;
  right: 20px;
  font-size: 14px;
  z-index: 1;
}

.dropdown__list .MuiSvgIcon-root {
  /* color: white; */
  font-size: inherit;
  margin-right: 5px;
}
.hide {
  display: none;
}
.dropdown ul {
  border-radius: 5px;
  width: 120px;
  background-color: white;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 3px rgba(0, 0, 0, 0.12);
}
.dropdown__list ul li {
  list-style: none;
  padding: 10px;
  cursor: pointer;
}

.dropdown__list ul li:hover {
  background-color: #eeeeee;
}

@media screen and (max-width: 630px) {
  .sidebar {
    flex: 1;
  }
  .side__bar {
    display: none;
  }
  .sidebar__header {
    padding-right: 10px;
  }
  .sidebar__header .MuiIconButton-root {
    padding-right: 7px !important;
  }
}

.navbar {
  background-color: rgba(0, 128, 105, 1);
  display: flex;
  justify-content: space-evenly;
  padding: 10px;
}

.navbar__status,
.navbar__contacts {
  color: white;
  display: flex;
  align-items: center;
  cursor: pointer;
}

.navbar__status i,
.navbar__contacts i {
  margin-right: 5px;
}

.navbar__status:hover,
.navbar__contacts:hover {
  background-color: #128c7e;
}

.navbar {
  background-color:  rgba(0, 128, 105, 1);
  display: flex;
  justify-content: space-evenly;
  padding: 10px;
}

.navbar__status,
.navbar__contacts {
  color: white;
  display: flex;
  align-items: center;
  cursor: pointer;
  position: relative;
}

.navbar__status i,
.navbar__contacts i {
  margin-right: 5px;
}

.navbar__status::after,
.navbar__contacts::after {
  content: "";
  position: absolute;
  bottom: -9px;
  left: 0;
  width: 100%;
  height: 2px;
  background-color: white;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.navbar__status:hover::after,
.navbar__contacts:hover::after,
.navbar__status.active::after,
.navbar__contacts.active::after {
  opacity: 1;
}

</style>
