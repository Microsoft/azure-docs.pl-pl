---
title: Lista obsługiwanych klasyfikacji
description: Ta strona zawiera listę obsługiwanych klasyfikacji systemu w usłudze Azure kontrolą.
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 02/05/2021
ms.openlocfilehash: 37df9a276339b80a81e6ac5d5db14872de9edff4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065858"
---
# <a name="supported-classifications-in-azure-purview"></a>Obsługiwane klasyfikacje w usłudze Azure kontrolą

W tym artykule wymieniono obsługiwane i zdefiniowane klasyfikacje systemu w usłudze Azure kontrolą (wersja zapoznawcza).


- **Próg DISTINCT dopasowania**: całkowita liczba unikatowych wartości danych, które należy znaleźć w kolumnie przed uruchomieniem przez skaner wzorca danych. Nasze reguły klasyfikacji systemu wymagają, aby w każdej kolumnie znajdować się co najmniej 8 odrębnych wartości, które podlegają klasyfikacji. System wymaga tej wartości, aby upewnić się, że kolumna zawiera wystarczającą ilość danych dla skanera, aby precyzyjnie sklasyfikować go. Na przykład kolumna zawierająca wiele wierszy zawierających wartość 1 nie zostanie sklasyfikowana. Kolumny zawierające jeden wiersz z wartością i resztą wierszy mają wartości null, które również nie są klasyfikowane. Jeśli określisz wiele wzorców, ta wartość ma zastosowanie do każdego z nich.

- **Minimalny próg dopasowania**: jest to minimalny procent wartości danych w kolumnie, która musi zostać znaleziona przez skaner dla klasyfikacji, która ma zostać zastosowana. Wartość klasyfikacji systemu jest ustawiona na 60%.


## <a name="defined-system-classifications"></a>Zdefiniowane klasyfikacje systemu

