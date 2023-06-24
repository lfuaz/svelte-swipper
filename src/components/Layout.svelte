<script>
  export let title;

  import Star from "../assets/star-solid.svg";
  import Fire from "../assets/fire-solid.svg";
  import Zap from "../assets/bolt-solid.svg";
  import Otter from "../assets/otter-solid (1).svg";
  import Message from "../assets/comment-solid.svg";

  const handleClick = (e) => {
    location.href = e.currentTarget.href;
  };
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
      <li class:active={title === "favourite"}>
        <a href="/favourite" on:pointerdown={(e) => handleClick(e)}>
          <img loading="lazy" alt="info-button" src={Star} /></a
        >
      </li>

      <li class:active={title === "zap"}>
        <a href="/zap" on:pointerdown={(e) => handleClick(e)}>
          <img loading="lazy" alt="info-button" src={Zap} /></a
        >
      </li>

      <li class:active={title === "home"}>
        <a href="/" on:pointerdown={(e) => handleClick(e)}>
          <img loading="lazy" alt="home-button" src={Otter} />
        </a>
      </li>

      <li class:active={title === "fire"}>
        <a href="/fire" on:pointerdown={(e) => handleClick(e)}
          ><img loading="lazy" alt="user-button" src={Fire} /></a
        >
      </li>

      <li class:active={title === "contact"}>
        <a href="/contact" on:pointerdown={(e) => handleClick(e)}
          ><img loading="lazy" alt="user-button" src={Message} /></a
        >
      </li>
    </ul>
  </nav>
</footer>
