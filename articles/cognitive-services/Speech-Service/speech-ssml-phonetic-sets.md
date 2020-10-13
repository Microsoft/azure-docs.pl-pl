---
title: Zestawy fonetyczne mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, w jaki sposób alfabetyczne alfabety fonetyczne są mapowane na Międzynarodowy alfabet fonetyczny (IPA) i kiedy należy używać tego zestawu.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78675341"
---
# <a name="speech-service-phonetic-sets"></a>Zestawy fonetyczne usługi mowy

Usługa mowy definiuje alfabety fonetyczne ("zestawy telefonów" jako krótkie), składające się z siedmiu języków: `en-US`,,,,, `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` i `zh-TW` . Zestawy telefonów usługi Speech Service są zwykle mapowane na <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Międzynarodowy alfabet fonetyczny (IPA <span class="docon docon-navigate-external x-hidden-focus"></span> ) </a>. Zestawy telefonów usługi Speech Service są używane w połączeniu z [językiem SSML (Speech syntezing Language)](speech-synthesis-markup.md)w ramach oferty usługi zamiany tekstu na mowę. W tym artykule dowiesz się, jak te zestawy telefonów są mapowane i kiedy używać zestawu telefonów.

# <a name="en-us"></a>[pl-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>Suprasegmentals angielski

| Przykład 1 (wystąpienie początku dla z spółgłoską, Inicjał słowa dla samogłosek) | Przykład 2 (intervocalic dla zgodne, jądro programu Word Medial dla samogłosek) | Przykład 3 (Coda dla spółgłoski, wyraz końcowy dla samogłosek) | Komentarze |
|--|--|--|--|
| Burger/b er **1** r-g AX r/ | Falafel/f AX-l AA **1** -f AX l/ | gitarze/g IH-t AA **1** r/ | Zestaw telefonów usługi Speech Service jest obciążeniowy po wygłosach nałożonej sylaby |
| inopportune/IH **2** -n AA-p AX r-t UW 1 n/ | niepodobieństwo/d IH-s IH **2**-m AX-l EH 1-r AX-t m/ | pracownicy/w er 1 r k-f Ao **2** r s/ | Zestaw telefonów usługi Speech Service nałożył nacisk po samogłosowym sylabie. |

### <a name="english-vowels"></a>Angielskie głoski

| `sapi` | `ipa` | Przykład 1     | Przykład 2 | Przykład 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| m     | `i`   | **EA**t       | f**EE**l  | Vall**EY**                  |
| ih     | `ɪ`   | **i**f        | wszystkie**i**  |                             |
| ey     | `eɪ`  | **te**       | g**a**  | d**AY**                     |
| EH     | `ɛ`   | **e**bardzo     | p**e**   | m**EH** (rzadki wyraz na końcu) |
| AE     | `æ`   | **ktywny**    | c**a**t   | n**Ah** (rzadki wyraz na końcu) |
| aa     | `ɑ`   | **o**bstinate | p**o**ppy | r**Ah** (rzadki wyraz na końcu) |
| ao     | `ɔ`   | **o**zakres    | c —**au**SE | UT —**Ah**                    |
| zapomniano     | `ʊ`   | b**oo**k      |           |                             |
| Okaż     | `oʊ`  | **o**LD       | CL**o**ne | g**o**                      |
| UW     | `u`   | **U**      | b**oo**St | **oo** t                     |
| Protokół     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| ay     | `aɪ`  | **i**CE       | b**i**  | FL**y**                     |
| AW     | `aʊ`  | **jednostka organizacyjna**t       | s**OU**th | c**ow**                     |
| Oy-     | `ɔɪ`  | **Oi**l       | j**Oi**n  | **Oy-** t                     |
| UW y   | `ju`  | **Yu**ma      | p**u**Man | f**Wa**                     |
| oś     | `ə`   | **a**go       | WOM**n** | to**a**                    |

### <a name="english-r-colored-vowels"></a>Angielskie i kolorowe odgłoski języka R

