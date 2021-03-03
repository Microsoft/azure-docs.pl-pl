---
title: Jednostki identyfikacji
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750030"
---
### <a name="financial-account-identification"></a>Identyfikacja konta finansowego

Ta kategoria jednostki obejmuje informacje finansowe i oficjalne formy identyfikacji.

#### <a name="category-aba-routing-number"></a>Kategoria: numer routingu ABA

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        ABA numer routingu

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Numery routingu tranzytowego stowarzyszenia bankowego (ABA).
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>Kategoria: kod SWIFT

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Kod SWIFT

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Kody SWIFT dla informacji o instrukcji płatności.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Kategoria: karta kredytowa

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Karta kredytowa

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Numery kart kredytowych. 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Kategoria: Międzynarodowy numer konta bankowego (IBAN) 

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Karta kredytowa

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Kody IBAN dla informacji o instrukcji płatności.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identyfikacja specyficzna dla instytucji rządowych i krajów/regionów

> [!NOTE]
> Następujące jednostki finansowe i specyficzne dla kraju nie są zwracane z `domain=phi` parametrem:
> * Numery paszportów
> * Identyfikatory podatków

Następujące jednostki są pogrupowane i wyszczególnione według kraju:

#### <a name="argentina"></a>Argentyna

:::row:::
    :::column span="":::
        **Jednostka**

        Numer tożsamości narodowej (DNI) Argentyna

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>Austria

:::row:::
    :::column span="":::
        **Jednostka**

        Karta tożsamości Austria

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Austria numer identyfikacyjny podatku

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Austria wartość dodana podatku (VAT)

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australia

:::row:::
    :::column span="":::
        **Jednostka**

        Numer konta bankowego Australii

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Australijski numer firmy

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numer firmy w Australii

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Licencja na sterownik Australii  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer konta medycznego Australii

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Australii

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Australii

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numer pliku podatku Australii

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>Belgia

:::row:::
    :::column span="":::
        **Jednostka**

        Belgijski numer krajowy

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Belgia Wartość dodana podatku (VAT)

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>Brazylia 

:::row:::
    :::column span="":::
        **Jednostka**

        Numer podmiotu prawnego w Brazylii (numer CNPJ)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer numer CPF Brazylii

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Krajowa karta IDENTYFIKACYJNa (RG)

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Kanada

