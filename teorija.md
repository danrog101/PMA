# PMA — Najbitnija teorija za kolokvij

Sažetak cijelog gradiva: HTML, CSS i JavaScript. Fokus na ono što se pita na ispitu.

---

# 1. DIO — HTML

## HTML osnove
- HTML definira **strukturu i značenje** sadržaja, NE izgled (izgled je CSS).
- **id** = jedinstven (samo 1 element po stranici), znak `#` u CSS-u.
- **class** = može više elemenata dijeliti, znak `.` u CSS-u.
- Element može imati više klasa: `class="vazno zadnje"` (odvojeno razmakom).

## Blok vs Inline
- **BLOK** (novi red, cijela širina): `div, p, h1-h6, ul, ol, li, section, header, footer`
- **INLINE** (isti red, koliko treba): `span, a, em, strong, img, b, i`

## Samozatvarajuće oznake (nemaju zatvarajući tag)
`img, br, hr, input, meta, link, source`
- Normalne (trebaju zatvaranje): `p, div, h1, span, a, ul, li, strong...`
- Trik: ako omata sadržaj → treba zatvaranje. Ako je samostalna → samozatvarajuća.

## DOCTYPE i meta
- `<!DOCTYPE html>` → deklarira HTML5.
- `<meta charset="UTF-8">` → skup znakova (za č, ž, š).
- `<meta name="viewport">` → prilagodba na mobitelu.

---

# 2. DIO — CSS

## Pravilo
```css
selektor {
  svojstvo: vrijednost;
}
```

## Mjerne jedinice
- **Apsolutne** (fiksne): px, cm, in, pt
- **Relativne**: % (roditelj), em/rem (font), vh/vw (viewport)

## Selektori
| Selektor | Znak | Odabire |
|----------|------|---------|
| Univerzalni | `*` | sve |
| Tip | `p` | sve tog tipa |
| ID | `#ime` | element s tim id |
| Klasa | `.ime` | sve s tom klasom |
| Atribut | `a[href]` | s tim atributom |
| Pseudo-klasa | `:hover` | stanje |
| Pseudo-element | `::before` | dio elementa |

- `p.vazno` = p elementi s klasom vazno
- `p.vazno.zadnje` = p s OBJE klase
- `a[href^="https"]` = **počinje** s https (`^`)
- `a[href$=".hr"]` = **završava** s .hr (`$`)
- `a[href*="x"]` = **sadrži** x (`*`)

## Pseudo-klase vs Pseudo-elementi
- **Pseudo-KLASA** = JEDNA `:` → stanje (`:hover, :visited, :checked, :first-child`)
- **Pseudo-ELEMENT** = DVIJE `::` → dio (`::before, ::after, ::first-letter`)
- Trik: DVIJE dvotočke = ELEMENT.

## Kombinatori
- razmak = potomak (`div p`)
- `>` = direktno dijete (`ul > li`)
- `+` = susjed odmah nakon (`h1 + p`)
- `~` = svi na istoj razini nakon (`h1 ~ p`)

---

# 3. CSS KASKADA (tko pobjeđuje)

Redom prioriteta (**V-I-S-R**):
1. **Važnost** (`!important`) — najjača
2. **Izvor** (preglednik < korisnik < programer)
3. **Specifičnost** (koliko precizan selektor)
4. **Redoslijed** (zadnje napisano pobjeđuje ako je sve isto)

- `!important` je UVIJEK najjača (i nad specifičnijim selektorom).

---

# 4. SPECIFIČNOST (kako se računa)

Piše se kao **(a, b, c)**:
- **a** = broj ID-eva (`#`)
- **b** = broj klasa, atributa, pseudo-klasa (`.`, `[]`, `:`)
- **c** = broj elemenata (tagova), pseudo-elemenata (`::`)
- `*` i kombinatori (`> + ~`) se NE broje.

