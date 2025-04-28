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
 
let handlekurv = [];
let visBetalingsform = false;
let erStudent = false;
let kundeInfo = {
  navn: "",
  adresse: "",
  postnummer: "",
  poststed: "",
  erStudent: false
};
 
let cartCount = 0;
 
$: totalPris = beregnTotal();
 
function updateCartCount() {
  try {
    const cart = JSON.parse(localStorage.getItem("valgteProdukter")) || [];
    cartCount = cart.length;
  } catch (error) {
    console.error("Feil ved lasting av handlekurv:", error);
    cartCount = 0;
  }
}
 
onMount(() => {
  try {
    const lagretData = localStorage.getItem("valgteProdukter");
    handlekurv = lagretData ? JSON.parse(lagretData) : [];
  } catch (error) {
    console.error("Feil ved parsing av handlekurv-data:", error);
    localStorage.removeItem("valgteProdukter");
    handlekurv = [];
  }
  updateCartCount();
  window.addEventListener('storage', updateCartCount);
  return () => window.removeEventListener('storage', updateCartCount);
});
 
function fjernProdukt(index) {
  handlekurv.splice(index, 1);
  handlekurv = [...handlekurv];
  localStorage.setItem("valgteProdukter", JSON.stringify(handlekurv));
  updateCartCount();
}
 
function beregnTotal() {
  let total = handlekurv.reduce((sum, produkt) => sum + produkt.pris, 0);
  if (erStudent) {
    total = total - ( total * 0.1);
  }
  return Math.round(total);
}
 
$: if (erStudent !== undefined) {
  totalPris = beregnTotal();
}
 
function lagreOrdre() {
  const ordre = {
    kundeInfo: {
      ...kundeInfo,
      erStudent
    },
    produkter: handlekurv,
    totalPris: beregnTotal(),
    dato: new Date().toISOString()
  };
 
  let ordreHistorikk = JSON.parse(localStorage.getItem("ordreHistorikk") || "[]");
  ordreHistorikk.push(ordre);
  localStorage.setItem("ordreHistorikk", JSON.stringify(ordreHistorikk));
 
  handlekurv = [];
  localStorage.removeItem("valgteProdukter");
  visBetalingsform = false;
  alert("Takk for din bestilling!");
}
</script>
 
