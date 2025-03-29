<template>
  <div>
    <div>
      <h2>Korisnički podaci</h2>
      <div id="korisnik"></div>
    </div>
    <div class="">
      <h2>Košarica</h2>
      <div id="kosarica"></div>

      <h3 id="ukupna-cijena"></h3>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

const slike = {
  Jabuka: "https://www.svgrepo.com/show/530203/apple.svg",
  Mrkva: "https://www.svgrepo.com/show/530216/carrot.svg",
  Sir: "https://www.svgrepo.com/show/530219/cake.svg",
  Kruh: "https://www.svgrepo.com/show/530223/bread.svg",
};

const proizvodi = [
  { naziv: "Jabuka", cijena: 0.25 },
  { naziv: "Mrkva", cijena: 0.12 },
  { naziv: "Kruh", cijena: 2.0 },
  { naziv: "Sir", cijena: 4.48 },
];

const korisnik = {
  jeAdmin: true,
  osobni_podaci: {
    ime: "Marko",
    prezime: "Krivić",
    adresa: {
      grad: "Pula",
      ulica: "Veruda",
      broj: 32,
    },
    broj_telefona: "+091-123-456",
  },
  kosarica: [
    { naziv: "Jabuka", količina: 4 },
    { naziv: "Mrkva", količina: 12 },
    { naziv: "Sir", količina: 1 },
    { naziv: "Kruh", količina: 4 },
  ],
};

function dohvatiCijenu(naziv) {
  let proizvod = proizvodi.find((p) => p.naziv === naziv);
  return proizvod.cijena;
}

function sveukupnaCijena() {
  return korisnik.kosarica
    .reduce((ukupno, stavke) => {
      return ukupno + dohvatiCijenu(stavka.naziv) * stavka.količina;
    }, 0)
    .toFixed(2);
}

function najskupljaStavka() {
  let maxStavka = korisnik.kosarica[0];
  let maxUkupno = dohvatiCijenu(maxStavka.naziv) * maxStavka.količina;

  for (let i = 1; i < korisnik.kosarica.length; i++) {
    const stavka = korisnik.kosarica[i];
    let ukupno = dohvatiCijenu(stavka.naziv) * stavka.količina;

    if (ukupno > maxUkupno) {
      maxStavka = stavka;
      maxUkupno = ukupno;
    }
  }
  return maxStavka.naziv;
}

onMounted(() => {
  const korisnikDiv = document.getElementById("korisnik");
  const klasa = korisnik.jeAdmin ? "admin" : "user";

  korisnikDiv.innerHTML = `
    <h3 class="${klasa}">Korisnički podaci</h3>
    <p class="${klasa}"><strong>Ime:</strong> ${korisnik.osobni_podaci.ime} ${korisnik.osobni_podaci.prezime}</p>
    <p class="${klasa}"><strong>Adresa:</strong> ${korisnik.osobni_podaci.adresa.ulica} ${korisnik.osobni_podaci.adresa.broj}, ${korisnik.osobni_podaci.adresa.grad}</p>
    <p class="${klasa}"><strong>Telefon:</strong> ${korisnik.osobni_podaci.broj_telefona}</p>
  `;

  let kosaricaDiv = document.getElementById("kosarica");
  let najskuplja = najskupljaStavka();

  korisnik.kosarica.forEach((stavka) => {
    let cijena = dohvatiCijenu(stavka.naziv);
    let ukupno = (cijena * stavka.količina).toFixed(2);
    let klasaStavke =
      stavka.naziv === najskuplja ? "stavka najskuplja" : "stavka";

    kosaricaDiv.innerHTML += `
      <div class="${klasaStavke}">
        <img src="${slike[stavka.naziv]}" alt="${stavka.naziv}">
        <div>
          <strong>${stavka.naziv}</strong><br>
          Cijena: €${cijena} | Količina: ${stavka.količina}<br>
          Ukupno: €${ukupno}
        </div>
      </div>
    `;
  });

  document.getElementById(
    "ukupna-cijena"
  ).innerText = `Ukupna cijena: €${sveukupnaCijena()}`;
});
</script>

<style>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.admin {
  color: blue;
}
.user {
  color: black;
}
.stavka {
  border: 1px solid #ccc;
  padding: 10px;
  margin: 10px 0;
  border-radius: 8px;
  display: flex;
  align-items: center;
  gap: 10px;
}
.najskuplja {
  border: 2px solid red;
  background-color: #ffe5e5;
}
img {
  width: 40px;
  height: 40px;
}
</style>