### Primjeri
- `p` → (0,0,1)
- `.gumb` → (0,1,0)
- `#glava` → (1,0,0)
- `div p` → (0,0,2)
- `.nav a` → (0,1,1)
- `#meni li a` → (1,0,2)
- `.nav .stavka a:hover` → (0,3,1)

### Usporedba
Gledaš brojeve **slijeva nadesno**. Veći prvi broj pobjeđuje odmah.
- (1,0,0) vs (0,5,5) → **(1,0,0)** pobjeđuje (id jači od svih klasa)
- (0,2,0) vs (0,1,5) → **(0,2,0)** (prvi isti, drugi 2>1)

Pravilo: **id > klasa > element**, bez obzira na količinu.

---

# 5. NASLJEĐIVANJE

- **NASLJEĐUJE SE** (tekst/font): `color, font-family, font-size, font-weight, text-align, line-height`
- **NE NASLJEĐUJE SE** (kutija): `width, height, margin, padding, border, background`
- Trik: izgled slova → nasljeđuje. Kutija/prostor → ne nasljeđuje.
- Vrijednosti: `inherit` (naslijedi), `initial` (zadano), `unset`, `revert`.

---

# 6. BOX MODEL

Četiri sloja **iznutra van**: **content → padding → border → margin**
- **content** = sadržaj
- **padding** = razmak unutar (do okvira), ima boju pozadine
- **border** = okvir
- **margin** = razmak izvan (do drugih), proziran

## Preklapanje margina
- Vertikalne margine se **PREKLAPAJU** (uzima se veća, ne zbraja).
- Horizontalne se NE preklapaju.
- Kod **flexboxa NEMA** preklapanja.

## box-sizing (ČEST ZADATAK)
- **content-box** (zadano): width = samo sadržaj. Stvarna širina = **width + padding×2 + border×2**
- **border-box**: width UKLJUČUJE padding i border. Stvarna širina = **točno width**.

Primjer (content-box): width:300 + padding:20 + border:2 → 300 + 40 + 4 = **344px**
Isti s border-box → **300px**.

---

# 7. FLEXBOX (jednodimenzionalno)

- `display: flex` na spremniku.
- **glavna os** ← `justify-content`
- **sporedna os** ← `align-items`
- `flex-direction`: row (zadano), row-reverse, column, column-reverse
- Vrijednosti justify-content/align-items: `flex-start, flex-end, center, space-between, space-around, space-evenly`

## Svojstva
| Svojstvo | Gdje | Što |
|----------|------|-----|
| justify-content | spremnik | glavna os |
| align-items | spremnik | sporedna os |
| flex-direction | spremnik | smjer glavne osi |
| gap | spremnik | razmak |
| order | element | redoslijed |
| align-self | element | sporedna os za taj element |
| flex | element | grow shrink basis |

- `flex-grow: 2` (a ostali 1) = taj element zauzima **duplo više** slobodnog prostora.
- `flex` je kratica za **flex-grow, flex-shrink, flex-basis**.

---

# 8. GRID (dvodimenzionalno)

- `display: grid`
- `grid-template-columns: repeat(3, 1fr)` = 3 stupca jednake širine
- `fr` = frakcija slobodnog prostora
- `grid-column: 1 / 4` = od linije 1 do 4; `-1` = zadnja linija

---

# 9. POZICIONIRANJE

| position | Ponašanje |
|----------|-----------|
| static | zadano |
| relative | pomak od SVOJE pozicije (ostali se ne miču) |
| absolute | od najbližeg POZICIONIRANOG roditelja (izlazi iz toka) |
| fixed | od PROZORA, ne miče se pri scrollu |
| sticky | lijepi se kad dođe do granice |

- `z-index`: veći = ISPRED, negativan = iza.

---

# 10. PRILAGODLJIVI DIZAJN

- `@media (min-width: 768px) {}` = ekrani ŠIRI od 768px
- `@media (max-width: 600px) {}` = ekrani UŽI od 600px
- Tipovi: all, print, screen, speech
- Operatori: and, not, only. **OR ne postoji** — koristi zarez.
- **Mobile first**: prvo mobitel, pa veći ekrani. Razlog: upiti imaju prednost + mobiteli sporiji + lakša nadogradnja.
- Obavezan `<meta name="viewport">`.

