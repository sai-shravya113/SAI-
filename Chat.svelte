<script>
  import {
    Avatar,
    ClickAwayListener,
    IconButton,
  } from "@material-ui/core";
  import {
    AttachFile,
    InsertEmoticon,
    Mic,
    MoreVert,
    SearchOutlined,
    ArrowBack,
  } from "@material-ui/icons";
  import { Link, useParams } from "svelte-routing";
  import { onMount } from 'svelte';
  import db from "../firebase";
  import { useStateValue } from "../StateProvider";
  import firebase from "firebase";

  let seed = Math.floor(Math.random() * 50000);
  let input = '';
  const { roomId, receiver } = useParams();
  const [roomName, setRoomName] = useState('');
  const [messages, setMessages] = useState([]);
  let showdropdown = false;
  const [user] = useStateValue();

  onMount(() => {
    setSeed(Math.floor(Math.random() * 50000));
    if (roomId) {
      db.collection("Users")
        .doc(receiver)
        .onSnapshot((snapshot) => {
          if (snapshot.data()) {
            setRoomName(snapshot.data().name);
          }
          console.log("snapshot:", snapshot.data());
        });
      db.collection("Messages")
        .doc(roomId)
        .collection("messages")
        .orderBy("timestamp", "asc")
        .onSnapshot((resultsnap) => {
          setMessages(
            resultsnap.docs.map((doc) => {
              return doc.data();
            })
          );
        });
    }
  });

  const sendMessage = (e) => {
    e.preventDefault();
    if (input) {
      db.collection("Messages").doc(roomId).collection("messages").add({
        message: input,
        sender: user.email,
        receiver: receiver,
        name: user.displayName,
        timestamp: firebase.firestore.FieldValue.serverTimestamp(),
      });
      setInput("");
    } else {
      alert("Type something first");
    }
  };
</script>

