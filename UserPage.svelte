<script>
  import { Link } from 'svelte-routing';
  import Sidebar from "./Components/Sidebar.svelte";
  import Chat from "./Components/Chat.svelte";
  import { onMount } from 'svelte';
  import db from "./firebase";
  import { auth, provider } from "./firebase";
  import { useStateValue } from "./StateProvider";
  import { Button } from "@material-ui/core";
  import Loading from "./Components/Loading.svelte";

  const homepagestyle = `
    margin: 20px;
  `;

  let user;
  let loading = true;

  const [{}, dispatch] = useStateValue();

  onMount(() => {
    const listener = auth.onAuthStateChanged((authUser) => {
      loading = false;
      user = authUser;
    });
    return () => listener();
  });

  const removeRoom = (roomid) => {
    db.collection("Rooms")
      .doc(roomid)
      .delete()
      .then(() => {
        alert("Room Deleted");
      });
  };

  const signIn = () => {
    auth
      .signInWithPopup(provider)
      .then((result) => {
        dispatch({
          type: "SET_USER",
          user: result.user,
        });
        const docref = db.collection("Users").doc(result.user.email);
        docref.set({
          email: result.user.email,
          name: result.user.displayName,
        });
      })
      .catch((error) => {
        alert(error.message);
      });
  };

  let isLoggedIn = user !== null;

  let showLoginPage = loading || !isLoggedIn;

  let hideSidebar = false;
  let hideChat = true;

  let showProjectInfo = false;

  let removeRoomFunction = removeRoom;

  let imageUrl = "https://stories.jobaaj.com/files/manage/thumb/641bf7335dd8e.jpg";
</script>

{#if showLoginPage}
  <div class="login">
    <div class="login__container">
      <img src="{imageUrl}" alt="" />
      <div class="login__text">
        <h1>Sign in to</h1>
        <br />
        <h1>HAL SMART TOWNSHIP</h1>
      </div>
      <Button on:click={signIn}>Sign In with Google</Button>
      <footer style="{homepagestyle}">
        <p><Link to="/">Back to Homepage</Link>.</p>
      </footer>
    </div>
  </div>
{:else}
  <div class="app">
    <div class="app__body">
      <Router basename={process.env.PUBLIC_URL}>
        <Switch>
          <Route path="/user" exact>
            <Sidebar hide="{hideSidebar}" />
            <Chat hide="{hideChat}" removeRoom="{removeRoomFunction}" />
            {#if showProjectInfo}
              <div class="project__info">
                <img src="{imageUrl}" alt=""/>
                <div class="text">
                  <h1>Resolver</h1>
                </div>
              </div>
            {/if}
          </Route>
          <Route path="/rooms/:roomId/:receiver">
            <Sidebar hide="true" />
            <Chat hide="false" removeRoom="{removeRoomFunction}" />
          </Route>
        </Switch>
      </Router>
    </div>
  </div>
{/if}

<style>
  /* Add your styles here */
  
/* feature 1 -> login module css*/
/***    General     ***/
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: "Roboto", sans-serif
}

body {
    margin: 0 auto
}

.m-5-auto {
    margin: 5rem auto
}

.primary-button {
    margin-top: 5rem;
    margin-right: 1rem;
    padding: .6rem;
    width: 10rem;
    background: #222;
    border: none;
    color: #fff;
    font-size: 1.2rem;
    transition: all .5s;
    cursor: pointer;
    text-transform: capitalize
}

/***    Landing Page     ***/
.text-center{
    text-align: center!important;
/* display: flex;
justify-content: center; */
}
.main-title,
.main-para {
    color: #f1f1f1;

}

.main-title {
    padding-top: 2rem;
    font-size: 3rem;
    font-family: Arial, Helvetica, sans-serif;
    text-transform: uppercase
}

.main-para {
    padding-top: 4rem;
    font-size: 2.5rem;
    text-Transform: capitalize;
}

.headerimg{
    padding-top: 2rem;
}

.reg_btn span {
    display: inline-block;
    position: relative;
    transition: 0.5s;
}

.reg_btn span:after {
    content: '\00bb';
    position: absolute;
    opacity: 0;
    top: 0;
    right: -20px;
    transition: 0.5s;
}

.reg_btn:hover span {
  padding-right: 25px;
}

.reg_btn:hover span:after {
  opacity: 1;
  right: 0;
}

.buttons{
    padding-top: -10px;
}

/***    Login Page     ***/
.main{
    background-color: white;
    height: 100vh !important;
}
.login h2 {
    font-weight: 300;
}

.login form {
    display: inline-block;
    background: #f3f3f3;
    border: 1px solid #ddd;
    border-radius: 2px;
    padding: 2rem;
    margin: 2rem 0 1rem 0
}

.login form label {
    float: left;
    font-size: .9rem;
    margin: 0;
    padding: 0
}

.right-label {
    float: right;
    cursor: pointer;
}

.login input {
    width: 15rem;
    padding: .3rem;
    border-radius: 5px;
    outline: none;
    border: none
}

#sub_btn {
    display: block;
    width: 100%;
    padding: .3rem;
    border: none;
    background: #222;
    color: #fff;
    border-radius: 3px;
}

#sub_btn:hover {
    background: #333;
    transition: all .5s
}

footer p {
    margin: 0;
    font-size: 1rem;
    color: white;
}

/***    Register Page     ***/
#checkbox {
    width: 1rem
}

form span {
    font-size: .8rem
}

#reset_pass_lbl {
    float: left
}

/***    Home Page     ***/
.home-page-title {
    color: #222
}

</style>
