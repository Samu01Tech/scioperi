<script>
  import IoMdTrain from "svelte-icons/io/IoMdTrain.svelte";
  import IoMdBus from "svelte-icons/io/IoMdBus.svelte";
  import IoMdAirplane from "svelte-icons/io/IoMdAirplane.svelte";
  import IoMdWarning from "svelte-icons/io/IoMdWarning.svelte";
  import IoMdBoat from "svelte-icons/io/IoMdBoat.svelte";
  import IoMdCloseCircle from "svelte-icons/io/IoMdCloseCircle.svelte";
  // async function getUsers() {
  //   let response = await fetch(
  //     "https://api.rss2json.com/v1/api.json?rss_url=http%3A%2F%2Fscioperi.mit.gov.it%2Fmit2%2Fpublic%2Fscioperi%2Frss"
  //   );
  //   let list = await response.json();
  //   console.log(list);
  //   return list.items;
  // }
  const promise = getStrikes();

  async function getStrikes() {
    const data = await fetch(
      `https://api.rss2json.com/v1/api.json?rss_url=http%3A%2F%2Fscioperi.mit.gov.it%2Fmit2%2Fpublic%2Fscioperi%2Frss`
    ).then((r) => r.json());

    console.log(data);

    const strikes = parseStrikes(data.items);

    const sectors = new Set();
    const regions = new Set();
    const provinces = new Set();
    const modes = new Set();

    strikes.forEach((strike) => {
      sectors.add(strike.sector);
      regions.add(strike.region);
      provinces.add(strike.province);
      modes.add(strike.mode);
    });

    return {
      strikes,
      sectors: [...sectors].sort(),
      regions: [...regions].sort(),
      provinces: [...provinces].sort(),
      modes: [...modes].sort(),
    };
  }

  function parseStrikes(strikes) {
    const parsedStrikes = [];
    strikes.forEach((strike) => {
      parsedStrikes.push({
        id: parseInt(strike["guid"].split("/")[3].trim()),
        startDate: strike["title"]
          .split(" ")[2]
          .trim()
          .split("/")
          .reverse()
          .join("-"),
        endDate: strike["description"]
          .split("<br>")[1]
          .split(":")[1]
          .trim()
          .split("/")
          .reverse()
          .join("-"),
        sector: strike["description"].split("<br>")[2].split(":")[1].trim(),
        relevance: strike["description"].split("<br>")[3].split(":")[1].trim(),
        region: strike["description"].split("<br>")[4].split(":")[1].trim(),
        province: strike["description"].split("<br>")[5].split(":")[1].trim(),
        mode: strike["description"]
          .split("<br>")[0]
          .split(":")[1]
          .trim()
          .toLowerCase(),
        categorySyn: strike["description"]
          .split("<br>")[6]
          .split(":")[1]
          .trim(),
        categoryPer: strike["description"]
          .split("<br>")[7]
          .split(":")[1]
          .trim(),
        link: strike["guid"].trim(),
      });
    });
    console.log(parsedStrikes);
    return parsedStrikes;
  }
</script>

<svelte:head>
  <!-- Primary Meta Tags -->
  <title>Scioperi Italia</title>
  <meta name="title" content="Scioperi Italia" />
  <meta
    name="description"
    content="Rimani aggiornato sugli scioperi in programma nella penisola."
  />
  <meta
    name="keywords"
    content="sciopero, scioperi, treni, ritardo, italia, ministero dei trasporti"
  />
  <meta name="robots" content="index, follow" />
  <meta name="copyright" content="@Samu01Tech" />
  <meta name="language" content="IT" />
  <meta name="author" content="Samuele" />
  <meta name="creationdate" content="04/09/2021" />
  <meta name="distribution" content="local" />
  <meta name="rating" content="general" />

  <!-- Open Graph / Facebook -->
  <meta property="og:type" content="website" />
  <meta property="og:url" content="https://scioperi-italia.netlify.app/" />
  <meta property="og:title" content="Scioperi Italia" />
  <meta
    property="og:description"
    content="Rimani aggiornato sugli scioperi in programma nella penisola."
  />
  <meta property="og:image" content="/favicon.png" />

  <!-- Twitter -->
  <meta property="twitter:card" content="summary_large_image" />
  <meta property="twitter:url" content="https://scioperi-italia.netlify.app/" />
  <meta property="twitter:title" content="Scioperi Italia" />
  <meta
    property="twitter:description"
    content="Rimani aggiornato sugli scioperi in programma nella penisola."
  />
  <meta property="twitter:image" content="./favicon.png" />

  <link rel="icon" type="image/png" href="./favicon.png" />
