# PMA — Rješenja zadataka

Rješenja idu uz [zadaci.md](zadaci.md). Za objašnjenja vidi [sablone_kompletno.md](sablone_kompletno.md) i [pitanja_i_odgovori.md](pitanja_i_odgovori.md).

⚠️ Podsjetnik: ID-eve elemenata (`getElementById`) uvijek uskladi s HTML-om koji dobiješ, a `localhost` zamijeni IP adresom s ispita!

---

## 1. Kino ulaznice (TIP B — lokalni popis)

```javascript
// Stvaramo NUMERIRANU listu <ol> unutar #lista
const ol = document.createElement("ol");
document.getElementById("lista").appendChild(ol);

// ===== DODAVANJE REZERVACIJE U POPIS =====
document.getElementById("dodaj").addEventListener("click", (e) => {
  e.preventDefault();

  const prezime = document.getElementById("prezime").value.trim();
  const cijena = parseFloat(document.getElementById("cijena").value);
  const kolicina = parseInt(document.getElementById("kolicina").value);

  if (prezime.length < 3) {
    alert("Prezime mora imati najmanje 3 znaka!");
    return;
  }
  if (isNaN(cijena) || cijena <= 0) {
    alert("Cijena mora biti veca od 0!");
    return;
  }
  if (isNaN(kolicina) || kolicina < 1 || kolicina > 5) {
    alert("Broj karata mora biti izmedju 1 i 5!");
    return;
  }

  const ukupno = cijena * kolicina;

  const li = document.createElement("li");
  li.dataset.prezime = prezime;
  li.dataset.ukupno = ukupno;
  li.textContent = `${prezime} - ${ukupno}kn `;

  const ukloniBtn = document.createElement("button");
  ukloniBtn.textContent = "Ukloni";
  ukloniBtn.addEventListener("click", () => {
    li.remove();
  });

  li.appendChild(ukloniBtn);
  ol.appendChild(li);

  document.getElementById("prezime").value = "";
  document.getElementById("cijena").value = "";
  document.getElementById("kolicina").value = "";
});

// ===== SPREMANJE NA SERVER (POST) =====
document.getElementById("spremi").addEventListener("click", (e) => {
  e.preventDefault();

  const lis = document.querySelectorAll("#lista li");
  if (lis.length === 0) {
    alert("Nema rezervacija za spremiti!");
    return;
  }

  const rezervacije = [];
  lis.forEach((li) => {
    rezervacije.push({
      prezime: li.dataset.prezime,
      cijena: parseFloat(li.dataset.ukupno),
    });
  });

  fetch("http://localhost:4000/karte", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ rezervacije: rezervacije }),
  })
    .then((res) => res.json())
    .then((data) => {
      ol.innerHTML = "";

      let brojOsoba = 0;
      let suma = 0;
      data.forEach((el) => {
        if (el.rezervacije) {
          brojOsoba += el.rezervacije.length;
          el.rezervacije.forEach((r) => (suma += r.cijena));
        } else {
          brojOsoba++;
          suma += el.cijena;
        }
      });

      document.getElementById("brOSoba").innerText = brojOsoba;
      document.getElementById("sumaKarte").innerText = suma.toFixed(2);
    })
    .catch((err) => console.log("Greska:", err));
});
```

---

## 2. Aviokarte (TIP A)

