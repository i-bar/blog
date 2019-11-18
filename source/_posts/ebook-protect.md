---
title: Cum protejăm cărțile electronice vândute online
date: 2021-07-13 16:43:06
tags:
  - ebook
  - drm
---

## Să protejăm cărțile publicate online sau nu?

Motive pentru a le proteja:
- Nu pot fi trimise altcuiva (în principal prietenilor / rudelor);
- Nu pot fi încărcate pe internet și redistribuite.

Motive pentru a NU le proteja:
- Orice protecție poate fi eliminată: există multe pagini online care fac asta (google "remove DRM");
- Cititorul este obligat să folosească un anumit program pentru a deschide cărțile (dar nu mereu, depinde de tipul de protecție);
- Cititorul are de urmat mai mulți pași pentru a descărca o carte;
- Aceiași pași trebuie repetați dacă se dorește citirea cărții pe mai multe dispozitive (laptop / telefon / tabletă).

## Metoda de protecție #1. **DRM** - Digital Rights Management

### Cum funcționează?

1. Cărțile sunt **criptate** cu anumite tipuri de tehnologii care diferă de la o companie la alta: Apple, Amazon, Adobe au metode proprii de criptare și decriptare.
2. La momentul criptării, editura **hotărăște detalii** precum:
   - permite (sau nu) printarea cărții (sau a unui număr linitat de pagini)
   - permite (sau nu) copierea cărții (copierea întregii cărți pe un singur alt dispozitiv sau copierea unui număr limitat de pagini)
   - stabilirea unui termen de valabilitate al link-ului de descărcare al cărții (e.g. e valid doar astăzi)
3. La achiziționare, cititorul primește un link de la care va **descărca un fișier criptat** (extensia nu este `epub` ci diferă de la o metodă de criptare la alta. Pentru Adobe, extensia este `acsm`)
4. Pentru a citi cartea, cititorul trebuie să o **decripteze** folosind o aplicație care corespunde celei care a criptat-o: Kindle pentru Amazon, Adobe Digital Editions pentru Adobe.

### Exemplu: Adobe DRM

**Este standardul curent: cea mai folosită tehnologie pentru protejat cărțile electronice.**

1. Cărțile se criptează cu **Adobe Content Server**.
Acesta nu este disponibil online decât prin parteneri Adobe, cum ar fi:
   - **EditionGuard**
     Extensie pentru WordPress: https://wordpress.org/plugins/editionguard-for-woocommerce-ebook-sales-with-drm/
     Preț: https://www.editionguard.com/pricing/
   - **DataLogics**
     Preț: 10000$ pentru primul an + 0.22$ per tranzacție.
2. Cărțile se decriptează și se citesc **doar cu Adobe Digital Editions**: o aplicație gratuită care se poate descărca de la următorul link: https://www.adobe.com/ro/solutions/ebook/digital-editions/download.html . Pentru decriptare, utilizatorul trebuie să își creeze un cont Adobe și să folosească ID-ul propriu de acolo (adresa de email).

## Metoda #2. **Social DRM** sau **Digital Watermark** (filigran digital)

### Cum funcționează?

Pentru a proteja o carte electronică prin metoda "Social DRM", **se inserează în conținutul cărții un "Filigran digital"**. Acesta poate fi de două tipuri:

- **Vizibil**: o semnătură de genul "Ex libris..." sau "Cartea a fost achiziționată de...", fie la începutul, la sfârșitul sau pe parcursul cărții. 
   Scopul: Prezența unor detalii personale (email / nume / număr de telefon) poate descuraja distribuirea cărții.
- **Invizibil**: se adaugă cărții un identificator transparent care identifică unic persoana care a achiziționat cartea.
   Scopul: Diferite programe (e.g. Digimarc Guardian / Link-Busters) scanează periodic cărțile distribuite pe internet pentru astfel de filigrame și se poate descoperi astfel piratarea cărții.

**Editorul**: Pentru semnare, în momentul în care se crează un link pentru a fi trimis clientului, editorul trebuie să introducă datele personale ale clientului (email / nume / nr de telefon).

**Cititorul** descarcă cartea de la link-ul primit, ca pe orice alt pdf / epub. Cartea poate fi copiată și citită pe oricâte dispozitive ale aceluiași proprietar. De asemenea, poate fi citită cu orice aplicație care poate deschide fișiere `pdf` sau `epub`.

### Exemple

1. **EditionGuard** (care oferă și Adobe DRM) adaugă un filigran de genul "cartea a fost cumpărată de...".
   - semnează atât `epub`-uri cât și `pdf`-uri;
   - preț: minim 39$ pe lună: https://www.editionguard.com/pricing/
   - setări posibile: 
      - unde să fie adăugat filigranul: la început / la sfârșit / pe parcurs sau orice combinație între acestea;
      - câte zile este valabil link-ul; 
      - de câte ori se poate accesa link-ul.

2. **Booxtream**: https://www.booxtream.com/
   - semnează DOAR `epub`-uri;
   - preț: 0.5€ pentru un `epub` cu filigran. Poate fi descărcat de max 255 de ori și e valabil 2 ani. Plata minimă este pentru 200 de `epub`-uri, deci 100€.
   - setări posibile: 
      - unde să fie adăugat filigranul: la început / la sfârșit / pe parcurs sau orice combinație între acestea; Sau la sfârșitul fiecărui capitol;
      - câte zile este valabil link-ul; 
      - de câte ori se poate accesa link-ul.

## Pdf protejat cu parolă

### Cum funcționează?

**Doar pentru `pdf`-uri.**

**Editorul** salvează cartea în format `pdf` și o protejează cu o parolă.
**Cititorul** trebuie să introducă aceeași parolă pentru a deschide cartea.

### Exemple

Când este salvat un document din `word`, cu extensia `pdf`, la `Opțiuni` se poate alege și o parolă.

Se poate folosi și **Adobe Acrobat DC**. Preț: 15.5€ / lună: https://acrobat.adobe.com/us/en/acrobat/how-to/pdf-file-password-permissions.html

Se poate adăuga o parolă carților și **online**, doar că trebuie încărcat documentul pe diferite site-uri. 

## Concluzii

Tendința pare să fie dinspre DRM spre Social DRM (filigran) sau chiar fără protecție, pe principiul că genul de persoane care ar citi o carte nu ar pirata-o.

Pe de altă parte, TOATE librăriile din România pe care le-am găsit că ar vinde cărți online folosesc Adobe DRM.

### Alte librării cum fac?

Librăriile **din România** care vând și cărți electronice le protejează cu **Adobe DRM**:

- Instrucțiuni de la **Humanitas** pentru cum se descarcă o carte electronică protejată cu Adobe DRM: https://www.libhumanitas.ro/ebook.html .
- Instrucțiuni și detalii generale de la **Libris**: https://www.libris.ro/despre-ebook.htm?tester=1 .
- **Nemira** folosesc tot Adobe DRM dar nu oferă instrucțiuni decât la achizițioare.

**Polirom** și **Litera** redirecționează la parteneri (e.g. Libris, Google Play) pentru ebooks: 
- https://www.polirom.ro/ebook
- https://www.litera.ro/ebooks

Librăriile **din afara României** recurg din ce în ce mai des la **Social DRM**: O'Reilly (cărți tehnice), Springer, Pottermore (cărți Harry Potter). Conform următorului studiu, filigranul a căpătat amploare mai ales în Europa (Italia, Germania, Olanda, Suedia): https://goodereader.com/blog/e-book-news/everything-you-need-to-know-about-social-drm-for-ebooks