---
title: Diagnozowanie i rozwiązywanie problemów z dostępnością zestawów SDK usługi Azure Cosmos w środowiskach wieloregionowych
description: Więcej informacji o zachowaniu dostępności zestawu SDK usługi Azure Cosmos w przypadku korzystania z wielu środowisk regionalnych.
author: ealsur
ms.service: cosmos-db
ms.date: 02/16/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 34c6e7ad8473f02f2772c84ea63aee2a41b97306
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559697"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>Diagnozowanie i rozwiązywanie problemów z dostępnością zestawów SDK usługi Azure Cosmos w środowiskach wieloregionowych
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule opisano zachowanie najnowszej wersji zestawów SDK usługi Azure Cosmos w przypadku wystąpienia problemu z łącznością z określonym regionem lub przejścia w tryb failover w regionie.

Wszystkie zestawy SDK usługi Azure Cosmos umożliwiają dostosowanie preferencji regionalnej. Następujące właściwości są używane w różnych zestawach SDK:

* Właściwość [ConnectionPolicy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) w zestawie SDK platformy .net v2.
* Właściwości [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) lub [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) w programie .NET v3 SDK.
* Metoda [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) w zestawie SDK języka Java v4.
* Parametr [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) w zestawie SDK języka Python.
* [CosmosClientOptions. ConnectionPolicy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) w zestawie SDK js.

Po ustawieniu preferencji regionalnych klient będzie łączył się z regionem, jak wspomniano w poniższej tabeli:

|Typ konta |Odczyty |Zapisy |
|------------------------|--|--|
| Pojedynczy region zapisu | Preferowany region | Region podstawowy  |
| Wiele regionów zapisu | Preferowany region | Preferowany region  |

Jeśli **nie ustawisz preferowanego regionu**, klient zestawu SDK domyślnie będzie regionem podstawowym:

|Typ konta |Odczyty |Zapisy |
|------------------------|--|--|
| Pojedynczy region zapisu | Region podstawowy | Region podstawowy |
| Wiele regionów zapisu | Region podstawowy  | Region podstawowy  |

> [!NOTE]
> Region podstawowy odwołuje się do pierwszego regionu na [liście regionów konta usługi Azure Cosmos](distribute-data-globally.md)

W normalnych warunkach klient zestawu SDK będzie łączył się z preferowanym regionem (Jeśli ustawiona jest preferencja regionalna) lub do regionu podstawowego (jeśli nie ustawiono preferencji), a operacje będą ograniczone do tego regionu, chyba że wystąpi którykolwiek z poniższych scenariuszy.

W takich przypadkach klient korzystający z zestawu SDK usługi Azure Cosmos udostępnia dzienniki i zawiera informacje o ponownych próbach w ramach **informacji diagnostycznych dotyczących operacji**:

* Właściwość *RequestDiagnosticsString* na odpowiedziach w zestawie SDK platformy .net v2.
* Właściwość *diagnostyki* odpowiedzi i wyjątków w programie .NET v3 SDK.
* Metoda *getDiagnostics()* w zakresie odpowiedzi i wyjątków w zestawie SDK języka Java w wersji 4.

Podczas określania następnego regionu w kolejności preferencji klient zestawu SDK użyje listy regionów konta, aby określić priorytety preferowanych regionów (jeśli istnieją).