```javascript
document.getElementById('btnSpremi').addEventListener('click', function(e) {
    e.preventDefault();

    let ime = document.getElementById('ime').value.trim();
    let klasa;
    if (document.getElementById('e').checked) klasa = 'ekonomska';
    else if (document.getElementById('p').checked) klasa = 'poslovna';
    else if (document.getElementById('v').checked) klasa = 'vip';
    let prtljaga = document.getElementById('prtljaga').checked;

    if (ime.length < 3) {
        alert("Ime mora imati najmanje 3 znaka!");
        return;
    }

    let cijena = 0;
    if (klasa === "ekonomska") cijena = 50;
    else if (klasa === "poslovna") cijena = 100;
    else if (klasa === "vip") cijena = 200;
    if (prtljaga) cijena += 20;

    let podaci = {
        ime: ime,
        cijena: cijena,
        detalji: { ime, klasa, prtljaga }
    };

    fetch("http://localhost:4000/karte", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(podaci)
    })
    .then(res => res.text())
    .then(data => {
        alert(data);
        document.getElementById('ime').value = '';
        document.getElementById('prtljaga').checked = false;
        document.getElementById('e').checked = true;
    })
    .catch(err => {
        console.error(err);
        alert("Došlo je do greške pri komunikaciji sa serverom");
    });
});

document.getElementById('dohvatiSve').addEventListener('click', function(e) {
    e.preventDefault();

    fetch("http://localhost:4000/karte")
    .then(res => res.json())
    .then(data => {
        let lista = document.createElement('ul');
        let suma = 0;
        let brojacVIP = 0;

        data.forEach(function(karta) {
            if (karta.detalji.klasa.toLowerCase() === 'vip') brojacVIP++;
            suma += karta.cijena;

            let li = document.createElement('li');
            li.innerText = karta.detalji.klasa[0].toUpperCase() + " - " + karta.detalji.ime;
            lista.appendChild(li);
        });

        const popisDiv = document.getElementById('popis');
        popisDiv.innerHTML = '';
        popisDiv.appendChild(lista);

        document.getElementById('suma').innerText = suma;
        document.getElementById('brojac').innerText = brojacVIP;
    })
    .catch(err => {
        console.error(err);
        alert("Došlo je do greške pri dohvaćanju karata");
    });
});
```

---

## 3. Mobitel — POST varijanta (TIP A)

```javascript
document.getElementById("posalji").addEventListener("click", (e) => {
  e.preventDefault();

  const model = document.getElementById("model").value.trim();
  const serial = document.getElementById("serial").value.trim();
  const vlasnik = document.getElementById("vlasnik").value.trim();
  const petG = document.getElementById("petG").checked;

  if (model.length < 3) {
    alert("Model mora imati najmanje 3 znaka!");
    return;
  }

  if (serial === "" || serial.length !== 6 || isNaN(serial)) {
    alert("Serijski broj mora biti cijeli broj od točno 6 znamenki!");
    return;
  }

  if (vlasnik === "") {
    alert("Ime vlasnika ne smije biti prazno!");
    return;
  }

  const zaServer = {
    model: model,
    serial: Number(serial),
    vlasnik: vlasnik,
    petG: petG,
  };

  fetch("http://localhost:4000/mob/novi", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(zaServer),
  })
    .then((res) => res.json())
    .then((data) => {
      document.getElementById("odgovor").innerText = JSON.stringify(data);
      document.getElementById("ucitaj").click();
    })
    .catch((err) => console.log("Greška kod slanja:", err));
});

// ===== BRIŠI (samo čisti formu) =====
document.getElementById("brisi").addEventListener("click", (e) => {
  e.preventDefault();

  document.getElementById("model").value = "";
  document.getElementById("serial").value = "";
  document.getElementById("vlasnik").value = "";
  document.getElementById("petG").checked = false;
});

// ===== UČITAJ (GET popis svih) =====
document.getElementById("ucitaj").addEventListener("click", (e) => {
  e.preventDefault();

  fetch("http://localhost:4000/mob/svi")
    .then((res) => res.json())
    .then((data) => {
      const lista = document.createElement("ol");

      data.forEach((zahtjev) => {
        const li = document.createElement("li");
        let tekst = zahtjev.serial + " - " + zahtjev.vlasnik;
        if (zahtjev.petG) tekst += " - 5G";
        li.innerText = tekst;
        lista.appendChild(li);
      });

      const rezultatDiv = document.getElementById("rezultat");
      rezultatDiv.innerHTML = "";
      rezultatDiv.appendChild(lista);
    })
    .catch((err) => console.log("Greška kod dohvata:", err));
});
```

---

## 4. Servis — GET po ID + PUT (status 204, postotak)

