---
title: Nazwane jednostki do informacji osobistych
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: eedfe4f24797a9b564479b2f8d3f4d04b0751272
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779816"
---
> [!NOTE]
> Aby wykryć chronione informacje o kondycji (Fi), użyj `domain=phi` parametru i wersji modelu `2020-04-01` lub nowszej.
>
> Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi&model-version=2020-07-01`
 
Następujące kategorie jednostek są zwracane podczas wysyłania żądań do `/v3.1-preview.2/entities/recognition/pii` punktu końcowego.

| Kategoria   | Subcategory (Podkategoria) | Opis                          | Uruchamianie wersji modelu | Uwagi |
|------------|-------------|--------------------------------------|------------------------|---|
| Osoba     | Nie dotyczy         | Nazwy osób.  | `2019-10-01`  | Zwracany także przez `domain=phi` . |
| Persontype | Nie dotyczy         | Typy zadań lub role przechowywane przez osobę. | `2020-02-01` | |
| PhoneNumber | Nie dotyczy | Numery telefonów (tylko numery telefonów USA i UE). | `2019-10-01` | Zwracany także przez `domain=phi` . |
|Organizacja  | Nie dotyczy | Firmy, grupy polityczne, zespoły muzyczne, trefle sportowe, instytucje rządowe i organizacje publiczne.  | `2019-10-01` | Narodowe i religijne nie są uwzględnione w tym typie jednostki.  |
|Organizacja | Leczniczych | Firmy medyczne i grupy. | `2020-04-01` |  |
|Organizacja | Wymiana zapasowa | Giełdowe grupy wymiany. | `2020-04-01` |  |
| Organizacja | Sports | Organizacje dotyczące sportu. | `2020-04-01` |  |
| Adres | Nie dotyczy | Pełne adresy wysyłkowe.  | `2020-04-01` | Zwracany także przez `domain=phi` . |
| Współrzędne GPS w Unii Europejskiej | Nie dotyczy | Współrzędne GPS dla lokalizacji w Unii Europejskiej.  | `2019-10-01` |  |
| Poczta e-mail | Nie dotyczy | Adresy e-mail. | `2019-10-01` | Zwracany także przez `domain=phi` .   |
| Adres URL | Nie dotyczy | Adresy URL do witryn sieci Web. | `2019-10-01` | Zwracany także przez `domain=phi` . |
| Adres IP | Nie dotyczy | Adresy IP sieci. | `2019-10-01` | Zwracany także przez `domain=phi` . |
| DateTime | Nie dotyczy | Daty i godziny dnia. | `2019-10-01` |  | 
| Data i godzina | Data | Daty kalendarzowe. | `2019-10-01` | Zwracany także przez `domain=phi` . |
| Liczba | Nie dotyczy | Liczby i ilości liczbowe. | `2019-10-01` |  |
| Ilość | Wiek | Ważności. | `2019-10-01` | | |

## <a name="azure-information"></a>Informacje o platformie Azure

Ta kategoria jednostki obejmuje identyfikowalne informacje dotyczące platformy Azure, w tym informacje o uwierzytelnianiu i parametry połączenia. Dostępne począwszy od wersji modelu `2019-10-01` . Nie zwrócono z `domain=phi` parametrem.

| Subcategory (Podkategoria)                           | Opis                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Klucz uwierzytelniania usługi Azure DocumentDB             | Klucz autoryzacji dla serwera Azure Cosmos DB.                           |
| Parametry połączenia z usługą Azure IAAS Database i parametry połączenia usługi Azure SQL | Parametry połączenia dla bazy danych infrastruktury platformy Azure jako usługi (IaaS) i parametry połączenia SQL. |
| Parametry połączenia usługi Azure SQL           | Parametry połączenia dla bazy danych w Azure SQL Database.                                |
| Parametry połączenia usługi Azure IoT           | Parametry połączenia dla usługi Azure IoT.                        |
| Hasło ustawienia publikowania platformy Azure        | Hasło dla ustawień publikowania platformy Azure.                                        |
| Azure Redis Cache parametry połączenia   | Parametry połączenia dla pamięci podręcznej Redis.                             |
| SAS platformy Azure                             | Parametry połączenia dla oprogramowania na platformie Azure jako usługi (SaaS).                     |
| Azure Service Bus parametry połączenia   | Parametry połączenia dla usługi Azure Service Bus.                                 |
| Klucz konta usługi Azure Storage             | Klucz konta dla konta usługi Azure Storage.                                   |
| Klucz konta usługi Azure Storage (ogólny)   | Ogólny klucz konta dla konta usługi Azure Storage.                           |
| SQL Server parametry połączenia          | Parametry połączenia dla komputera z systemem SQL Server.                                         |

## <a name="identification"></a>Identyfikacja

[!INCLUDE [supported identification entities](./identification-entities.md)]
