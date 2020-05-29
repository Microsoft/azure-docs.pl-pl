---
title: Jednostki identyfikacji
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f07b71bf8996612798b87d32a21a15ec72db0b32
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140943"
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

Poniższe jednostki są pogrupowane i wyszczególnione według kraju:

Argentyna
* Numer tożsamości narodowej (DNI) Argentyna

Australia
* Numer paszportu Australii
* Numer pliku podatku Australii
* Numer licencji sterownika Australii
* Numer konta medycznego Australii
* Numer konta bankowego Australii

Belgia
* Belgijski numer krajowy

Brazylia 
* Numer podmiotu prawnego w Brazylii (numer CNPJ)
* Numer numer CPF Brazylii
* Krajowa karta IDENTYFIKACYJNa (RG)

Kanada
* Numer ubezpieczenia społecznego w Kanadzie
* Numer licencji na sterownik Kanady
* Numer konta bankowego Kanady
* Numer paszportu Kanady
* Osobisty numer identyfikacyjny kondycji Kanady (PHIN)
* Numer Usługa kondycji Kanady

Chile
* Numer karty tożsamości 

Chiny
* Numer karty tożsamości rezydentnej Chin (ChRL)

Chorwacja
* Numer karty tożsamości Chorwacji
* Numer identyfikacji osobistej Chorwacji (OIB)

Republika Czeska
* Numer prywatnego numeru tożsamości

Dania
* Dania osobisty numer identyfikacyjny

Unia Europejska (UE)
* Krajowy numer identyfikacyjny UE
* Numer paszportu UE
* Numer licencji sterownika UE
* Numer ubezpieczenia społecznego (SSN) UE lub równoważny identyfikator
* Numer identyfikacji podatkowej UE (NIP)
* Numer karty debetowej UE

Finlandia
* Identyfikator Narodowy Finlandii
* Numer paszportu Finlandia

Francja
* Francja National ID Card (CNI)
* Francja — numer ubezpieczenia społecznego (INSEE)
* Numer paszportu Francji
* Numer licencji sterownika Francja

Niemcy
* Numer karty tożsamości (Niemcy)
* Niemiecki numer paszportu
* Numer licencji sterownika niemieckiego

Grecja 
* Numer krajowej karty IDENTYFIKACYJNej Grecji

Hongkong
* Numer karty tożsamości Hongkong (HKID)

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

Japonia
* Numer rejestracji rezydenta Japonia
* Numer karty zamieszkania japońskiego
* Numer licencji sterownika Japonii
* Numer ubezpieczenia społecznego (SIN)
* Numer paszportu Japonia
* Numer konta bankowego Japonii

Malezja
* Malezja — numer karty tożsamości

Holandia
* Numer usługi BSN (Holandia)

Nowa Zelandia
* Numer Ministerstwa kondycji Nowej Zelandii

Norwegia
* Numer identyfikacyjny Norwegii

Filipiny
* Filipiny — ujednolicony numer IDENTYFIKACYJNy dla wielu przeznaczeniów

Polska
* Karta tożsamości Polska
* Krajowy identyfikator Polski (PESEL)
* Paszport Polski

Portugalia 
* Numer karty obywatela Portugalia

Arabia Saudyjska
* Identyfikator Narodowy Arabii Saudyjskiej

Singapur
* Numer krajowej karty rejestracyjnej rejestracji (NRIC)

Republika Południowej Afryki
* Numer identyfikacyjny Republiki Południowej Afryki

Korea Południowa
* Numer rejestracji rezydenta Korea Południowa

Hiszpania 
* Numer ubezpieczenia społecznego (SSN) w Hiszpanii

Szwecja
* Narodowy identyfikator Szwecji
* Szwecja numer paszportu

Tajwan 
* Narodowy identyfikator Tajwanu
* Certyfikat rezydentny tajwański (łuk/TARC)
* Numer paszportu tajwańskiego

Tajlandia
* Kod identyfikacji populacji tajlandzkiej

Zjednoczone Królestwo
* Identyfikator paszportu
* kwart Numer licencji sterownika
* kwart Krajowy numer ubezpieczenia (NINO)
* kwart Numer Usługa kondycji Narodowy
* kwart Numer rzutowania wyborczyego
* USA/ZJEDNOCZONE KRÓLESTWO Numer paszportu

Stany Zjednoczone
* Numer ubezpieczenia społecznego USA (SSN)
* Numer licencji sterownika USA
* USA/ZJEDNOCZONE KRÓLESTWO Numer paszportu
* Numer identyfikacji podatkowej w Stanach Zjednoczonych (ITIN)
* Numer w agencji wymuszania narkotyków (DEA)
* Numer konta bankowego USA