Aby uzyskać szczegółowe informacje na temat gwarancji SLA w ramach tych zdarzeń, zobacz [umowy SLA for Availability (dostępność](high-availability.md#slas-for-availability)).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Usuwanie regionu z konta

Po usunięciu regionu z konta usługi Azure Cosmos każdy klient zestawu SDK, który aktywnie korzysta z tego konta, będzie wykrywał usuwanie regionu za pośrednictwem kodu odpowiedzi zaplecza. Następnie klient oznacza region punktu końcowego jako niedostępny. Klient ponawia próbę wykonania bieżącej operacji, a wszystkie przyszłe operacje są trwale kierowane do następnego regionu w kolejności preferencji.

## <a name="adding-a-region-to-an-account"></a>Dodawanie regionu do konta

Co 5 minut klient SDK usługi Azure Cosmos odczytuje konfigurację konta i odświeża regiony, z których ma świadomość.

Jeśli usuniesz region, a później dodasz go ponownie do konta, jeśli dodany region ma wyższy priorytet preferencji regionalnych w konfiguracji zestawu SDK niż bieżący region połączony, zestaw SDK powróci do użycia tego regionu na stałe. Po wykryciu dodanego regionu wszystkie przyszłe żądania są kierowane do niego.

Jeśli klient zostanie skonfigurowany w taki sposób, aby łączył się z regionem, który nie ma konta usługi Azure Cosmos, preferowany region jest ignorowany. W przypadku dodania tego regionu później klient wykryje go i przestanie się trwale w tym regionie.

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Przechodzenie w tryb failover w regionie zapisu w jednym koncie regionu zapisu

Jeśli zainicjujesz tryb failover bieżącego regionu zapisu, następne żądanie zapisu zakończy się niepowodzeniem ze znaną odpowiedzią zaplecza. Po wykryciu tej odpowiedzi klient będzie wysyłać zapytanie do konta w celu uzyskania informacji o nowym regionie zapisu, a następnie ponawiać próbę wykonania bieżącej operacji i trwale kierować wszystkie przyszłe operacje zapisu do nowego regionu.

## <a name="regional-outage"></a>Awaria regionalna

Jeśli konto jest pojedynczym regionem zapisu, a w trakcie operacji zapisu występuje awaria regionalna, zachowanie jest podobne do [ręcznej pracy awaryjnej](#manual-failover-single-region). W przypadku żądań odczytu lub wielu kont regionów zapisu zachowanie jest podobne do [usuwania regionu](#remove-region).

## <a name="session-consistency-guarantees"></a>Gwarancje spójności sesji

W przypadku korzystania ze [spójności sesji](consistency-levels.md#guarantees-associated-with-consistency-levels)klient musi zagwarantować, że może odczytać własne zapisy. W przypadku kont w jednym regionie zapisu, w których preferencja regionu odczytu różni się od regionu zapisu, mogą wystąpić przypadki, w których użytkownik wystawia zapis i podczas odczytu z regionu lokalnego nie otrzymał jeszcze replikacji danych (szybkość ograniczenia światła). W takich przypadkach zestaw SDK wykrywa konkretny błąd operacji odczytu i ponawia próbę odczytu w regionie podstawowym, aby zapewnić spójność sesji.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Przejściowe problemy z łącznością w protokole TCP

W scenariuszach, w których klient usługi Azure Cosmos SDK jest skonfigurowany do korzystania z protokołu TCP, w przypadku danego żądania mogą wystąpić sytuacje, w których warunki sieci tymczasowo wpływają na komunikację z określonym punktem końcowym. Te tymczasowe warunki sieciowe mogą być takie jak limity czasu protokołu TCP i niedostępność usługi (HTTP 503). Klient ponowi próbę wykonania żądania lokalnie w tym samym punkcie końcowym przez kilka sekund przed wyświetleniem błędu.

Jeśli użytkownik skonfigurował listę preferowanych regionów z więcej niż jednym regionem, a konto usługi Azure Cosmos ma wiele regionów zapisu lub pojedynczy region zapisu, a operacja jest żądaniem odczytu, klient wykryje awarię lokalną i spróbuje ponownie wykonać operację pojedynczą w następnym regionie z listy preferencji.

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [umowy SLA dostępność](high-availability.md#slas-for-availability).
* Użyj najnowszego [zestawu SDK platformy .NET](sql-api-sdk-dotnet-standard.md)
* Użyj najnowszego [zestawu SDK języka Java](sql-api-sdk-java-v4.md)
* Użyj najnowszego [zestawu SDK języka Python](sql-api-sdk-python.md)
* Użyj najnowszego [zestawu SDK węzła](sql-api-sdk-node.md)