| `sapi` | `ipa` | Przykład 1    | Przykład 2      | Przykład 3  |
|--------|-------|--------------|----------------|------------|
| IH r   | `ɪɹ`  | **Przeczyść**s     | t amisu**IR**   | n-**kolczyk**   |
| EH r   | `ɛɹ`  | Płaszczyzna **powietrzna** | ently**aplikacji** | SC**AR**e  |
| zapomniano r   | `ʊɹ`  |              |                | c**ur**   |
| ay r   | `aɪɹ` | **Wygasnąć**ziemi  | stąp f**IR**  | ch**OIR**  |
| AW r   | `aʊɹ` | **godz**. s    | p**ższa**pełna   | s**nasze**   |
| Ao r   | `ɔɹ`  | **lub**akresu   | m**lub**Al      | **OAR** s   |
| AA r   | `ɑɹ`  | **AR**TIST   | St**AR**t      | c**AR**    |
| er r   | `ɝ`   | **odgięty**    | b**IR**d       | f**ur**    |
| AX r   | `ɚ`   |              | wszystkie Gy**er**    | SUPP**er** |

### <a name="english-semivowels"></a>Semivowels angielski

| `sapi` | `ipa` | Przykład 1           | Przykład 2  | Przykład 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**itd **. de** | Al**w**AYS |           |
| Y      | `j`   | **t**ARD, f**e**w   | włączone**i**  |           |

### <a name="english-aspirated-oral-stops"></a>Przejścia w Stanach doustnych w języku angielskim

| `sapi` | `ipa` | Przykład 1 | Przykład 2   | Przykład 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**UT   | ha**PP**pl  | FLA**p**   |
| b      | `b`   | **b**IG   | NUM**b**er  | CRA**b**   |
| t      | `t`   | ALK **t**  | Interfejs CAPI**t**Al | sough**t** |
| d      | `d`   | **d**IG   | uruchomiono obiekt OM**d**  | ro**d**    |
| k      | `k`   | **c**UT   | **soczewka**z umowy SLA | IRA**q**   |
| g      | `g`   | **g**o    | **g**o     | Agent dra**g**   |

### <a name="english-nasal-stops"></a>Nasal w języku angielskim

| `sapi` | `ipa` | Przykład 1        | Przykład 2  | Przykład 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**o, Smash   | Urząd**certyfikacji**— era | Roo**m**    |
| n      | `n`   | **n**o, s**n** | te**n**t   | pisklęta**n** |
| gazu     | `ŋ`   |                  | li**n**k   | s**ing**    |

### <a name="english-fricatives"></a>Fricatives angielski

| `sapi` | `ipa` | Przykład 1   | Przykład 2        | Przykład 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | eć **f**    | Le**f**t         | HAL**f**   |
| v      | `v`   | **v**RG   | e **.**        | Lo ***** e   |
| th     | `θ`   | **th**w    | EMPA**th**y      | Mon**th**  |
| DH     | `ð`   | **th**    | mo**th**       | smoo**th** |
| s      | `s`   | **s**     | RI**s**         | fakt**s**  |
| z      | `z`   | **z**AP     | komp**s**y         | dziecko**s**   |
| pok     | `ʃ`   | **SH** e    | abbrevia**TI** | ru**SH**   |
| zh     | `ʒ`   | Acques **J** | & Poświęć chwilę**s**uruj     | Gara**g**e |
| h      | `h`   | ELP **h**    | pl**h**ANCE      | a –**h**a!  |

### <a name="english-affricates"></a>Affricates angielski

| `sapi` | `ipa` | Przykład 1 | Przykład 2    | Przykład 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**w  | Fu**t**uruj   | Atta**ch** |
| jh     | `dʒ`  | Oy- **j**   | ORI**g**Inal | Oran**g**e |

### <a name="english-approximants"></a>Approximants angielski

| `sapi` | `ipa` | Przykład 1          | Przykład 2  | Przykład 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | Identyfikator **l****, g g AD**  | wpis PA**l** | Chi**ll** |
| r      | `ɹ`   | **r**Ed, b**r**owanie | w najdalszej postaci**rekordu zasobu** | Ta**r**   |

# <a name="fr-fr"></a>[fr — FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Suprasegmentals francuski

Zestaw telefonu usługi mowy umożliwia nanaciskanie po samogłosach z naciskiem na sylabę; `fr-FR` zestaw telefonów usługi Speech Service nie obsługuje IPA "ˌ". Jeśli konieczne jest podIPAa, należy użyć IPA bezpośrednio.

### <a name="french-vowels"></a>Głoski francuskie