<div class="handlekurv-container">
  <h1>Din handlekorg</h1>
 
  {#if handlekurv.length === 0}
      <p>Handlekorga er tom</p>
  {:else}
      <div class="produkter">
          {#each handlekurv as produkt, index}
              <div class="produkt-kort">
                  <img src={produkt.bilde} alt={produkt.varenamn}>
                  <div class="produkt-info">
                      <h3>{produkt.varenamn}</h3>
                      <p>Pris: {produkt.pris} kr</p>
                      <button on:click={() => fjernProdukt(index)}>Fjern</button>
                  </div>
              </div>
          {/each}
      </div>
     
      <div class="total">
          <div class="student-rabatt">
              <label>
                  <input type="checkbox" bind:checked={erStudent}>
                  Er du student? (10% rabatt)
              </label>
          </div>
          <h2>Totalt: {totalPris} kr {erStudent ? '(inkl. studentrabatt)' : ''}</h2>
          {#if !visBetalingsform}
              <button class="checkout-btn" on:click={() => visBetalingsform = true}>Gå til betaling</button>
          {/if}
      </div>
 
      {#if visBetalingsform}
          <div class="betalingsform">
              <h2>Leveringsinformasjon</h2>
              <form on:submit|preventDefault={lagreOrdre}>
                  <div class="form-gruppe">
                      <label for="navn">Navn:</label>
                      <input type="text" id="navn" bind:value={kundeInfo.navn} required>
                  </div>
                  <div class="form-gruppe">
                      <label for="adresse">Adresse:</label>
                      <input type="text" id="adresse" bind:value={kundeInfo.adresse} required>
                  </div>
                  <div class="form-gruppe">
                      <label for="postnummer">Postnummer:</label>
                      <input type="text" id="postnummer" bind:value={kundeInfo.postnummer} required>
                  </div>
                  <div class="form-gruppe">
                      <label for="poststed">Poststad:</label>
                      <input type="text" id="poststed" bind:value={kundeInfo.poststed} required>
                  </div>
                  <button type="submit" class="bestill-btn">Bestill</button>
              </form>
          </div>
      {/if}
  {/if}
</div>
<footer>
  <section id="høgresidetekst">
      <p>friluft@gmail.com</p>
      <p> 2025</p>
  </section>
  <section id="venstresidetekst">
     
  </section>
</footer>
 
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
 
  .handlekurv-container {
      margin-top: 8rem;
      padding: 2rem;
      max-width: 1200px;
      margin-left: auto;
      margin-right: auto;
      font-family: 'quicksand', sans-serif;
  }
 
  .produkter {
      display: grid;
      gap: 2rem;
      margin-top: 2rem;
      font-family: 'quicksand', sans-serif;
  }
 
  .produkt-kort {
    display: flex;
    background-color: #ebebe3;
    padding: 1rem;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    font-family: 'quicksand', sans-serif;
}
 
  .produkt-kort img {
      width: 100px;
      height: 100px;
      object-fit: cover;
      border-radius: 4px;
  }
 
  .produkt-info {
      margin-left: 1rem;
      flex-grow: 1;
  }
 
  .produkt-info h3 {
      margin: 0;
      font-size: 1.2rem;
  }
 
  button {
      background-color: rgb(47, 60, 41);
      color: white;
      border: none;
      padding: 0.5rem 1rem;
      border-radius: 4px;
      cursor: pointer;
      margin-top: 0.5rem;
      font-family: 'quicksand', sans-serif;
  }
 
  button:hover {
      background-color: rgb(67, 80, 61);
  }
 
  .total {
      margin-top: 2rem;
      text-align: right;
      padding: 1rem;
      background-color: #ebebe3;
      border-radius: 8px;
      border: 0.3rem solid rgb(47, 60, 41);
      font-family: 'quicksand', sans-serif;
  }
 
  .checkout-btn {
      font-size: 1.1rem;
      padding: 0.75rem 1.5rem;
  }
 
  .betalingsform {
      background-color: #ebebe3;
      padding: 2rem;
      border-radius: 8px;
      border: 0.3rem solid rgb(47, 60, 41);
      margin-top: 2rem;
      font-family: 'quicksand', sans-serif;
  }
 
  .form-gruppe {
      margin-bottom: 1rem;
  }
 
  .form-gruppe label {
      display: block;
      margin-bottom: 0.5rem;
      font-weight: bold;
      font-family: 'quicksand', sans-serif;
  }
 
  .form-gruppe input {
      width: 100%;
      padding: 0.5rem;
      border: 1px solid rgb(47, 60, 41);
      border-radius: 4px;
  }
 
  .bestill-btn {
      background-color: rgb(47, 60, 41);
      color: white;
      padding: 1rem 2rem;
      border: none;
      border-radius: 4px;
      font-size: 1.1rem;
      cursor: pointer;
      margin-top: 1rem;
      font-family: 'quicksand', sans-serif;
  }
 
  .bestill-btn:hover {
      background-color: rgb(67, 80, 61);
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
 
  .student-rabatt {
    margin-bottom: 1rem;
    padding: 0.8rem;
    background-color: rgb(216, 180, 109, 0.15);
    border-radius: 0.5rem;
    font-family: 'quicksand', sans-serif;
  }
 
  .student-rabatt label {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 1rem;
    color: rgb(47, 60, 41);
    cursor: pointer;
    font-family: 'quicksand', sans-serif;
  }
 
  .student-rabatt input[type="checkbox"] {
    width: 1.2rem;
    height: 1.2rem;
    cursor: pointer;
    accent-color: rgb(47, 60, 41);
  }
 
  h1, h2, h3, p, span, button, label {
    font-family: 'quicksand', sans-serif;
  }
 
  footer a.buttonf img,
  footer a.buttoni img,
  footer a.buttont img {
    width: 3rem;
    height: auto;
    display: inline-block;
    padding: 0;
    border-radius: 0.6rem;
  }
 
  footer .høgresidetekst {
    text-align: right;
    margin: 0;
  }
 
  footer .venstresidetekst {
    text-align: left;
    margin: 0;
  }
 
  footer {
    background-color: rgb(47, 60, 41);
    color: rgb(255, 255, 255);
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem;
  }
</style>