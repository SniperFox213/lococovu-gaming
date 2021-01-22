<script>
  // Importing modules
  import { goto } from "@sapper/app";
  import { onMount } from "svelte";
  
  import moment from "moment";

  import Cookie from "cookie-universal";
  const cookies = Cookie();

  import storage from "local-storage";

  // Importing stores
  import { stores } from "@sapper/app";
  const { page } = stores();

  // Importing actions
  import getProfile from "../../actions/profile/getProfile.action";

  // Importing components
  import Spinner from "../../components/Loader/Spinner.svelte";
  import Background from "../../components/Layout/Background.svelte";

  // Redirecting user to needed page
  onMount(() => {
    // Checking something...
    if ($page.query.type == "updateSecurityCode") {
      let token = $page.query.token;
      let securityCode = $page.query.securityCode;

      if (token == null) token = cookies.get('token');

      // And now let's update this security code
      // and then let's redirect our user to
      // main application
      getProfile(token)
      .then((response) => {
        if (cookies.get('token') != token) cookies.set('token', token, { path: "/", domain: ".lococovu.me", expires: moment().add('1', 'year').toDate() })

        // Let's save this securityCode to our
        // localStorage and let's send user
        // to application page
        storage.set(`AT-${response.id}`, securityCode);

        setTimeouut(() => {
          window.location.href = "/app";
        }, 1000);
      });
    } else {
      goto(`https://lococovu.me/authorize?return=${encodeURIComponent('https://gaming.lococovu.me/app')}`);
    };
  });
</script>

<!-- Page's Layout -->
<Background />

<main class="w-full h-screen flex justify-center items-center">
  <!-- Loader -->
  <Spinner size="20" color="#fff" />
</main>