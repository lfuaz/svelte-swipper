<script>
  import Layout from "../../components/Layout.svelte";
  import swal from "sweetalert";
  let name = "";
  let email = "";
  let message = "";

  function handleSubmit(event) {
    //verify if the form is empty
    if (name == "" || email == "" || message == "") {
      swal("Oops", "Please fill all the fields!", "error");
      return false;
    } else {
      swal("Perfect !", {
        icon: "success",
        buttons: [false],
      });
    }
  }
</script>

<Layout title="contact">
  <h1>Contact</h1>

  <!-- back to home -->
  <!-- form contact  -->
  <div class="container">
    <form class="input-form" on:submit|preventDefault={handleSubmit}>
      <input
        type="text"
        id="name"
        name="name"
        class="input-field"
        placeholder="Your name.."
        bind:value={name}
      />

      <input
        type="email"
        id="email"
        name="email"
        class="input-field"
        placeholder="Your email.."
        bind:value={email}
      />

      <textarea
        id="message"
        name="message"
        class="textarea-field"
        placeholder="Write something.."
        style="height:200px"
        bind:value={message}
      />

      <input type="submit" value="Submit" class="input-field" />
    </form>
  </div>
</Layout>
