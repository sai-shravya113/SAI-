<script>
  import { onMount } from 'svelte';
  import { auth } from '../firebase';
  import { useStateValue } from '../StateProvider';
  import db from '../firebase';
  import { actionTypes } from '../reducer';
</script>

<div class="app__body">
  {#if $user}
    <Router>
      <!-- Replace 'Route' with Svelte routing components -->
      <Switch>
        <!-- Replace 'Route' with Svelte routing components -->
        <Route path="/admin">
          <Sidebar hide={false} />
          <Chat hide={true} on:removeRoom={removeRoom} />
          <div class="project__info">
            <img src="https://stories.jobaaj.com/files/manage/thumb/641bf7335dd8e.jpg" alt="" />
            <div class="text">
              <h1>Resolver</h1>
            </div>
          </div>
        </Route>
        <Route path="/rooms/:roomId/:receiver">
          <Sidebar hide={true} />
          <Chat hide={false} on:removeRoom={removeRoom} />
        </Route>
      </Switch>
    </Router>
  {:else}
    <!-- Handle the case when the user is not logged in -->
    <div class="login">
      <div class="login__container">
        <img src="https://stories.jobaaj.com/files/manage/thumb/641bf7335dd8e.jpg" alt="" />
        <div class="login__text">
          <h1>Sign in to</h1>
          <br />
          <h1>HAL SMART TOWNSHIP</h1>
        </div>
        <button on:click={signInWithGoogle}>Sign In with Google</button>
      </div>
    </div>
  {/if}
</div>

<script>
  // State and logic
  const [{ user }, dispatch] = useStateValue();
  const [loading, setLoading] = useState(true);

  // Fetch user data on component mount
  onMount(() => {
    setLoading(true);
    const listener = auth.onAuthStateChanged((authUser) => {
      setLoading(false);
      if (authUser) {
        dispatch({
          type: actionTypes.SET_USER,
          user: authUser,
        });
      } else {
        dispatch({
          type: actionTypes.SET_USER,
          user: null,
        });
      }
    });
    return () => listener();
  });

  // Remove room function
  const removeRoom = (roomid) => {
    db.collection("Rooms")
      .doc(roomid)
      .delete()
      .then(() => {
        alert("Room Deleted");
      });
  };

  // Handle sign-in with Google
  const signInWithGoogle = () => {
    // Replace this with the appropriate logic for signing in with Google
    console.log('Sign in with Google');
  };
</script>

<style>
  /* Add your styles here */
  .app {
  background-color: #dadbd3;
  /* display: grid; */
  place-items: center;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100vw;
}
.app__body {
  display: flex;
  background-color: #ededed;
  height: 90vh;
  width: 90vw;
  box-shadow: -1px 4px 20px -6px rgba(0, 0, 0, 0.7);
}

.project__info {
  flex: 0.65;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: white;
  background-image: linear-gradient(rgba(0, 0, 0, 0.6), rgba(0, 0, 0, 0.6)),
    url("whatsapp-bg.jpg");
  background-position: center;
  background-repeat: no-repeat;
  background-color: #054740;
}
.project__info h1 {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  font-size: 2.2rem;
  font-weight: 400;
}
.project__info img {
  -o-object-fit: contain;
  object-fit: contain;
  max-width: 300px;
}

@media screen and (max-width: 800px) {
  .app__body {
    height: 100vh;
    width: 100vw;
  }
}

@media screen and (max-width: 630px) {
  .app__body {
    overflow: auto;
  }
  .app__body {
    overflow: auto;
  }
  .project__info {
    display: none;
  }
}

/* UserPage */

.login__container {
  padding: 40px;
  margin: 10px;
  text-align: center;
  background-color: white;
  border-radius: 10px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 3px rgba(0, 0, 0, 0.12);
}
.login__container > img {
  -o-object-fit: contain;
  object-fit: contain;
  height: 100px;
  margin-bottom: 40px;
}
.login__container > button {
  margin-top: 50px;
  text-transform: inherit !important;
  background-color: #0a8d48 !important;
  color: white;
} 

</style>
