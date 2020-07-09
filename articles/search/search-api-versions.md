---
title: Wersje interfejsu API
titleSuffix: Azure Cognitive Search
description: Zasady wersji dla interfejsów API REST platformy Azure Wyszukiwanie poznawcze i biblioteki klienta w zestawie SDK platformy .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830098"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Wersje interfejsu API na platformie Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze regularnie przedstawia aktualizacje funkcji. Czasami, ale nie zawsze, te aktualizacje wymagają nowej wersji interfejsu API w celu zachowania zgodności z poprzednimi wersjami. Opublikowanie nowej wersji pozwala określić, kiedy i w jaki sposób integrują aktualizacje usługi Search w kodzie.

Zgodnie z regułą zespół usługi Azure Wyszukiwanie poznawcze publikuje nowe wersje tylko wtedy, gdy jest to konieczne, ponieważ może to wymagać pewnego wysiłku, aby uaktualnić kod w celu użycia nowej wersji interfejsu API. Nowa wersja jest wymagana tylko wtedy, gdy jakiś aspekt interfejsu API zmienił się w taki sposób, który powoduje przerwanie zgodności z poprzednimi wersjami. Takie zmiany mogą wystąpić z powodu poprawek do istniejących funkcji lub z powodu nowych funkcji, które zmieniają istniejący obszar powierzchni interfejsu API.

Ta sama reguła dotyczy aktualizacji zestawu SDK. Zestaw SDK platformy Azure Wyszukiwanie poznawcze jest zgodny z regułami [wersji semantycznej](https://semver.org/) , co oznacza, że jej wersja ma trzy części: główna, pomocnicza i numer kompilacji (na przykład 1.1.0). Nowa główna wersja zestawu SDK jest wydawana tylko dla zmian, które Przerwij zgodność z poprzednią wersją. Niekrytyczne aktualizacje funkcji spowodują zwiększenie wersji pomocniczej, a poprawki błędów spowodują zwiększenie wersji kompilacji.

> [!NOTE]
> Wystąpienie usługi Azure Wyszukiwanie poznawcze obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Możesz nadal korzystać z wersji, gdy nie jest już Najnowsza, ale zalecamy przeprowadzenie migracji kodu w celu użycia najnowszej wersji. W przypadku korzystania z interfejsu API REST należy określić wersję interfejsu API w każdym żądaniu za pośrednictwem parametru API-Version. Podczas korzystania z zestawu SDK platformy .NET wersja zestawu SDK określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego zestawu SDK, możesz nadal uruchamiać ten kod bez zmian, nawet jeśli usługa zostanie uaktualniona w celu obsługi nowszej wersji interfejsu API.

## <a name="rest-apis"></a>Interfejsy API REST

Ta tabela zawiera historię wersji bieżących i wcześniej wydanych wersji interfejsu API REST Search Service. Dokumentacja jest publikowana pod kątem bieżących i stabilnych wersji.

| Wersja&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Stan | Problem ze zgodnością wstecz |
|-------------|--------|------------------------------|
| [Zarządzanie 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | Ogólnie dostępna | Najnowsza stabilna wersja interfejsów API REST zarządzania z zaawansowaniem w programie Endpoint Protection. Dodaje prywatny punkt końcowy, obsługę linków prywatnych i reguły sieciowe dla nowych usług. |
| [Zarządzanie 2019-10-01-Preview](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | Wersja zapoznawcza  | Mimo numeru wersji jest to nadal bieżąca wersja zapoznawcza interfejsów API REST zarządzania. Obecnie nie ma żadnych funkcji w wersji zapoznawczej. Wszystkie funkcje w wersji zapoznawczej zostały ostatnio przenoszone do ogólnej dostępności. |
| Zarządzanie 2015-08-19  | Stable| Pierwsza ogólnie dostępna wersja interfejsów API REST zarządzania. Zapewnia obsługę administracyjną, skalowanie w górę i zarządzanie kluczami interfejsu API. |
| Zarządzanie 2015-08-19 — wersja zapoznawcza | Wersja zapoznawcza| Pierwsza wersja zapoznawcza interfejsów API REST zarządzania. |
| [Wyszukiwanie 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Stable | Najnowsza stabilna wersja interfejsów API REST przeszukiwania wyszukiwania, z zaawansowaniem oceniania istotności. |
| [Wyszukiwanie 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview)| Wersja zapoznawcza | Wersja zapoznawcza skojarzona z stabilną wersją. |
| Wyszukiwanie 2019-05-06 | Stable | Dodaje typy złożone. |
| Wyszukiwanie 2019-05-06 — wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza skojarzona z stabilną wersją. |
| Wyszukaj 2017-11-11 | Stable  | Dodaje wzbogacanie umiejętności i AI. |
| Wyszukiwanie 2017-11-11 — wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza skojarzona z stabilną wersją. |
| Wyszukaj 2016-09-01 |Stable | Dodaje indeksatory|
| Wyszukiwanie 2016-09-01 — wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza skojarzona z stabilną wersją.|
| Wyszukaj 2015-02-28 | Stable  | Pierwsza ogólnie dostępna wersja.  |
| Wyszukiwanie 2015-02-28 — wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza skojarzona z stabilną wersją. |
| Wyszukiwanie 2014-10-20 — wersja zapoznawcza | Wersja zapoznawcza | Druga publiczna wersja zapoznawcza. |
| Wyszukiwanie 2014-07-31 — wersja zapoznawcza | Wersja zapoznawcza | Pierwsza publiczna wersja zapoznawcza. |

## <a name="azure-sdk-for-net"></a>Zestaw Azure SDK dla platformy .NET

Historia wersji pakietu jest dostępna w witrynie NuGet.org. Ta tabela zawiera linki do poszczególnych stron pakietu.

| Wersja zestawu SDK | Stan | Opis |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0 — wersja zapoznawcza. 4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Wersja zapoznawcza | Nowa Biblioteka kliencka z zestawu Azure .NET SDK, wydana 2020. Dotyczy wersji interfejsu API REST 2020-06-30|
| [**Microsoft. Azure. Search 10,0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Ogólnie dostępne, wydane mogą 2019. Dotyczy wersji interfejsu API REST 2019-05-06.|
| [**Microsoft. Azure. Search 8,0 — wersja zapoznawcza**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Wersja zapoznawcza, wydana kwiecień 2019. Dotyczy wersji interfejsu API REST 2019-05-06-Preview.|
| [**Microsoft. Azure. Management. Search 3.0.0**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | Jest przeznaczony dla interfejsu API REST zarządzania — Version = 2015-08-19. |

## <a name="azure-sdk-for-java"></a>Zestaw Azure SDK dla języka Java

| Wersja zestawu SDK | Stan | Opis  |
|-------------|--------|------------------------------|
| [**Java SearchManagementClient 1.35.0**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | Jest przeznaczony dla interfejsu API REST zarządzania — Version = 2015-08-19.|

## <a name="azure-sdk-for-python"></a>Zestaw Azure SDK dla środowiska Python

| Wersja zestawu SDK | Stan | Opis  |
|-------------|--------|------------------------------|
| [**Python Azure-zarządzanie — wyszukiwanie 1,0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Stable | Jest przeznaczony dla interfejsu API REST zarządzania — Version = 2015-08-19. |