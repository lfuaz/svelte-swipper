<script>
  import { onMount, onDestroy } from "svelte";
  import Layout from "../../components/Layout.svelte";
  import Chat from "../../assets/meuf.jpg";

  let drag = false;
  let targ;
  let offsetX;
  let offsetY;
  let coordX;
  let coordY;
  let originalCoordX;
  let originalCoordY;
  let rotationDeg = 0;

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
    } else {
      rotationDeg = Math.max(deltaX / 10, -15);
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

  function handleDrop() {
    // rotationDeg must have dead zone between -10 and 10 to cancel out
    if (rotationDeg > 20) {
      console.log("right");
    } else if (rotationDeg < -20) {
      console.log("left");
    } else {
      console.log("cancel");
    }

    // Callback function to handle the drop direction
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
  <div class="dragme"><img src={Chat} alt="chat" loading="lazy" /></div>
</Layout>
