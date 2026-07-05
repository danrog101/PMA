# PMA KOLOKVIJ — ŠABLONE + RJEČNIK PREPOZNAVANJA
### Napravljeno iz 8 starih zadataka: Kino, Aviokarte, Prijava ispita, Mobitel, Servis (GET/PUT), Studenti, Koncert, Popis proizvoda

---

# DIO 1: RJEČNIK — "kad zadatak kaže X, ti pišeš Y"

## Validacija unosa

**"ne smije biti prazno, niti kraće od 3 znaka"**
```javascript
const ime = document.getElementById("ime").value.trim();
if (ime.length < 3) { ... }        // pokriva i prazno (0 < 3)
```

**"polje ne smije biti prazno"**
```javascript
if (naziv === "") { ... }
```

**"mora biti cijeli broj duljine TOČNO 6 znamenki"**
```javascript
const sifra = document.getElementById("sifra").value.trim();  // STRING, ne parseInt!
if (sifra === "" || sifra.length !== 6 || isNaN(sifra)) { ... }
// u objekt za server ide: Number(sifra)
```
⚠️ NIKAD parseInt pa .length — broj nema .length!

**"cijena mora biti veća od 0"**
```javascript
const cijena = Number(document.getElementById("cijena").value);
if (isNaN(cijena) || cijena <= 0) { ... }
```

**"između 1 i 5 (uključeno)"**
```javascript
if (broj < 1 || broj > 5) { ... }    // "uključeno" = < i >, ne <= i >=
```

**"popis ne smije sadržavati više od 5 artikala... ispisati poruku u alert"**
```javascript
if (artikli.length >= 5) {
    alert("Popis ne smije imati više od 5 artikala!");
    return;
}
```

**"trajanje > 100 minuta"** → `if (trajanje > 100)` — pazi: 100 točno NE ulazi!

## Kako se validacija PRIKAZUJE — dvije škole

**"ne smije se slati ako ime nije ispravno"** (bez spominjanja boja) → alert + return:
```javascript
if (ime.length < 3) {
    alert("Ime mora imati najmanje 3 znaka!");
    return;
}
```

**"okvire SVIH polja koja imaju neispravnu vrijednost obojite u crvenu boju...
ako su ispravni uklonite oblikovanje"** → zastavica, BEZ ranog returna:
```javascript
let ispravno = true;

if (ime.length < 3) {
    imeEl.style.border = "2px solid red";
    ispravno = false;
} else {
    imeEl.style.border = "";        // OBAVEZNO makni kad je ok!
}
// ... ista struktura za SVAKO polje ...

if (!ispravno) return;              // tek NAKON svih provjera
```
Ključna riječ za prepoznavanje: **"svih polja"** → moraš provjeriti sva, znači zastavica.

## Čitanje elemenata forme

**tekstualno polje** → `.value.trim()`
**broj iz polja** → `.value.trim()` za provjeru duljine, `Number(...)` za slanje/računanje
**checkbox** ("checkbox služi za odabir...") → `.checked` (boolean! NIKAD .value)
**radio buttoni** ("klasa", "sjedalo", "pozicija"):
```javascript
let klasa;
if (document.getElementById("e").checked) klasa = "ekonomska";
else if (document.getElementById("p").checked) klasa = "poslovna";
else if (document.getElementById("v").checked) klasa = "vip";
```

## Slanje

**"poslati POST zahtjev na adresu ... Poslužitelj ne prihvaća FormData"**
```javascript
fetch("http://localhost:4000/ruta", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(zaServer),
})
```
("ne prihvaća FormData" = obavezno JSON.stringify + Content-Type header)