```javascript
// ===== UČITAJ ZAPIS (GET) =====
document.getElementById("ucitaj").addEventListener("click", (e) => {
  e.preventDefault();
  const id = document.getElementById("id_unos").value;

  fetch(`http://localhost:4000/zahtjev/${id}`)
    .then((res) => {
      if (res.status === 204) {
        document.getElementById("greska").innerText =
          "Ne postoji podatak sa ovim ID-om";
        return null;
      }
      document.getElementById("greska").innerText = "";
      return res.json();
    })
    .then((data) => {
      if (!data) return;

      document.getElementById("id_rez").value = data.id;
      document.getElementById("ime").value = data.podaci.vlasnik;
      document.getElementById("serijski").value = data.podaci.broj;
      document.getElementById("popravljen").checked = data.podaci.popravljen;

      document.getElementById("id_rez").disabled = true;
    })
    .catch((err) => console.log("Greška:", err));
});

// ===== AŽURIRAJ ZAPIS (PUT) =====
document.getElementById("arz").addEventListener("click", (e) => {
  e.preventDefault();

  const id = document.getElementById("id_rez").value;
  const vlasnik = document.getElementById("ime").value;
  const serijski = document.getElementById("serijski").value;
  const popravljen = document.getElementById("popravljen").checked;

  if (vlasnik.trim() === "" || serijski.trim() === "") {
    alert("Vlasnik i serijski broj ne smiju biti prazni!");
    return;
  }
  const serBroj = Number(serijski);
  if (serBroj < 100 || serBroj > 999) {
    alert("Serijski broj mora biti u rasponu 100-999!");
    return;
  }

  const zapis = {
    id: Number(id),
    podaci: {
      vlasnik: vlasnik,
      broj: serBroj,
      popravljen: popravljen,
    },
  };

  fetch(`http://localhost:4000/zahtjev/${id}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(zapis),
  })
    .then((res) => res.json())
    .then((svi) => {
      const ukupno = svi.length;
      const popravljeni = svi.filter(
        (el) => el.podaci.popravljen === true
      ).length;
      const post = (popravljeni / ukupno) * 100;

      document.getElementById("postotak").innerText = "Popravljeno je " + post + "% mobitela";
    })
    .catch((err) => console.log("Greška:", err));
});
```

---

## 5. Popis studenata (GET / GET po id / DELETE, dinamički gumbi)

```javascript
// Klik na "Učitaj" - GET svih studenata
document.getElementById('btnUcitaj').addEventListener('click', (e) => {
    e.preventDefault();

    fetch('http://localhost:4000/studenti')
        .then(res => res.json())
        .then(data => {
            prikaziStudente(data);
        })
        .catch(err => console.log("Greška kod dohvaćanja studenata:", err));
});

// Funkcija za prikaz osnovnog popisa studenata
function prikaziStudente(studenti) {
    const popisUl = document.querySelector('#popis ul');
    popisUl.innerHTML = '';

    studenti.forEach(st => {
        const li = document.createElement('li');
        li.textContent = `${st.id} - ${st.student} `;

        const detaljiBtn = document.createElement('button');
        detaljiBtn.textContent = 'Detalji';
        detaljiBtn.addEventListener('click', () => {
            prikaziDetalje(st.id);
        });

        li.appendChild(detaljiBtn);
        popisUl.appendChild(li);
    });
}

// Funkcija za GET detalja jednog studenta
function prikaziDetalje(id) {
    fetch(`http://localhost:4000/studenti/${id}`)
        .then(res => res.json())
        .then(student => {
            const detaljiUl = document.querySelector('#detalji ul');
            detaljiUl.innerHTML = '';

            for (let key in student) {
                const li = document.createElement('li');
                li.textContent = key + ": " + JSON.stringify(student[key]);
                detaljiUl.appendChild(li);
            }

            const brisiBtn = document.createElement('button');
            brisiBtn.textContent = 'Briši';
            brisiBtn.addEventListener('click', () => {
                obrisiStudenta(id);
            });

            const brisiLi = document.createElement('li');
            brisiLi.appendChild(brisiBtn);
            detaljiUl.appendChild(brisiLi);
        })
        .catch(err => console.log("Greška kod dohvaćanja detalja:", err));
}

// Funkcija za DELETE studenta
function obrisiStudenta(id) {
    fetch(`http://localhost:4000/studenti/${id}`, {
        method: 'DELETE'
    })
        .then(res => res.json())
        .then(preostaliStudenti => {
            document.querySelector('#detalji ul').innerHTML = '';
            prikaziStudente(preostaliStudenti);
        })
        .catch(err => console.log("Greška kod brisanja:", err));
}
```

---

## 6. Koncert (TIP A, Date objekt)

```javascript
document.getElementById("spremi").addEventListener("click", (e) => {
  e.preventDefault();

  const ime = document.getElementById("ime").value.trim();
  const trajanje = Number(document.getElementById("trajanje").value);

  let sjedalo;
  if (document.getElementById("v").checked) sjedalo = "vip";
  else if (document.getElementById("r").checked) sjedalo = "regular";
  else if (document.getElementById("f").checked) sjedalo = "fanpit";

  const datum = new Date().toLocaleDateString("en-US");

  if (ime.length < 3) {
    alert("Ime mora imati najmanje 3 znaka!");
    return;
  }
  if (!datum) {
    alert("Datum nije definiran!");
    return;
  }

  let cijena = 0;
  if (sjedalo === "vip") cijena = 10;
  else if (sjedalo === "regular") cijena = 5;
  else if (sjedalo === "fanpit") cijena = 2;
  if (trajanje > 100) cijena += 5;

  const zaServer = {
    cijena: cijena,
    datum: datum,
    detalji: {
      ime: ime,
      sjedalo: sjedalo,
      trajanje: trajanje,
    },
  };

  fetch("http://localhost:4000/koncert", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(zaServer),
  })
    .then((res) => res.text())
    .then((data) => {
      alert(data);

      document.getElementById("ime").value = "";
      document.getElementById("trajanje").value = "";
      document.getElementById("v").checked = true;
    })
    .catch((err) => console.log("Greška kod slanja:", err));
});

// ===== DOHVATI (GET) =====
document.getElementById("dohvati").addEventListener("click", (e) => {
  e.preventDefault();

  fetch("http://localhost:4000/koncert")
    .then((res) => res.json())
    .then((data) => {
      const lista = document.createElement("ol");
      let suma = 0;
      let brojac = 0;

      data.forEach((karta) => {
        suma += karta.cijena;
        if (karta.detalji.sjedalo.toLowerCase() === "vip") brojac++;

        const li = document.createElement("li");
        li.innerText = karta.detalji.sjedalo + " - " + karta.detalji.ime;
        lista.appendChild(li);
      });

      const popisDiv = document.getElementById("popis");
      popisDiv.innerHTML = "";
      popisDiv.appendChild(lista);

      document.getElementById("suma").innerText = "Ukupna cijena: " + suma + " €";
      document.getElementById("brojac").innerText = "Broj VIP: " + brojac;
    })
    .catch((err) => console.log("Greška kod dohvata:", err));
});
```

---

## 7. Popis proizvoda (TIP B — lokalni popis, max 5, najskuplji)

```javascript
let listaArtikala = [];
const ol = document.createElement("ol");
document.getElementById("lista").appendChild(ol);

document.getElementById("Dodaj").addEventListener("click", (e) => {
  e.preventDefault();

  const naziv = document.getElementById("naziv").value.trim();
  const cijena = Number(document.getElementById("cijena").value);

  if (naziv.length < 4 || isNaN(cijena) || cijena <= 0) {
    alert("Naziv mora imati barem 4 znaka, a cijena mora biti veca od 0!");
    return;
  }

  if (listaArtikala.length >= 5) {
    alert("Popis ne smije sadrzavati vise od 5 artikala!");
    return;
  }

  listaArtikala.push({ naziv: naziv, cijena: cijena });

  const li = document.createElement("li");
  li.innerText = `${naziv} - ${cijena}€`;
  ol.appendChild(li);

  document.getElementById("naziv").value = "";
  document.getElementById("cijena").value = "";
});

// ===== SPREMANJE POPISA (POST) =====
document.getElementById("Spremi").addEventListener("click", (e) => {
  e.preventDefault();

  if (listaArtikala.length === 0) {
    alert("Popis je prazan!");
    return;
  }

  let ukupnaCijena = 0;
  listaArtikala.forEach((art) => (ukupnaCijena += art.cijena));

  const zaServer = {
    cijena: ukupnaCijena,
    artikli: listaArtikala,
  };

  fetch("http://localhost:4000/popis", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(zaServer),
  })
    .then((res) => res.json())
    .then((data) => {
      const brojPopisa = data.length;
      let suma = 0;
      let max = 0;
      data.forEach((p) => {
        suma += p.cijena;
        if (p.cijena > max) max = p.cijena;
      });

      document.getElementById("brPopisa").innerText = brojPopisa;
      document.getElementById("sumaPopisa").innerText = suma.toFixed(2);
      document.getElementById("maxPopis").innerText = max.toFixed(2);

      listaArtikala = [];
      ol.innerHTML = "";
    })
    .catch((err) => console.log("Greska:", err));
});
```

---

## Grupa B - Novi zaposleník

```javascript
// ===== DODAJ NOVOG ZAPOSLENIKA =====
document.getElementById("dodaj").addEventListener("click", (e) => {
  e.preventDefault();

  const ime = document.getElementById("ime").value.trim();
  const prezime = document.getElementById("prezime").value.trim();
  const email = document.getElementById("email").value.trim();
  const godina = Number(document.getElementById("godina").value);
  const kolegij = document.getElementById("kolegij").value;
  const status = document.querySelector('input[name="status"]:checked').value;

  if ((ime + prezime).length < 6) {
    alert("Ime i prezime skupna moraju biti najmanje 6 znakova!");
    return;
  }

  if (!email.includes("@")) {
    alert("Email mora sadržavati @");
    return;
  }

  if (godina <= 1990) {
    alert("Godina rođenja mora biti veća od 1990!");
    return;
  }

  let bodovi = 0;
  if (kolegij === "Informatika") {
    bodovi = 63 * 0.05;
  } else {
    bodovi = 63;
  }

  if (status === "redovni") {
    bodovi += 10;
  }

  const zaposleník = {
    ime: ime,
    prezime: prezime,
    email: email,
    godinaRođenja: godina,
    kolegiji: [kolegij],
    bodovi: bodovi,
    statusRedovni: status === "redovni" ? true : false,
    prijave: []
  };

  fetch("http://localhost:4000/studenti", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(zaposleník)
  })
    .then(res => res.json())
    .then(data => {
      alert("Zaposleník dodan!");
      document.getElementById("ime").value = "";
      document.getElementById("prezime").value = "";
      document.getElementById("email").value = "";
      document.getElementById("godina").value = "";
      document.getElementById("pretraži").click();
    })
    .catch(err => console.log(err));
});

