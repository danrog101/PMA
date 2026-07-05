# PMA Kolokvij — Pitanja i odgovori (objašnjenja)

Odgovori na najčešća pitanja/nejasnoće kod rješavanja kolokvijskih zadataka.

---

## 1. Datum — treba li `"en-US"`?

`new Date().toLocaleDateString()` **bez argumenta** koristi jezik preglednika — u Hrvatskoj daje `"20. 08. 2025."`. Na slici zadatka piše `datum: 8/20/2025` — američki format (mjesec/dan/godina). Zato `"en-US"`:

```javascript
new Date().toLocaleDateString()         // "20. 08. 2025."  (hrvatski)
new Date().toLocaleDateString("en-US")  // "8/20/2025"      (kao na slici!)
```

**Pravilo:** pogledaj format na slici u zadatku i odaberi. Ako slika ima točkice i hrvatski redoslijed — dovoljno je bez argumenta.

---

## 2. Što su `<span>` elementi?

`<span>` je HTML element za komadić teksta — kao `<div>`, ali "u liniji" (ne prelama red). Zadaci ga koriste kao mjesto gdje ubaciš samo broj:

```html
<p>Broj osoba sa rezervacijom: <span id="brojOsoba"></span></p>
```

```javascript
document.getElementById("brojOsoba").innerText = brojac;  // ubaci broj u prazan span
```

Za tebe je **potpuno isto kao div** — nađeš ga po ID-u i upišeš tekst.

---

## 3. `for...in` vs `forEach` — kada koji?

**Ključna razlika:**

| | radi na | znači |
|---|---|---|
| `forEach` | **NIZU** `[...]` | "za svaki element niza" |
| `for...in` | **OBJEKTU** `{...}` | "za svaki ključ objekta" |

Kod detalja studenta server vraća **jedan objekt**, ne niz:

```javascript
{ id: 101, student: "Ivana", razina: "D", kolegiji: ["OARWA", "UUI"], redovni: true }
```

Na tome `data.forEach` **ne postoji** — srušilo bi se. Zato:

```javascript
for (let key in data) {                     // key redom postaje "id", "student", "razina"...
    const li = document.createElement("li");
    li.innerText = key + ": " + JSON.stringify(data[key]);
    ul.appendChild(li);
}
```

`JSON.stringify(data[key])` tu služi jer vrijednost može biti **niz** (kolegiji!). Običan `+ data[key]` bi za niz ispisao ružno; stringify sve pretvori u čitljiv tekst: `kolegiji: ["OARWA","UUI"]`.

**Kako znaš koji koristiti?** Pogledaj što server vraća: `[...]` na početku → niz → `forEach`; `{...}` → objekt → `for...in`.

---

## 4. `filter` vs brojač u `forEach`

Oba rade isto — biraj što ti je draže! `filter` izdvoji elemente koji zadovoljavaju uvjet u novi niz, pa `.length` prebroji:

```javascript
// s filterom (jedna linija):
const popravljeni = svi.filter((el) => el.podaci.popravljen).length;

// ISTO s forEach i brojačem:
let popravljeni = 0;
svi.forEach((el) => {
    if (el.podaci.popravljen) popravljeni++;
});
```

Ako ti je forEach + brojač prirodniji — koristi samo njega svugdje, `filter` nije obavezan. Postotak onda:

```javascript
const post = (popravljeni / svi.length) * 100;
```

---

## 5. `.click()` — u koji then ide?

**U drugi.** Prvi then samo pretvara odgovor (`res.json()`), drugi radi s podacima:

```javascript
fetch(..., { method: "POST", ... })
    .then((res) => res.json())        // 1. then: pretvori odgovor
    .then((data) => {                 // 2. then: TU radiš stvari
        document.getElementById("odgovor").innerText = JSON.stringify(data);
        document.getElementById("ucitaj").click();   // auto-refresh popisa
    })
    .catch((err) => console.log("greška", err));
```

---

## 6. `disabled = true` — gdje ide?

**Unutar drugog then-a GET-a po ID-u**, odmah nakon punjenja polja — ID zaključavaš tek kad je zapis učitan:

```javascript
.then((data) => {
    if (!data) return;
    document.getElementById("id_rez").value = data.id;
    // ...ostala polja...
    document.getElementById("id_rez").disabled = true;   // ← tu, na kraju punjenja
})
```

**Ne na vrh skripte!**

---

## 7. Gdje kreiram listu (`createElement("ol")`)?

**Unutar drugog then-a, svaki put iznova.** NE prije handlera!

Logika: svaki klik na Učitaj/Dohvati donese svježe podatke → napraviš novu praznu listu → napuniš je → zamijeniš staru (`innerHTML = ""` + `appendChild`).

Jedino što ide **na vrh skripte prije handlera** je lokalni niz kod TIP B zadataka (Kino, Popis proizvoda):

```javascript
let rezervacije = [];   // mora živjeti IZMEĐU klikova → izvan handlera
```

---

## 8. Gumb "Briši" — zašto ga ne kreiram s createElement?

Jer **već postoji u HTML-u**! Forma u početnoj verziji aplikacije ima gumbe Pošalji i Briši. Ti mu samo dodaš listener:

```javascript
document.getElementById("brisi").addEventListener("click", (e) => {
    e.preventDefault();
    document.getElementById("ime").value = "";
    document.getElementById("check").checked = false;
});
```