**"poslati PUT zahtjev na ISTU adresu sa koje je dohvaćen podatak"**
```javascript
fetch(`http://localhost:4000/zahtjev/${id}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(zapis),
})
```

**"poslati DELETE zahtjev ... gdje umjesto <id> mora biti ID"**
```javascript
fetch(`http://localhost:4000/studenti/${id}`, { method: "DELETE" })
```
(DELETE nema body ni headers)

**"gdje umjesto <id> mora biti broj upisan u polju"** → template literal:
```javascript
fetch(`http://localhost:4000/zahtjev/${id}`)     // kose navodnice ` ` !
```

**"Format podatka možete vidjeti na slici"** → SVETO PISMO. Objekt gradiš
TOČNO kao na slici: ista imena ključeva, ista ugniježđenost, brojevi kao
brojevi, booleani bez navodnika:
```javascript
// slika: { "cijena": 70, "detalji": { "ime": "Maja", "klasa": "ekonomska", "prtljaga": true } }
const zaServer = {
    cijena: cijena,
    detalji: { ime: ime, klasa: klasa, prtljaga: prtljaga },
};
```

**"*ID se automatski dodaje na poslužitelju"** → NE stavljaš id u objekt!

**"Datum se mora dohvatiti pomoću objekta Date"** (format na slici 8/20/2025):
```javascript
const datum = new Date().toLocaleDateString("en-US");
```

## Odgovor servera

**"Poslužitelj šalje odgovor u JSON formatu"** → `.then((res) => res.json())`
**server vraća čisti tekst** → `.then((res) => res.text())`
**"prikažite poruku iz odgovora u DIV elementu sa ID atributom odgovor"**
```javascript
document.getElementById("odgovor").innerText = JSON.stringify(data);
// ili data.poruka ako vidiš (console.log!) da server vraća {poruka: "..."}
```

**"ako ne postoji zapis, poslužitelj vraća status 204 (No Content)"**
→ provjera u PRVOM then-u, res.json() se NE SMIJE zvati na 204:
```javascript
.then((res) => {
    if (res.status === 204) {
        document.getElementById("greska").innerText = "Ne postoji podatak sa ovim ID-om";
        return null;
    }
    document.getElementById("greska").innerText = "";
    return res.json();
})
.then((data) => {
    if (!data) return;
    // ... normalan rad s podacima ...
})
```

## Prikaz podataka

**"numerirana lista"** → `document.createElement("ol")` (1. 2. 3.)
**"nenumerirana lista"** → `document.createElement("ul")` (točkice)
**"u formatu 'klasa – ime'"** → `li.innerText = el.klasa + " - " + el.ime;`
**"za klasu prikazano samo PRVO SLOVO (npr. P – Ante)"**
```javascript
li.innerText = el.detalji.klasa[0].toUpperCase() + " - " + el.detalji.ime;
```
**"– (POV) ako je ispit pred povjerenstvom" / "- 5G ako je 5G model"** → uvjetni dodatak:
```javascript
let tekst = el.kolegij + " - " + el.student;
if (el.povjerenstvo) tekst += " - (POV)";
li.innerText = tekst;
```
**"lista se mora nalaziti unutar DIV elementa sa ID rezultat"**
```javascript
const div = document.getElementById("rezultat");
div.innerHTML = "";           // uvijek prvo očisti!
div.appendChild(lista);
```
**"ispišite u odgovarajuće SPAN elemente"** → isto kao div, samo je meta span:
```javascript
document.getElementById("brojOsoba").innerText = brojac;
```
**"ispišite sve podatke koji se nalaze u sklopu odgovora"** (ne znaš ključeve!):
```javascript
for (let key in data) {
    const li = document.createElement("li");
    li.innerText = key + ": " + JSON.stringify(data[key]);  // stringify pokriva i nizove/objekte!
    ul.appendChild(li);
}
```

## Statistika

**"zbroj cijena svih"** → `suma += el.cijena;` u forEach
**"broj X koji su Y"** → `if (uvjet) brojac++;` u forEach
**"koliko je ukupno spremljeno"** → `data.length`
**"postotak"**
```javascript
const post = (data.filter((el) => el.podaci.popravljen).length / data.length) * 100;
```
**"iznos najskupljeg"** → traženje maksimuma:
```javascript
let najveci = 0;
data.forEach((el) => {
    if (el.cijena > najveci) najveci = el.cijena;
});
```
**"ukupna cijena je UMNOŽAK broja karata i cijene"** → `const ukupno = cijena * brojKarata;`

## Ostalo

**"automatski ažurira popis (bez pritiskanja na tipku Učitaj)"**
```javascript
document.getElementById("ucitaj").click();     // u then-u nakon POST-a
```
**"onemogućiti mijenjanje ID-a"** → `document.getElementById("id_rez").disabled = true;`
**"pritiskom na Briši sve vrijednosti se brišu, BEZ slanja zahtjeva"** → samo reset:
```javascript
document.getElementById("ime").value = "";
document.getElementById("check").checked = false;
```
**"uz svaki podatak dodati button element"** → gumb u forEach-u (šablona 8)
**"nakon uspješnog slanja izbrišite sve rezervacije iz popisa"**
```javascript
rezervacije = [];                                  // isprazni lokalni niz
document.getElementById("popis").innerHTML = "";   // i prikaz
```
**"niz sa svim NIZOVIMA rezervacija"** → odgovor je niz u nizu → dupli forEach (šablona 13)
**"logiku lokalnog spremanja možete implementirati prema vlastitoj želji"**
→ zadatak s LOKALNIM popisom (šablona 12) — ništa se ne šalje dok se ne klikne Spremi!

---

# DIO 2: ŠABLONE

## ŠABLONA 0: Kostur svakog handlera (znaš, ali za potpunost)
```javascript
document.getElementById("gumb").addEventListener("click", (e) => {
    e.preventDefault();
    // 1. čitanje forme (ako treba)
    // 2. provjere (ako treba)
    // 3. izračuni (ako treba)
    // 4. objekt za server (ako je POST/PUT)
    fetch("...", { ... })
        .then((res) => res.json())
        .then((data) => {
            // ⬅ OVDJE ide neka od šablona ispod
        })
        .catch((err) => console.log("greška", err));
});
```

## ŠABLONA 1: Izračun cijene (radio klasa + dodatak)
Aviokarte, Koncert — "cijena ovisi o klasi te opciji prtljage (dodatnih 20€ ako...)"
```javascript
let cijena = 0;
if (klasa === "ekonomska") cijena = 50;
else if (klasa === "poslovna") cijena = 100;
else if (klasa === "vip") cijena = 200;
if (prtljaga) cijena += 20;             // checkbox varijanta
// if (trajanje > 100) cijena += 5;     // brojčani uvjet varijanta (Koncert)
```

## ŠABLONA 2: Niz → lista
```javascript
const lista = document.createElement("ol");   // ili "ul" — čitaj zadatak!
data.forEach((el) => {
    const li = document.createElement("li");
    li.innerText = el.nesto + " - " + el.drugo;   // format iz zadatka, putanje sa slike!
    lista.appendChild(li);
});
const div = document.getElementById("rezultat");
div.innerHTML = "";
div.appendChild(lista);
```

## ŠABLONA 3: Lista + suma + brojač
```javascript
const lista = document.createElement("ul");
let suma = 0;
let brojac = 0;
data.forEach((el) => {
    suma += el.cijena;
    if (el.detalji.klasa.toLowerCase() === "vip") brojac++;
    const li = document.createElement("li");
    li.innerText = el.detalji.klasa[0].toUpperCase() + " - " + el.detalji.ime;
    lista.appendChild(li);
});
const div = document.getElementById("popis");
div.innerHTML = "";
div.appendChild(lista);
document.getElementById("suma").innerText = "Ukupna cijena: " + suma + " €";
document.getElementById("brojac").innerText = "Broj VIP putnika: " + brojac;
```

## ŠABLONA 4: Jedan zapis → popuni polja forme (GET po ID-u)
```javascript
document.getElementById("id_rez").value = data.id;
document.getElementById("ime").value = data.podaci.vlasnik;
document.getElementById("serijski").value = data.podaci.broj;
document.getElementById("popravljen").checked = data.podaci.popravljen;
document.getElementById("id_rez").disabled = true;
```

## ŠABLONA 5: Status 204 (vidi rječnik gore — ide u PRVI then)

## ŠABLONA 6: PUT + statistika iz odgovora (Servis)
```javascript
const zapis = {
    id: Number(id),
    podaci: { vlasnik: vlasnik, broj: Number(serijski), popravljen: popravljen },
};
fetch(`http://localhost:4000/zahtjev/${id}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(zapis),
})
    .then((res) => res.json())
    .then((svi) => {
        const post = (svi.filter((el) => el.podaci.popravljen).length / svi.length) * 100;
        document.getElementById("postotak").innerText = "Popravljeno je " + post + "% mobitela";
    })
    .catch((err) => console.log("greška", err));
```

## ŠABLONA 7: Ispis svih ključeva (vidi rječnik — for...in + JSON.stringify)

## ŠABLONA 8: Lista s gumbima + delegacija (Studenti)
Građenje liste s gumbom uz svaki element:
```javascript
data.forEach((st) => {
    const li = document.createElement("li");
    li.textContent = "id: " + st.id + " - student: " + st.student + " ";
    const btn = document.createElement("button");
    btn.textContent = "Detalji";
    btn.dataset.id = st.id;          // zapamti id na gumbu
    li.appendChild(btn);
    lista.appendChild(li);
});
```
Hvatanje klikova na te gumbe — ODVOJENI handler na roditelju:
```javascript
document.getElementById("popis").addEventListener("click", (e) => {
    if (e.target.tagName !== "BUTTON") return;
    const id = e.target.dataset.id;
    fetch(`http://localhost:4000/studenti/${id}`)
        .then((res) => res.json())
        .then((student) => { /* šablona 7 + gumb Briši s dataset.id */ })
        .catch((err) => console.log("greška", err));
});
```

## ŠABLONA 9: DELETE + osvježavanje (Studenti)
```javascript
document.getElementById("detalji").addEventListener("click", (e) => {
    if (e.target.tagName !== "BUTTON") return;
    const id = e.target.dataset.id;
    fetch(`http://localhost:4000/studenti/${id}`, { method: "DELETE" })
        .then((res) => res.json())
        .then(() => {
            document.getElementById("detalji").innerHTML = "";   // očisti detalje
            document.getElementById("ucitaj").click();            // osvježi popis
        })
        .catch((err) => console.log("greška", err));
});
```

## ŠABLONA 10: Auto-refresh — .click() (vidi rječnik)

## ŠABLONA 11: Reset forme nakon uspjeha / gumb Briši
```javascript
document.getElementById("ime").value = "";
document.getElementById("sifra").value = "";
document.getElementById("povjerenstvo").checked = false;
document.getElementById("e").checked = true;   // radio: vrati na prvu opciju
// + ako zadatak ima crvene okvire, makni ih: el.style.border = "";
```

## ŠABLONA 12: LOKALNI popis (Kino, Popis proizvoda) — POSEBAN TIP ZADATKA!
Prepoznajеš ga po: "dodaje se u popis" BEZ slanja, "logiku lokalnog spremanja
implementirajte po želji", a POST se šalje TEK na "Spremi" i to CIJELI popis odjednom.

```javascript
// lokalno spremište — niz na vrhu skripte, IZVAN handlera
let rezervacije = [];