// ===== PRETRAŽI ZAPOSLENIKE =====
document.getElementById("pretraži").addEventListener("click", (e) => {
  e.preventDefault();

  const kolegij = document.getElementById("filterKolegij").value;
  const godinaRođenja = document.getElementById("filterGodina").value;

  fetch(`http://localhost:4000/studenti?kolegiji=${kolegij}&godinaRođenja=${godinaRođenja}`)
    .then(res => res.json())
    .then(data => {
      let html = "";
      data.forEach(z => {
        html += `<li>${z.ime} ${z.prezime} - Bodovi: ${z.bodovi}</li>`;
      });
      document.getElementById("rezultat").innerHTML = `<ul>${html}</ul>`;
    })
    .catch(err => console.log(err));
});
```

---

## Grupa C - Novi sportaš

```javascript
// ===== DODAJ NOVOG SPORTAŠA =====
document.getElementById("dodaj").addEventListener("click", (e) => {
  e.preventDefault();

  const ime = document.getElementById("ime").value.trim();
  const prezime = document.getElementById("prezime").value.trim();
  const email = document.getElementById("email").value.trim();
  const godina = Number(document.getElementById("godina").value);
  const sport = document.getElementById("sport").value;
  const status = document.querySelector('input[name="statusSportasa"]:checked').value;

  if ((ime + prezime).length < 5) {
    alert("Ime i prezime skupna moraju biti najmanje 5 znakova!");
    return;
  }

  if (!email.includes("@")) {
    alert("Email mora sadržavati @");
    return;
  }

  if (godina < 1980 || godina > 2005) {
    alert("Godina rođenja mora biti između 1980 i 2005!");
    return;
  }

  let treningo = 0;
  if (sport === "nogomет") treningo = 4;
  else if (sport === "tenis") treningo = 3;
  else if (sport === "plivanje") treningo = 5;

  if (status === "profesionalan") {
    treningo += 2;
  }

  if (sport === "plivanje") {
    treningo = treningo + (treningo * 0.10);
  }

  const sportaš = {
    ime: ime,
    prezime: prezime,
    email: email,
    godinaRođenja: godina,
    sport: sport,
    statusProfesionalni: status === "profesionalan" ? true : false,
    planTreninga: {
      treninziTjedno: treningo,
      sport: sport
    }
  };

  fetch("http://localhost:4000/sportasi", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(sportaš)
  })
    .then(res => res.json())
    .then(data => {
      alert("Sportaš dodan!");
      document.getElementById("ime").value = "";
      document.getElementById("prezime").value = "";
      document.getElementById("email").value = "";
      document.getElementById("godina").value = "";
      document.getElementById("pretraži").click();
    })
    .catch(err => console.log(err));
});