| `sapi` | `ipa` | Przykład 1     | Przykład 2       | Przykład 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **rbre**     | p**a**TTE       | IR**a**   |
| aa     | `ɑ`   |               | p**â**te        | p**a**s   |
| AA ~   | `ɑ̃`  | **pl**fant    | ENF**EN**t      | t**em**PS |
| oś     | `ə`   |               | p**e**Tite      | l**e**    |
| EH     | `ɛ`   | lle **e**      | p**e**RDU       | ét**AI**t |
| Europejska     | `ø`   | **œu**FS      | CR — ser**UE**     | Qu**UE**  |
| ey     | `e`   | ému           | crétin          | ôté       |
| EH ~   | `ɛ̃`  | Port **wiadomości błyskawicznych** | p**Ein**hwyć    | mat**in** |
| m     | `i`   | **Dée**      | PET**i**      | am**i**   |
| Outlook     | `œ`   | **œu**f       | p**UE**r        |           |
| Niestety     | `ɔ`   | **o**bstacle  | c**o**RPS pliku       |           |
| Niestety   | `ɔ̃`  | **na**rozmiar      | r**na**deur     | b**na**   |
| Okaż     | `o`   | diteur **au**  | b**Eau**    | p**ô**    |
| OE ~   | `œ̃ ` | **odinstalować**        | l**un**di       | **Wyrejestrowanie** br  |
| UW     | `u`   | **jednostka organizacyjna**trage   | Intr**OU**Vable | **jednostki organizacyjnej**    |
| uy     | `y`   | **u**ne       | p**u**imię Nir       | él**u**   |

### <a name="french-consonants"></a>Zgodne z francuskimi

| `sapi` | `ipa` | Przykład 1   | Przykład 2     | Przykład 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**Ille   | ro**b**e                         |
| d      | `d`   | **d**wygasnąć    | Piotr**d**EUR   | Chau**d**e                       |
| f      | `f`   | Emme **f**   | Su**FF**IXE   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | BA**gu**e                        |
| gazu     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)**parkowanie** |
| hy     | `ɥ`   | h**u**Iku   | n**u**wygasnąć     |                                  |
| k      | `k`   | Arte języka **c**   | é**c**Aille   | być**c**                          |
| l      | `l`   | Ong **l**    | é**l**wygasnąć     | BA**l**                          |
| m      | `m`   | **m**Adam  | AI**m**er     | ZZ**mm**e                        |
| n      | `n`   | **n**jednostek organizacyjnych    | te**n**IR     | Niemniej**NN**e                        |
| nj     | `ɲ`   |             |               | Pei**GN**e                       |
| p      | `p`   | **p**Atte   | Odtwórz**jako**     | Urząd certyfikacji**p**                          |
| r      | `ʁ`   | **r**na     | Zmień**r**IoT   | Senti**r**                       |
| s      | `s`   | Ourir **s**  | z**SS**EZ     | PA**SS**e                        |
| pok     | `ʃ`   | **ch**ante | ma**iersz**   | ZZ**ch**e                        |
| t      | `t`   | ête **t**    | ô**t**      | ne**t**                          |
| v      | `v`   | **wersja**    | w programie**v**ENTER  | rê**v**e                         |
| w      | `w`   | **jednostka organizacyjna**i     | iersz k**OU**    |                                  |
| Y      | `j`   | **y**od     | p**i**étiner  | Mars**Ille**                    |
| z      | `z`   | * * z * * éro   | Rai**s**onner | ro**s**e                         |
| zh     | `ʒ`   | Ardin **j**  | Man**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** Arbre                     |
|        | `t‿`  |             |               | Quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *tylko dla niektórych wyrazów obcych.*

> [!TIP]
> `fr-FR`Zestaw telefonów usługi Speech Service nie obsługuje następujących francuskich liasions, `n‿` , `t‿` i `z‿` . Jeśli są potrzebne, należy rozważyć użycie IPA bezpośrednio.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Suprasegmentals niemiecki

| Przykład 1 (wystąpienie początku dla z spółgłoską, Inicjał słowa dla samogłosek) | Przykład 2 (intervocalic dla zgodne, jądro programu Word Medial dla samogłosek) | Przykład 3 (Coda dla spółgłoski, wyraz końcowy dla samogłosek) | Komentarze |
|--|--|--|--|
| Anders/a **1** n-d AX r s/ | Multiplikationszeichen/m zapomniano l-t m-p l m-k a-TS y co **1** n s-TS AY-c n/ | Biologiej/b m-do-l m **1**/ | Zestaw telefonów usługi Speech Service jest obciążeniowy po wygłosach nałożonej sylaby |
| Allgemeinwissen/a **2** l-g AX-m AY 1 n-v IH-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ EH n t-z. **2** AX r-g zapomniano ng s-f IH AX r-m a/ | Computertomographie/kLy m-p y UW 1-t AX r-t min-m-g r a-f m **2**/ | Zestaw telefonów usługi Speech Service nałożył nacisk po samogłosowym sylabie. |