</svelte:head>

<section class="hero is-primary">
  <div class="hero-body">
    <p class="title">Scioperi</p>
  </div>
</section>
<section class="section">
  <div class="container">
    {#await promise}
      <div class="grid">
        <div class="button is-loading is-large is-primary is-rounded" />
      </div>
    {:then list}
      {#each list.strikes as item}
        <div class="box">
          <div class="columns is-vcentered has-text-centered-touch">
            <div class="column is-two-thirds">
              <div class="notification">
                <div class="columns">
                  <div class="column is-narrow">
                    <!--  icona tipo sciopero -->
                    {#if item.sector == "Ferroviario"}
                      <span class="icon-text">
                        <span class="icon is-large has-text-success">
                          <i class=""><IoMdTrain /></i>
                        </span>
                      </span>
                    {:else if item.sector == "Trasporto pubblico locale"}
                      <span class="icon-text">
                        <span class="icon is-large has-text-info">
                          <i class=""><IoMdBus /></i>
                        </span>
                      </span>
                    {:else if item.sector == "Aereo"}
                      <span class="icon-text">
                        <span class="icon is-large has-text-warning">
                          <i class=""><IoMdAirplane /></i>
                        </span>
                      </span>
                    {:else if item.sector == "Generale"}
                      <span class="icon-text">
                        <span class="icon is-large has-text-danger">
                          <i class=""><IoMdWarning /></i>
                        </span>
                      </span>
                    {:else if item.sector == "Marittimo"}
                      <span class="icon-text">
                        <span class="icon is-large has-text-info">
                          <i class=""><IoMdBoat /></i>
                        </span>
                      </span>
                    {:else}
                      <span class="icon-text">
                        <span class="icon is-large has-text-danger">
                          <i class=""><IoMdCloseCircle /></i>
                        </span>
                      </span>
                    {/if}
                  </div>
                  <div class="column">
                    <!-- tipo sciopero -->
                    <div class="title">
                      {item.sector}
                    </div>
                  </div>
                </div>
                <!-- data inizio -->
                {#if item.startDate != undefined}
                  <div class="subtitle is-lowercase ">
                    <!-- ore -->
                    {"Dal " + item.startDate + " al " + item.endDate}
                  </div>
                {/if}
                <div class="subtitle">{item.mode}</div>
              </div>
            </div>
            <div class="column has-text-centered">
              <div class="subtitle"><b>Rilevanza</b>: {item.relevance}</div>
              <!-- Rilevanza -->
              <div class="subtitle"><b>Regioni</b>: {item.region}</div>
              <!-- Regione -->
              <div class="subtitle"><b>Province</b>: {item.province}</div>
              <!-- Provincia -->
            </div>
          </div>
          <hr class="is-hidden-desktop" />
          <div class="content">
            <div class="is-size-6">{item.categoryPer.toLowerCase()}</div>
            <!-- Data fine -->
            <div class="is-size-6">{item.categorySyn}</div>
            <!-- Dettagli -->
          </div>
        </div>
      {/each}
    {:catch error}
      <div class="columns is-centered has-text-centered">
        <div class="column is-half">
          <img src="error.gif" alt="Error" />
          <h4 class="is-size-4">Oh no. Qualcosa è andato storto</h4>
          <p style="box is-danger">{error.message}</p>
        </div>
      </div>
    {/await}
  </div>
</section>

<style lang="scss">
  @import "https://cdn.jsdelivr.net/npm/bulma@0.9.2/css/bulma.min.css";
  .grid {
    display: grid;
    place-items: center;
  }
</style>