---

# 11. SLIKE, ANIMACIJE, FONTOVI

- **SVG = vektorska** (ne gubi kvalitetu). Fotografije = **rasterska** (pikseli).
- `srcset` = više izvora (1x, 2x). `<picture>` + `<source>` za različite prikaze.
- `transform`: translate, scale, rotate, skew
- Animacija: `@keyframes` (faze **0%–100%**) + `animation-*` (name, duration, timing-function, delay, iteration-count, direction)
- Fontovi: Google Fonts (link/@import) ili lokalno `@font-face`.

---

# 12. DIO — JAVASCRIPT

## Tipovi podataka
- **Primitivni (jednostavni)**: number, string, boolean, null, undefined, symbol, bigint
- **Složeni (referentni)**: **object, array, function** (spremaju se po REFERENCI)

## typeof (napamet)
- `typeof "x"` → "string"
- `typeof 5` → "number"
- `typeof true` → "boolean"
- `typeof undefined` → "undefined"
- `typeof null` → **"object"** (bug!)
- `typeof function(){}` → "function"

## Reference (VAŽNO)
```js
let a = 5; let b = a; b = 10;   // a je i dalje 5 (primitiv - kopija)

const x = [1,2,3]; const y = x; y.push(4);  // x je [1,2,3,4]! (referenca - dijele isti niz)
const o1 = {a:1}; const o2 = {a:1};
o1 === o2;   // false (dva različita objekta)
```

## Falsy vrijednosti (samo 6!)
`false, 0, "", null, undefined, NaN`
- Sve ostalo TRUTHY, uključujući: `"0"`, `[]`, `{}`, `" "`, `"false"` (svi truthy!)

## Operatori
- `||` (ILI) → vraća **prvi TRUTHY** (ili zadnji ako svi falsy)
- `&&` (I) → vraća **prvi FALSY** (ili zadnji ako svi truthy)
- `??` (nullish) → vraća **prvi definiran** (preskače samo null/undefined, NE 0/"")

## == vs ===
- `==` pretvara tipove (`"5" == 5` → true)
- `===` provjerava i tip (`"5" === 5` → false)
- `null == undefined` → true, ali `null == 0` → false
- `undefined == 0` → false
- `[] == 0` → true, `" " == 0` → true
- `broj + ""` → daje STRING (`3 + 2 + ""` → "5")

## var / let / hoisting
```js
console.log(a); var a = 10;   // undefined (var se hoista bez vrijednosti)
console.log(b); let b = 10;   // GREŠKA (ReferenceError)
```
- `var` = funkcijski doseg, hoista se
- `let/const` = blokovski doseg
- Deklaracija funkcije se hoista, function expression ne

## Scope / shadowing
```js
const boja = "crvena";
function f() { const boja = "plava"; }  // nova lokalna
f();
console.log(boja);  // crvena (vanjska netaknuta)
```
- S `const/let` u funkciji = nova lokalna (vanjska ostaje)
- Bez `const/let` = mijenja vanjsku

## Funkcije
- Default parametar: `function f(a, b = 10)` → ako b nije poslan, koristi 10
- Rest: `function f(...args)` → skuplja argumente u niz
- Arrow: `(a, b) => a + b`
- **Arrow funkcija NEMA svoj this** (nasljeđuje iz okoline) — obična funkcija ima
- `async function` UVIJEK vraća **Promise**

## Nizovi (metode)
- `.forEach()` → prolazi kroz svaki (ne vraća)
- `.map()` → transformira svaki, vraća NOVI niz
- `.filter()` → vraća novi niz s elementima koji zadovoljavaju uvjet
- `arr[5]` na kratkom nizu = **undefined** (ne greška)
- `for...in` → daje **INDEKSE** (0,1,2)
- `for...of` → daje **VRIJEDNOSTI**
- `break` prekida petlju, `continue` preskače iteraciju

