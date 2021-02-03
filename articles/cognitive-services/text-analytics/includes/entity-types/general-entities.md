---
title: Ogólne nazwane jednostki
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500035"
---
Funkcja NER dla analiza tekstu zwraca następującą ogólną (nieidentyfikującą) kategorię jednostek. na przykład podczas wysyłania żądań do `/entities/recognition/general` punktu końcowego.


| Kategoria | Opis                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person (Osoba)](#category-person)     | Nazwy osób.  |
| [Persontype](#category-persontype) | Typy zadań lub role przechowywane przez osobę. |
| [Lokalizacja](#category-location)    | Punkty orientacyjne, struktury, funkcje geograficzne i geopolityczne, w naturalny i ludzki sposób |
| [Organizacja](#category-organization)  | Firmy, grupy polityczne, zespoły muzyczne, trefle sportowe, instytucje rządowe i organizacje publiczne.  |
| [Zdarzenie](#category-event)  | Zdarzenia historyczne, społeczne i wystąpienia naturalne. |
| [Product](#category-product) | Obiekty fizyczne różnych kategorii. |
| [Czy](#category-skill) | Możliwość, umiejętność lub doświadczenie.  |
| [Adres](#category-address) | Pełne adresy wysyłkowe.  |
| [Numer telefonu](#category-phonenumber) | Numery telefonów. |
| [Poczta e-mail](#category-email) | Adresy e-mail. |
| [Adres URL](#category-url) | Adresy URL do witryn sieci Web. |
| [Adres IP](#category-ip) | Adresy IP sieci. |
| [Data/godzina](#category-datetime) | Daty i godziny dnia. |
| [Liczba](#category-quantity) | Liczbowe pomiary i jednostki. |


### <a name="category-person"></a>Kategoria: osoba

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Person (Osoba)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Nazwy osób.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategoria: Persontype

Ta kategoria zawiera następującą jednostkę:


:::row:::
    :::column span="":::
        **Jednostka**

        Persontype

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Typy zadań lub role przechowywane przez osobę
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Kategoria: Lokalizacja

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Lokalizacja

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Tereny, struktury, funkcje geograficzne i jednostki geopolityczne, w naturalny i ludzki sposób.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorii

Jednostka w tej kategorii może mieć następujące podkategorie.

:::row:::
    :::column span="":::
        **Podkategoria jednostki**

        Jednostka geopolityczna (GPE)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Miasta, kraje/regiony, Stany.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Strukturalnych

    :::column-end:::
    :::column span="2":::

        Struktury zbudowanych. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Geograficznych

    :::column-end:::
    :::column span="2":::

        Funkcje geograficzne i naturalne, takie jak rzek, oceany i Deserts.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>Kategoria: organizacja

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Organizacja

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Firmy, grupy polityczne, zespoły muzyczne, trefle sportowe, instytucje rządowe i organizacje publiczne. Narodowe i religijne nie są uwzględnione w tym typie jednostki.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorii

Jednostka w tej kategorii może mieć następujące podkategorie.

:::row:::
    :::column span="":::
        **Podkategoria jednostki**

        Leczniczych

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Firmy medyczne i grupy.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Wymiana zapasowa

    :::column-end:::
    :::column span="2":::

        Giełdowe grupy wymiany. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sports

    :::column-end:::
    :::column span="2":::

        Organizacje dotyczące sportu.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Kategoria: zdarzenie

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Zdarzenie

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Zdarzenia historyczne, społeczne i wystąpienia naturalne.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es` , `fr` ,,, `de` `it` `zh-hans` , `ja` , `ko` `pt-pt` i `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorii

Jednostka w tej kategorii może mieć następujące podkategorie.

:::row:::
    :::column span="":::
        **Podkategoria jednostki**

        Twórcz

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Wydarzenia i święta kulturowe.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tocz

    :::column-end:::
    :::column span="2":::

        Zdarzenia występujące w naturalny sposób.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sports

    :::column-end:::
    :::column span="2":::

        Wydarzenia sportowe.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Kategoria: produkt

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Produkt

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Obiekty fizyczne różnych kategorii.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Podkategorii

Jednostka w tej kategorii może mieć następujące podkategorie.

:::row:::
    :::column span="":::
        **Podkategoria jednostki**

        Produkty obliczeniowe
    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Produkty obliczeniowe.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Kategoria: umiejętność

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Czy

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Możliwość, umiejętność lub doświadczenie.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>Kategoria: adres

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Adres

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Pełny adres wysyłkowy.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategoria: numer telefonu

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Numery telefonów (tylko numery telefonów USA i UE).
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>Kategoria: E-mail

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Adresy e-mail.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>Kategoria: adres URL

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Adres URL

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Adresy URL do witryn sieci Web. 
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Kategoria: adres IP

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Adres IP

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        adresy IP sieci. 
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategoria: Data i godzina

Ta kategoria zawiera następujące jednostki:

:::row:::
    :::column span="":::
        **Jednostka**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Daty i godziny dnia. 
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Jednostki w tej kategorii mogą mieć następujące podkategorie

#### <a name="subcategories"></a>Podkategorii

Jednostka w tej kategorii może mieć następujące podkategorie.

:::row:::
    :::column span="":::
        **Podkategoria jednostki**

        Date (Data)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Daty kalendarzowe.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Godzina

    :::column-end:::
    :::column span="2":::

        Razy dziennie.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Zakres dat

    :::column-end:::
    :::column span="2":::

        Zakresy dat.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Zakres czasu

    :::column-end:::
    :::column span="2":::

        Zakresy czasu.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Czas trwania

    :::column-end:::
    :::column span="2":::

        Czasów trwania.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Set

    :::column-end:::
    :::column span="2":::

        Ustawianie, powtarzające się czasy.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategoria: ilość

Ta kategoria zawiera następujące jednostki:

:::row:::
    :::column span="":::
        **Jednostka**

        Liczba

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Liczby i ilości liczbowe.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorii

Jednostka w tej kategorii może mieć następujące podkategorie.

:::row:::
    :::column span="":::
        **Podkategoria jednostki**

        Liczba

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Numery.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Procent

    :::column-end:::
    :::column span="2":::

        Wartości procentowe
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Liczby porządkowe

    :::column-end:::
    :::column span="2":::

        Liczby porządkowe.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Wiek

    :::column-end:::
    :::column span="2":::

        Ważności.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Waluta

    :::column-end:::
    :::column span="2":::

        Waluty
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Wymiary

    :::column-end:::
    :::column span="2":::

        Wymiary i pomiary.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Temperatura

    :::column-end:::
    :::column span="2":::

        Nosząc.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
