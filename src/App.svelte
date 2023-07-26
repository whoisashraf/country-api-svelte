<script lang="ts">
  import { afterUpdate } from "svelte";
  let Theme = "light";
  let searchedCountry = "";
  let countriesData = [];
  let countries = [];
  async function fetchData(url: string) {
    const data = await fetch(url);
    countries = await data.json();
    countries.sort((a, b) => {
      if (a.name.common === b.name.common) {
        return 0;
      } else {
        return a.name.common < b.name.common ? -1 : 1;
      }
    });
    countriesData = countries;
  }
  fetchData("https://restcountries.com/v3.1/all");
  function handleSearch() {
    countries = countries.filter((c) => {
      return c.name.common
        .toLowerCase()
        .includes(searchedCountry.toLowerCase());
    });
  }
  function handleFilter(event) {
    countries = countries.filter((c) => {
      return (
        c.region.toLowerCase() === event.target.firstChild.data.toLowerCase()
      );
    });
    if (event.target.firstChild.data === "All") {
      countries = countriesData;
    }
  }
  $: if (searchedCountry === "") {
    countries = countriesData;
  }

  let popUpData = [];
  let showPopUp = false;
  function popUp(c) {
    popUpData = c;
    showPopUp = true;
  }

  afterUpdate(() => {
    // Set the theme to the body tag
    document.body.setAttribute("data-bs-theme", Theme);
  });
</script>

<svelte:head>
  <link
    rel="stylesheet"
    href="	https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css"
  />
  <script
    defer
    src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"
    integrity="sha384-geWF76RCwLtnZ8qwWowPQNguL3RmwHVBC9FhGdlKrxdiJJigb/j/68SIy3Te4Bkz"
    crossorigin="anonymous"
  ></script>
</svelte:head>

<svelte:body data-bs-theme={Theme} />
<section>
  <nav class="navbar bg-secondary-subtle sticky-top">
    <div class="container-fluid m-2">
      <p class="h5">Where in the World?</p>
      {#if Theme === "light"}
        <button class="h6 btn" on:click={() => (Theme = "dark")}>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-moon pe-1"
            viewBox="0 0 16 16"
          >
            <path
              d="M6 .278a.768.768 0 0 1 .08.858 7.208 7.208 0 0 0-.878 3.46c0 4.021 3.278 7.277 7.318 7.277.527 0 1.04-.055 1.533-.16a.787.787 0 0 1 .81.316.733.733 0 0 1-.031.893A8.349 8.349 0 0 1 8.344 16C3.734 16 0 12.286 0 7.71 0 4.266 2.114 1.312 5.124.06A.752.752 0 0 1 6 .278zM4.858 1.311A7.269 7.269 0 0 0 1.025 7.71c0 4.02 3.279 7.276 7.319 7.276a7.316 7.316 0 0 0 5.205-2.162c-.337.042-.68.063-1.029.063-4.61 0-8.343-3.714-8.343-8.29 0-1.167.242-2.278.681-3.286z"
            /></svg
          >Dark Mode
        </button>
      {:else}
        <button class="h6 btn" on:click={() => (Theme = "light")}>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-brightness-high"
            viewBox="0 0 16 16"
          >
            <path
              d="M8 11a3 3 0 1 1 0-6 3 3 0 0 1 0 6zm0 1a4 4 0 1 0 0-8 4 4 0 0 0 0 8zM8 0a.5.5 0 0 1 .5.5v2a.5.5 0 0 1-1 0v-2A.5.5 0 0 1 8 0zm0 13a.5.5 0 0 1 .5.5v2a.5.5 0 0 1-1 0v-2A.5.5 0 0 1 8 13zm8-5a.5.5 0 0 1-.5.5h-2a.5.5 0 0 1 0-1h2a.5.5 0 0 1 .5.5zM3 8a.5.5 0 0 1-.5.5h-2a.5.5 0 0 1 0-1h2A.5.5 0 0 1 3 8zm10.657-5.657a.5.5 0 0 1 0 .707l-1.414 1.415a.5.5 0 1 1-.707-.708l1.414-1.414a.5.5 0 0 1 .707 0zm-9.193 9.193a.5.5 0 0 1 0 .707L3.05 13.657a.5.5 0 0 1-.707-.707l1.414-1.414a.5.5 0 0 1 .707 0zm9.193 2.121a.5.5 0 0 1-.707 0l-1.414-1.414a.5.5 0 0 1 .707-.707l1.414 1.414a.5.5 0 0 1 0 .707zM4.464 4.465a.5.5 0 0 1-.707 0L2.343 3.05a.5.5 0 1 1 .707-.707l1.414 1.414a.5.5 0 0 1 0 .708z"
            />
          </svg> Light Mode
        </button>
      {/if}
    </div>
  </nav>

  <div class="container mt-3">
    <form
      class="d-flex justify-content-between"
      role="search"
      on:submit|preventDefault
    >
      <input
        class="form-control me-2 w-25"
        type="search"
        id="search"
        placeholder="Search"
        aria-label="Search"
        bind:value={searchedCountry}
        on:keyup={handleSearch}
      />
      <div class="dropdown">
        <button
          class="btn bg-secondary-subtle dropdown-toggle"
          type="button"
          data-bs-toggle="dropdown"
          aria-expanded="false"
        >
          Filter by region
        </button>
        <ul class="dropdown-menu mt-2">
          <li>
            <a class="dropdown-item filter" on:click={handleFilter}>All</a>
          </li>
          <li>
            <a class="dropdown-item filter" on:click={handleFilter}>Africa</a>
          </li>
          <li>
            <a class="dropdown-item filter" on:click={handleFilter}>Americas</a>
          </li>
          <li>
            <a class="dropdown-item filter" on:click={handleFilter}>Asia</a>
          </li>
          <li>
            <a class="dropdown-item filter" on:click={handleFilter}>Europe</a>
          </li>
          <li>
            <a class="dropdown-item filter" on:click={handleFilter}>Oceania</a>
          </li>
        </ul>
      </div>
    </form>
  </div>

  <div class="container">
    <div class="row g-5 py-5 row-cols-1 row-cols-md-2 row-cols-lg-3">
      {#each countries as country}
        <div class="feature col" on:click={popUp(country)}>
          <div class="card mx-auto" style="width: 20rem;">
            <img
              src={country.flags.png}
              alt="${country.flags.alt}"
              class="card-img-top"
              width="300"
              height="200"
            />
            <div class="card-body">
              <p class="card-text">{country.name.common}</p>
              <p class="card-text">Population: {country.population}</p>
              <p class="card-text">Region: {country.region}</p>
              <p class="card-text">Capital: {country.capital}</p>
            </div>
          </div>
        </div>
      {/each}
    </div>
  </div>

  {#if showPopUp}
    <div class="container-fluid fixed-top top-0 w-100 h-100 card">
      <div class="position-relative mt-3">
        <button
          class=" btn bg-secondary-subtle my-5 h6"
          on:click={() => (showPopUp = false)}>Back</button
        >
        <div class="container row row-cols-1  row-cols-md-2">
          <img
            src={popUpData.flags.png}
            alt={popUpData.flags.alt}
            class="img-contain"
          />
          <div class="">
            <div class="card-body">
              <h3 class="h3 card-text">{popUpData.name.common}</h3>
              <p class="card-text h5">Native Name: {popUpData.name.common}</p>
              <p class="card-text h5">Population: {popUpData.population}</p>
              <p class="card-text h5">Region: {popUpData.region}</p>
              <p class="card-text h5">Sub-region: {popUpData.subregion}</p>
              <p class="card-text h5">Capital: {popUpData.capital}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  {/if}
</section>
