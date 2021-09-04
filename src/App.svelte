<script>
  import IoMdTrain from "svelte-icons/io/IoMdTrain.svelte";
  import IoMdBus from "svelte-icons/io/IoMdBus.svelte";
  import IoMdAirplane from "svelte-icons/io/IoMdAirplane.svelte";
  import IoMdWarning from "svelte-icons/io/IoMdWarning.svelte";
  import IoMdBoat from "svelte-icons/io/IoMdBoat.svelte";
  import IoMdCloseCircle from "svelte-icons/io/IoMdCloseCircle.svelte";
  async function getUsers() {
    let response = await fetch(
      "https://api.factmaven.com/xml-to-json/?xml=http://scioperi.mit.gov.it/mit2/public/scioperi/rss"
    );
    let list = await response.json();
    return list.rss.channel.item;
  }
  const promise = getUsers();
</script>

<section class="hero is-primary">
  <div class="hero-body">
    <p class="title">Scioperi</p>
  </div>
</section>
<section class="section">
  <div class="container">
    <!-- <div class="tags are-large">
      <span class="tag is-rounded is-primary">Tutti</span>
      <span class="tag is-rounded">Generale</span>
      <span class="tag is-rounded">Ferroviario</span>
      <span class="tag is-rounded">Trasporto pubblico locale</span>
      <span class="tag is-rounded">Aereo</span>
      <span class="tag is-rounded">Marittimo</span>
      <span class="tag is-rounded">Elicotteri</span>
      <span class="tag is-rounded">Altro</span>
    </div> -->
    {#await promise}
    <div class="grid">
      <div class="button is-loading is-large is-primary is-rounded">    </div>
    </div>
    {:then list}
      {#each list as item}
        <div class="box">
          <div class="columns is-vcentered has-text-centered-touch">
            <div class="column is-two-thirds">
              <div class="notification">
                <div class="columns">
                  <div class="column is-narrow"><!--  icona tipo sciopero -->
                    {#if item.title.split("-")[1] == " Settore: Ferroviario "}
                      <span class="icon-text">
                        <span class="icon is-large has-text-success">
                          <i class=""><IoMdTrain /></i>
                        </span>
                      </span>
                    {:else if item.title.split("-")[1] == " Settore: Trasporto pubblico locale "}
                      <span class="icon-text">
                        <span class="icon is-large has-text-info">
                          <i class=""><IoMdBus /></i>
                        </span>
                      </span>
                    {:else if item.title.split("-")[1] == " Settore: Aereo "}
                      <span class="icon-text">
                        <span class="icon is-large has-text-warning">
                          <i class=""><IoMdAirplane /></i>
                        </span>
                      </span>
                    {:else if item.title.split("-")[1] == " Settore: Generale "}
                      <span class="icon-text">
                        <span class="icon is-large has-text-danger">
                          <i class=""><IoMdWarning /></i>
                        </span>
                      </span>
                    {:else if item.title.split("-")[1] == " Settore: Marittimo "}
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
                  <div class="column"><!-- tipo sciopero -->
                    <div class="title">
                      {item.title.split("-")[1]}
                    </div>
                  </div>
                </div>
                <div class="title">{item.title.split("-")[0]}</div><!-- data inizio -->
                {#if item.description
                  .split("<br/>")[0]
                  .split(":")[2] == undefined}{:else}
                  <div class="subtitle is-lowercase "><!-- ore -->
                    {item.description.split("<br/>")[0].split(":")[2]}
                    
                  </div>
                {/if}
              </div>
            </div>
            <div class="column has-text-centered">
              <div class="subtitle">{item.title.split("-")[2]}</div><!-- Rilevanza -->
              <div class="subtitle">{item.title.split("-")[3]}</div><!-- Regione -->
              <div class="subtitle">{item.title.split(" -")[4]}</div><!-- Provincia -->
            </div>
          </div>
          <hr class="is-hidden-desktop" />
          <div class="content">
            <div class="is-size-6">{item.description.split("<br/>")[1]}</div><!-- Data fine -->
            <div class="is-size-6">{item.description.split("<br/>")[7]}</div><!-- Dettagli -->
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
  .grid{
    display: grid;
    place-items: center;
  }
</style>
