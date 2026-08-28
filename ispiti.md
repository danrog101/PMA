# PMA — Stari kolokviji i ispitni rokovi (8 zadataka)

Zbirka zadataka s prošlih kolokvija/rokova. Svaki zadatak: preuzme se zip s početnim HTML + CSS, dovrši se aplikacija po uputama. Dozvoljena je promjena i dodavanje HTML i CSS dijelova (ne brisanje!).

**Tipovi zadataka:**
- **TIP A** — "server odmah": svaki unos se odmah šalje POST-om
- **TIP B** — "lokalno pa server": unosi se skupljaju lokalno, POST ide na kraju s cijelim popisom

---

## 1. rok — Kino ulaznice (TIP B) — `kino.zip`

### Dodaj proizvod — forma za rezervaciju ulaznica
- Za svaku rezervaciju upisuje se: prezime kupca, cijena karte i broj karata.
- Provjere: prezime ne smije biti prazno niti kraće od 3 znaka; cijena veća od 0; broj karata između 1 i 5 (uključeno). Neispravan unos se ne smije dodati u popis.
- Pritiskom na „Dodaj u popis" stvara se nova rezervacija (ako je unos ispravan) i dodaje u okvir „Popis rezervacija" u obliku `<prezime> - <ukupna cijena>kn` u **numeriranu listu**. Struktura `<li>` elementa po želji (zbog lakšeg dohvata podataka pri slanju).
- **Ukupna cijena = broj karata × pojedinačna cijena.**
- Uz svaku rezervaciju u popisu dodati **button za uklanjanje samo te rezervacije** iz popisa.

### Popis artikala — prikaz i spremanje
- Pritiskom na „Spremi" poslati **POST** na `http://<ip-adresa>:4000/karte`
- Tijelo zahtjeva sadrži podatke o trenutnim rezervacijama. Logika lokalnog spremanja po vlastitoj želji.
- Format podatka (JSON):
```json
{
  "rezervacije": [
    { "prezime": "Ivić", "ukupno": 100 },
    { "prezime": "Smith", "ukupno": 270 }
  ]
}
```
- Nakon uspješnog slanja **izbrisati sve rezervacije iz popisa**.
- Odgovor: **niz sa svim nizovima rezervacija** na poslužitelju. Iz njega izračunati: **koliko je osoba rezerviralo karte** i **zbroj cijena svih karata** → ispisati u odgovarajuće **span** elemente.
- Napomena: poslužitelj na početku već ima dva niza rezervacija (moraju ući u statistiku).

Primjer prikaza: `1. Ivić - 100kn [Briši]` … `Broj osoba sa rezervacijom: 2`, `Cijena svih karata: 370kn`

---

## 2. kolokvij — Aviokarte (TIP A) — `grupaB.zip`

### Nova karta
- Unos: putnik, klasa (radio), opcija prtljage (checkbox).
- Provjera: ime kupca nije kraće od 3 znaka.
- „Spremi" → **POST** na `http://<ip-adresa>:4000/karte` (ne smije se slati ako ime nije ispravno).
- Prije slanja izračunati cijenu: ovisi o klasi (Ekonomska 50€, Poslovna 100€, VIP 200€) + **20€ ako je prtljaga uključena**.
- Format podatka:
```json
{
  "cijena": 70,
  "detalji": {
    "ime": "Maja",
    "klasa": "ekonomska",
    "prtljaga": true
  }
}
```
- *ID se automatski dodaje na poslužitelju.

### Popis karata
- „Dohvati" → **GET** na `http://<ip-adresa>:4000/karte`, odgovor je niz.
- Prikaz u **nenumeriranoj listi** u formatu `klasa – ime`, gdje se za klasu prikazuje **samo prvo slovo veliko** (npr. `P – Ante`, `E – Maja`).
- Ispod liste prikazati **zbroj cijena svih karata** i **broj putnika u VIP klasi**.

Primjer: `• P - Ante`, `• E - Maja`, `• V - Ivana` … `Ukupna cijena: 390 €`, `Broj VIP putnika: 1`

---

