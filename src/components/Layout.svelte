<script>
  export let title;

  import Home from "../assets/home.svg";
  import Info from "../assets/info.svg";
  import User from "../assets/user.svg";
  import Zap from "../assets/zap.svg";
</script>

<header>
  <!-- Header content -->
</header>

<main>
  <slot />
  <!-- This will render the content of the child pages -->
</main>

<footer>
  <nav>
    <ul>
      <li class:active={title === "zap"}>
        <a href="/zap"> <img loading="lazy" alt="info-button" src={Info} /></a>
      </li>

      <li class:active={title === "about"}>
        <a href="/about"> <img loading="lazy" alt="info-button" src={Zap} /></a>
      </li>

      <li class:active={title === "home"}>
        <a href="/">
          <img loading="lazy" alt="home-button" src={Home} />
        </a>
      </li>

      <li class:active={title === "contact"}>
        <a href="/contact"
          ><img loading="lazy" alt="user-button" src={User} /></a
        >
      </li>
    </ul>
  </nav>
</footer>