// ===== PRETRAŽI SPORTAŠE =====
document.getElementById("pretraži").addEventListener("click", (e) => {
  e.preventDefault();

  const sport = document.getElementById("filterSport").value;

  fetch(`http://localhost:4000/sportasi?sport=${sport}`)
    .then(res => res.json())
    .then(data => {
      let html = "";
      data.forEach(s => {
        html += `<li>${s.ime} ${s.prezime} - Sport: ${s.sport} - Treningo/tjednu: ${s.planTreninga.treninziTjedno}</li>`;
      });
      document.getElementById("rezultat").innerHTML = `<ul>${html}</ul>`;
    })
    .catch(err => console.log(err));
});
```

---

## 3. Ispitni rok - Koncert

```javascript
// ===== DODAJ NOVU KARTU =====
document.getElementById("spremi").addEventListener("click", (e) => {
  e.preventDefault();

  const izvođač = document.getElementById("izvođač").value.trim();
  const datum = new Date().toLocaleDateString("hr-HR");
  const trajanje = Number(document.getElementById("trajanje").value);
  let pozicija;
  if (document.getElementById("vip").checked) pozicija = "vip";
  else if (document.getElementById("regular").checked) pozicija = "regular";
  else if (document.getElementById("fanpit").checked) pozicija = "fanpit";

  if (izvođač.length < 3) {
    alert("Izvođač mora imati najmanje 3 znaka!");
    return;
  }

  if (!datum) {
    alert("Datum nije definiran!");
    return;
  }

  let cijena = 0;
  if (pozicija === "vip") cijena = 10;
  else if (pozicija === "regular") cijena = 5;
  else if (pozicija === "fanpit") cijena = 2;

  if (trajanje > 100) {
    cijena += 5;
  }

  const karta = {
    cijena: cijena,
    datum: datum,
    detalji: {
      ime: izvođač,
      sjedalo: pozicija,
      trajanje: trajanje
    }
  };

  fetch("http://localhost:4000/koncert", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(karta)
  })
    .then(res => res.text())
    .then(msg => {
      alert(msg);
      document.getElementById("izvođač").value = "";
      document.getElementById("trajanje").value = "";
      document.getElementById("regular").checked = true;
      document.getElementById("dohvati").click();
    })
    .catch(err => console.log(err));
});

