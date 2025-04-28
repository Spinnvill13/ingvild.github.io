<div class="top-banner">
  <span> 10% studentrabatt</span>
  <span> Vår utsal</span>
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
import fil from "./produkt.json";
import { onMount } from 'svelte';
 
let produkter = fil;
let valgtKategori = "";
let cartCount = 0;
 
function visKategori(kategori) {
    valgtKategori = kategori;
}
 
function lagreData(event, produkt) {
  event.preventDefault();
  if (produkt.lagerantall > 0) {
    produkt.lagerantall -= 1;
 
    let handlekurv = [];
    try {
      handlekurv = JSON.parse(localStorage.getItem("valgteProdukter")) || [];
    } catch (error) {
      console.error("Feil ved parsing av localStorage:", error);
      localStorage.removeItem("valgteProdukter");
    }
 
    handlekurv.push({
      varenamn: produkt.varenamn,
      pris: produkt.pris,
      kategori: produkt.kategori,
      bilde: bildeForProdukt(produkt),
      lagerantall: produkt.lagerantall
    });
 
    localStorage.setItem("valgteProdukter", JSON.stringify(handlekurv));
   
   
    const oppdaterteProdukter = produkter.map(p =>
      p.varenamn === produkt.varenamn ? {...p, lagerantall: produkt.lagerantall} : p
    );
    localStorage.setItem("produkter", JSON.stringify(oppdaterteProdukter));
    produkter = oppdaterteProdukter;
   
    updateCartCount();
  } else {
    alert("Produktet er utsolgt!");
  }
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
 
onMount(() => {
 
  const lagredeProdukt = localStorage.getItem("produkter");
  if (lagredeProdukt) {
    produkter = JSON.parse(lagredeProdukt);
  } else {
   
    localStorage.setItem("produkter", JSON.stringify(produkter));
  }
 
  updateCartCount();
  window.addEventListener('storage', updateCartCount);
  return () => window.removeEventListener('storage', updateCartCount);
});
 
function bildeForProdukt(produkt) {
  return produkt.bilde || "/bilderprosjekt/default.jpg";
}
</script>
 
<h3>Velg en kategori:</h3>
 
<div class="kategori-container">
  <button class="kategori-knapp" on:click={() => visKategori("")}>
    <img src="/bilderprosjekt/alle.png" alt="Alle kategorier">
    Alle kategorier
  </button>
  <button class="kategori-knapp" on:click={() => visKategori("telt")}>
    <img src="/bilderprosjekt/teltikon.png" alt="Telt">
    Telt
  </button>
  <button class="kategori-knapp" on:click={() => visKategori("sko")}>
    <img src="/bilderprosjekt/skoikon.png" alt="Sko">
    Sko
  </button>
  <button class="kategori-knapp" on:click={() => visKategori("sykkel")}>
    <img src="/bilderprosjekt/sykkelikon.png" alt="Sykkel">
    Sykkel
  </button>
  <button class="kategori-knapp" on:click={() => visKategori("sovepose")}>
    <img src="/bilderprosjekt/soveposeikon.png" alt="Sovepose">
    Sovepose
  </button>
</div>
 
<h1>{valgtKategori ? valgtKategori.charAt(0).toUpperCase() + valgtKategori.slice(1) : "Alle produkter"}</h1>
 
<section class="Produkt">
  {#each produkter.filter(produkt => !valgtKategori || produkt.kategori.toLowerCase() === valgtKategori) as produkt}
  <div class="ni-kolonnar ei-rad">
    <img src={bildeForProdukt(produkt)} alt={produkt.varenamn}>
    <div class="produkt-info">
      <span>{produkt.varenamn}</span>
      <span>Pris: {produkt.pris} kr</span>
      <span>Antall: {produkt.lagerantall} stk</span>
    </div>
    <button on:click|preventDefault={(event) => lagreData(event, produkt)}>Legg til i handlekorg</button>
    <p>Bilete henta frå pixabey</p>
  </div>
  {/each}
</section>
 
<style>
.Produkt {
  margin: 2rem auto;
  padding: 1rem;
  max-width: 1200px;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 2rem;
  font-family: 'quicksand', sans-serif;
}
 
.Produkt div {
  background-color: #f7f7f2;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 1.5rem;
  text-decoration: none;
  color: black;
  font-size: 1rem;
  font-family: Arial, sans-serif;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.1);
  border-radius: 1rem;
  transition: transform 0.2s ease;
}
 
.Produkt div:hover {
  transform: translateY(-4px);
}
 
.Produkt img {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 0.75rem;
  margin-bottom: 1rem;
  box-shadow: 0 0.25rem 0.5rem rgba(0, 0, 0, 0.1);
  border: none;
}
 
.Produkt .produkt-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}
 
.Produkt .produkt-info span {
  margin: 0.2rem 0;
  font-size: 1rem;
}
 
.Produkt button {
  margin-top: 1rem;
  background-color: rgb(47, 60, 41);
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}
 
.Produkt button:hover {
  background-color: rgb(67, 80, 61);
}
 
.Produkt p {
  font-size: 0.7rem;
  color: rgb(47, 60, 41);
  margin-top: 0.5rem;
}
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
 
h3 {
  margin-top: 8rem;
  font-family: 'quicksand', sans-serif;
}
 
p {
  font-size: 0.8rem;
  color: rgb(47, 60, 41);
  margin-top: 0.5rem;
  font-family: 'quicksand', sans-serif;
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
 
.kategori-knapp {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  background-color: rgb(47, 60, 41);
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
  font-family: 'quicksand', sans-serif;
  transition: background-color 0.2s ease;
}
 
.kategori-knapp:hover {
  background-color: rgb(67, 80, 61);
}
 
.kategori-knapp img {
  width: 1.5rem;
  height: 1.5rem;
  object-fit: contain;
}
 
.kategori-container {
  display: flex;
  gap: 1rem;
  margin: 1rem 0;
  font-family: 'quicksand', sans-serif;
}
 
h1 {
  font-family: 'quicksand', sans-serif;
}
 
span {
  font-family: 'quicksand', sans-serif;
}
 
button {
  font-family: 'quicksand', sans-serif;
}
</style>