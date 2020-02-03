<script>
  // Firebase App (the core Firebase SDK) is always required and
  // must be listed before other Firebase SDKs
  import firebase from "firebase/app";

  // Add the Firebase services that you want to use
  import "firebase/auth";
  import "firebase/database";

  import {
    Container,
    Row,
    Col,
    Collapse,
    Navbar,
    NavbarToggler,
    NavbarBrand,
    Nav,
    NavItem,
    NavLink,
    UncontrolledDropdown,
    DropdownToggle,
    DropdownMenu,
    DropdownItem,
    Badge,
    Button,
    ButtonGroup,
    ListGroup,
    ListGroupItem,
    Spinner
  } from "sveltestrap";

  const firebaseConfig = {
    apiKey: "FIREBASEAPIKEY",
    authDomain: "ym-todo-app.firebaseapp.com",
    databaseURL: "https://ym-todo-app.firebaseio.com",
    projectId: "ym-todo-app",
    storageBucket: "ym-todo-app.appspot.com",
    messagingSenderId: "382021898454",
    appId: "1:382021898454:web:8b9681b9cd123b46"
  };

  firebase.initializeApp(firebaseConfig);
  var database = firebase.database();

  Date.prototype.toDateInputValue = function() {
    var local = new Date(this);
    local.setMinutes(this.getMinutes() - this.getTimezoneOffset());
    return local.toJSON().slice(0, 10);
  };
  let todoList = [];
  let doneList = [];
  let r = false;
  let loading = true;
  let name = "";
  let date = new Date().toDateInputValue();
  database.ref().once("value", function(snapshot) {
    todoList = snapshot.val().todoList ? snapshot.val().todoList : [];
    doneList = snapshot.val().doneList ? snapshot.val().doneList : [];
    loading = false;
  });

  $: todoList, doneList, save();
  function save() {
    if (r == true) {
      database.ref("todoList").set(todoList);

      database.ref("doneList").set(doneList);
    }
  }

  function addTodo() {
    r = true;
    let obj = { name, date };
    todoList.push(obj);
    console.log(todoList);
    todoList = todoList;
  }

  function deleteTodo(i) {
    r = true;
    todoList.splice(i, 1);
    todoList = todoList;
  }

  function deleteDoneTodo(i) {
    r = true;
    doneList.splice(i, 1);
    doneList = doneList;
  }

  function unfinTodo(i) {
    r = true;
    var el = doneList[i];
    doneList.splice(i, 1);
    doneList = doneList;
    todoList = [...todoList, el];
  }
  function finTodo(i) {
    r = true;
    var el = todoList[i];
    todoList.splice(i, 1);
    todoList = todoList;
    doneList = [...doneList, el];
  }
</script>

<style>
  .somemargin {
    margin: 1em;
    width: calc(100% - 2em);
  }
  div.cont {
    width: 100%;
    margin-top: 2em;
  }
  el.buttongroup {
    margin-left: 1em;
  }
  div.cont > input,
  div.cont > button {
    width: 100%;
  }
  body,
  html {
  }
  main {
    background-clip: padding-box;
    /* text-align: center; */
    height: calc(100vh - 2em);
    width: calc(100vw - 2em);

    padding: 1em;
    /* max-width: 240px; */
    margin: 0 auto;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
  .done {
    text-decoration: line-through;
  }
</style>

<svelte:head>
  <title>Svelte To-Do Application</title>
  <link rel="shortcut icon" href="/favicon.ico" type="image/x-icon" />
  <link rel="icon" href="/favicon.ico" type="image/x-icon" />
  <link
    rel="stylesheet"
    href="https://stackpath.bootstrapcdn.com/bootswatch/4.4.1/flatly/bootstrap.min.css" />
  <script src="https://www.gstatic.com/firebasejs/7.8.0/firebase-app.js">

  </script>
  <!-- If you enabled Analytics in your project, add the Firebase SDK for Analytics -->
  <script src="https://www.gstatic.com/firebasejs/7.8.0/firebase-analytics.js">

  </script>
  <!-- Add Firebase products that you want to use -->
  <script src="https://www.gstatic.com/firebasejs/7.8.0/firebase-auth.js">

  </script>
  <script src="https://www.gstatic.com/firebasejs/7.8.0/firebase-firestore.js">

  </script>
</svelte:head>
<main>

  <Navbar color="dark" dark>
    <NavbarBrand href="/">
      Svelte To-Do Application
      {#if loading}
        <Spinner size="sm" type="grow" />
      {/if}
    </NavbarBrand>

  </Navbar>
  <div class="cont">
    <center>
      <Row>
        <Col xs="12">

          <input class="somemargin" type="text" bind:value={name} />
        </Col>
        <Col xs="12">
          <input class="somemargin" type="date" bind:value={date} />
        </Col>
        <Col xs="12">
          <Button block color="primary" on:click={addTodo}>Add To-Do</Button>
        </Col>
      </Row>
    </center>
  </div>
  <div class="cont">
    <center>
      <Row>
        <Col>
          <ListGroup>
            {#each todoList as td, i}
              <ListGroupItem>
                {td.name}
                <Badge color="dark">{td.date}</Badge>
                <el class="buttongroup">
                  <Button on:click={() => finTodo(i)}>
                    <ion-icon name="checkmark-circle-outline" />
                  </Button>
                  <Button on:click={() => deleteTodo(i)}>
                    <ion-icon name="trash" />
                  </Button>
                </el>
              </ListGroupItem>
            {/each}
            {#each doneList as d, di}
              <ListGroupItem color="danger">
                <span class="done">{d.name}</span>
                <Badge color="light">{d.date}</Badge>
                <el class="buttongroup">
                  <Button on:click={() => unfinTodo(di)}>
                    <ion-icon name="checkmark-circle-outline" />
                  </Button>
                  <Button on:click={() => deleteDoneTodo(di)}>
                    <ion-icon name="trash" />
                  </Button>
                </el>
              </ListGroupItem>
            {/each}
          </ListGroup>
        </Col>
      </Row>
    </center>
  </div>
  <script src="https://unpkg.com/ionicons@4.5.10-0/dist/ionicons.js">

  </script>
</main>
