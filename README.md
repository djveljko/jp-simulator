# 🎰 JP Simulator

Alat za podešavanje i testiranje jackpot (JP) konfiguracija sa 3 nivoa: **Platinum**, **Gold** i **Diamond**.

Na osnovu mesečnog Total Bet-a i parametara svakog nivoa simulator računa:

- koliko često svaki JP pada i koliki je prosečan drop,
- JP RTP, ukupni RTP, House Edge i GGR,
- raspon i rizik kroz više meseci (Monte Carlo).

Ceo alat je jedan HTML fajl (`index.html`). Ne treba instalacija ni server: fajl se otvori u browseru ili se objavi preko GitHub Pages.

---

## Sadržaj

- [Brzi start](#brzi-start)
- [Funkcionalnosti](#funkcionalnosti)
- [Kako se koristi](#kako-se-koristi)
- [Model simulacije](#model-simulacije)
- [Monte Carlo i metrike rizika](#monte-carlo-i-metrike-rizika)
- [Obrnuti kalkulator](#obrnuti-kalkulator)
- [Analiza osetljivosti](#analiza-osetljivosti)
- [Konvertor valuta](#konvertor-valuta)
- [Čuvanje konfiguracija i Excel export](#čuvanje-konfiguracija-i-excel-export)
- [Pretpostavke i ograničenja](#pretpostavke-i-ograničenja)
- [Rešavanje problema](#rešavanje-problema)
- [Tehnički detalji](#tehnički-detalji)
- [Istorija izmena](#istorija-izmena)

---

## Brzi start

**Lokalno:** preuzmi `index.html` i otvori ga u browseru (Chrome, Edge ili Firefox).

**GitHub Pages:** otvori link repozitorijuma (`https://<korisnik>.github.io/<repo>/`). Posle svake izmene na GitHub-u sačekaj 1-2 minuta i osveži stranicu sa **Ctrl+F5**.

Jezik se menja dugmetom **🌐 EN / 🌐 SR** u zaglavlju. Izbor se pamti, a promena jezika ne briše unete vrednosti ni rezultate.

Za prvi test:

1. Upiši **Mesečni Total Bet (EUR)**, npr. `11,000,000`.
2. Za svaki JP nivo upiši **Base**, **Min**, **Max**, **Standard** i **Hidden Contribution**.
3. Klikni **▶ Run Single Simulation** za jedan mesec ili **🎲 Run Monte Carlo** za prosek i raspon.

---

## Funkcionalnosti

| Oblast | Šta radi |
|---|---|
| Simulacija | Event-based simulacija jednog meseca, sa grafikonom kumulativnog JP RTP-a |
| Monte Carlo | Serije meseci (npr. 100 × 12) u kojima se stanje JP-a prenosi iz meseca u mesec |
| Rizik | Procenat meseci iznad praga JP RTP-a ili isplate, najgori mesec, P99, najveći drop |
| Scenariji | 3 alternativne konfiguracije (A, B, C), single run i Monte Carlo, tabela za poređenje |
| Analitička procena | Očekivani JP RTP i broj dropova odmah dok se unose parametri |
| Obrnuti kalkulator | Iz cilja (JP RTP ili prosečan drop + učestalost) predlaže Base, Min, Max, Std i Hidden |
| Analiza osetljivosti | Kako se JP RTP i učestalost menjaju kada se pomera jedan parametar |
| Validacija | Upozorenja za Base > Min, Min ≥ Max, hidden overflow i neaktivne nivoe |
| Konvertor valuta | Tabele konfiguracije u 60+ fiat i kripto valuta, sa live kursevima |
| Konfiguracije | Čuvanje i učitavanje JSON fajla, automatsko čuvanje poslednje sesije |
| Export | Excel sa konfiguracijom, rezultatima, Monte Carlo, scenarijima, osetljivošću i predlogom kalkulatora |
| Jezik | Srpski i engleski, dugme 🌐 u zaglavlju (izbor se pamti u browseru) |
| Izgled | Dark i light mode, prilagođeno i za telefon |

---

## Kako se koristi

### 1. Globalni parametri

| Polje | Opis |
|---|---|
| Win/Bet Ratio (bez JP) [%] | RTP base game-a bez JP-a, npr. 96.5 |
| Mesečni Total Bet (EUR) | Ukupan iznos opklada u mesecu, osnova za sve proračune |
| Simulation Granularity | Veličina jednog koraka u EUR (preporuka 1). Utiče samo na preciznost iznosa dropa, ne na brzinu |
| Random seed | Prazno = novi slučajan seed. Upisan seed daje identičan rezultat pri svakom pokretanju |
| Početno stanje JP-a | **Zagrejan** = JP već radi (steady state). **Od Base** = novo lansiranje, JP kreće od Base |
| Valuta JP konfiguracije | Valuta u kojoj se unose Base, Min, Max i Bet Requirement (vidi ispod). Podrazumevano EUR |

Ispod parametara se odmah vide **očekivani JP RTP (analitički)**, očekivani broj dropova po nivou i seed korišćen u poslednjem pokretanju.

### Valuta JP konfiguracije

Kada se menja postojeći JP koji nije u EUR (npr. RSD), iznosi se ne moraju ručno konvertovati:

1. U **Valuta JP konfiguracije** izaberi valutu (fiat, kripto ili custom). Kod kripto valuta bira se i jedinica (1x, milli, micro).
2. Base, Min, Max i Bet Requirement (glavna konfiguracija i scenariji) upiši tačno kako stoje u postojećem JP-u.
3. Ispod svakog polja se odmah vidi ekvivalent u EUR, a pored izbora valute kurs koji se koristi (live ili ugrađeni).

**Total Bet, simulacija i svi rezultati ostaju u EUR**, kao u reportima. Kod iznosa dropova u zagradi je prikazana i originalna valuta, npr. `24.91 (2,923 RSD)`. Rezultati su isti kao da je konfiguracija ručno preračunata u EUR.

Ako polja već imaju iznose pri promeni valute, alat pita da li da ih preračuna (isti JP u drugoj valuti) ili da ostavi iste brojeve. Predlog obrnutog kalkulatora se upisuje u izabranoj valuti. Kurs se uzima u trenutku računanja, pa se EUR ekvivalent može malo pomeriti kad se kursevi osveže.

### 2. Parametri JP nivoa

| Polje | Opis |
|---|---|
| Base Value | Minimalna početna vrednost JP-a posle dropa |
| Min / Max Drop Zone | Opseg u kome se nasumično bira iznos na kom JP pada (hit point) |
| Bet Requirement | Minimalna opklada za JP. Samo informativno, ne utiče na simulaciju |
| Standard Contribution [%] | Vidljivi deo opklade koji puni JP |
| Hidden Contribution [%] | Skriveni deo koji se skuplja u rezervu i puni **sledeći** JP |

GAP = Min - Base. Veći GAP znači da JP mora više da naraste pre nego što može da padne.

### 3. Pokretanje

- **▶ Run Single Simulation:** jedan mesec. Dobar za brzu proveru, ali je kod retkih nivoa (npr. Diamond) jedan mesec samo jedan slučajan ishod.
- **🎲 Run Monte Carlo:** serije meseci. Glavne tabele tada prikazuju **prosek po mesecu**, a Monte Carlo sekcija prikazuje raspon i rizik.
- Ispod tabela piše odakle su prikazani brojevi (jedan mesec ili Monte Carlo prosek).

### 4. Scenariji

Sekcija **Scenario Comparison Tool** se pojavljuje posle prve simulacije.

- Svaki scenario ima svoje Std, Hidden, Base, Min i Max po nivou, uz live analitičku procenu.
- **▶ Run Scenario** pušta jedan mesec, **🎲 MC Scenario** pušta Monte Carlo.
- **🎲 Monte Carlo za sve** pušta Current + A + B + C jednim klikom.
- Svi koriste **isti seed** kao glavna simulacija, pa razlike u tabeli dolaze od konfiguracije, a ne od slučajnosti.

---

## Model simulacije

### Mehanika

1. Total Bet se deli na korake veličine *granularity* (npr. 1 EUR).
2. U svakom koraku vidljivi JP raste za `granularity × Std%`, a hidden rezerva za `granularity × Hidden%`.
3. Hit point se bira **uniformno** između Min i Max.
4. Kada JP dostigne hit point, isplata je trenutna vidljiva vrednost.
5. Posle dropa **sledeći JP kreće od Base + skupljena hidden rezerva**, a rezerva se vraća na 0.
6. Bira se novi hit point i ciklus se ponavlja.

Nivoi su međusobno nezavisni. Simulator ne prolazi kroz svaki EUR, nego direktno računa korak u kom JP pada (event-based). Rezultat je identičan petlji korak po korak, a izvršavanje je hiljadama puta brže.

### Analitičke formule (dugoročni prosek)

Uz `E[H] = (Min + Max) / 2`:

```
Ciklus (Total Bet između dva dropa) = (E[H] - Base) / (Std% + Hidden%)
Dropova mesečno                     = Total Bet / Ciklus
JP RTP nivoa                        = (Std% + Hidden%) × E[H] / (E[H] - Base)
```

JP RTP je veći od ukupne kontribucije (Std + Hidden). Razliku čini Base vrednost koju pri svakom dropu dodaje operater.

**Primer:** Base 10, Min 15, Max 35, Std 0.27%, Hidden 0.03%:

- E[H] = 25 EUR, ciklus = 15 / 0.003 = 5,000 EUR Total Bet-a
- Kod 11M Total Bet-a: ~2,200 dropova mesečno
- JP RTP nivoa = 0.30% × 25 / 15 = **0.50%**

### Validacija hidden kontribucije

U najgorem slučaju JP raste od Base do Max i tokom tog ciklusa skupi hidden rezervu. Ako bi sledeći JP krenuo od vrednosti:

- **iznad Max:** greška (HIDDEN OVERFLOW), jer bi JP pao odmah i isplatio više od Max.
- **iznad Min:** upozorenje, jer deo JP-ova može pasti odmah po resetu.

---

## Monte Carlo i metrike rizika

Monte Carlo pušta **N serija × M meseci** (podrazumevano 100 × 12, najviše 5,000 × 120). Unutar jedne serije meseci idu jedan za drugim, a vrednost JP-a i hidden rezerva se prenose u sledeći mesec, kao u stvarnom radu.

| Metrika | Značenje |
|---|---|
| Prosečan JP RTP | Prosek svih simuliranih meseci |
| Mesečni raspon (P5 - P95) | 90% meseci ima JP RTP u ovom rasponu |
| Raspon za ceo period (P5 - P95) | Isto, za prosek cele serije (npr. godine) |
| 95% CI proseka | Koliko je precizno izračunat sam prosek (iz nezavisnih serija) |
| Volatilnost | Std dev / prosek mesečnog JP RTP-a |
| Dropovi po mesecu | Prosek, min, max i std dev po nivou |
| Meseci sa ≥1 dropom | Važno za retke nivoe. Npr. Diamond 0.17/mes = drop u ~16% meseci, 1 na ~6 meseci |

**Rizik i najgori mesec.** Uz Monte Carlo podešavanja mogu se uneti dva praga:

- **Prag mesečnog JP RTP (%):** koliko meseci ga prelazi.
- **Prag mesečne JP isplate (EUR):** koliko meseci ukupna isplata prelazi taj iznos.

Prikazuju se još najgori mesec (max JP RTP), P99, najgori period, najveća mesečna JP isplata i najveći pojedinačni drop. Promena praga odmah preračunava rezultate, bez ponovnog pokretanja.

**Grafikon:** kod Monte Carla prikazuje prvu seriju kroz sve mesece. Isprekidana linija je očekivani (analitički) JP RTP. Kod retkih nivoa jasno se vide "skokovi" kada Diamond padne.

---

## Obrnuti kalkulator

Iz cilja predlaže konfiguraciju. Postoje dva moda:

- **Ciljni ukupni JP RTP + učestalost:** zadaju se ukupni JP RTP, udeo svakog nivoa (zbir 100%) i učestalost.
- **Ciljni prosečan drop + učestalost:** zadaju se prosečan drop u EUR i učestalost, a JP RTP je rezultat.

Učestalost se unosi kao **dropova mesečno** ili **meseci po dropu**.

Dodatni parametri:

| Parametar | Podrazumevano | Značenje |
|---|---|---|
| Hidden udeo u kontribuciji | 10% | Koji deo kontribucije ide u hidden |
| Min/Max zona ± | 40% | Min = 60% proseka, Max = 140% proseka |
| Base (% od Min) | 60% | Base kao procenat Min vrednosti |
| Zaokruži iznose | uključeno | Base, Min i Max na "lepe" iznose |

Rezultat je tabela sa Base, Min, Max, Std, Hidden, očekivanim dropovima i RTP-om po nivou, plus odstupanje od cilja zbog zaokruživanja. Dugmad **Primeni na Current / Scenario A / B / C** upisuju predlog u izabranu konfiguraciju. Posle toga se pokrene Monte Carlo za proveru.

---

## Analiza osetljivosti

Menja jedan parametar (Std, Hidden, Base, Min ili Max) jednog nivoa, a sve ostalo ostaje kao u izabranoj konfiguraciji (Current, A, B ili C).

- **Od / Do / Broj tačaka** (2-41). **↔ Predloži opseg** popunjava razuman opseg oko trenutne vrednosti.
- Prvi grafik prikazuje ukupni JP RTP, drugi broj dropova mesečno izabranog nivoa. Isprekidana linija označava trenutnu vrednost.
- Opcija **Dodaj Monte Carlo raspon** za svaku tačku pušta Monte Carlo (podrazumevano 30 serija) i crta mesečni raspon P5 - P95 kao senku.
- Tabela označava nevažeće tačke i tačke sa upozorenjem u validaciji.

---

## Konvertor valuta

Generiše tabele konfiguracije (Current i scenariji) u izabranoj valuti.

- **Live kursevi** se učitavaju pri otvaranju i osvežavaju na svakih 5 minuta (ili dugmetom Refresh):
  - fiat: `api.exchangerate-api.com`, rezervni izvor `open.er-api.com`
  - kripto: `api.coingecko.com`
- Status u zaglavlju: **✅ Live**, **⚠️ Delimično live** (samo fiat ili samo kripto) ili **⚠️ Fallback rates** (ugrađeni kursevi).
- **Zaokruživanje:** fiat na 2 značajne cifre (npr. 1.19 → 1.2, 41.65 → 42). Valute bez decimala (RSD, HUF, JPY, KRW, IDR, VND, CLP, ISK) idu na ceo broj.
- **Kripto prefiksi:** milli (mBTC) i micro (μBTC), za čitljive iznose.
- **Custom valuta:** kod (3-5 slova), tip (fiat: 1 EUR = X, kripto: 1 coin = X EUR) i kurs. Live osvežavanje je ne prepisuje.

---

## Čuvanje konfiguracija i Excel export

Dugmad u zaglavlju:

- **💾 Sačuvaj:** JSON fajl sa svim poljima (globalni parametri, JP nivoi, scenariji, pragovi, kalkulator, custom valute).
- **📂 Učitaj:** učitava sačuvan JSON i osvežava sve procene i validacije.
- **↩ Poslednja sesija:** vraća poslednje unete vrednosti. Čuvaju se automatski, samo u tom browseru i na tom računaru.

**📊 Export to Excel** pravi fajl sa sheet-ovima:

| Sheet | Sadržaj |
|---|---|
| Configuration | Globalni parametri i konfiguracija Current + A/B/C, sa očekivanim RTP-om po nivou |
| Results | Rezultati prikazani u glavnim tabelama (jedan mesec ili Monte Carlo prosek) |
| Monte Carlo | Sve MC metrike i metrike rizika za Current i scenarije |
| Scenarios | Poređenje single run rezultata (ako su pokretani) |
| Osetljivost | Poslednja analiza osetljivosti (ako je pokretana) |
| Obrnuti kalkulator | Poslednji predlog kalkulatora (ako je računat) |

Vrednosti se izvoze kao brojevi, pa se u Excel-u mogu dalje računati.

---

## Pretpostavke i ograničenja

- **Uniformni hit point.** Hit point se bira ravnomerno između Min i Max. Dugoročni prosek (JP RTP, broj dropova, prosečan drop) zavisi samo od **proseka** hit pointa, pa je model tačan za proseke ako je stvarni prosečan drop blizu (Min + Max) / 2. Oblik stvarne raspodele utiče na raspon i ekstreme (P5 - P95, najgori mesec). Provera: uporediti stvarni prosečan drop sa (Min + Max) / 2.
- **Total Bet je ravnomeran tok,** isti svakog meseca. Pojedinačne opklade se ne modeluju, pa Bet Requirement ne utiče na rezultat.
- **Base game je fiksan** (Total Bet × Win/Bet Ratio), pa sva varijansa dolazi od JP-a.
- **GGR ne uključuje JP obavezu,** tj. novac koji na kraju meseca stoji u JP-ovima.
- **Početno stanje:** za novo lansiranje koristi se "Od Base". "Zagrejan" odgovara JP-u koji već radi.
- **Analitičke procene** su dugoročni prosek i ne pokazuju mesečni raspon. Za raspon se koristi Monte Carlo.

---

## Rešavanje problema

**U zaglavlju piše "Fallback rates"**

1. Otvori API linkove iz sekcije [Konvertor valuta](#konvertor-valuta) direktno u browseru. Ako se ne učitaju, mreža ili firewall blokira domene.
2. Probaj u Incognito prozoru. Ako tamo radi, problem pravi ekstenzija (ad blocker ili privacy ekstenzija).
3. Probaj preko hotspota sa telefona. Ako tamo radi, IT treba da odobri ta tri domena.

**Vidi se stara verzija posle izmene**

- Osveži sa **Ctrl+F5**. Kod GitHub Pages sačekaj da se završi deploy (kartica *Actions*).
- Proveri da novi fajl nije sačuvan kao `index (1).html`.

**Excel export ne radi**

- Export koristi SheetJS biblioteku sa CDN-a i zato treba internet pri učitavanju stranice.

---

## Tehnički detalji

- Jedan fajl: HTML, CSS i JavaScript (vanilla, bez framework-a).
- Spoljne zavisnosti:
  - SheetJS `xlsx 0.20.1` (CDN), za Excel export
  - Google Fonts (Inter), samo za izgled
  - API-ji za kurseve (opciono, postoje ugrađeni kursevi)
- Generator slučajnih brojeva: `mulberry32`, sa nezavisnim tokom za svaki nivo i svaku seriju. Isti seed daje isti rezultat, a scenariji dobijaju isti niz hit pointova radi fer poređenja.
- Svi proračuni se izvršavaju lokalno u browseru i nijedan podatak o konfiguraciji se ne šalje nigde.

---

## Istorija izmena

### v3.2

- **Valuta JP konfiguracije:** Base, Min, Max i Bet Requirement se unose u originalnoj valuti postojećeg JP-a, uz EUR ekvivalent ispod svakog polja. Total Bet, simulacija i rezultati ostaju u EUR.

### v3.1

- **Dvojezični interfejs (srpski / engleski):** prevedeni su svi natpisi, objašnjenja, upozorenja, poruke i Excel export. Promena jezika samo ponovo ispisuje postojeće rezultate, bez ponovnog računanja.

### v3

- **Novi model hidden kontribucije:** hidden puni sledeći JP (Base + rezerva). Simulacija, validacija i procene sada koriste isti model.
- **Event-based simulacija:** isti rezultat kao petlja po koracima, hiljadama puta brže.
- **Monte Carlo kao serije meseci** sa prenosom stanja. Izbor početnog stanja (zagrejan ili od Base).
- **Glavne tabele posle Monte Carla** prikazuju prosek, a ne poslednje pokretanje.
- **Monte Carlo za scenarije** i "Monte Carlo za sve", sa istim seed-om za fer poređenje.
- **Retki dropovi** se prikazuju decimalno ("0.17/mes, 1 na ~6 mes"), uz procenat meseci sa bar jednim dropom.
- **Mesečni raspon P5 - P95**, raspon za period i ispravan CI proseka.
- **Nove funkcije:** metrike rizika, obrnuti kalkulator, analiza osetljivosti, čuvanje i učitavanje konfiguracija.
- **Ispravke:**
  - netačan generator slučajnih brojeva sa seed-om
  - brisanje seed-a posle Monte Carla
  - neaktivni nivoi su prijavljivali milione dropova od 0 EUR
  - zaokruživanje valuta (npr. Min Bet 1 EUR prikazivan kao 0.00 USD)
  - resetovanje kripto prefiksa pri osvežavanju kurseva
  - custom kripto valute su računate kao fiat
  - CSS greške i čitljivost grafikona u light modu
- **Live kursevi:** direktni pozivi API-ja umesto CORS proxy-ja, rezervni fiat izvor, nezavisno učitavanje fiat i kripto kurseva.
- **Excel export:** numeričke vrednosti i novi sheet-ovi.
