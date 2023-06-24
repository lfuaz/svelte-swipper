<script>
  import { onMount, onDestroy } from "svelte";
  import Layout from "../../components/Layout.svelte";
  import Meuf from "../../assets/meuf.jpg";
  import MeufJ from "../../assets/meuf-jaune.jpg";

  let drag = false;
  let targ;
  let offsetX;
  let offsetY;
  let coordX;
  let coordY;
  let originalCoordX;
  let originalCoordY;
  let rotationDeg = 0;
  let speed = 150; //ms

  let startDrag = (e) => {
    // determine event object
    if (!e) {
      e = window.event;
    }
    if (e.preventDefault) e.preventDefault();

    // IE uses srcElement, others use target
    targ = e.target ? e.target : e.srcElement;

    // traverse up the DOM tree until .dragme element is found
    while (targ && targ.className !== "dragme") {
      targ = targ.parentNode;
    }

    if (!targ || targ.className !== "dragme") {
      return;
    }

    // calculate event X, Y coordinates
    if (e.type === "touchstart") {
      offsetX = e.touches[0].clientX;
      offsetY = e.touches[0].clientY;
    } else {
      offsetX = e.clientX;
      offsetY = e.clientY;
    }

    // assign default values for top and left properties
    if (!targ.style.left) {
      targ.style.left = "0px";
    }
    if (!targ.style.top) {
      targ.style.top = "0px";
    }

    // calculate integer values for top and left properties
    coordX = parseInt(targ.style.left);
    coordY = parseInt(targ.style.top);
    originalCoordX = coordX;
    originalCoordY = coordY;
    drag = true;

    // move div element
    if (e.type === "touchstart") {
      document.addEventListener("touchmove", dragDiv, { passive: false });
      document.addEventListener("touchend", touchEndCallback);
    } else {
      document.onmousemove = dragDiv;
      document.onmouseup = () => stopDrag(handleDrop);
    }

    return false;
  };

  function dragDiv(e) {
    let nextImage = document.querySelector(".waiting");
    if (!drag) {
      return;
    }
    if (!e) {
      e = window.event;
    }

    // calculate drag distance
    let deltaX, deltaY;
    if (e.type === "touchmove") {
      deltaX = e.touches[0].clientX - offsetX;
      deltaY = e.touches[0].clientY - offsetY;
    } else {
      deltaX = e.clientX - offsetX;
      deltaY = e.clientY - offsetY;
    }

    // update element position
    targ.style.left = coordX + deltaX + "px";
    targ.style.top = coordY + deltaY + "px";

    // calculate drag direction
    let direction = deltaX > 0 ? "right" : "left";
    // apply rotation based on drag direction
    if (direction === "left") {
      rotationDeg = Math.min(deltaX / 10, 15);
      let opacity = mapRange(deltaX, 0, 200, 0, 1);
      document.querySelectorAll(".choice")[0]["style"].opacity = opacity * -1;
      if (opacity * -1 < 0.9 && opacity * -1 > 0.3)
        nextImage["style"].scale = opacity * -1;
    } else {
      rotationDeg = Math.max(deltaX / 10, -15);
      let opacity = mapRange(deltaX, 0, 200, 0, 1);
      document.querySelectorAll(".choice")[1]["style"].opacity = opacity;
      if (opacity < 0.9 && opacity > 0.3) nextImage["style"].scale = opacity;
    }

    // apply rotation to element
    targ.style.transform = `rotate(${rotationDeg}deg)`;

    return { isRotating: false, rotationDeg };
  }

  function stopDrag(callback) {
    drag = false;
    document.onmousemove = null;
    document.onmouseup = null;
    document.removeEventListener("touchmove", dragDiv);
    document.removeEventListener("touchend", touchEndCallback);

    //start animation
    targ.style.transition = `all ${speed}ms linear`;

    // Return "dragme" item to its original position and rotation
    targ.style.left = originalCoordX + "px";
    targ.style.top = originalCoordY + "px";
    targ.style.transform = "rotate(0deg)";

    // Determine drop direction
    let direction = targ.style.left > originalCoordX ? "right" : "left";

    // Invoke callback with drop direction
    callback(direction);
  }

  function touchEndCallback() {
    stopDrag(handleDrop);
  }

  function resetChoice() {
    let nextImage = document.querySelector(".waiting");

    setTimeout(
      () => (document.querySelector(".dragme")["style"].transition = "none"),
      speed
    );
    document.querySelectorAll(".choice").forEach((choice) => {
      choice["style"].opacity = 0;
    });
    nextImage["style"].transition = "all 0.3s linear";
    nextImage["style"].scale = 0.3;
  }

  function handleDrop() {
    // rotationDeg must have dead zone between -10 and 10 to cancel out
    if (rotationDeg > 20) {
      console.log("dropped right");
      resetChoice();
    } else if (rotationDeg < -20) {
      console.log("dropped left");
      resetChoice();
    } else {
      console.log("cancel moove");
      resetChoice();
    }

    // Callback function to handle the drop direction
  }

  function mapRange(value, x1, y1, x2, y2) {
    return ((value - x1) * (y2 - x2)) / (y1 - x1) + x2;
  }

  onMount(() => {
    document.onmousedown = startDrag;
    document.addEventListener("touchstart", startDrag, { passive: false });

    return () => {
      document.onmousedown = null;
      document.removeEventListener("touchstart", startDrag);
    };
  });
</script>

<Layout title="home">
  <h1>Brozz</h1>
  <div class="container">
    <div class="dragme"><img src={Meuf} alt="chat" loading="lazy" /></div>
    <img src={MeufJ} alt="chat" loading="lazy" class="waiting" />
    <div class="container_choice">
      <div class="choice" />
      <div class="choice" />
    </div>
  </div>
</Layout>