// --- DODAJ U POPIS (bez fetch-a!) ---
document.getElementById("dodaj").addEventListener("click", (e) => {
    e.preventDefault();
    const prezime = document.getElementById("prezime").value.trim();
    const cijena = Number(document.getElementById("cijena").value);
    const brojKarata = Number(document.getElementById("brojKarata").value);

    if (prezime.length < 3) { alert("Prezime min 3 znaka!"); return; }
    if (isNaN(cijena) || cijena <= 0) { alert("Cijena mora biti > 0!"); return; }
    if (brojKarata < 1 || brojKarata > 5) { alert("Broj karata 1-5!"); return; }
    // (Popis proizvoda: if (artikli.length >= 5) { alert(...); return; })

    const ukupno = cijena * brojKarata;
    rezervacije.push({ prezime: prezime, ukupno: ukupno });  // format sa slike!

    // ponovno iscrtaj listu iz niza
    const lista = document.createElement("ol");
    rezervacije.forEach((r, index) => {
        const li = document.createElement("li");
        li.innerText = r.prezime + " - " + r.ukupno + "kn ";
        const btn = document.createElement("button");     // gumb za uklanjanje
        btn.textContent = "Briši";
        btn.dataset.index = index;                          // pamti POZICIJU u nizu
        li.appendChild(btn);
        lista.appendChild(li);
    });
    const div = document.getElementById("popis");
    div.innerHTML = "";
    div.appendChild(lista);
});

