---
title: Jednostki identyfikacji
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 49a2b285d9494dfc5ca0863c021733b4bc874efa
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779335"
---
Ta kategoria jednostki obejmuje informacje finansowe i oficjalne formy identyfikacji. Dostępne począwszy od wersji modelu `2019-10-01` . Poniżej znajdują się podtypy. 

### <a name="financial-account-identification"></a>Identyfikacja konta finansowego

| Nazwa podtypu               | Opis                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Numer rozliczeniowy ABA        | Numery routingu tranzytowego stowarzyszenia bankowego (ABA).                  |
| Kod SWIFT                 | Kody SWIFT dla informacji o instrukcji płatności.                           |
| Karta kredytowa                | Numery kart kredytowych.                                                       |
| Międzynarodowy numer konta bankowego (IBAN)                  | Kody IBAN dla informacji o instrukcji płatności.                            |


### <a name="government-and-countryregion-specific-identification"></a>Identyfikacja specyficzna dla instytucji rządowych i krajów/regionów

> [!NOTE]
> Następujące jednostki finansowe i specyficzne dla kraju nie są zwracane z `domain=phi` parametrem:
> * Numery paszportów
> * Identyfikatory podatków

Następujące jednostki są pogrupowane i wyszczególnione według kraju:

Argentyna
* Numer tożsamości narodowej (DNI) Argentyna

Austria
* Karta tożsamości Austria
* Austria numer identyfikacyjny podatku
* Austria wartość dodana podatku (VAT)

Australia
* Numer konta bankowego Australii
* Australijski numer firmy
* Australijski numer firmy
* Numer licencji sterownika Australii
* Numer konta medycznego Australii
* Numer paszportu Australii
* Numer pliku podatku Australii

Belgia
* Belgijski numer krajowy
* Numer NIP dodany przez wartość Belgii

Brazylia 
* Numer podmiotu prawnego w Brazylii (numer CNPJ)
* Numer numer CPF Brazylii
* Krajowa karta IDENTYFIKACYJNa (RG)

Bułgaria
* Jednolity numer cywilny Bułgarii

Kanada
* Numer konta bankowego Kanady
* Numer licencji na sterownik Kanady
* Numer Usługa kondycji Kanady
* Numer paszportu Kanady
* Osobisty numer identyfikacyjny kondycji Kanady (PHIN)
* Numer ubezpieczenia społecznego w Kanadzie

Chile
* Numer karty tożsamości 

Chiny
* Numer karty tożsamości rezydentnej Chin (ChRL)

Chorwacja
* Numer karty tożsamości Chorwacji
* Numer krajowej karty IDENTYFIKACYJNej Chorwacji
* Numer identyfikacji osobistej Chorwacji (OIB)

Cypr
* Numer karty Cypru Identity
* Numer identyfikacji podatkowej na Cyprze

Republika Czeska
* Numer prywatnego numeru tożsamości

Dania
* Dania osobisty numer identyfikacyjny

Estonia
* Osobisty kod identyfikacyjny Estonii

Unia Europejska (UE)
* Numer karty debetowej UE
* Numer licencji sterownika UE
* Krajowy numer identyfikacyjny UE
* Numer paszportu UE
* Numer ubezpieczenia społecznego (SSN) UE lub równoważny identyfikator
* Numer identyfikacji podatkowej UE (NIP)

Finlandia
* Europejski numer ubezpieczenia dla zdrowia Finlandii
* Identyfikator Narodowy Finlandii
* Numer paszportu Finlandia

Francja
* Numer licencji sterownika Francja
* Francja — numer ubezpieczenia zdrowotnego
* Francja National ID Card (CNI)
* Numer paszportu Francji
* Francja — numer ubezpieczenia społecznego (INSEE)
* Francja — numer identyfikacji podatkowej (numéro WWH)
* Numer NIP dodany przez wartość Francja

Niemcy
* Numer licencji sterownika niemieckiego
* Numer karty tożsamości (Niemcy)
* Niemiecki numer paszportu
* Numer NIP (Niemcy)
* Numer NIP dodany przez wartość Niemcy

Grecja 
* Numer krajowej karty IDENTYFIKACYJNej Grecji
* Numer identyfikacji podatkowej Grecji

Hongkong
* Numer karty tożsamości Hongkong (HKID)

Węgry
* Numer identyfikacyjny narodowej Węgier
* Numer identyfikacji podatkowej na Węgrzech
* Wartość Węgier — dodany numer podatku

