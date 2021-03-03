---
title: Nazwane jednostki do informacji osobistych
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: 97167485dae155670f0eb83fc3ef9cb658952251
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750169"
---
> [!NOTE]
> Aby wykryć chronione informacje o kondycji (Fi), użyj `domain=phi` parametru i wersji modelu `2020-04-01` lub nowszej.
>
> Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Następujące kategorie jednostek są zwracane podczas wysyłania żądań do `/v3.1-preview.3/entities/recognition/pii` punktu końcowego.


| Kategoria   |  Opis                          |
|------------|-------------|
| [Person (Osoba)](#category-person)      |  Nazwy osób.  |
| [Persontype](#category-persontype) | Typy zadań lub role przechowywane przez osobę. |
| [Numer telefonu](#category-phonenumber) |Numery telefonów (tylko numery telefonów USA i UE). |
| [Organizacja](#category-organization) |  Firmy, grupy, instytucje rządowe i inne organizacje.  |
| [Adres](#category-address) | Pełne adresy wysyłkowe.  |
| [Poczta e-mail](#category-email) | Adresy e-mail.   |
| [Adres URL](#category-url) | Adresy URL do witryn sieci Web.  |
| [Adres IP](#category-ip) | Adresy IP sieci.  |
| [Data/godzina](#category-datetime) | Daty i godziny dnia. | 
| [Liczba](#category-quantity) | Liczby i ilości liczbowe.  |
| [Informacje o platformie Azure](#azure-information) | Identyfikowalne informacje dotyczące platformy Azure, takie jak informacje o uwierzytelnianiu.  |
| [Identyfikacja](#identification) | Identyfikacja właściwa dla finansów i kraju.  |

### <a name="category-person"></a>Kategoria: osoba

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Person (Osoba)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Nazwy osób. Zwracany także przez `domain=phi` .
      
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

        Typy zadań lub role przechowywane przez osobę.
      
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

        Numery telefonów (tylko numery telefonów USA i UE). Zwracany także przez `domain=phi` .
      
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
:::row-end:::
:::row:::
    :::column span="":::

        Wymiana zapasowa

    :::column-end:::
    :::column span="2":::

        Giełdowe grupy wymiany. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sports

    :::column-end:::
    :::column span="2":::

        Organizacje dotyczące sportu.
      
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
:::row-end:::

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
:::row-end:::

#### <a name="subcategories"></a>Podkategorii

Jednostka w tej kategorii może mieć następujące podkategorie.

:::row:::
    :::column span="":::
        **Podkategoria jednostki**

        Wiek

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Ważności.
      
    :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Informacje o platformie Azure

Te kategorie jednostek obejmują identyfikowalne informacje dotyczące platformy Azure, w tym informacje o uwierzytelnianiu i parametry połączenia. Nie zwrócono z `domain=phi` parametrem.

:::row:::
    :::column span="":::
        **Jednostka**

        Klucz uwierzytelniania usługi Azure DocumentDB 

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Klucz autoryzacji dla serwera Azure Cosmos DB.   
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Parametry połączenia z usługą Azure IAAS Database i parametry połączenia usługi Azure SQL

    :::column-end:::
    :::column span="2":::

        Parametry połączenia dla bazy danych infrastruktury platformy Azure jako usługi (IaaS) i parametry połączenia SQL.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Parametry połączenia usługi Azure SQL

    :::column-end:::
    :::column span="2":::

        Parametry połączenia dla bazy danych w Azure SQL Database.
      
    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Parametry połączenia usługi Azure IoT  

    :::column-end:::
    :::column span="2":::

        Parametry połączenia dla usługi Azure IoT. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Hasło ustawienia publikowania platformy Azure  

    :::column-end:::
    :::column span="2":::

        Hasło dla ustawień publikowania platformy Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis Cache parametry połączenia 

    :::column-end:::
    :::column span="2":::

        Parametry połączenia dla pamięci podręcznej Redis.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SAS platformy Azure 

    :::column-end:::
    :::column span="2":::

        Parametry połączenia dla oprogramowania na platformie Azure jako usługi (SaaS).
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Service Bus parametry połączenia

    :::column-end:::
    :::column span="2":::

        Parametry połączenia dla usługi Azure Service Bus.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Klucz konta usługi Azure Storage 

    :::column-end:::
    :::column span="2":::

       Klucz konta dla konta usługi Azure Storage. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Klucz konta usługi Azure Storage (ogólny)

    :::column-end:::
    :::column span="2":::

       Ogólny klucz konta dla konta usługi Azure Storage.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server parametry połączenia 

    :::column-end:::
    :::column span="2":::

       Parametry połączenia dla komputera z systemem SQL Server.
      
    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identyfikacja

[!INCLUDE [supported identification entities](./identification-entities.md)]
