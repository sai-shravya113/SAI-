
<script>
  import { Router, Route } from "svelte-routing";
  import LandingPage from "./Components/pages/LandingPage.svelte";
  import AdminPage from "./AdminPage.sveltelte";
  import UserPage from "./UserPage.svelte";
  import ForgetPasswordPage from "./Components/pages/ForgetPasswordPage.svelte";
  import HomePage from "./Components/pages/HomePage.svelte";
  import Footer from "./Footer.svelte";
  let src =
    "https://raw.githubusercontent.com/PureshwarGonekar/Hal-Smart_Township/master/public/logo.ico";
  const FooterStyle = `
    background: #222;
    font-size: 0.8rem;
    color: #fff;
    position: absolute;
    bottom: 0;
    padding: 1rem;
    margin: 0;
    width: 100%;
    opacity: 0.5;
  `;
</script>

<Router>
  <div>
    <Route exact path="/" component={LandingPage} />
    <Route path="/admin" component={AdminPage} />
    <Route path="/user" component={UserPage} />
    <Route path="/forget-password" component={ForgetPasswordPage} />
    <Route path="/home" component={HomePage} />
  </div>
</Router>

<Footer {FooterStyle} />

<!-- Footer.svelte -->
<script>
  export let style;
</script>

<p class="text-center" style={style}>Designed & coded by HAL Vocation Trainee</p>
