---
title: Nazwane jednostki do informacji osobistych
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/30/2020
ms.author: aahi
ms.openlocfilehash: dd7a8b94aefbf389afef30b327ffaa367a30dd51
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108508"
---
> [!NOTE]
> Aby wykryć `PHI` (chronione informacje o kondycji), użyj `domain=phi` parametru i wersji modelu `2020-04-01` lub nowszej.
>
> Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
Następujące kategorie jednostek są zwracane podczas wysyłania żądań do `/v3.1-preview.1/entities/recognition/pii` punktu końcowego.

| Kategoria   | Subcategory | Opis                          | Uruchamianie wersji modelu | Uwagi |
|------------|-------------|--------------------------------------|------------------------|---|
| Person (Osoba)     | Nie dotyczy         | Nazwy osób.  | `2019-10-01`  | Zwracany także przez `domain=phi` . |
| Persontype | Nie dotyczy         | Typy zadań lub role przechowywane przez osobę. | `2020-02-01` | |
| PhoneNumber | Nie dotyczy | Numery telefonów (tylko numery telefonów USA i UE). | `2019-10-01` | Zwrócono również z`domain=phi` |
|Organizacja  | Nie dotyczy | Firmy, grupy polityczne, zespoły muzyczne, trefle sportowe, instytucje rządowe i organizacje publiczne.  | `2019-10-01` | Narodowe i religijne nie są uwzględnione w tym typie jednostki.  |
|Organizacja | Leczniczych | Firmy medyczne i grupy. | `2020-04-01` | Zwracany także przez `domain=phi` . |
|Organizacja | Wymiana zapasowa | Giełdowe grupy wymiany. | `2020-04-01` | Zwracany także przez `domain=phi` . |
| Organizacja | Sports | Organizacje dotyczące sportu. | `2020-04-01` | Zwracany także przez `domain=phi` . |
| Adres | Nie dotyczy | Pełne adresy wysyłkowe.  | `2020-04-01` | Zwracany także przez `domain=phi` . |
| Współrzędne GPS w Unii Europejskiej | Nie dotyczy | Współrzędne GPS dla lokalizacji w Unii Europejskiej.  | `2019-10-01` |  |
| E-mail | Nie dotyczy | Adresy e-mail. | `2019-10-01` | Zwracany także przez `domain=phi` .   |
| Adres URL | Nie dotyczy | Adresy URL do witryn sieci Web. | `2019-10-01` | Zwracany także przez `domain=phi` . |
| Adres IP | Nie dotyczy | Adresy IP sieci. | `2019-10-01` | |
| DateTime | Nie dotyczy | Daty i godziny dnia. | `2019-10-01` |  | 
| DateTime | Data | Daty kalendarzowe. | `2019-10-01` | Zwracany także przez `domain=phi` . |
| Liczba | Nie dotyczy | Liczby i ilości liczbowe. | `2019-10-01` |  |
| Liczba | Wiek | Ważności. | `2019-10-01` | | |
| Międzynarodowa Klasyfikacja Chorób (ICD-9-CM) | Nie dotyczy | Jednostki odnoszące się do międzynarodowej klasyfikacji chorób, dziewiątej poprawki.   | `2020-04-01` | |
| Międzynarodowa Klasyfikacja Chorób (ICD-10-CM) | Nie dotyczy | Jednostki odnoszące się do międzynarodowej klasyfikacji chorób, dziesiątej poprawki.    | `2020-04-01` | |

## <a name="azure-information"></a>Informacje o platformie Azure

Ta kategoria jednostki obejmuje identyfikowalne informacje dotyczące platformy Azure, w tym informacje o uwierzytelnianiu i parametry połączenia. Dostępne począwszy od wersji modelu `2019-10-01` . Nie zwrócono z `domain=phi` parametrem.

| Subcategory                           | Opis                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Klucz uwierzytelniania usługi Azure DocumentDB             | Klucz autoryzacji dla serwera usługi Azure DocumentDB.                           |
| Parametry połączenia z usługą Azure IAAS Database i parametry połączenia usługi Azure SQL | Parametry połączenia dla bazy danych infrastruktury platformy Azure jako usługi (IaaS) i parametry połączenia SQL. |
| Parametry połączenia usługi Azure SQL           | Parametry połączenia dla bazy danych w Azure SQL Database.                                |
| Parametry połączenia usługi Azure IoT           | Parametry połączenia dla Internetu rzeczy (IoT) platformy Azure.                        |
| Hasło ustawienia publikowania platformy Azure        | Hasło dla ustawień publikowania platformy Azure.                                        |
| Azure Redis Cache parametry połączenia   | Parametry połączenia dla pamięci podręcznej platformy Azure dla Redis.                             |
| SAS platformy Azure                             | Parametry połączenia dla oprogramowania Azure jako usługi (SAS).                     |
| Azure Service Bus parametry połączenia   | Parametry połączenia dla usługi Azure Service Bus.                                 |
| Klucz konta usługi Azure Storage             | Klucz konta dla konta usługi Azure Storage.                                   |
| Klucz konta usługi Azure Storage (ogólny)   | Ogólny klucz konta dla konta usługi Azure Storage.                           |
| SQL Server parametry połączenia          | Parametry połączenia dla programu SQL Server.                                         |

## <a name="identification"></a>Identyfikacja

[!INCLUDE [supported identification entities](./identification-entities.md)]