<div class:class={hide ? "chat Chat" : "chat"}>
  <div class="chat__header">
    <Link to="/">
      <IconButton>
        <ArrowBack />
      </IconButton>
    </Link>
    <Avatar src={`you can add here`} />
    <div class="chat__headerInfo">
      <h3>{roomName}</h3>
      <p>
        {messages.length !== 0
          ? `Last seen at ` +
            new Date(
              messages[messages.length - 1]?.timestamp?.toDate()
            ).toUTCString()
          : ""}
      </p>
    </div>
    <div class="chat__headerRight">
      <IconButton
        on:click={() =>
          alert(
            "Not added this functionality.\nClick on three dots to delete room."
          )
        }
      >
        <SearchOutlined />
      </IconButton>
      <ClickAwayListener on:click={() => setDropdown(false)}>
        <div class="dropdown">
          <IconButton
            on:click={() => {
              setDropdown(!showdropdown);
            }}
          >
            <MoreVert />
          </IconButton>
          <div class:class={showdropdown ? "dropdown__list" : "dropdown__list hide"}>
            <ul>
              {/* <Link to="/">
                <li
                  on:click={() => {
                    removeRoom(roomId);
                  }}
                >
                  Delete Room
                </li>
              </Link> */}
            </ul>
          </div>
        </div>
      </ClickAwayListener>
    </div>
  </div>
  <div class="chat__body">
    {#each messages as message}
      <div key={message.timestamp}>
        <p
          class={`chat__message ${message.receiver === receiver && "chat__receiver"}`}
        >
          <span class="chat__name">{message.name}</span>
          {message.message}
          <span class="chat__timestamp">
            {new Date(message.timestamp?.toDate()).toUTCString()}
          </span>
        </p>
      </div>
    {/each}
  </div>
  <div class="chat__footer">
    <IconButton
      on:click={() =>
        alert(
          "Not added this functionality.\nClick on three dots on top right to delete room."
        )
      }
    >
      <InsertEmoticon />
    </IconButton>
    <IconButton
      class="attach__file"
      on:click={() =>
        alert(
          "Not added this functionality.\nClick on three dots on top right to delete room."
        )
      }
    >
      <AttachFile />
    </IconButton>
    <form>
      <input
        required
        type="text"
        on:input={(e) => setInput(e.target.value)}
        value="{input}"
        placeholder="Type a message"
      />
      <button type="submit" on:click={sendMessage}>
        Send a message
      </button>
    </form>
    <IconButton
      on:click={() =>
        alert(
          "Not added this functionality.\nClick on three dots on top right to delete room."
        )
      }
    >
      <Mic />
    </IconButton>
  </div>
</div>

<style>
  /* Add your styles here */
  .chat {
  flex: 0.65;
  display: flex;
  flex-direction: column;
}
.chat__header {
  padding: 20px;
  display: flex;
  align-items: center;
  border-bottom: 1px solid lightgray;
}
.chat__headerInfo {
  flex: 1;
  padding-left: 20px;
}
.chat__headerInfo > p {
  color: gray;
  font-size: 13px;
}
.chat__headerRight {
  display: flex;
  justify-content: space-between;
  min-width: 60px;
}
.chat__body {
  flex: 1;
  background-image: url("../whatsapp-original-bg.png");
  /*background-image: url("https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png");*/
  background-repeat: repeat;
  background-position: center;
  padding: 30px;
  overflow: scroll;
  -ms-overflow-style: none;
  scrollbar-width: none;
}
.chat__body::-webkit-scrollbar {
  display: none;
}
.chat__message {
  position: relative;
  justify-content: space-between;
  min-width: 150px;
  padding: 7px;
  font-size: 14px;
  background-color: #ffffff;
  border-radius: 10px;
  width: -webkit-fit-content;
  width: -moz-fit-content;
  width: fit-content;
  margin-bottom: 50px;
  max-width: 270px;
  word-wrap: break-word !important;
  word-break: keep-all;
}

.chat__name {
  position: absolute;
  top: -16px;
  font-weight: 700;
  font-size: x-small;
}
.chat__receiver {
  margin-left: auto;
  background-color: #dcf8c6;
}
.chat__timestamp {
  position: absolute;
  bottom: -15px;
  right: 0px;
  /*margin-left: 10px;*/
  font-size: xx-small;
  word-wrap: normal !important;
  word-break: keep-all;
}
/*
.msg__status{
  position: absolute;
  bottom: 0;
  right: 5px;
}
.chat__message .MuiSvgIcon-root{
  font-size:16px;
  margin-left: 3px;
  color: gray;
}

*/
.chat__footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 62px;
  border-top: 1px solid lightgray;
}
.chat__footer > form {
  flex: 1;
  display: flex;
}
.chat__footer > form > input {
  border-radius: 30px;
  margin: 5px 10px;
  padding: 9px 12px 11px;
  border: none;
  flex: 1 1 auto;
  box-sizing: border-box;
  width: inherit;
  min-width: 0;
  min-height: 20px;
  font-weight: 400;
  font-size: 15px;
  line-height: 20px;
  outline: none;
  will-change: width;
}
.chat__footer > form > button {
  display: none;
}
.chat__footer > .MuiSvgIcon-root {
  padding: 10px;
  color: gray;
}

.chat__headerRight .dropdown {
  position: relative;
}

.chat__headerRight .dropdown__list {
  display: block;
  position: absolute;
  top: 50px;
  right: 20px;
  z-index: 1;
}
.chat__headerRight .hide {
  display: none;
}
.chat__headerRight .dropdown ul {
  border-radius: 5px;
  width: 120px;
  background-color: white;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 3px rgba(0, 0, 0, 0.12);
}
.chat__headerRight .dropdown__list ul li {
  list-style: none;
  padding: 10px;
  cursor: pointer;
}

.chat__headerRight .dropdown__list ul li:hover {
  background-color: #eeeeee;
  border-radius: 5px;
}
.Chat {
  display: none;
}

@media screen and (max-width: 630px) {
  .chat {
    flex: 1;
    height: 100vh;
  }
  .chat .MuiIconButton-root {
    padding: 0 !important;
  }
  .chat__body {
    padding: 30px 20px;
  }
  .chat__message {
    max-width: 200px;
  }
  .chat__header {
    padding-left: 10px;
    padding-right: 10px;
  }
  .chat__header .MuiAvatar-root {
    margin-left: 10px;
  }
  .chat__footer {
    padding: 0 10px;
  }
  .attach__file {
    display: none !important;
  }
  .chat__headerRight .dropdown__list {
    top: 40px;
    right: 10px;
  }
}
</style>