// --- UKLONI JEDNU (delegacija, bez fetch-a!) ---
document.getElementById("popis").addEventListener("click", (e) => {
    if (e.target.tagName !== "BUTTON") return;
    const index = Number(e.target.dataset.index);
    rezervacije.splice(index, 1);              // izbaci 1 element na toj poziciji

    // ponovno iscrtaj (isti blok kao gore — indexi se moraju osvježiti!)
    const lista = document.createElement("ol");
    rezervacije.forEach((r, i) => {
        const li = document.createElement("li");
        li.innerText = r.prezime + " - " + r.ukupno + "kn ";
        const btn = document.createElement("button");
        btn.textContent = "Briši";
        btn.dataset.index = i;
        li.appendChild(btn);
        lista.appendChild(li);
    });
    const div = document.getElementById("popis");
    div.innerHTML = "";
    div.appendChild(lista);
});

// --- SPREMI: POST CIJELOG popisa ---
document.getElementById("spremi").addEventListener("click", (e) => {
    e.preventDefault();
    fetch("http://localhost:4000/karte", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ rezervacije: rezervacije }),   // OBLIK SA SLIKE!
    })
        .then((res) => res.json())
        .then((data) => {
            // statistika iz odgovora (šablona 13) ...
            rezervacije = [];                                   // isprazni lokalni popis
            document.getElementById("popis").innerHTML = "";
        })
        .catch((err) => console.log("greška", err));
});
```
Nove stvari ovdje: `push` (dodaj u niz), `splice(index, 1)` (izbaci iz niza),
`forEach((el, index) => ...)` (drugi parametar = pozicija), `dataset.index`.

## ŠABLONA 13: Odgovor je NIZ NIZOVA (Kino)
"dobit ćete niz sa svim NIZOVIMA rezervacija... izračunajte koliko je osoba
i zbroj cijena":
```javascript
let brojOsoba = 0;
let suma = 0;
data.forEach((niz) => {              // vanjski forEach: po popisima
    niz.forEach((r) => {             // unutarnji: po rezervacijama u popisu
        brojOsoba++;
        suma += r.ukupno;
    });
});
document.getElementById("brojOsoba").innerText = brojOsoba;
document.getElementById("cijenaSvih").innerText = suma + "kn";
```
⚠️ Prije pisanja OBAVEZNO console.log(data) — ako su elementi objekti
(npr. {rezervacije: [...]}), unutarnja petlja je `niz.rezervacije.forEach(...)`.

## ŠABLONA 14: Najveći element (Popis proizvoda — "iznos najskupljeg popisa")
```javascript
let brojPopisa = data.length;
let suma = 0;
let najskuplji = 0;
data.forEach((p) => {
    suma += p.cijena;
    if (p.cijena > najskuplji) najskuplji = p.cijena;
});
document.getElementById("spremljeno").innerText = "Spremljeno popisa: " + brojPopisa;
document.getElementById("suma").innerText = "Cijena svih popisa: " + suma + "€";
document.getElementById("najskuplji").innerText = "Cijena najskupljeg popisa: " + najskuplji + "€";
```

## ŠABLONA 15: POST s ugniježđenim nizom (Popis proizvoda)
Format sa slike: { "cijena": 4.7, "artikli": [ {naziv, cijena}, ... ] }
```javascript
let ukupno = 0;
artikli.forEach((a) => { ukupno += a.cijena; });   // ukupna cijena popisa