`createElement("button")` radiš **samo za gumbe koji se stvaraju dinamički** — po jedan uz svaku stavku liste (Detalji/Briši kod studenata). Njih ne može biti u HTML-u jer ne znaš unaprijed koliko će stavki biti.

---

## 9. Punjenje polja forme — kada?

Šablona ispod ide **samo kod GET-a po ID-u** ("prikažite detalje zapisa u poljima za unos"):

```javascript
document.getElementById("id_rez").value = data.id;
document.getElementById("ime").value = data.podaci.vlasnik;
document.getElementById("serijski").value = data.podaci.broj;
document.getElementById("popravljen").checked = data.podaci.popravljen;  // checkbox = .checked!
document.getElementById("id_rez").disabled = true;
```

---

## 10. Točke/putanje — odakle znam `data.podaci.vlasnik`?

**Iz strukture podataka na serveru** — slika u zadatku ili datoteka tipa `rezervacije.js`. Točka = "uđi razinu dublje":

```javascript
{
  "id": 3,                        →  data.id
  "podaci": {                     →  data.podaci
    "vlasnik": "Josipa",          →  data.podaci.vlasnik
    "broj": 357                   →  data.podaci.broj
  },
  "popravljen": true              →  data.popravljen   (VANI! ne data.podaci.popravljen)
}
```

**Svaka `{` znači još jednu točku.** Isto vrijedi u filteru/forEach-u: `el.podaci.popravljen` jer je `el` jedan takav objekt iz niza.

Savjet: prije pisanja koda, pored svake vrijednosti na slici doslovno napiši putanju.

---

## 11. `dataset` — kad i zašto?

Samo kod **dinamičkih gumba u listi**. Problem: imaš 10 studenata → 10 gumba "Detalji" — kad netko klikne, kako znaš KOJI student? Rješenje: pri stvaranju gumba na njega zalijepiš id:

```javascript
btn.dataset.id = st.id;    // gumb u HTML-u postane: <button data-id="101">
```

Kasnije u handleru pročitaš: `e.target.dataset.id`. Gumb "nosi" svoju informaciju.

---

## 12. `e.target.tagName` + JEDNOSTAVNIJA varijanta (bez dataset i delegacije)

`e.target` = element koji je stvarno kliknut; `.tagName` = njegov tip (`"BUTTON"`, `"LI"`, `"UL"`...). Provjera `if (e.target.tagName !== "BUTTON") return;` služi da handler na roditelju ignorira klikove mimo gumba.

**Ali postoji varijanta bez svega toga** — listener zakačiš direktno na gumb u trenutku stvaranja:

```javascript
data.forEach((st) => {
    const li = document.createElement("li");
    li.textContent = "id: " + st.id + " - student: " + st.student + " ";

    const btn = document.createElement("button");
    btn.textContent = "Detalji";
    btn.addEventListener("click", () => {        // listener ODMAH na gumb
        fetch(`http://localhost:4000/studenti/${st.id}`)   // st.id direktno vidljiv!
            .then((res) => res.json())
            .then((student) => { /* ... */ });
    });

    li.appendChild(btn);
    lista.appendChild(li);
});
```

Nema dataset, nema tagName, nema delegacije — gumb pamti `st.id` sam od sebe (closure). **Ako ti je ovo lakše, koristi ovo.** Delegacija (+ dataset + tagName) treba samo ako se traže odvojeni handleri za svaku operaciju.

---

## 13. `splice` + varijanta bez njega

`rezervacije.splice(index, 1)` = "od pozicije `index` izbaci 1 element iz niza".

Ako splice niste učili, ista stvar s filterom:

```javascript
// makni element na poziciji index:
rezervacije = rezervacije.filter((r, i) => i !== index);
// čitaj: "zadrži sve elemente čija pozicija NIJE index"
```

Napomena: `forEach` i `filter` daju **drugi parametar = pozicija elementa**:

```javascript
rezervacije.forEach((r, index) => { ... });   // r = element, index = 0, 1, 2...
```

---

## 14. Niz u nizu — kako ga prepoznati i obraditi

Server (Kino zadatak) vraća:

```javascript
[                                                                     ← vanjski niz (popisi)
  [ {prezime:"Ivić", ukupno:100}, {prezime:"Smith", ukupno:270} ],    ← unutarnji niz #1
  [ {prezime:"Anić", ukupno:50} ]                                     ← unutarnji niz #2
]
```

**Dvije razine `[` = dva forEach-a, jedan u drugom:**

```javascript
let brojOsoba = 0;
let suma = 0;

data.forEach((niz) => {              // vanjski: po popisima
    niz.forEach((r) => {             // unutarnji: po rezervacijama unutar popisa
        brojOsoba++;
        suma += r.ukupno;
    });
});

document.getElementById("brojOsoba").innerText = brojOsoba;
document.getElementById("cijenaSvih").innerText = suma + "kn";
```

---

## ZLATNO PRAVILO (sažetak svega)

Pola odgovora na "koliko točaka?", "forEach ili for...in?", "jedan ili dva forEach-a?" **uvijek se čita iz oblika podataka**, ne pamti napamet:

- `{...}` objekt → točka za svaku razinu, `for...in` za nepoznate ključeve
- `[...]` niz → `forEach`
- `[[...]]` niz u nizu → `forEach` u `forEach`-u
- ne znaš što je stiglo → `console.log(data)` pa pogledaj u konzolu (F12)!
