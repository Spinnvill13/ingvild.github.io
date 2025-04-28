<div class="top-banner">
  <span> 10% studentrabatt</span>
  <span> Vårutsal</span>
  <span> Gratis levering</span>
</div>
<header>
  <a href="/prosjektfagdag/framside" class="testheader">
      <img src="/bilderprosjekt/logobaal.png" alt="Logo" class="header-logo">
  </a>
  <nav class="headernav">
    <ul>
      <li><a href="/prosjektfagdag/produkt"> <img src="/bilderprosjekt/telt.png" alt="Produkter"> Produkt</a></li>
      <li>
        <a href="/prosjektfagdag/handlekorg" class="cart-link">
          <img src="/bilderprosjekt/handlekorg.png" alt="Handlekorg">
          Handlekorg
          {#if cartCount > 0}
            <span class="cart-badge">{cartCount}</span>
          {/if}
        </a>
      </li>
      <li><a href="/prosjektfagdag/profil"> <img src="/bilderprosjekt/person.png" alt="Profil"> Din profil</a></li>
    </ul>
  </nav>
</header>
<script>
 
  import { onMount } from "svelte";
 
  let ordreHistorikk = [];
  let person = [];
  let fornamn = "";
  let etternamn = "";
  let telefon = "";
  let godkjent = false;
  let showVilkår = false;
  let visOrdrer = false;
  let cartCount = 0;
 
  onMount(() => {
    try {
      const lagretHistorikk = localStorage.getItem("ordreHistorikk");
      ordreHistorikk = lagretHistorikk ? JSON.parse(lagretHistorikk) : [];
      const lagretPerson = localStorage.getItem("person");
      person = lagretPerson ? JSON.parse(lagretPerson) : [];
      updateCartCount();
    } catch (error) {
      console.error("Feil ved lasting av data:", error);
      ordreHistorikk = [];
      person = [];
    }
  });
 
  function formaterDato(datoString) {
    const dato = new Date(datoString);
    return dato.toLocaleString('no-NO', {
      day: '2-digit',
      month: '2-digit',
      year: 'numeric',
      hour: '2-digit',
      minute: '2-digit'
    });
  }
 
  function toggleVilkår() {
    showVilkår = !showVilkår;
  }
 
  function registrerperson() {
    const nyPerson = {
      fornamn,
      etternamn,
      telefon
    };
    person = [nyPerson];
    localStorage.setItem("person", JSON.stringify(person));
    visOrdrer = true;
  }
 
  function loggUt() {
    person = [];
    localStorage.removeItem("person");
    visOrdrer = false;
  }
  function updateCartCount() {
    try {
      const cart = JSON.parse(localStorage.getItem("valgteProdukter")) || [];
      cartCount = cart.length;
    } catch (error) {
      console.error("Feil ved lasting av handlekurv:", error);
      cartCount = 0;
    }
  }
</script>
 
 
<div class="profil-container">
  <h1>Din Profil</h1>
 
  {#if person.length === 0}
    <div class="registrering">
      <h2>Registrer deg</h2>
      <p>Fornavn:</p>
      <input bind:value={fornamn} placeholder="Fornamn">
      <p>Etternavn:</p>
      <input bind:value={etternamn} placeholder="Etternamn">
      <p>Telefon:</p>
      <input type="number" bind:value={telefon} placeholder="Telefon">
      <br><br>
      <label>
        <input type="checkbox" bind:checked={godkjent}>
        Eg godkjenner <button type="button" on:click={toggleVilkår} class="link-button">personvernsærkleringa</button>
      </label>
      {#if showVilkår}
        <section class="vilkår-info">
          <h2>Vilkår</h2>
        <p>      
          Vilkår og personvern
          Vi tek personvernet ditt på alvor.
         
          Når du opprettar ein profil på nettstaden vår, samtykkjer du til at vi kan lagre nødvendige personopplysningar som namn, telefonnummer, adresse og kjøpshistorikk. <br>
          Desse opplysningane vert brukt for å forbetre opplevinga di.
         
          Du har rett til innsyn i, og sletting av, dine personopplysningar. Dersom du ønskjer å få sletta eller endra informasjonen vi har lagra om deg, kan du ta kontakt med oss når som helst.
         
          Vi deler ikkje personopplysningar med tredjepartar utan at du har gitt tydeleg samtykke. All behandling av data skjer i samsvar med personvernforordninga (GDPR), og vi jobbar kontinuerleg for å sikre at opplysningane dine er trygge hos oss.
         
          Ved å godkjenne desse vilkåra, samtykkjer du til at vi kan behandle personopplysningane dine som beskreve ovanfor. <br>
          Du kan når som helst trekke tilbake samtykket ditt ved å kontakte oss – då slettar vi all informasjon vi har lagra om deg.
         
          Vi vil informere deg dersom det skjer vesentlege endringar i personvernvilkåra våre.
         
         
        </p>
                       
             
        </section>
      {/if}
      <br><br>
      <button on:click={registrerperson} disabled={!godkjent}>
          Registrer person
      </button>
    </div>
  {:else}
    <div class="bruker-info">
      <p>Innlogga som {person[0].fornamn} {person[0].etternamn}</p>
      <button on:click={loggUt}>Logg ut</button>
    </div>
  {/if}
 
  {#if visOrdrer && person.length > 0}
    <div class="ordre-historikk">
      <h2>Dine bestillinga</h2>
     
      {#if ordreHistorikk.length === 0}
        <p>Du har ingen tidligare bestillinga</p>
      {:else}
        {#each ordreHistorikk.slice().reverse() as ordre}
          <div class="ordre-kort">
            <div class="ordre-header">
              <h3>Bestilling - {formaterDato(ordre.dato)}</h3>
              <p class="total-pris">Total: {ordre.totalPris} kr</p>
            </div>
           
            <div class="kunde-info">
              <h4>Leveringsadresse:</h4>
              <p>{ordre.kundeInfo.navn}</p>
              <p>{ordre.kundeInfo.adresse}</p>
              <p>{ordre.kundeInfo.postnummer} {ordre.kundeInfo.poststed}</p>
              <p>{ordre.kundeInfo.erStudent ? 'Student' : ''}</p>
            </div>
           
            <h4>Produkt:</h4>
            <ul>
              {#each ordre.produkter as produkt}
                <li class="produkt-item">
                  <img src={produkt.bilde} alt={produkt.varenamn}>
                  <div class="produkt-info">
                    <span class="produkt-navn">{produkt.varenamn}</span>
                    <span class="produkt-pris">{produkt.pris} kr</span>
                  </div>
                </li>
              {/each}
            </ul>
          </div>
        {/each}
      {/if}
    </div>
  {/if}
</div>
 
<style>
  .top-banner {
  background-color: #113c1b;
  color: white;
  font-size: 0.9rem;
  display: flex;
  justify-content: center;
  gap: 2rem;
  padding: 0.5rem;
  font-family: 'quicksand', sans-serif;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1001;
}
 
header {
  top: 2.5rem;
}
header {
  background-color: rgb(255, 255, 255);
  backdrop-filter: blur(5px);
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: rgb(255, 255, 255);
  font-size: 1.3rem;
  padding: 1rem;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1000;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
  border-bottom: 1px solid rgba(0, 0, 0, 0.05);
}
 
.header-logo {
  width: 8rem;
  height: auto;
}
 
nav ul {
  margin: 0;
  padding: 0;
  display: flex;
  list-style-type: none;
}
 
nav li {
  margin: 1.2rem;
  display: flex;
  align-items: center;
}
 
nav a {
  text-decoration: none;
  color: rgb(47, 60, 41);
  white-space: nowrap;
  font-family: 'quicksand', sans-serif;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}
 
nav a img {
  width: 1.5rem;
  height: 1.5rem;
  object-fit: contain;
}
 
.cart-link {
  position: relative;
}
 
.cart-badge {
  position: absolute;
  top: -0.5rem;
  right: -0.5rem;
  background-color: rgb(216, 180, 109);
  color: rgb(47, 60, 41);
  border-radius: 50%;
  width: 1.2rem;
  height: 1.2rem;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.8rem;
  font-weight: bold;
}
 
  .profil-container {
    margin-top: 8rem;
    padding: 2rem;
    max-width: 1200px;
    margin-left: auto;
    margin-right: auto;
    font-family: 'quicksand', sans-serif;
  }
 
  .registrering,
  .bruker-info,
  .vilkår-info,
  .ordre-kort {
    background-color: #f7f7f2;
    border-radius: 1rem;
    box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.1);
    border: none;
    padding: 2rem;
    margin-bottom: 2rem;
    font-family: 'quicksand', sans-serif;
  }
 
  .registrering input[type="text"], .registrering input[type="number"] {
    width: 100%;
    padding: 0.5rem;
    margin-bottom: 1rem;
    border: 1px solid rgb(47, 60, 41);
    border-radius: 4px;
  }
 
  .ordre-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1.5rem;
    padding-bottom: 1rem;
    border-bottom: 2px solid rgb(47, 60, 41);
  }
 
  .ordre-header h3 {
    margin: 0;
    color: rgb(47, 60, 41);
  }
 
  .total-pris {
    font-size: 1.2rem;
    font-weight: bold;
    color: rgb(47, 60, 41);
  }
 
  .kunde-info {
    margin-bottom: 2rem;
  }
 
  .kunde-info h4 {
    color: rgb(47, 60, 41);
    margin-bottom: 0.5rem;
  }
 
  .kunde-info p {
    margin: 0.2rem 0;
  }
 
  .produkt-item img {
    width: 60px;
    height: 60px;
    object-fit: cover;
    border-radius: 4px;
    margin-right: 1rem;
  }
 
  .produkt-info {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-grow: 1;
  }
 
  .produkt-navn {
    font-weight: bold;
  }
 
  .produkt-pris {
    color: rgb(47, 60, 41);
  }
 
  button {
    background-color: rgb(47, 60, 41);
    color: white;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.2s ease;
  }
 
  button:hover {
    background-color: rgb(67, 80, 61);
  }
 
  button:disabled {
    background-color: #cccccc;
    cursor: not-allowed;
  }
 
  h1, h2, h3, p, span, button, label {
    font-family: 'quicksand', sans-serif;
  }
</style>
 
 