Indie
* Numer stałego konta Indie (PAN)
* Liczba unikatowych identyfikatorów (AADHAAR) Indii

Indonezja
* Numer karty tożsamości Indonezja (KTP)

Irlandia
* Numer Personal Public Service (PPS)

Izrael
* Krajowy identyfikator Izraela
* Numer konta bankowego Izraela

Włochy
* Identyfikator licencji sterownika Włochy
* Kod Obrachunkowy Włoch
* Numer podatkowy dodany do wartości Włoch

Japonia
* Numer konta bankowego Japonii
* Numer licencji sterownika Japonii
* Japoński mój numer my
* Japoński mój numer firmy
* Numer rejestracji rezydenta Japonia
* Numer karty zamieszkania japońskiego
* Numer ubezpieczenia społecznego (Japonia)
* Numer paszportu Japonia

Łotwa
* Łotwa — kod osobisty

Litwa
* Kod osobisty Litwa

Luxemburg
* Luxemburg Narodowy numer identyfikacyjny (osoby fizyczne)
* Luxemburg Narodowy numer identyfikacyjny (osoby niebędące osobami naturalnymi)

Malezja
* Malezja — numer karty tożsamości

Malta
* Numer karty tożsamości Malty
* Numer identyfikacji podatkowej Malty

Holandia
* Numer usługi BSN (Holandia)
* Holandia numer identyfikacji podatkowej
* Numer NIP dodany przez wartość Holandia

Nowa Zelandia
* Numer konta bankowego Nowej Zelandii
* Numer licencji sterownika Nowej Zelandii
* Numer przychodu w Nowej Zelandii
* Numer Ministerstwa kondycji Nowej Zelandii
* Numer opieki społecznej dla Nowej Zelandii

Norwegia
* Numer identyfikacyjny Norwegii

Filipiny
* Filipiny — ujednolicony numer IDENTYFIKACYJNy dla wielu przeznaczeniów

Polska
* Karta tożsamości Polska
* Krajowy identyfikator Polski (PESEL)
* Numer paszportu Polska
* Numer REGON Polska
* Numer identyfikacji podatkowej Polska

Portugalia 
* Numer karty obywatela Portugalia
* Numer identyfikacji podatkowej Portugalii

Rumunia
* Osobisty kod liczbowy Rumunii (CNP)

Rosja
* Rosyjski numer paszportu (kraj)
* Rosyjski numer paszportu (międzynarodowy)

Arabia Saudyjska
* Identyfikator Narodowy Arabii Saudyjskiej

Singapur
* Numer krajowej karty rejestracyjnej rejestracji (NRIC)

Słowacja 
* Numer osobisty w Słowacji

Słowenia
* Numer identyfikacji podatkowej Słowenii
* Słowenia — unikatowy główny numer obywatela

Republika Południowej Afryki
* Numer identyfikacyjny Republiki Południowej Afryki

Korea Południowa
* Numer rejestracji rezydenta Korea Południowa

Hiszpania 
* Hiszpania — DNI
* Numer ubezpieczenia społecznego (SSN) w Hiszpanii
* Numer identyfikacji podatkowej Hiszpania

Szwecja
* Narodowy identyfikator Szwecji
* Szwecja numer paszportu
* Szwecja numer identyfikacji podatkowej

Szwajcaria
* Szwajcarski numer ubezpieczenia społecznego AHV

Tajwan 
* Narodowy identyfikator Tajwanu
* Certyfikat rezydentny tajwański (łuk/TARC)
* Numer paszportu tajwańskiego

Tajlandia
* Kod identyfikacji populacji tajlandzkiej

Turcja
* Turecki Narodowy numer identyfikacyjny

Ukraina
* Numer paszportu Ukrainy (krajowe)
* Numer paszportu Ukrainy (międzynarodowy)

Zjednoczone Królestwo
* kwart Numer licencji sterownika
* kwart Numer rzutowania wyborczyego
* kwart Numer Narodowy Usługa kondycji (NHS)
* kwart Krajowy numer ubezpieczenia (NINO)
* kwart Numer paszportu
* kwart Unikatowy numer referencyjny podatnika

Stany Zjednoczone
* Numer ubezpieczenia społecznego USA (SSN)
* Numer licencji sterownika USA
* Numer paszportu USA
* Numer identyfikacji podatkowej w Stanach Zjednoczonych (ITIN)
* Liczba Stanów Zjednoczonych (DEA)
* Numer konta bankowego USA