## Objekti — pristup vrijednosti
```js
let osoba = { ime: "Petar", adresa: { grad: "Split" } };
osoba.ime              // "Petar" (dot)
osoba["ime"]           // "Petar" (bracket)
osoba.adresa.grad      // "Split" (ugniježđeno)
```

---

# 13. DOM

## nodeType brojevi
- **1 = element** (div, p)
- **3 = tekst**
- **8 = komentar** (`<!-- -->`)
- **9 = document**

## Selekcija
- `document.getElementById("id")` → jedan po id
- `document.querySelector(".klasa")` → **PRVI** element (CSS selektor)
- `document.querySelectorAll(".klasa")` → **SVI** elementi
- `getElementsByClassName` → svi s klasom

## children vs childNodes
- `children` / `childElementCount` / `firstElementChild` → samo ELEMENTI (ignoriraju tekst/komentare)
- `childNodes` / `firstChild` → SVE (tekst, komentari)

## Manipulacija sadržaja
- `innerHTML` → PARSIRA HTML (`<b>` postane podebljano)
- `innerText` → doslovno tekst (`<b>` se vidi kao slova)

## Dodavanje/uklanjanje
- `appendChild(el)` → dodaje na kraj (zadnje dijete)
- `insertBefore(novi, ref)` → umeće novi PRIJE ref
- `element.remove()` → uklanja element
- `cloneNode(true)` → duboka kopija (s djecom)

---

# 14. DOGAĐAJI (Events)

- `element.addEventListener("click", funkcija)` → dodaje listener (vrsta + callback)
- `element.onclick = funkcija` → uklanja se s `onclick = null`
- `removeEventListener("click", funkcija)` → uklanja (treba ista imenovana funkcija)
- `e.type` → vrsta događaja ("click", "keydown")
- `e.key` → pritisnuta tipka
- `e.preventDefault()` → sprječava zadano ponašanje (npr. slanje forme)
- `e.stopPropagation()` → zaustavlja širenje događaja

## Propagacija
- Događaj se širi kroz DOM: **capturing** (od vrha) i **bubbling** (od elementa prema vrhu, zadano)
- Vrste događaja: miš (click, mouseenter), tipkovnica (keydown, keyup), forma (submit, focus)
- **Callback** = funkcija proslijeđena kao argument koja se poziva kad se dogodi događaj

## Arrow i this u listeneru
- Arrow funkcija u listeneru → `this` NE pokazuje na element (nema svoj this)
- Obična funkcija → `this` pokazuje na element

---

# 15. EVENT LOOP (najvažnije za "što ispisuje")

## Redoslijed izvršavanja
1. **Sav sinkroni kod prvo** (obični console.log) — redom kako piše
2. **Mikrotaskovi** (Promise `.then`) — nakon sinkronog
3. **Makrotaskovi** (setTimeout) — sortirani po ms, na kraju

## Pravila
- `setTimeout(fn, 0)` NIKAD ne pretekne sinkroni kod (ide nakon)
- setTimeoutovi se sortiraju po **milisekundama** (0 prije 500 prije 1000), ne po redu pisanja
- Ugniježđeni setTimeout: unutarnji ulazi u red tek KAD se vanjski izvrši
- **Callback BEZ setTimeouta = sinkroni** (izvršava se odmah na svom mjestu)
- Promise `.then` (mikrotask) ide PRIJE setTimeouta (makrotask)
- `await` pauzira async funkciju, pušta sinkroni kod da se izvrši

## Primjer
```js
setTimeout(() => console.log("A"), 0);
console.log("B");
Promise.resolve().then(() => console.log("C"));
// Ispis: B, C, A  (sinkroni B, mikrotask C, makrotask A)
```

---

# 16. PROMISE / ASYNC / AWAIT