// ===== DOHVATI SVE KARTE =====
document.getElementById("dohvati").addEventListener("click", (e) => {
  e.preventDefault();

  fetch("http://localhost:4000/koncert")
    .then(res => res.json())
    .then(data => {
      const lista = document.createElement("ol");
      let suma = 0;
      let brojVIP = 0;

      data.forEach(karta => {
        suma += karta.cijena;
        if (karta.detalji.sjedalo === "vip") brojVIP++;

        const li = document.createElement("li");
        li.innerText = `${karta.detalji.sjedalo[0].toUpperCase()} - ${karta.detalji.ime}`;
        lista.appendChild(li);
      });

      document.getElementById("popis").innerHTML = "";
      document.getElementById("popis").appendChild(lista);

      document.getElementById("suma").innerText = suma + " €";
      document.getElementById("brojVIP").innerText = brojVIP;
    })
    .catch(err => console.log(err));
});
```

---

## Vinarija - 1. ROK (GET + PUT)

```javascript
// UČITAJ ZAPIS - GET sa ID-om
document.getElementById('ucitaj').addEventListener('click', (e) => {
  e.preventDefault();
  
  const idEl = document.getElementById('id_unos');
  const id = idEl.value.trim();
  
  if(!id) {
    alert("Trebam ID vina!");
    idEl.style.border = "2px solid red";
    return;
  }
  
  idEl.style.border = "2px solid black";
  
  fetch(`http://localhost:4000/vino/${id}`)
    .then(res => {
      if(res.status === 204) {
        alert("Ne postoji podatak sa ovim ID-om!");
        return null;
      }
      return res.json();
    })
    .then(data => {
      if(!data) return;
      
      document.getElementById('id_rez').value = data.id;
      document.getElementById('ime').value = data.ime;
      document.getElementById('vinog').value = data.vinog;
      document.getElementById('godina').value = data.godinaProizvodnje;
      document.getElementById('proizvodac').value = data.nazivProizvodaca;
      document.getElementById('organsko').checked = data.organsko;
      
      document.getElementById('id_rez').disabled = true;
      document.getElementById('id_rez').style.backgroundColor = "#e0e0e0";
    })
    .catch(err => {
      console.log("Greška pri učitavanju:", err);
      alert("Došlo je do greške!");
    });
});