### <a name="german-vowels"></a>Niemieckie samogłoski

| `sapi` | `ipa`     | Przykład 1                             | Przykład 2     | Przykład 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| z     | `aː`      | **A**                              | Maßst**a**b   | Schem**a**                         |
| a      | `a`       | **Bfall**                            | B**a**ch      | Agath**a**                         |
| Niestety     | `ɔ`       | **O**Sten                             | PF**o**Sten   |                                    |
| EH    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1</sup>](#de-v-1) FASCI**AE** |
| EH     | `ɛ`       | **ä**ndern                            | ProZ**e**NT   | Amygdal**AE**                      |
| oś     | `ə`       | [<sup>2</sup>](#de-v-2)"v**e**rstauen | Aach**e**n    | Frag**e**                          |
| m     | `iː`      | **Uruchomiono**                              | ABB**IE**gt   | Relativitätstheor**IE**            |
| ih     | `ɪ`       | **Nnung**                            | s**i**Ngen    | Drewno**y**                          |
| Europejska     | `øː`      | **Ö**dawcy                              | ABL**ö**Sten  | MALM**ö**                          |
| Okaż     | `o`, `oː` | **o**HNE                              | Balk**o**n    | Trept**ow**                        |
| Outlook     | `œ`       | **Ö**ffnung                           | BEF**ö**rdern |                                    |
| ey     | `e`, `eː` | Berhard **E**                          | ABF**e**gt    | b                                  |
| UW     | `uː`      | **U**Zrób                               | H**u**t       | AKK**u**                           |
| zapomniano     | `ʊ`       | **U**nterschiedes                     | b**u**NT      |                                    |
| oznaczony     | `yː`      | **Ü**bermut                           | PFL**ü**gt    | **Ü** mężczyzn                           |
| uy     | `ʏ`       | **ü**ppig                             | S**y**trzon    |                                    |

<a id="de-v-1"></a>
**1** *tylko w przypadku wyrazów obcych pochodzenia, takich jak: FASCI**AE**.*<br>
<a id="de-v-2"></a>
**2** *Word-intially tylko w **przypadku**wyrazów obcych pochodzenia, takich jak ppointment. Sylaba — początkowo w: "v**e**rstauen.*

### <a name="german-diphthong"></a>Diphthong niemiecki

| `sapi` | `ipa`       | Przykład 1    | Przykład 2          | Przykład 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **EI**nsam   | Unabhängigk**EI**t | ABT**EI** |
| AW     | `au`        | ßen **au**    | ABB**au**St        | Krótkoterminowe**aktualizacje**  |
| Oy-     | `ɔy`, `ɔʏ̯` | Phorie **UE** | TR**äu**Mt         | SCH**UE** |

### <a name="german-semivowels"></a>Semivowels niemiecki

| `sapi` | `ipa` | Przykład 1 | Przykład 2    | Przykład 3  |
|--------|-------|-----------|--------------|------------|
| AX r   | `ɐ`   |           | abänd**er**n | Zablokuj**er** |

### <a name="german-consonants"></a>Zgodne z niemieckimi

| `sapi` | `ipa` | Przykład 1 | Przykład 2 | Przykład 3 |
|--|--|--|--|--|
| b | `b` | **B**Ank |  | [<sup>1</sup>](#de-c-1) PU**b** |  |
| c | `ç` | **Ch**emie | mögli**ch**St | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3</sup>](#de-c-3) Len**d**l | [<sup>4</sup>](#de-c-4) Clau**d**e |  |
| jh | `ʤ` | EFF **J** | gemana**g**t | [<sup>5</sup>](#de-c-5) Kanał**g**e |
| f | `f` | Ahrtdauer **F** | Angri**FF**slustig | abbruchrei**f** |  |
| g | `g` | **g**UT |  | [<sup>6</sup>](#de-c-6) Protokół gre**g** |  |
| h | `h` | Ausanbau **H** |  |  |  |
| Y | `j` | **J**od | Reakt**i**na | HU**i** |  |
| k | `k` | **K**OMA | Aspe**k**t | Flec**k** |  |
| l | `l` | **l**. | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**UT | **M**t | Leh**m** |  |
| n | `n` | **n**Wycofaj | u**n**d | Tak**n** |  |
| gazu | `ŋ` | [<sup>7</sup>](#de-c-7)**ng**Uyen | Schwa**te** | R **.** |  |
| p | `p` | **P**Artner | abru**p**t | TI**p** |  |
| PF | `pf` | Dysk ERD **PF** | Nr**PF**t | Do**PF** |  |
| r | `ʀ`, `r`, `ʁ` | Eise **R** | KNU**RR**t | Haa**r** |  |
| s | `s` | taccato [<sup>8</sup>](#de-c-8)**S** | Analiza**biznesowej**t | mie**s** |  |
| pok | `ʃ` | **SCH**ułę | mi**SCH**t | Lappi**SCH** |  |
| t | `t` | Raum **T** | S**t**raße | Mu za**t** |  |
| TS | `ts` | **Z**g | AR**z**t | WIT**z** |  |
| ch | `tʃ` | **Tsch**echien | aufgepu**Tsch**t | bundesdeu**Tsch** |  |
| v | `v` | **w**inken | P**u**Alle | [<sup>9</sup>](#de-c-9) Gr**oo**ve |  |
| x | [<sup>10</sup>](#de-c-10) `x` ,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) BA**ch**erach | Dr**ch**t mögli**ch**St | Schma**ch** "i**ch** |
| z | `z` | uper **s** |  |  |  |
| zh | `ʒ` | **G**enre | B**ezinski** | EDVI**g**e |

<a id="de-c-1"></a>
**1** *tylko w przypadku wyrazów obcych pochodzenia, takich jak: PU**b**.*<br>
<a id="de-c-2"></a>
**2** *miękkie "ch" po "e" i "i"*<br>
<a id="de-c-3"></a>
**3** *tylko słowa pochodzenie obce, takie jak: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *tylko w przypadku wyrazów obcych pochodzenia, takich jak: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *tylko w przypadku wyrazów obcych pochodzenia, takich jak: kanał**g**e.*<br>
<a id="de-c-6"></a>
**6** *wyraz — Terminal, tylko w przypadku wyrazów obcych pochodzenia, takich jak gre**g**.*<br>
<a id="de-c-7"></a>
**7** *tylko w przypadku wyrazów obcych pochodzenia, takich jak: **ng**Uyen.*<br>
<a id="de-c-8"></a>
**8** *tylko w przypadku wyrazów obcych pochodzenia, takich jak: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *tylko w przypadku wyrazów obcych pochodzenia, takich jak: GR**oo**ve.*<br>
<a id="de-c-10"></a>
**10** *IPA `x` to twarda "ch" po wszystkich samodzielnym wygłosom (a, AA, "zapomniano, UW i diphthong aw").*<br>
<a id="de-c-11"></a>
**11** *IPA `ç` to miękkie "ch" po samodzielnym użyciu (IH, m, eh, AE, uy, UE, OE, UE również w diphthongs AY, Oy-) i spółgłoskach*<br>
<a id="de-c-12"></a>
**12** *wyraz — początkowo tylko w słowach pochodzenie obce, na przykład: " **J**UAN". Sylaba — początkowo również w wyrazach, takich jak: BA**ch**erach.*<br>

### <a name="german-oral-consonants"></a>Niemieckie spółgłoski ustne

| `sapi` | `ipa` | Przykład 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b AX — ^ a 1 x t-l IH c/ |

> [!NOTE]
> Musimy dodać [ \] numer telefonu GS między dwoma odrębnymi odgłosami, z wyjątkiem dwóch samogłosek jest to oryginalny diphthong. Ta doustna Spółgłoska to glottal, aby uzyskać więcej informacji, zobacz <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank"> glottal Stop <span class="docon docon-navigate-external x-hidden-focus"> </a> </a> .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Hiszpańskie samogłosek

| `sapi` | `ipa` | Przykład 1    | Przykład 2     | Przykład 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **LTO**     | ntar**a**    | urzędy certyfikacji**a**     |
| mogę      | `i`   | **bérica**  | AV**i**Spa    | podatek**i**     |
| e      | `e`   | LeFante **e** | na**e**nAby    | Elefant**e** |
| o      | `o`   | **o**caso    | ENC**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**NTA     | Juanl**u**   |

### <a name="spanish-consonants"></a>Zgodne z hiszpańskimi

| `sapi` | `ipa`      | Przykład 1  | Przykład 2      | Przykład 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | Bao**b**AB     | Baoba**b**     |
| ch     | `tʃ`       | **ch**eque | co**ch**e      | Marraque**ch** |
| d      | `d`        | **d**Edo   |                | portlan**d**   |
|        | `ð`        |            | **d**o       | Verda**d**     |
| f      | `f`        | ácil **f**  | ele**f**ante   | PU**f**        |
| g      | `g`        | **g**tongijska  |                | dópin**g**     |
|        | `ɣ`        |            | a**g**UA       | tuare**g**     |
| j      | `j`        | **ODO**   | cal**i**przedsiębio   | **odosij y**        |
| JJ     | `j.j` `jj` |            | VI**wszystko**      |                |
| k      | `k`        | Oche języka **c**  | bo**c**a       | titáni**c**    |
| l      | `l`        | ápiz **l**  | a**l**a        | przewód**1**     |
| wszystki     | `ʎ`        | **wszystkie**Ave  | desarro**wszystko**o |                |
| m      | `m`        | porządek **m** | **m**AR       | álbu**m**      |
| n      | `n`        | **n**Ada   | ce**n**a       | rató**n**      |
| nj     | `ɲ`        | **aña**   | Ara-**ń**    |                |
| p      | `p`        | **p**Oca   | do**p**o       | konie**p**       |
| r      | `ɾ`        |            | Urząd certyfikacji**r**a       | Abri**r**      |
| zasobu     | `r`        | Adio **r**  | co**RR**e      | PU**RR**       |
| s      | `s`        | Accountable Care Organization **s**   | VA**s**o       | pelo**s**      |
| t      | `t`        | Oldo **t**  | **t**AR       | Disque**t**    |
| th     | `θ`        | **z**ebra  | a**z**ul       | lápi**z**      |
| w      | `w`        | h**u**ESO  | AG**u**a       | Gua**u**       |
| x      | `x`        | OTA **j**   | **j**o        | relokacji**j**      |

> [!TIP]
> `es-ES`Zestaw telefonów usługi Speech Service nie obsługuje następujących IPA w języku hiszpańskim,, `β` `ð` i `ɣ` . Jeśli są potrzebne, należy rozważyć użycie IPA bezpośrednio.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

Zestaw telefonów usługi Speech Service `zh-CN` jest oparty na natywnym zestawie <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">pinyin <span class="docon docon-navigate-external x-hidden-focus"></span> </a> telefonu.

### <a name="tone"></a>Ton komunikatu

| Sygnał pinyin | `sapi` | Przykład znaku |
|-------------|--------|-------------------|
| mā          | ma 1  | 妈                 |
| má          | ma 2  | 麻                 |
| mǎ          | ma 3  | 马                 |
| mà          | ma 4  | 骂                 |
| ruchom          | ma 5  | 嘛                 |

#### <a name="example"></a>Przykład

| Znak | Usługa rozpoznawania mowy                |
|-----------|-------------------------------|
| 组织关系      | zu 3-Zhi 1-Guan 1-XI 5 |
| 累进        | Lei 3 — Jin 4                 |
| 西宅巷       | XI 1 — Zhai 2 — Xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

Zestaw telefonów usługi mowy dla `zh-TW` systemu jest oparty na zestawie natywnego <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">telefonu <span class="docon docon-navigate-external x-hidden-focus"></span> Bopomofo</a> .

### <a name="tone"></a>Ton komunikatu

| Ton usługi mowy | Ton Bopomofo | Przykład (słowo) | Telefony usługi mowy | Mowy | Pinyin (拼音) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | puste         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚ ˇ                   | ㄉㄚ ˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥ ˇ ㄗ ̇               | 一ㄥ ˇ ㄗ ̇  | yǐng Zi    |

#### <a name="example"></a>Przykład

| Znak | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡ ˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢ ˇ ㄉㄧㄠ ˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

Zestaw telefonów usługi mowy dla `ja-JP` systemu jest oparty na natywnym zestawie <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">znaków <span class="docon docon-navigate-external x-hidden-focus"></span> kana</a> telefonu.

### <a name="stress"></a>Plastyczności

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` podobciążenie  |

#### <a name="example"></a>Przykład

| Znak | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | Przejdź do ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***