## 2. kolokvij — Servis mobitela / POST varijanta (TIP A) — `mobitel.zip`

### Slanje novih zahtjeva — forma s 4 polja i 2 gumba
- 1. polje: model i marka mobitela — ne smije biti prazno **niti kraće od 3 znaka**.
- Serijski broj: **cijeli broj duljine točno 6 znamenki**, ne smije biti prazno.
- Ime vlasnika: ne smije biti prazno.
- Checkbox: je li 5G model (bez provjere).
- „Pošalji" → JSON objekt s podacima → **POST** na `http://<ip_adresa>:4000/mob/novi`. Poslužitelj **ne prihvaća FormData**.
- Format podatka:
```json
{
  "model": "Huawei P30",
  "serial": 998415,
  "vlasnik": "Marko",
  "petG": true
}
```
- „Briši": briše sve vrijednosti unosa, **bez slanja zahtjeva**.
- Forma ne smije slati ako je bilo koji podatak krivo unesen — **okvire SVIH neispravnih polja obojiti crveno**; ako su svi ispravni, ukloniti crveni okvir.
- Nakon ispravnog slanja prikazati **poruku iz odgovora** u DIV `id="odgovor"` (postoji u početnoj verziji). Odgovor je u JSON formatu.

### Popis zahtjeva
- „Učitaj" → **GET** na `http://<ip_adresa>:4000/mob/svi`, odgovor je niz.
- Prikaz u **numeriranoj listi** u formatu `Serijski broj – Vlasnik – 5G` (5G samo ako je 5G model), u DIV `id="rezultat"` (postoji u početnoj verziji).
- Nakon uspješnog slanja novog zahtjeva **automatski ažurirati popis** (bez klika na „Učitaj").

Primjer: `1. 356187 - Ivan`, `2. 358711 - Ana - 5G`, `3. 998415 - Marko - 5G`

---

## 2. kolokvij — Prijava ispita (TIP A) — `prijava.zip`
*(strukturno identičan Mobitelu — ista pravila, druga imena!)*

### Slanje novih prijava — forma s 4 polja i 2 gumba
- Ime studenta: ne smije biti prazno **niti kraće od 3 znaka**.
- Naziv kolegija: ne smije biti prazan.
- Šifra kolegija: **cijeli broj točno 6 znamenki**, ne smije biti prazno.
- Checkbox: polaganje pred povjerenstvom (bez provjere).
- „Pošalji" → **POST** na `http://<ip_adresa>:4000/ispiti/novi`. Ne prihvaća FormData.
- Format podatka:
```json
{
  "student": "Ema Milić",
  "kolegij": "OOP",
  "oznaka": 356187,
  "povjerenstvo": false
}
```
- „Briši": briše unose bez slanja.
- Neispravan unos → **crveni okviri svih neispravnih polja**; ispravni → ukloniti okvir.
- Poruku iz odgovora prikazati u DIV `id="odgovor"`. Odgovor je JSON.

### Popis prijava
- „Učitaj" → **GET** na `http://<ip_adresa>:4000/ispiti/svi`.
- **Numerirana lista** u formatu `Kolegij – Student – (POV)` (POV samo ako je pred povjerenstvom), u DIV `id="rezultat"`.
- Nakon uspješnog slanja **automatski ažurirati popis**.

Primjer: `1. PMA - Ivan Perić`, `2. OOP - Ana Horvat - (POV)`, `3. OOP - Ema Milić`

---

## 2. rok — Servis mobitela / GET+PUT varijanta — `servis.zip`

### Učitaj zapis
- „Učitaj" → **GET** na `http://<ip-adresa>:4000/zahtjev/<id>` gdje je `<id>` broj iz polja za unos. Nije potrebna provjera unosa.
- Napomena: poslužitelj ima zapise ID 0–9, ali **nije dozvoljeno ograničavanje unosa** — mora se moći poslati bilo koji ID.
- Ako zapis ne postoji: poslužitelj vraća **status 204 (No Content)** bez sadržaja → ispisati `Ne postoji podatak sa ovim ID-om` u `<p id="greska">`.
- Ako postoji: status 200 + podatak u JSON obliku:
```json
{
  "id": 3,
  "podaci": {
    "vlasnik": "Josipa",
    "broj": 357,
    "popravljen": true
  }
}
```
- Dobivene detalje prikazati u **poljima za unos** donjeg dijela sučelja (ID, vlasnik, serijski broj, popravljen).

### Ažuriranje zapisa
- Nakon učitavanja moguće je mijenjati podatke; **onemogućiti mijenjanje ID-a**.
- „Ažuriraj" → **PUT** na istu adresu s koje je podatak dohvaćen; tijelo = ažurirani podatak u **istom obliku** kao dohvaćeni.
- Provjere prije slanja: vlasnik i serijski broj ne smiju biti prazni; **serijski broj u rasponu 100–999 (uključeno)**.
- Odgovor na ispravan PUT: niz svih spremljenih zapisa → izračunati i ispisati **postotak popravljenih mobitela** u `<p id="postotak">`.

---

## 3. rok — Popis studenata — `studenti.zip`

*(Napomena: prikaz ne mora biti oblikovan identično slikama.)*

### Učitaj osnovno
- „Učitaj" → **GET** na `http://<ip_adresa>:4000/studenti` → JSON niz objekata s ID-om i imenom studenta.
- U DIV `id="popis"` prikazati sve elemente **jedan ispod drugoga** (ID i ime).
- Uz svaki podatak dodati **button „Detalji"**.

### Prikaz detalja
- Klik na „Detalji" → **GET** na `http://<ip_adresa>:4000/studenti/<id>` (ID pripadajućeg studenta).
- Odgovor: JSON objekt s detaljnim podacima o studentu (može sadržavati i nizove, npr. kolegiji!).
- U DIV `id="detalji"` **ispisati SVE podatke** iz odgovora.
- Ispod detalja dodati **button „Briši"** → **DELETE** na `http://<ip_adresa>:4000/studenti/<id>` (ID trenutno prikazanog studenta).
- Nakon uspješnog brisanja poslužitelj vraća niz preostalih studenata. Osvježiti stranicu: **isprazniti „detalji"** i u „popis" prikazati aktualne podatke s poslužitelja (samo ID i imena neizbrisanih).

Primjer detalja: `ID: 101, Student: Ivana, Razina: D, Smjer: INF, Kolegiji: [Paralelno Prog., Strojno učenje, OARWA, UUI], Redovni: true [Briši]`

---

## 3. ispitni rok — Koncert (TIP A) — `grupaA.zip`

### Unos podataka
- Unos: izvođač (ime), trajanje, pozicija sjedala (radio); datum kupnje se generira.
- Provjera: ime nije kraće od 3 znaka.
- „Spremi" → **POST** na `http://<ip-adresa>:4000/koncert` (ne smije se slati ako ime nije ispravno i datum nije definiran).
- **Datum se mora dohvatiti pomoću objekta `Date`.**
- Prije slanja izračunati cijenu: ovisi o poziciji sjedala (VIP 10€, Regular 5€, FanPit 2€) **+ 5€ ako je trajanje > 100 minuta**.
- Format podatka:
```json
{
  "cijena": 10,
  "datum": "8/20/2025",
  "detalji": {
    "ime": "Urban",
    "sjedalo": "vip",
    "trajanje": 60
  }
}
```
- *ID se dodaje automatski na serveru.

### Prikaz podataka
- „Dohvati" → **GET** na `http://<ip-adresa>:4000/koncert` → niz svih podataka.
- **Numerirana lista** u formatu `sjedalo – ime`.
- Ispod liste: **zbroj cijena svih narudžbi** i **broj narudžbi s VIP pozicijom**.

Primjer: `1. fanpit – Daleka obala`, `2. regular – Bijelo Dugme`, `3. vip – Urban` … `Ukupna cijena: 32 €`, `Broj VIP: 1`

---

## Popis proizvoda (TIP B) — `popis.zip`

### Dodaj proizvod — forma za dodavanje proizvoda u trenutni popis
- Za svaki proizvod: naziv i cijena.
- Provjere: naziv ne smije biti prazan **niti kraći od 4 znaka**; cijena mora biti **veća od 0**. Neispravan unos se ne smije dodati u popis.
- „Dodaj u popis" (ako je unos ispravan) → novi proizvod u okvir „Popis artikla" u obliku `<naziv> - <cijena>€` u **numeriranu listu**. Struktura `<li>` po želji.
- **Popis ne smije sadržavati više od 5 artikala.** Ako korisnik pokuša unijeti više od 5, ispisati poruku u **`alert`** prozoru.

### Popis artikla — prikaz trenutnog popisa i spremanje
- „Spremi" → **POST** na `http://<ip-adresa>:4000/popis`
- Tijelo: podaci o trenutnom popisu — **niz artikala i ukupna cijena**. Logika lokalnog spremanja po želji.
- Format podatka:
```json
{
  "cijena": 4.7,
  "artikli": [
    { "naziv": "Kava", "cijena": 2 },
    { "naziv": "Mlijeko", "cijena": 1.7 },
    { "naziv": "Kruh", "cijena": 1 }
  ]
}
```
- Nakon uspješnog slanja **izbrisati sve artikle iz popisa**.
- Odgovor (kod ispravnog POST-a): **niz sa svim popisima** spremljenima na poslužitelju (JSON). Iz njega izračunati: **koliko je ukupno popisa spremljeno**, **zbroj cijena svih popisa** i **iznos najskupljeg popisa** → ispisati u odgovarajuće **span** elemente.
- Napomena: podaci moraju biti izračunati iz dobivenog odgovora (ne „lokalno" brojanje!); server već sadrži nekoliko spremljenih popisa.

Primjer: `1. Kava - 2€`, `2. Mlijeko - 1.7€`, `3. Kruh - 1€` … `Spremljeno popisa: 2`, `Cijena svih popisa: 11.19€`, `Cijena najskupljeg popisa: 6.49€`

# Grupa B - Novi zaposleník

Sa stranice kolegija preuzimite datoteku `"GrupaB.zip"` i otpakurajte arhivu na računalo. U datoteci se nalazi početni izgled aplikacije (HTML + CSS). Dovršite aplikaciju prema uputama ispod. Dozvoljenja je promjena i dodavanje HTML i CSS dijelova (ne brisanje!)

## Novi zaposleník

Kroz sučelje je potrebno unijeti podatke o novom zaposleniku – **ime i prezime, email adresu, godinu rođenja, kolegij i status** (redovni/vanzredni). Ime i prezime skupna ne smiju biti kraće od **6 znakova**, email mora imati znak '@'. Svi podaci moraju biti uneseni. Redovni student mora imati godinu rođenja >1990. (Grešku ispišite u <p> element sa id=„greska")

Prije slanja zahtjeva trebno je izračunati broj bodova koje student nosi za prijavu – osnovna plača ovisi o kolegiju (Informatika: 50€, ostali: 100€). Ako je kolegij Informatika, povećava se ukupan broj bodova za 5%. Ako student ima status redovnog studenta, dodaje se 10 dodatnih bodova, ako je kolegij informatika, povećava se broj bodova za 5%.

Nakon unosa svih podataka, pritiskom na tipku „Dodaj" trebno je poslati **POST zahtjev** na adresu http://<ip-adresa>:4000/studenti (ne smije se slati ako provjere nisu dobre).
Format JSON podatka kojeg poslužitelj prihvaća možete vidjeti na slici (i poslužitelju)

Nakon slanja podatka, očistite podatke sa forme.

## Učitaj zapis

Kroz sučelje je potrebno unijeti podatak za pretrežvanje zaposlenika. Pritiskom na tipku „Pretraži" trebno je poslati **GET zahtjev** http://<ip-adresa>:4000/studenti sa parametrom „kolegiji" i „godinaRođenja".

Poslužitelj kao odgovor vraća niz sa svim zaposlenicima na traženom poziciji stariji od tražene godine u JSON obliku.

Kada dohvatanja podataka trebno ih je prikazati na sučelje, primjer možete vidjeti na slici (ne mora biti identičan)

ID se automatski dodaje na poslužitelju.

---
---

## 📄 **GRUPA_C_NOVI_SPORTAŠ.md**

````markdown
# Grupa C - Novi sportaš

Sa stranice kolegija preuzimite datoteku `"GrupaC.zip"` i otpakurajte arhivu na računalo. U datoteci se nalazi početni izgled aplikacije (HTML + CSS). Dovršite aplikaciju prema uputama ispod. Dozvoljenja je promjena i dodavanje HTML i CSS dijelova (ne brisanje!)

## Novi sportaš

Kroz sučelje je potrebno unijeti podatke o novom sportašu – **ime i prezime, email adresu, godinu rođenja, sport i status** (profesionalac/amateri). Ime i prezime skupna ne smiju biti kraće od **5 znakova**, email mora imati znak '@'. Svi podaci moraju biti uneseni. Profesionalni sportaš mora imati godinu rođenja 1980-2005. (Grešku ispišite u <p> element sa id=„greska")

Prije slanja zahtjeva trebno je izračunati broj treninga tjedno po sportu – osnovna plača danu ovisi o sportu (nogomет: 4, tenis: 3, plivanje: 5). Ako je sportaš profesionalan i njegov se 2 dodatna treninga na osnovni, a ako je sport plivanje se povećava dodat 10%.

Nakon unosa svih podataka, pritiskom na tipku „Dodaj" trebno je poslati **POST zahtjev** na adresu http://<ip-adresa>:4000/sportasi (ne smije se slati ako provjere nisu dobre).
Format JSON podatka kojeg poslužitelj prihvaća možete vidjeti na slici (i poslužitelju)

Nakon slanja podatka, očistite podatke sa forme.

## Učitaj zapis

Kroz sučelje je potrebno unijeti podatak za pretrežvanje sportaša. Pritiskom na tipku „Pretraži" trebno je poslati **GET zahtjev** http://<ip-adresa>:4000/sportasi sa parametrom „sport".

Poslužitelj kao odgovor vraća niz sa svim sportašima u traženom sportu stariji od tražene godine u JSON obliku.

Kada dohvatanja podataka trebno ih je prikazati na sučelje, primjer možete vidjeti na slici (ne mora biti identičan)

ID se automatski dodaje na poslužitelju.

---
## 📄 **3_ISPITNI_ROK_KONCERT.md**

````markdown
# 3. Ispitni rok - Koncert

Sa stranice kolegija preuzimite datoteku `"grupaA.zip"` i otpakurajte arhivu na računalo. U datoteci se nalazi početni izgled aplikacije (HTML + CSS). Dovršite aplikaciju prema uputama ispod. Dozvoljenja je promjena i dodavanje HTML i CSS dijelova (ne brisanje!)

## Unos podatka

Kroz sučelje je potrebno unijeti podatke o novom koncertu – **izvođač, trajanje u minutama, pozicija sjedala (radio buttoni)**. Izvođač nije biti kraći od **3 znaka**.

Pritiskom unosa trebino je izračunati cijenu karte – cijena ovisi o poziciji sjedala (VIP (10€), Regular (5€), FanPit (2€)) + dodatnih 5€ ako je trajanje **veće od 100 minuta**.

Format JSON podatka kojeg poslužitelj prihvaća možete vidjeti na slici (i poslužitelju)

Nakon slanja zahtjeva trebno je izbrisati vrijednosti iz forme.

## Prikaz podataka

Pritiskom na tipku „Dohvati" trebno je poslati **GET zahtjev** na http://<ip-adresa>:4000/koncert.

Poslužitelj kao odgovor vraća niz sa svim kartama i trebam izračunati cijenu karte – cijena ovisi o poziciji sjedala i kojih dužine koncerta i brojati koliko je VIP sjedala.

Prikazati podatke o naraždbama u numeriranoj listi u formatu „**sjedalo – ime**", gdje narudžbi te broj narudžbi koji je VIP poziciji.

---