- **Promise** = objekt koji predstavlja budući rezultat (pending → fulfilled/rejected)
- **resolve** → uspjeh → poziva `.then`
- **reject** → greška → poziva `.catch`
- `.finally()` → uvijek se izvrši (i kod uspjeha i greške)
- Kad Promise padne (reject), preskaču se `.then` do prvog `.catch`
- **await** radi SAMO u async funkciji; pauzira dok Promise ne završi
- try/catch u async funkciji hvata greške iz await

## Call Stack / Callback Queue
- **Call Stack** = LIFO (zadnji uđe, prvi izađe)
- **Callback Queue** = FIFO (prvi uđe, prvi izađe)

---

# 17. HTTP / API / FETCH

## HTTP metode (CRUD)
- **GET** = dohvat (bez tijela)
- **POST** = kreiranje novog (s tijelom)
- **PUT** = zamjena/ažuriranje (s tijelom, id u URL-u)
- **DELETE** = brisanje (id u URL-u, bez tijela)

## Statusni kodovi
- **200** OK (uspjeh), **201** kreirano, **204** nema sadržaja
- **4xx** = greška KLIJENTA (404 = nije nađeno)
- **5xx** = greška SERVERA (500)

## HTTP zahtjev (request) — komponente
- Metoda, URL, Zaglavlje (headers), Tijelo (body)

## HTTP odgovor (response) — komponente
- Statusna linija (protokol + statusni kod), Zaglavlje, prazan red, Tijelo (body)

## fetch
- `fetch(url)` vraća **Promise**
- `response.json()` vraća Promise (zato await)
- `JSON.stringify(obj)` → objekt u JSON string (za slanje)
- `JSON.parse(str)` → JSON string u objekt

## Valjani JSON
- Ključevi u DVOSTRUKIM navodnicima: `{"ime": "Ana"}`
- Nevaljano: jednostruki navodnici, ključ bez navodnika

## REST API / pojmovi
- **REST** = arhitektura/skup pravila za HTTP komunikaciju
- **API** = Application Programming Interface (komunikacija između aplikacija)
- **Endpoint** = specifična putanja/URL na koju se šalje zahtjev
- Ključne komponente komunikacije: **Send i Receive**
- **Query string**: `?userId=5` (prvi parametar `?`, sljedeći `&`): `?lang=en&theme=dark`

---

# 18. TROSLOJNA ARHITEKTURA

1. **Prezentacijski sloj** (frontend/klijent) — HTML/CSS/JS u pregledniku, ono što korisnik vidi
2. **Aplikacijski sloj** (backend/logika) — poslužitelj, obrada (Express/Node)
3. **Podatkovni sloj** (baza podataka) — pohrana podataka

---

# NAJČEŠĆE ZAMKE (zapamti napamet)

1. **Falsy = samo `false, 0, "", null, undefined, NaN`** — `"0"`, `[]`, `{}` su TRUTHY
2. **for...in = indeksi**, for...of = vrijednosti
3. **typeof null = "object"** (bug)
4. **async funkcija = uvijek Promise**
5. **`||` = prvi truthy, `&&` = prvi falsy**
6. **nodeType: 1-element, 3-tekst, 8-komentar, 9-dokument**
7. **box-sizing content-box: width + padding×2 + border×2**; border-box = width je konačan
8. **querySelector = prvi, querySelectorAll = svi**
9. **callback bez setTimeouta = sinkroni** (odmah)
10. **arrow funkcija = nema svoj this**
11. **selektor atributa: `^` počinje, `$` završava, `*` sadrži**
12. **nizovi/objekti = referenca** (b=a dijele isto); primitivi = kopija
13. **arr[nepostojeći] = undefined** (ne greška)
14. **setTimeout(fn,0) = nakon sinkronog**, sortiraju se po ms
15. **specifičnost: id > klasa > element** (gledaj slijeva)
16. **kaskada: V-I-S-R** (!important najjači)
17. **flexbox: justify-content = glavna, align-items = sporedna**
18. **SVG = vektorska**, fotografije = rasterska
19. **vertikalne margine se preklapaju** (veća), flexbox nema preklapanja
20. **mobile first** = prvo mobitel pa veći ekrani