:::row:::
    :::column span="":::
        **Jednostka**

        Numer konta bankowego Kanady

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numer licencji na sterownik Kanady

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer usługi kondycji Kanady

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Kanady

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Osobisty numer identyfikacyjny kondycji Kanady (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numer ubezpieczenia społecznego w Kanadzie

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości Chile

    :::column-end:::
:::row-end:::

#### <a name="china"></a>Chiny

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości rezydentnej Chin (ChRL)

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Unia Europejska (UE)

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty debetowej UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer licencji sterownika UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Krajowy numer identyfikacyjny UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer ubezpieczenia społecznego (SSN) UE lub równoważny identyfikator

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer identyfikacji podatkowej UE (NIP)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Współrzędne GPS w Unii Europejskiej

    :::column-end:::
:::row-end:::

#### <a name="france"></a>Francja

:::row:::
    :::column span="":::
        **Jednostka**

        Numer licencji sterownika Francja

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francja — numer ubezpieczenia zdrowotnego

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francja National ID Card (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Francji

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francja — numer ubezpieczenia społecznego (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francja — numer identyfikacji podatkowej (numéro WWH)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer NIP (Francja)

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>Niemcy

:::row:::
    :::column span="":::
        **Jednostka**

        Numer licencji sterownika niemieckiego

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer karty tożsamości (Niemcy)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Niemiec

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer NIP (Niemcy)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer NIP dodany przez wartość Niemcy

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości Hongkong (HKID)

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Węgry

:::row:::
    :::column span="":::
        **Jednostka**

        Na Węgrzech osobisty numer identyfikacyjny

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer identyfikacji podatkowej na Węgrzech

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Wartość Węgier — dodany numer podatku

    :::column-end:::
:::row-end:::

#### <a name="india"></a>Indie

:::row:::
    :::column span="":::
        **Jednostka**

        Numer stałego konta Indie (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Liczba unikatowych identyfikatorów (AADHAAR) Indii

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonezja

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości Indonezja (KTP)

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlandia

:::row:::
    :::column span="":::
        **Jednostka**

        Numer Personal Public Service (PPS)

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>Izrael

:::row:::
    :::column span="":::
        **Jednostka**

        Krajowy identyfikator Izraela

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer konta bankowego Izraela

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>Włochy

:::row:::
    :::column span="":::
        **Jednostka**

        Identyfikator licencji sterownika Włochy

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kod Obrachunkowy Włoch

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer podatkowy dodany do wartości Włoch

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japonia

:::row:::
    :::column span="":::
        **Jednostka**

        Numer konta bankowego Japonii

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numer licencji sterownika Japonii

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonia "My Number" (osobista)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonia "My Number" (firmowa)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer rejestracji rezydenta Japonia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer karty pobytu Japonia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer ubezpieczenia społecznego (Japonia)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Japonia

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luksemburg

:::row:::
    :::column span="":::
        **Jednostka**

        Narodowy numer identyfikacyjny (osoby naturalne)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Krajowy numer identyfikacyjny w Luksemburgu (osoby niebędące osobami naturalnymi)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości Malty

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer identyfikacji podatkowej Malty

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nowa Zelandia

:::row:::
    :::column span="":::
        **Jednostka**

        Numer konta bankowego Nowej Zelandii

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer licencji sterownika Nowej Zelandii

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer przychodu w Nowej Zelandii

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer Ministerstwa kondycji Nowej Zelandii

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer opieki społecznej dla Nowej Zelandii

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipiny

:::row:::
    :::column span="":::
        **Jednostka**

        Filipiny — ujednolicony numer IDENTYFIKACYJNy dla wielu przeznaczeniów

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugalia 

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty obywatela Portugalia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer identyfikacji podatkowej Portugalii

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapur

:::row:::
    :::column span="":::
        **Jednostka**

        Numer krajowej karty rejestracyjnej rejestracji (NRIC)

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Republika Południowej Afryki

:::row:::
    :::column span="":::
        **Jednostka**

        Numer identyfikacyjny Republiki Południowej Afryki

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Korea Południowa

:::row:::
    :::column span="":::
        **Jednostka**

        Numer rejestracji rezydenta Korea Południowa

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>Hiszpania

:::row:::
    :::column span="":::
        **Jednostka**

        Hiszpania — DNI

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer ubezpieczenia społecznego (SSN) w Hiszpanii

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer identyfikacji podatkowej Hiszpania

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Szwajcaria

:::row:::
    :::column span="":::
        **Jednostka**

        Szwajcarski numer ubezpieczenia społecznego AHV

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Tajwan 

:::row:::
    :::column span="":::
        **Jednostka**

        Narodowy identyfikator Tajwanu

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certyfikat rezydentny tajwański (łuk/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu tajwańskiego

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Zjednoczone Królestwo

:::row:::
    :::column span="":::
        **Jednostka**

        kwart Numer licencji sterownika

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       kwart Numer rzutowania wyborczyego

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       kwart Numer Narodowy Usługa kondycji (NHS)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       kwart Krajowy numer ubezpieczenia (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       kwart lub numer paszportu USA

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       kwart Unikatowy numer referencyjny podatnika

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>Stany Zjednoczone

:::row:::
    :::column span="":::
        **Jednostka**

        Numer ubezpieczenia społecznego USA (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer licencji sterownika USA

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Stany Zjednoczone i Zjednoczone Królestwo Numer paszportu

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer identyfikacji podatkowej w Stanach Zjednoczonych (ITIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Liczba Stanów Zjednoczonych (DEA)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer konta bankowego USA

    :::column-end:::
:::row-end:::