Usługa Azure kontrolą klasyfikuje dane według [wyrażenia regularnego](https://wikipedia.org/wiki/Regular_expression) i [filtra zakwiatowego](https://wikipedia.org/wiki/Bloom_filter). Poniższe listy opisują format, wzorzec i słowa kluczowe dla zdefiniowanych klasyfikacji systemu Azure kontrolą.

Każda nazwa klasyfikacji jest poprzedzona przez firmę MICROSOFT.

## <a name="bloom-filter-classifications"></a>Klasyfikacje filtrów rozwiniętych

## <a name="city-country-and-place"></a>Miasto, kraj i miejsce

Filtry miasta, kraju i miejsca zostały przygotowane przy użyciu najlepszych zestawów danych dostępnych do przygotowania danych.

## <a name="person"></a>Person (Osoba)

Filtr osoby zakwiatowej został przygotowany przy użyciu poniższych dwóch zestawów danych.

- [2010 US dane spisu dla ostatnich nazw (162-K wpisów)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Popularne nazwy dzieci (od numeru PESEL), z użyciem wszystkich lat 1880-2019 (98-K)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>Klasyfikacje wyrażenia regularnego

## <a name="aba-routing"></a>Routing ABA

### <a name="format"></a>Format

Dziewięć cyfr, które mogą znajdować się w sformatowanym lub niesformatowanym wzorcu

### <a name="pattern"></a>Wzorce

Poprawić

- cztery cyfry zaczynające się od 0, 1, 2, 3, 6, 7 lub 8
- Łącznik
- cztery cyfry
- Łącznik
- cyfra

Niesformatowane: dziewięć kolejnych cyfr zaczynających się od 0, 1, 2, 3, 6, 7 lub 8

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Numer tożsamości narodowej (DNI) Argentyna

### <a name="format"></a>Format

Osiem cyfr z kropkami lub bez nich

### <a name="pattern"></a>Wzorce

Osiem cyfr:

- dwie cyfry
- opcjonalny okres
- trzy cyfry
- opcjonalny okres
- trzy cyfry

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Numer konta bankowego Australii

### <a name="format"></a>Format

Od sześciu do 10 cyfr z lub bez numeru rozgałęzienia stanu banku

### <a name="pattern"></a>Wzorce

Numer konta to sześć do 10 cyfr.

Numer rozgałęzienia stanu banku Australii:

- trzy cyfry
- Łącznik
- trzy cyfry

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Numer licencji sterownika Australii

### <a name="format"></a>Format
Dziewięć liter i cyfr

### <a name="pattern"></a>Wzorce
dziewięć liter i cyfr:

- dwie cyfry i litery (bez uwzględniania wielkości liter)
- dwie cyfry
- pięć cyfr lub liter (bez uwzględniania wielkości liter)

LUB

- jeden do dwóch opcjonalnych liter (bez uwzględniania wielkości liter)
- od czterech do dziewięciu cyfr

LUB

- dziewięć cyfr lub liter (bez uwzględniania wielkości liter)

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Australijski numer Medicare

### <a name="format"></a>Format

10-11 cyfr

### <a name="pattern"></a>Wzorce

10-11 cyfr:

- Pierwsza cyfra jest w zakresie 2-6
- Dziewiąta cyfra to cyfra kontrolna
- dziesiąta cyfra jest cyfrą problemu
- Jedenasta cyfra (opcjonalnie) jest numerem indywidualnym

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Numer paszportu Australii

### <a name="format"></a>Format

Litera, a po niej siedem cyfr

### <a name="pattern"></a>Wzorce

Litera (bez uwzględniania wielkości liter), po której następuje siedem cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_passport"></a>Paszport — słowo kluczowe \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Słowo kluczowe \_ Australia — \_ numer paszportu \_

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Numer pliku podatku Australii

### <a name="format"></a>Format

osiem do dziewięciu cyfr

### <a name="pattern"></a>Wzorce

osiem do dziewięciu cyfr zwykle jest prezentowane w następujący sposób:

- trzy cyfry
- opcjonalne miejsce
- trzy cyfry
- opcjonalne miejsce
- od dwóch do trzech cyfr, w których Ostatnia cyfra jest cyfrą kontrolną

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_australia_tax_file_number"></a>Słowo kluczowe \_ Australia — \_ \_ numer pliku podatku \_

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>Belgijski numer krajowy

### <a name="format"></a>Format

11 cyfr i opcjonalne ograniczniki

### <a name="pattern"></a>Wzorce

11 cyfr Plus ograniczniki:

- sześć cyfr i dwa opcjonalne okresy w formacie RR. MM. DD dla daty urodzenia
- Opcjonalny ogranicznik od kropki, kreska, spacja
- trzy kolejne cyfry (nieparzyste dla samców, nawet dla samic)
- Opcjonalny ogranicznik od kropki, kreska, spacja
- dwie cyfry kontroli

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_belgium_national_number"></a>Słowo \_ kluczowe \_ Belgii \_ Nr Narodowy

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Numer numer CPF Brazylii

### <a name="format"></a>Format

11 cyfr, które zawierają cyfrę sprawdzającą i mogą być sformatowane lub niesformatowane

### <a name="pattern"></a>Wzorce

Poprawić

- trzy cyfry
- okres
- trzy cyfry
- okres
- trzy cyfry
- Łącznik
- dwie cyfry, które są cyframi kontrolnymi

Niesformatowany:

- 11 cyfr, w których ostatnie dwie cyfry są cyframi kontrolnymi

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_brazil_cpf"></a>Słowo kluczowe \_ Brazylia \_ numer CPF

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Numer podmiotu prawnego w Brazylii (numer CNPJ)

### <a name="format"></a>Format

14 cyfr, które zawierają numer rejestracji, numer rozgałęzienia i cyfry kontroli oraz ograniczniki

### <a name="pattern"></a>Wzorce

14 cyfr Plus ograniczniki:

- dwie cyfry
- okres
- trzy cyfry
- okres
- trzy cyfry (te pierwsze osiem cyfr to numer rejestracji)
- ukośnik
- czterocyfrowy numer rozgałęzienia
- Łącznik
- dwie cyfry, które są cyframi kontrolnymi

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_brazil_cnpj"></a>Słowo kluczowe \_ Brazylia \_ numer CNPJ

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>Krajowa karta identyfikacyjna Brazylia (RG)

### <a name="format"></a>Format

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Wzorce

:::no-loc text="Registro Geral (old format):":::

- dwie cyfry
- okres
- trzy cyfry
- okres
- trzy cyfry
- Łącznik
- jedna cyfra, która jest cyfrą kontrolną

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 cyfr
- Łącznik
- jedna cyfra, która jest cyfrą kontrolną

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_brazil_rg"></a>Słowo kluczowe \_ Brazylia \_ RG

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Numer konta bankowego Kanady

### <a name="format"></a>Format

7 lub 12 cyfr

### <a name="pattern"></a>Wzorce

Numer konta bankowego w Kanadzie to 7 lub 12 cyfr.

Numer tranzytowy konta bankowego Kanady to:

- pięć cyfr
- Łącznik
- trzy cyfry lub
- zero &quot; 0&quot;
- osiem cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_canada_bank_account_number"></a>Słowo kluczowe w \_ Kanadzie \_ \_ numer konta bankowego \_

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Numer licencji na sterownik Kanady

### <a name="format"></a>Format

Różni się według prowincji

### <a name="pattern"></a>Wzorce

Różne wzorce obejmujące Alberta, Kolumbia Brytyjska, Manitoba, Nowy Brunszwik, Nowa Fundlandia/Labrador, Nowa Szkocja, Ontario, Wyspa Księcia Edward wyspy, Quebec i Saskatchewan

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_province_name_drivers_license_name"></a>Słowo kluczowe \_ [ \_ Nazwa prowincji \_ ] \_ Nazwa licencji sterowników \_

- Skrót prowincji, na przykład AB
- Nazwa prowincji, na przykład Alberta

#### <a name="keyword_canada_drivers_license"></a>Licencja dotyczące słów kluczowych dla \_ Kanady \_ \_

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Numer usługi kondycji Kanady

### <a name="format"></a>Format

10 cyfr

### <a name="pattern"></a>Wzorce

10 cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_canada_health_service_number"></a>Słowo kluczowe \_ Kanada — \_ \_ Numer usługi kondycji \_

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Numer paszportu Kanady

### <a name="format"></a>Format

dwie wielkie litery, po których następuje sześć cyfr

### <a name="pattern"></a>Wzorce

dwie wielkie litery, po których następuje sześć cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_canada_passport_number"></a>Słowo \_ kluczowe \_ Kanada \_ numer paszportu

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Paszport — słowo kluczowe \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Osobisty numer identyfikacyjny kondycji Kanady (PHIN)

### <a name="format"></a>Format

dziewięć cyfr

### <a name="pattern"></a>Wzorce

dziewięć cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_canada_phin"></a>Słowo kluczowe \_ Kanada \_ phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Słowa kluczowe \_ Kanada \_

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Numer ubezpieczenia społecznego w Kanadzie

### <a name="format"></a>Format

dziewięć cyfr z opcjonalnymi łącznikami lub spacjami

### <a name="pattern"></a>Wzorce

Poprawić

- trzy cyfry
- Łącznik lub spacja
- trzy cyfry
- Łącznik lub spacja
- trzy cyfry

Niesformatowane: dziewięć cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_sin"></a>Sin — słowo kluczowe \_

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Sin — słowo kluczowe \_ \_

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Numer karty tożsamości Chile

### <a name="format"></a>Format

siedem do ośmiu cyfr Plus ograniczników lub literę czeku

### <a name="pattern"></a>Wzorce

od siedmiu do ośmiu cyfr Plus ograniczniki:

- jeden do dwóch cyfr
- opcjonalny okres
- trzy cyfry
- opcjonalny okres
- trzy cyfry
- kreska
- jedna cyfra lub litera (bez uwzględniania wielkości liter), która jest cyfrą czeku

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_chile_id_card"></a>\_ \_ Karta identyfikatora Chile słowa kluczowego \_

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Numer chińskiej karty tożsamości (ChRL)

### <a name="format"></a>Format

18 cyfr

### <a name="pattern"></a>Wzorce

18 cyfr:

- sześć cyfr, które są kodem adresu
- osiem cyfr w formie RRRRMMDD, czyli Data urodzenia
- trzy cyfry, które są kodem zamówienia
- jedna cyfra, która jest cyfrą kontrolną

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_china_resident_id"></a>\_ \_ Identyfikator rezydentu w Chinach dla słowa kluczowego \_

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Numer karty kredytowej

### <a name="format"></a>Format

od 14 do 16 cyfr, które mogą być sformatowane lub niesformatowane (dddddddddddddddd), które muszą przekazać test Luhna.

### <a name="pattern"></a>Wzorce

Złożony i niezawodny wzorzec, który wykrywa karty ze wszystkich głównych marek na całym świecie, w tym wizy, MasterCard, karty odnajdowania, JCB, American Express, Gift Cards i Diner Cards.

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_cc_verification"></a>\_Weryfikacja DW dla słowa kluczowego \_

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>\_Nazwa DW słowa kluczowego \_

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Numer licencji sterownika Chorwacji

Ta jednostka typu informacji poufnych jest dostępna tylko w typie informacji poufnych o numerze licencji sterownika UE.

### <a name="format"></a>Format

osiem cyfr bez spacji i ograniczników

### <a name="pattern"></a>Wzorce

osiem cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keywords_eu_drivers_license_number"></a>Keywords \_ \_ — \_ Numer licencji \_ sterownika UE

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Słowa \_ kluczowe \_ Chorwacja \_ — \_ Numer licencji sterownika UE \_

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Numer karty tożsamości Chorwacji

Ta jednostka typu informacji poufnych jest uwzględniona w krajowym typie informacji o numerze identyfikacyjnym UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

dziewięć cyfr

### <a name="pattern"></a>Wzorce

dziewięć kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_croatia_id_card"></a>\_ \_ Karta identyfikatora słowa kluczowego Chorwacji \_

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>Numer identyfikacji osobistej Chorwacji (OIB)

### <a name="format"></a>Format

11 cyfr

### <a name="pattern"></a>Wzorce

11 cyfr:

- 10 cyfr
- końcowa cyfra jest cyfrą kontrolną

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_croatia_oib_number"></a>OIB — słowo kluczowe \_ \_ \_

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Dania osobisty numer identyfikacyjny

### <a name="format"></a>Format

10 cyfr zawierających łącznik

### <a name="pattern"></a>Wzorce

10 cyfr:

- sześć cyfr w formacie DDMMYY, który jest datą urodzenia
- Łącznik
- cztery cyfry, w których końcowa cyfra jest cyfrą kontrolną

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_denmark_id"></a>Słowo kluczowe \_ dania \_ identyfikatora

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>Numer karty debetowej UE

### <a name="format"></a>Format

16 cyfr

### <a name="pattern"></a>Wzorce

Złożony i niezawodny wzorzec

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_eu_debit_card"></a>Słowo kluczowe w \_ Unii Europejskiej — \_ karta debetowa \_

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Warunki karty słowa kluczowego \_ \_ \_ DICT

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>\_Warunki zabezpieczeń karty słowa kluczowego \_ \_ \_ DICT

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>\_Terminy wygaśnięcia karty słów kluczowych \_ \_ \_ DICT

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>Numer licencji sterownika UE

Są to jednostki w typie informacji poufnych o numerze licencji sterownika UE.

- Austria
- Belgia
- Bułgaria
- Chorwacja
- Cypr
- Czeski
- Dania
- Estonia
- Finlandia
- Francja
- Niemcy
- Grecja
- Węgry
- Irlandia
- Włochy
- Łotwa
- Litwa
- Luxemburg
- Malta
- Holandia
- Polska
- Portugalia
- Rumunia
- Słowacja
- Słowenia
- Hiszpania
- Szwecja
- kwart

## <a name="eu-national-identification-number"></a>Krajowy numer identyfikacyjny UE

Są to jednostki w krajowym typie informacji poufnych o numerze identyfikacyjnym Unii Europejskiej.

- Austria
- Belgia
- Bułgaria
- Chorwacja
- Cypr
- Czeski
- Dania
- Estonia
- Finlandia
- Francja
- Niemcy
- Grecja
- Węgry
- Irlandia
- Włochy
- Łotwa
- Litwa
- Luxemburg
- Malta
- Holandia
- Polska
- Portugalia
- Rumunia
- Słowacja
- Słowenia
- Hiszpania
- kwart

## <a name="eu-passport-number"></a>Numer paszportu UE

Są to jednostki znajdujące się w pakiecie numer paszportu UE typeThese informacji poufnych.

- Austria
- Belgia
- Bułgaria
- Chorwacja
- Cypr
- Czeski
- Dania
- Estonia
- Finlandia
- Francja
- Niemcy
- Grecja
- Węgry
- Irlandia
- Włochy
- Łotwa
- Litwa
- Luxemburg
- Malta
- Holandia
- Polska
- Portugalia
- Rumunia
- Słowacja
- Słowenia
- Hiszpania
- Szwecja
- kwart

## <a name="eu-social-security-number-or-equivalent-identification"></a>Numer ubezpieczenia społecznego UE lub równoważna identyfikacja

Są to jednostki, które znajdują się w numerze ubezpieczenia społecznego UE lub równoważnej identyfikacji typ informacji poufnych.

- Austria
- Belgia
- Chorwacja
- Czeski
- Dania
- Finlandia
- Francja
- Niemcy
- Grecja
- Węgry
- Portugalia
- Hiszpania
- Szwecja

## <a name="eu-tax-identification-number"></a>Numer identyfikacyjny podatku UE

Te jednostki znajdują się w typie informacji poufnych dla numeru NIP w Unii Europejskiej.

- Austria
- Belgia
- Bułgaria
- Chorwacja
- Cypr
- Czeski
- Dania
- Estonia
- Finlandia
- Francja
- Niemcy
- Grecja
- Węgry
- Irlandia
- Włochy
- Łotwa
- Litwa
- Luxemburg
- Malta
- Holandia
- Polska
- Portugalia
- Rumunia
- Słowacja
- Słowenia
- Hiszpania
- Szwecja
- kwart



## <a name="finland-national-id"></a>Identyfikator Narodowy Finlandii

### <a name="format"></a>Format

sześć cyfr plus znak wskazujący Century oraz trzy cyfry oraz cyfrę kontrolną

### <a name="pattern"></a>Wzorce

Wzorzec musi zawierać wszystkie następujące elementy:

- sześć cyfr w formacie DDMMYY, który jest datą urodzenia
- znacznik wieku ("-", "+" lub "a")
- 3-cyfrowy osobisty numer identyfikacyjny
- cyfra lub litera (bez uwzględniania wielkości liter), która jest cyfrą czeku

### <a name="keywords"></a>Słowa kluczowe

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Numer paszportu Finlandia

Ta jednostka typu informacji poufnych jest dostępna w typie informacji poufnych z numerem paszportu UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

kombinacja dziewięciu liter i cyfr

### <a name="pattern"></a>Wzorce

kombinacja dziewięciu liter i cyfr:

- dwie litery (bez uwzględniania wielkości liter)
- siedem cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keywords_eu_passport_number_common"></a>Słowa kluczowe w \_ Unii Europejskiej — \_ \_ liczba \_ wspólnych

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>\_ \_ Numer paszportu dla słowa kluczowego Finlandia \_

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Numer licencji sterownika Francja

Ta jednostka typu informacji poufnych jest dostępna w ramach numeru licencji sterownika UE typu informacji poufnych i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

12 cyfr

### <a name="pattern"></a>Wzorce

12 cyfr z walidacją do rabatu podobne wzorce, takie jak francuskie numery telefonów

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_french_drivers_license"></a>Słowa kluczowe \_ języka francuskiego \_ \_ licencja

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Francja National ID Card (CNI)

### <a name="format"></a>Format

12 cyfr

### <a name="pattern"></a>Wzorce

12 cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keywords_france_eu_national_id_card"></a>Słowa \_ kluczowe \_ Francja \_ UE \_ Identyfikator \_ krajowy

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Numer paszportu Francji

Ta jednostka typu informacji poufnych jest dostępna w typie informacji poufnych z numerem paszportu UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

dziewięć cyfr i liter

### <a name="pattern"></a>Wzorce

dziewięć cyfr i liter:

- dwie cyfry
- dwie litery (bez uwzględniania wielkości liter)
- pięć cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_passport"></a>Paszport — słowo kluczowe \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Francja (INSEE) lub równoważna identyfikacja

Ta jednostka typu informacji poufnych jest uwzględniona w numerze ubezpieczenia społecznego UE oraz równoważnej IDENTYFIKATORze informacji poufnych i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

15 cyfr

### <a name="pattern"></a>Wzorce

Musi być zgodny z jednym z dwóch wzorców:

- 13 cyfr, po których następuje spacja, po której następuje dwie cyfry lub
- 15 kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_fr_insee"></a>Słowo kluczowe \_ fr \_ Insee

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Numer licencji sterownika Niemcy

Ta jednostka typu informacji poufnych jest dołączona do typu informacji poufnych o numerze licencji sterownika UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

kombinacja 11 cyfr i liter

### <a name="pattern"></a>Wzorce

11 cyfr i liter (bez uwzględniania wielkości liter):

- cyfra lub litera
- dwie cyfry
- sześć cyfr lub liter
- cyfra
- cyfra lub litera

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_german_drivers_license_number"></a>Słowo \_ kluczowe \_ niemiecki \_ Numer licencji dla sterowników \_

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Numer karty tożsamości (Niemcy)

### <a name="format"></a>Format

od 1 listopada 2010: dziewięć liter i cyfr

od 1 kwietnia 1987 do 31 października 2010:10 cyfr

### <a name="pattern"></a>Wzorce

od 1 listopada 2010:

- jedna litera (bez uwzględniania wielkości liter)
- osiem cyfr

od 1 kwietnia 1987 do 31 października 2010:

- 10 cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_germany_id_card"></a>Słowo kluczowe \_ Niemcy — \_ \_ karta identyfikatora

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Numer paszportu Niemiec

Ta jednostka typu informacji poufnych jest uwzględniona w typie informacji poufnych z numerem paszportu UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

10 cyfr lub liter

### <a name="pattern"></a>Wzorce

Wzorzec musi zawierać wszystkie następujące elementy:

- pierwszy znak jest cyfrą lub literą z tego zestawu (C, F, G, H, J, K)
- trzy cyfry
- pięć cyfr lub liter z tego zestawu (C,-H, J-N, P, R, T, V-Z)
- cyfra

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_german_passport"></a>Słowo kluczowe \_ niemiecki \_ paszport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>Krajowa karta identyfikatora

### <a name="format"></a>Format

Kombinacja 7-8 liter i cyfr oraz kreski

### <a name="pattern"></a>Wzorce

Siedem liter i cyfr (stary format):

- Jedna litera (dowolna litera alfabetu greckiego)
- Kreska
- Sześć cyfr

Osiem liter i cyfr (nowy format):

- Dwie litery, których wielką literą występuje zarówno alfabet grecki, jak i łaciński (ABEZHIKMNOPTYX)
- Kreska
- Sześć cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_greece_id_card"></a>Keyword \_ — \_ \_ karta identyfikatorów

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>Numer karty tożsamości Hongkong (HKID)

### <a name="format"></a>Format

Kombinacja 8-9 liter i cyfr oraz nawiasów opcjonalnych wokół znaku końcowego

### <a name="pattern"></a>Wzorce

Kombinacja 8-9 liter:

- 1-2 liter (bez uwzględniania wielkości liter)
- Sześć cyfr
- Znak końcowy (dowolna cyfra lub litera A), który jest cyfrą czeku i jest opcjonalnie ujęty w nawiasy.

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_hong_kong_id_card"></a>\_ \_ \_ Karta identyfikatora hongkonga słowa kluczowego \_

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>Adres IP

### <a name="format"></a>Format

#### <a name="ipv4"></a>Adresów

Złożony wzorzec, które konta dla sformatowanych (okresowych) i niesformatowanych (bez kropek) wersji adresów IPv4

#### <a name="ipv6"></a>If

Złożony wzorzec obsługujący sformatowane numery protokołu IPv6 (w tym dwukropki)

### <a name="pattern"></a>Wzorce

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_ipaddress"></a>Element IPAddress słowa kluczowego \_

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Numer stałego konta Indie (PAN)

### <a name="format"></a>Format

10 liter lub cyfr

### <a name="pattern"></a>Wzorce

10 liter lub cyfr:

- Trzy litery (bez uwzględniania wielkości liter)
- Litera w C, P, H, F, A, T, B, L, J, G (bez uwzględniania wielkości liter)
- Litera
- Cztery cyfry
- Litera (bez uwzględniania wielkości liter)

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_india_permanent_account_number"></a>Słowo kluczowe " \_ Indie" \_ stałego \_ \_ numeru konta

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Liczba unikatowych identyfikatorów (AADHAAR) Indii

### <a name="format"></a>Format

12 cyfr zawierających opcjonalne spacje lub kreski

### <a name="pattern"></a>Wzorce

12 cyfr:

- Cyfra, która nie jest równa 0 ani 1
- Trzy cyfry
- Opcjonalne miejsce lub kreska
- Cztery cyfry
- Opcjonalne miejsce lub kreska
- Końcowa cyfra, która jest cyfrą kontrolną

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_india_aadhar"></a>Słowo kluczowe \_ Indie \_ Aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>Numer karty tożsamości Indonezja (KTP)

### <a name="format"></a>Format

16 cyfr zawierających okresy opcjonalne

### <a name="pattern"></a>Wzorce

16 cyfr:

- Dwucyfrowy kod prowincji
- Kropka (opcjonalnie)
- Dwucyfrowy kod Regency lub miasto
- Dwucyfrowy kod okręgu
- Kropka (opcjonalnie)
- Sześć cyfr w formacie DDMMYY, który jest datą urodzenia
- Kropka (opcjonalnie)
- Cztery cyfry

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_indonesia_id_card"></a>\_ \_ Karta identyfikatorów słów \_ kluczowych

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>Międzynarodowy numer konta bankowego (IBAN)

### <a name="format"></a>Format

Kod kraju (dwie litery) plus cyfry kontroli (dwie cyfry) plus cyfra :::no-loc text="bban"::: (do 30 znaków)

### <a name="pattern"></a>Wzorce

Wzorzec musi zawierać wszystkie następujące elementy:

- Dwuliterowy kod kraju
- Dwie cyfry kontroli (po których następuje spacja)
- 1-7 grup czterech liter lub cyfr (może być oddzielone spacjami)
- 1-3 liter lub cyfr

Format każdego z krajów jest nieco inny. Typ informacji poufnych dla IBAN obejmuje następujące 60 krajów:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Słowa kluczowe

Brak

## <a name="ireland-personal-public-service-pps-number"></a>Numer Personal Public Service (PPS)

### <a name="format"></a>Format

Stary format (do 31 grudnia 2012):

- siedem cyfr, po których następuje 1-2 liter

Nowy format (1 Jan 2013 i po):

- siedem cyfr, po których następuje dwie litery

### <a name="pattern"></a>Wzorce

Stary format (do 31 grudnia 2012):

- siedem cyfr
- jeden do dwóch liter (bez uwzględniania wielkości liter)

Nowy format (1 Jan 2013 i po):

- siedem cyfr
- litera (bez uwzględniania wielkości liter), która jest cyfrą czeku alfabetycznego
- Opcjonalna litera z zakresu A-I lub &quot; W&quot;

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keywords_ireland_eu_national_id_card"></a>Słowa kluczowe \_ Irlandia — \_ \_ Narodowy \_ Identyfikator \_ karty

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Numer konta bankowego Izraela

### <a name="format"></a>Format

13 cyfr

### <a name="pattern"></a>Wzorce

Poprawić

- dwie cyfry
- kreska
- trzy cyfry
- kreska
- osiem cyfr

Niesformatowany:

- 13 kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_israel_bank_account_number"></a>Słowo kluczowe \_ Izraela — \_ \_ numer konta bankowego \_

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Krajowy numer identyfikacyjny Izraela

### <a name="format"></a>Format

dziewięć cyfr

### <a name="pattern"></a>Wzorce

dziewięć kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_israel_national_id"></a>\_ \_ Identyfikator Narodowy słowa kluczowego Izraela \_

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Numer licencji sterownika Włochy

Ta jednostka typu informacji poufnych jest dołączona do typu informacji poufnych o numerze licencji sterownika UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

kombinacja 10 liter i cyfr

### <a name="pattern"></a>Wzorce

kombinacja 10 liter i cyfr:

- jedna litera (bez uwzględniania wielkości liter)
- litera &quot; A &quot; lub &quot; V &quot; (bez uwzględniania wielkości liter)
- siedem cyfr
- jedna litera (bez uwzględniania wielkości liter)

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_italy_drivers_license_number"></a>Słowo \_ kluczowe \_ Włochy \_ \_ Numer licencji

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Numer konta bankowego Japonii

### <a name="format"></a>Format

siedem lub osiem cyfr

### <a name="pattern"></a>Wzorce

Numer konta bankowego:

- siedem lub osiem cyfr
- kod rozgałęzienia konta bankowego:
- cztery cyfry
- spacja lub kreska (opcjonalnie)
- trzy cyfry

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_jp_bank_account"></a>\_ \_ Konto bankowe — słowo kluczowe \_

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Słowo kluczowe \_ JP — \_ \_ kod gałęzi banku \_

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Numer licencji sterownika Japonii

### <a name="format"></a>Format

12 cyfr

### <a name="pattern"></a>Wzorce

12 kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_jp_drivers_license_number"></a>Słowo kluczowe "keywords" — \_ \_ \_ \_ Numer licencji

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Numer paszportu Japonia

### <a name="format"></a>Format

dwie litery, po których następuje siedem cyfr

### <a name="pattern"></a>Wzorce

dwie litery (bez uwzględniania wielkości liter), po których następuje siedem cyfr

#### <a name="keyword_jp_passport"></a>Słowo kluczowe \_ JP \_ usługi Passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Numer karty pobytu Japonia

### <a name="format"></a>Format

12 liter i cyfr

### <a name="pattern"></a>Wzorce

12 liter i cyfr:

- dwie litery (bez uwzględniania wielkości liter)
- osiem cyfr
- dwie litery (bez uwzględniania wielkości liter)

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_jp_residence_card_number"></a>Słowo \_ kluczowe \_ \_ \_

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Numer rejestracji rezydenta Japonia

### <a name="format"></a>Format

11 cyfr

### <a name="pattern"></a>Wzorce

11 kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_jp_resident_registration_number"></a>\_ \_ Numer rejestracji rezydentów słowa \_ kluczowego JP \_

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Numer ubezpieczenia społecznego (Japonia)

### <a name="format"></a>Format

7-12 cyfr

### <a name="pattern"></a>Wzorce

7-12 cyfr:

- cztery cyfry
- Łącznik (opcjonalnie)
- sześć cyfr lub
- 7-12 kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_jp_sin"></a>Słowo kluczowe " \_ \_ Sin"

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>Numer karty identyfikacyjnej Malezji

### <a name="format"></a>Format

12 cyfr zawierających łączniki opcjonalne

### <a name="pattern"></a>Wzorce

12 cyfr:

- sześć cyfr w formacie YYMMDD, który jest datą urodzenia
- kreska (opcjonalnie)
- dwuliterowy kod miejsca urodzenia
- kreska (opcjonalnie)
- trzy cyfry losowe
- jednocyfrowy kod płci

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_malaysia_id_card_number"></a>Słowo \_ kluczowe \_ ID \_ — \_ numer karty

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>Numer usługi BSN (Holandia)

### <a name="format"></a>Format

osiem dziewięciu cyfr zawierających spacje opcjonalne

### <a name="pattern"></a>Wzorce

osiem dziewięciu cyfr:

- trzy cyfry
- spacja (opcjonalnie)
- trzy cyfry
- spacja (opcjonalnie)
- dwie cyfry

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keywords_netherlands_eu_national_id_card"></a>Słowa \_ kluczowe \_ Holandia \_ nazwa krajowa Unii Europejskiej \_ \_

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>Numer Ministerstwa kondycji Nowej Zelandii

### <a name="format"></a>Format

trzy litery, spacja (opcjonalnie) i cztery cyfry

### <a name="pattern"></a>Wzorce

- trzy litery (bez uwzględniania wielkości liter) z wyjątkiem "I" i "O"
- spacja (opcjonalnie)
- cztery cyfry

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_nz_terms"></a>Warunki dla słowa kluczowego \_ NZ \_

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Numer identyfikacyjny Norwegii

### <a name="format"></a>Format

11 cyfr

### <a name="pattern"></a>Wzorce

11 cyfr:

- sześć cyfr w formacie DDMMYY, który jest datą urodzenia
- 3-cyfrowy numer indywidualny
- dwie cyfry kontroli

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_norway_id_number"></a>Słowo \_ kluczowe \_ Norwegia \_ numer identyfikacyjny

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Filipiny — ujednolicony numer identyfikacyjny dla wielu celów

### <a name="format"></a>Format

12 cyfr rozdzielonych łącznikami

### <a name="pattern"></a>Wzorce

12 cyfr:

- cztery cyfry
- Łącznik
- siedem cyfr
- Łącznik
- jedna cyfra

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_philippines_id"></a>Identyfikator dla słowa kluczowego \_ \_

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Karta tożsamości Polska

### <a name="format"></a>Format

trzy litery i sześć cyfr

### <a name="pattern"></a>Wzorce

trzy litery (bez uwzględniania wielkości liter), po których następuje sześć cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_poland_national_id_passport_number"></a>Słowo kluczowe \_ Polski o \_ \_ identyfikatorze krajowym \_ paszportu \_

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>Krajowy identyfikator Polski (PESEL)

### <a name="format"></a>Format

11 cyfr

### <a name="pattern"></a>Wzorce

- 6 cyfr reprezentujących datę urodzenia w formacie YYMMDD
- 4 cyfry
- 1 cyfra kontrolki

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_pesel_identification_number"></a>PESEL słów kluczowych — \_ \_ numer identyfikacyjny \_

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Numer paszportu Polska

Ta jednostka typu informacji poufnych jest uwzględniona w typie informacji poufnych z numerem paszportu UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

dwie litery i siedem cyfr

### <a name="pattern"></a>Wzorce

Dwie litery (bez uwzględniania wielkości liter), po których następuje siedem cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_poland_national_id_passport_number"></a>Słowo kluczowe \_ Polski o \_ \_ identyfikatorze krajowym \_ paszportu \_

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Numer karty obywatela Portugalia

### <a name="format"></a>Format

osiem cyfr

### <a name="pattern"></a>Wzorce

osiem cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_portugal_citizen_card"></a>\_ \_ \_ Karta

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Numer licencji sterownika Portugalia

Ta jednostka typu informacji poufnych jest dostępna tylko w typie informacji poufnych o numerze licencji sterownika UE.

### <a name="format"></a>Format

dwa wzorce — dwie litery, po których następuje 5-8 cyfr ze znakami specjalnymi

### <a name="pattern"></a>Wzorce

Wzorzec 1: dwie litery, po których następuje 5/6 ze znakami specjalnymi:

- Dwie litery (bez uwzględniania wielkości liter)
- Łącznik
- Pięć lub sześć cyfr
- Spacja
- Jedna cyfra

Wzorzec 2: jedna litera, po której następuje 6/8 cyfr ze znakami specjalnymi:

- Jedna litera (bez uwzględniania wielkości liter)
- Łącznik
- Sześć lub osiem cyfr
- Spacja
- Jedna cyfra

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keywords_eu_drivers_license_number"></a>Keywords \_ \_ — \_ Numer licencji \_ sterownika UE

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Słowa kluczowe \_ Portugalię Portugalia \_ UE \_ \_ Numer licencji sterownika \_

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>Identyfikator Narodowy Arabii Saudyjskiej

### <a name="format"></a>Format

10 cyfr

### <a name="pattern"></a>Wzorce

10 kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_saudi_arabia_national_id"></a>Słowo kluczowe \_ Arabia \_ Saudyjska o \_ \_ identyfikatorze Narodowym

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Numer międzynarodowej karty tożsamości NIP (NRIC)

### <a name="format"></a>Format

dziewięć liter i cyfr

### <a name="pattern"></a>Wzorce

- dziewięć liter i cyfr:
- litera &quot; F &quot; , &quot; G &quot; , &quot; S &quot; lub &quot; T &quot; (bez uwzględniania wielkości liter)
- siedem cyfr
- cyfra sprawdzająca alfabetycznego

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_singapore_nric"></a>Słowo kluczowe \_ Singapur \_ NRIC

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>Numer identyfikacyjny Republiki Południowej Afryki

### <a name="format"></a>Format

13 cyfr, które mogą zawierać spacje

### <a name="pattern"></a>Wzorce

13 cyfr:

- sześć cyfr w formacie YYMMDD, który jest datą urodzenia
- cztery cyfry
- wskaźnik obywatelstwa jednocyfrowego
- cyfra &quot; 8 &quot; lub &quot; 9&quot;
- jedna cyfra, która jest cyfrą sumy kontrolnej

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_south_africa_identification_number"></a>\_ \_ \_ Numer identyfikacyjny dla słowa kluczowego w Republice Południowej Afryki \_

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Numer rejestracji rezydenta Korea Południowa

### <a name="format"></a>Format

13 cyfr zawierających łącznik

### <a name="pattern"></a>Wzorce

13 cyfr:

- sześć cyfr w formacie YYMMDD, który jest datą urodzenia
- Łącznik
- jedna cyfra określona przez Century i płeć
- czterocyfrowy kod regionu-urodzenia
- jedna cyfra służąca do rozróżniania osób, dla których poprzednie numery są identyczne
- cyfra kontrolna.

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_south_korea_resident_number"></a>Słowo kluczowe " \_ \_ \_ numer rezydentny Korea Południowa" \_

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>Numer ubezpieczenia społecznego (SSN) w Hiszpanii

Ta jednostka typu informacji poufnych jest uwzględniona w numerze ubezpieczenia społecznego UE lub równoważnej IDENTYFIKATORze informacji poufnych, która jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

11-12 cyfr

### <a name="pattern"></a>Wzorce

11-12 cyfr:

- dwie cyfry
- ukośnik (opcjonalnie)
- od siedmiu do ośmiu cyfr
- ukośnik (opcjonalnie)
- dwie cyfry

### <a name="keywords"></a>Słowa kluczowe

Brak

## <a name="sweden-national-id"></a>Narodowy identyfikator Szwecji

### <a name="format"></a>Format

10 lub 12 cyfr i opcjonalny ogranicznik

### <a name="pattern"></a>Wzorce

10 lub 12 cyfr i opcjonalny ogranicznik:

- dwie cyfry (opcjonalnie)
- Sześć cyfr w formacie daty YYMMDD
- Ogranicznik &quot; - &quot; lub &quot; + &quot; (opcjonalnie)
- cztery cyfry

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keywords_swedish_national_identifier"></a>Słowa kluczowe \_ szwedzki \_ Narodowy \_ Identyfikator

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Szwecja numer paszportu

Ta jednostka typu informacji poufnych jest uwzględniona w typie informacji poufnych z numerem paszportu UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

osiem cyfr

### <a name="pattern"></a>Wzorce

osiem kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_sweden_passport"></a>Słowo kluczowe \_ Szwecja \_ Passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Paszport — słowo kluczowe \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>Kod SWIFT

### <a name="format"></a>Format

cztery litery, po których następuje 5-31 liter lub cyfr

### <a name="pattern"></a>Wzorce

cztery litery, po których następuje 5-31 liter lub cyfr:

- czterocyfrowy kod banku (bez uwzględniania wielkości liter)
- opcjonalne miejsce
- 4-28 liter lub cyfr (podstawowy numer konta bankowego (BBAN))
- opcjonalne miejsce
- od jednej do trzech liter lub cyfr (reszta BBAN)

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_swift"></a>Słowo kluczowe \_ SWIFT

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Narodowy numer identyfikacyjny Tajwanu

### <a name="format"></a>Format

jedna litera (w języku angielskim), a następnie dziewięć cyfr

### <a name="pattern"></a>Wzorce

jedna litera (w języku angielskim), a następnie dziewięć cyfr:

- jedna litera (w języku angielskim, bez uwzględniania wielkości liter)
- cyfra &quot; 1 &quot; lub &quot; 2&quot;
- osiem cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_taiwan_national_id"></a>\_ \_ Identyfikator Narodowy Tajwanu słowa kluczowego \_

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Numer paszportu tajwańskiego

### <a name="format"></a>Format

- Numer paszportu biometrycznego: dziewięć cyfr
- Numer paszportu bez biometrycznego: dziewięć cyfr

### <a name="pattern"></a>Wzorce

Numer paszportu biometrycznego:

- znak &quot; 3&quot;
- osiem cyfr

Numer paszportu bez biometrycznego:

- dziewięć cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_taiwan_passport"></a>Słowo kluczowe \_ Tajwan — \_ paszport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Numer certyfikatu rezydentnego tajwańskiego (ARC/TARC)

### <a name="format"></a>Format

10 liter i cyfr

### <a name="pattern"></a>Wzorce

10 liter i cyfr:

- dwie litery (bez uwzględniania wielkości liter)
- osiem cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_taiwan_resident_certificate"></a>Słowo \_ kluczowe \_ Tajwan \_ certyfikatu rezydentnego

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>kwart Numer licencji sterownika

Ta jednostka typu informacji poufnych jest dołączona do typu informacji poufnych o numerze licencji sterownika UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

Kombinacja 18 liter i cyfr w określonym formacie

### <a name="pattern"></a>Wzorce

18 liter i cyfr:

- pięć liter (bez uwzględniania wielkości liter) lub cyfry &quot; 9 &quot; zamiast litery
- jedna cyfra
- pięć cyfr w formacie daty MMDDY dla daty urodzenia (siódmy znak jest zwiększany o 50, jeśli sterownik jest żeński, czyli 51 do 62 zamiast 01 do 12)
- dwie litery (bez uwzględniania wielkości liter) lub cyfry &quot; 9 &quot; zamiast litery
- pięć cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_uk_drivers_license"></a>\_Licencja na brytyjskie \_ sterowniki słów kluczowych \_

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>kwart Numer rzutowania wyborczyego

### <a name="format"></a>Format

dwie litery, po których następuje 1-4 cyfr

### <a name="pattern"></a>Wzorce

dwie litery (bez uwzględniania wielkości liter), po których następuje 1-4 cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_uk_electoral"></a>Słowo kluczowe \_ Zjednoczone Królestwo \_

- Nominacja Rady
- formularz nominacji
- Rejestr wyborczy
- Rzutowanie wyborczye

## <a name="uk-national-health-service-number"></a>kwart Numer krajowej usługi kondycji

### <a name="format"></a>Format

10-17 cyfr rozdzielonych spacjami

### <a name="pattern"></a>Wzorce

10-17 cyfr:

- trzy lub 10 cyfr
- spacja
- trzy cyfry
- spacja
- cztery cyfry

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_uk_nhs_number"></a>Słowo kluczowe \_ Zjednoczone Królestwo \_ NHS \_ Numer

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Słowo kluczowe \_ UK \_ NHS \_ Liczba1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Słowo kluczowe \_ UK \_ NHS \_ Numer \_ dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>kwart krajowy numer ubezpieczenia (NINO)

Ta jednostka typu informacji poufnych jest uwzględniona w krajowym typie informacji o numerze identyfikacyjnym UE i jest dostępna jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

siedem znaków lub dziewięć znaków rozdzielonych spacjami lub kreskami

### <a name="pattern"></a>Wzorce

dwa możliwe wzorce:

- dwie litery (prawidłowe NINOs używają tylko niektórych znaków z tego prefiksu, które są sprawdzane przez ten wzorzec; bez uwzględniania wielkości liter)
- sześć cyfr
- "A", "B", "C" lub "d" (jak prefiks, tylko niektóre znaki są dozwolone w sufiksie; bez uwzględniania wielkości liter)

LUB

- dwie litery
- spacja lub kreska
- dwie cyfry
- spacja lub kreska
- dwie cyfry
- spacja lub kreska
- dwie cyfry
- spacja lub kreska
- "A", "B", "C" lub "'D"


### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_uk_nino"></a>Słowo kluczowe \_ UK \_ Nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>kwart Unikatowy numer referencyjny podatnika

Ten typ informacji poufnych jest dostępny tylko w programie:

- Zasady ochrony przed utratą danych
- zasady zgodności komunikacji
- Zarządzanie informacjami
- Zarządzanie rekordami
- Usługa Microsoft Cloud App Security

### <a name="format"></a>Format

10 cyfr bez spacji i ograniczników

### <a name="pattern"></a>Wzorce

10 cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keywords_uk_eu_tax_file_number"></a>Słowa kluczowe — \_ Wielka Brytania — \_ \_ \_ numer pliku podatku UE \_

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>Numer konta bankowego USA

### <a name="format"></a>Format

6-17 cyfr

### <a name="pattern"></a>Wzorce

6-17 kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_usa_bank_account"></a>Słowo \_ kluczowe \_ USA \_ konto bankowe

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>Numer licencji sterownika USA

### <a name="format"></a>Format

Zależy od stanu

### <a name="pattern"></a>Wzorce

zależy od stanu — na przykład New York:

- dziewięć cyfr sformatowanych jak DDD będzie pasować.
- dziewięć cyfr, takich jak ddddddddd, nie jest zgodnych.

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_us_drivers_license_abbreviations"></a>Skróty słów kluczowych \_ \_ \_ \_

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Słowo kluczowe " \_ \_ licencja US Drivers" \_

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Słowo kluczowe \_ [State \_ name \_ ] \_ Nazwa licencji sterowników \_

- skrót stanu (na przykład &quot; NY &quot; )
- Nazwa stanu (na przykład &quot; New York &quot; )

## <a name="us-individual-taxpayer-identification-number-itin"></a>Numer identyfikacji podatkowej w Stanach Zjednoczonych (ITIN)

### <a name="format"></a>Format

dziewięć cyfr, które zaczynają się od &quot; 9 &quot; i zawierają &quot; 7 &quot; lub &quot; 8 &quot; jako czwartą cyfrę, opcjonalnie sformatowane przy użyciu spacji lub kresek

### <a name="pattern"></a>Wzorce

poprawić

- cyfra &quot; 9&quot;
- dwie cyfry
- spacja lub kreska
- &quot;7 &quot; lub &quot; 8&quot;
- cyfra
- spacja lub kreska
- cztery cyfry

niesformatowany

- cyfra &quot; 9&quot;
- dwie cyfry
- &quot;7 &quot; lub &quot; 8&quot;
- pięć cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_itin"></a>ITIN słowa kluczowego \_

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>Numer ubezpieczenia społecznego USA (SSN)

### <a name="format"></a>Format

dziewięć cyfr, które mogą znajdować się w sformatowanym lub niesformatowanym wzorcu

>[!Note]
> W przypadku wystawienia przed średnim 2011, numer PESEL ma silne formatowanie, w przypadku których niektóre części numeru muszą należeć do określonych zakresów, które mają być prawidłowe (ale nie ma sumy kontrolnej).
>

### <a name="pattern"></a>Wzorce

cztery funkcje szukają SSNs w czterech różnych wzorcach:

- Funkcja Func ( \_ SSN) umożliwia znalezienie SSNs z użyciem silnego formatowania sprzed 2011, które jest sformatowane przy użyciu kresek lub spacji (DDD-DD-dddd lub DDD DD dddd)
- \_Niesformatowany \_ numer PESEL umożliwia znalezienie SSNs z poprzedzającym 2011 silnym formatowaniem, które nie jest sformatowane jako dziewięć kolejnych cyfr (ddddddddd)
- Funkcja Func \_ randomd \_ sformatowana \_ 2011 opłata zaczyna znajdować SSNs, które są sformatowane przy użyciu kresek lub spacji (DDD-DD-dddd lub DDD DD dddd)
- Funkcja Func \_ losowo \_ niesformatowany \_ numer PESEL umożliwia znalezienie wpisu post-2011 SSNs, które nie są sformatowane jako dziewięć kolejnych cyfr (ddddddddd)

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_ssn"></a>Słowo kluczowe \_ SSN

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>USA/ZJEDNOCZONE KRÓLESTWO Numer paszportu

Zjednoczone Królestwo Typ informacji wrażliwych na numer paszportu jest dostępny w typie informacji poufnych o numerze w Unii Europejskiej i jest dostępny jako autonomiczna jednostka typu informacji poufnych.

### <a name="format"></a>Format

dziewięć cyfr

### <a name="pattern"></a>Wzorce

dziewięć kolejnych cyfr

### <a name="keywords"></a>Słowa kluczowe

#### <a name="keyword_passport"></a>Paszport — słowo kluczowe \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
