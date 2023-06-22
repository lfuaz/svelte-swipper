<script>
  import { Router, Route } from "svelte-routing";
  import Home from "./pages/Home/index.svelte";
  import About from "./pages/About/index.svelte";
  import Contact from "./pages/Contact/index.svelte";
</script>

<Router>
  <Route path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
</Router>