// AŽURIRAJ ZAPIS - PUT
document.getElementById('azuriraj').addEventListener('click', (e) => {
  e.preventDefault();
  
  const id = document.getElementById('id_rez').value;
  const imeEl = document.getElementById('ime');
  const ime = imeEl.value.trim();
  
  const vinogEl = document.getElementById('vinog');
  const vinog = vinogEl.value.trim();
  
  const godEl = document.getElementById('godina');
  const godina = parseInt(godEl.value);
  
  const prodEl = document.getElementById('proizvodac');
  const proizvodac = prodEl.value.trim();
  
  const organsko = document.getElementById('organsko').checked;
  
  if(!ime) {
    imeEl.style.border = "2px solid red";
    alert("Trebam ime vina!");
    return;
  }
  imeEl.style.border = "2px solid black";
  
  if(!vinog) {
    vinogEl.style.border = "2px solid red";
    alert("Trebam vrstu vina!");
    return;
  }
  vinogEl.style.border = "2px solid black";
  
  if(!godina || isNaN(godina)) {
    godEl.style.border = "2px solid red";
    alert("Trebam validnu godinu!");
    return;
  }
  godEl.style.border = "2px solid black";
  
  if(!proizvodac) {
    prodEl.style.border = "2px solid red";
    alert("Trebam naziv proizvodača!");
    return;
  }
  prodEl.style.border = "2px solid black";
  
  fetch(`http://localhost:4000/vino/${id}`, {
    method: "PUT",
    headers: { 
      "Content-Type": "application/json" 
    },
    body: JSON.stringify({
      id: parseInt(id),
      ime: ime,
      vinog: vinog,
      godinaProizvodnje: godina,
      nazivProizvodaca: proizvodac,
      organsko: organsko
    })
  })
    .then(res => res.json())
    .then(data => {
      alert("Vino uspješno ažurirano!");
      console.log("Ažurirani podaci:", data);
    })
    .catch(err => {
      console.log("Greška pri ažuriranju:", err);
      alert("Došlo je do greške!");
    });
});
```

---

## Keramika - Grupa A (GET + PUT)

```javascript
// UČITAJ ZAPIS - GET sa ID-om
document.getElementById('ucitaj').addEventListener('click', (e) => {
  e.preventDefault();
  
  const idEl = document.getElementById('id_unos');
  const id = idEl.value.trim();
  
  if(!id) {
    alert("Trebam ID kursa!");
    idEl.style.border = "2px solid red";
    return;
  }
  
  idEl.style.border = "2px solid black";
  
  fetch(`http://localhost:4000/keramika/${id}`)
    .then(res => {
      if(res.status === 204) {
        alert("Ne postoji podatak sa ovim ID-om!");
        return null;
      }
      return res.json();
    })
    .then(data => {
      if(!data) return;
      
      document.getElementById('id_rez').value = data.id;
      document.getElementById('ime').value = data.ime;
      document.getElementById('nivo').value = data.nivo;
      document.getElementById('trajanje').value = data.trajanjeTjedana;
      document.getElementById('predavac').value = data.nazivPredavaca;
      document.getElementById('iskustvo').value = data.iskustvoGodina;
      
      document.getElementById('id_rez').disabled = true;
      document.getElementById('id_rez').style.backgroundColor = "#e0e0e0";
    })
    .catch(err => {
      console.log("Greška pri učitavanju:", err);
      alert("Došlo je do greške!");
    });
});