const zaServer = {
    cijena: ukupno,
    artikli: artikli,        // cijeli lokalni niz ide unutra!
};
```

---

# DIO 3: KOJI ZADATAK KORISTI KOJE ŠABLONE

**Aviokarte (POST + GET):** 0, 1 (klasa+prtljaga), 2 (ul! prvo slovo veliko), 3, 11
**Mobitel / Prijava ispita (identični!):** 0, crveni okviri (rječnik), 2 (ol! uvjetni
  dodatak "- 5G"/"- (POV)"), 10 (.click), 11 (gumb Briši), 9-prikaz odgovora u div
**Koncert:** 0, 1 (sjedalo+trajanje>100), Date objekt, 2 (ol), 3, bez id-a u objektu
**Servis (GET po ID + PUT):** 0, 5 (204!), 4 (popuni formu, disabled), 6 (PUT+postotak)
**Studenti (GET/GET id/DELETE):** 0, 8 (gumbi+delegacija), 7 (svi ključevi), 9, 10
**Kino ulaznice:** 12 (lokalni popis!), 13 (niz nizova), 11
**Popis proizvoda:** 12 (lokalni popis, max 5!), 15, 14 (najskuplji), 2

## Dva TIPA zadataka — prvo prepoznaj koji je!
**TIP A — "server odmah":** svaki unos se ODMAH šalje POST-om (Aviokarte, Mobitel,
Prijava, Koncert). Prepoznaješ: "pritiskom na Spremi/Pošalji potrebno je poslati POST".
**TIP B — "lokalno pa server":** unosi se skupljaju u LOKALNI niz, prikazuju,
mogu se uklanjati, a POST ide tek na kraju s CIJELIM popisom (Kino, Popis proizvoda).
Prepoznaješ: "dodaje se u popis", "logiku lokalnog spremanja po želji",
"tijelo zahtjeva mora sadržavati podatke o trenutnim rezervacijama".

---

# DIO 4: CHECKLIST NA POČETKU ISPITA (5 minuta koje spašavaju sat)
1. Otvori HTML → prepiši sve id="..." (polja, gumbi, divovi/spanovi)
2. Pogledaj sliku formata podataka → nacrtaj putanje (el.cijena? el.detalji.ime?)
3. Odredi tip zadatka (A ili B gore)
4. Podcrtaj u zadatku: ol ili ul? koje provjere? alert ili crveni okvir?
   koji format teksta u listi? koji dodatni izračuni (suma/brojač/postotak/max)?
5. Piši handler po handler, testiraj svaki ODMAH (console.log + F12 konzola!)