// AŽURIRAJ ZAPIS - PUT
document.getElementById('azuriraj').addEventListener('click', (e) => {
  e.preventDefault();
  
  const id = document.getElementById('id_rez').value;
  
  const imeEl = document.getElementById('ime');
  const ime = imeEl.value.trim();
  
  const nivoEl = document.getElementById('nivo');
  const nivo = nivoEl.value.trim();
  
  const trajanjeEl = document.getElementById('trajanje');
  const trajanje = parseInt(trajanjeEl.value);
  
  const predEl = document.getElementById('predavac');
  const predavac = predEl.value.trim();
  
  const iskEl = document.getElementById('iskustvo');
  const iskustvo = parseInt(iskEl.value);
  
  if(!ime) {
    imeEl.style.border = "2px solid red";
    alert("Trebam ime kursa!");
    return;
  }
  imeEl.style.border = "2px solid black";
  
  if(!nivo) {
    nivoEl.style.border = "2px solid red";
    alert("Trebam nivo kursa!");
    return;
  }
  nivoEl.style.border = "2px solid black";
  
  if(!trajanje || isNaN(trajanje)) {
    trajanjeEl.style.border = "2px solid red";
    alert("Trebam validno trajanje!");
    return;
  }
  trajanjeEl.style.border = "2px solid black";
  
  if(!predavac) {
    predEl.style.border = "2px solid red";
    alert("Trebam naziv predavača!");
    return;
  }
  predEl.style.border = "2px solid black";
  
  if(iskustvo < 5) {
    iskEl.style.border = "2px solid red";
    alert("Iskustvo mora biti 5+ godina!");
    return;
  }
  iskEl.style.border = "2px solid black";
  
  fetch(`http://localhost:4000/keramika/${id}`, {
    method: "PUT",
    headers: { 
      "Content-Type": "application/json" 
    },
    body: JSON.stringify({
      id: parseInt(id),
      ime: ime,
      nivo: nivo,
      trajanjeTjedana: trajanje,
      nazivPredavaca: predavac,
      iskustvoGodina: iskustvo,
      materijali: true
    })
  })
    .then(res => res.json())
    .then(data => {
      alert("Kurs uspješno ažuriran!");
      console.log("Ažurirani podaci:", data);
    })
    .catch(err => {
      console.log("Greška pri ažuriranju:", err);
      alert("Došlo je do greške!");
    });
});
```
