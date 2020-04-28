---
title: Diagnozowanie i rozwiązywanie problemów podczas korzystania z zestawu .NET SDK usługi Azure Cosmos DB
description: Korzystaj z funkcji, takich jak rejestrowanie po stronie klienta i innych narzędzi innych firm, aby identyfikować, diagnozować i rozwiązywać problemy Azure Cosmos DB podczas korzystania z zestawu .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79137958"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnozowanie i rozwiązywanie problemów podczas korzystania z zestawu .NET SDK usługi Azure Cosmos DB
W tym artykule opisano typowe problemy, obejścia, kroki diagnostyczne i narzędzia używane w przypadku korzystania z [zestawu .NET SDK](sql-api-sdk-dotnet.md) z kontami interfejsu API SQL Azure Cosmos DB.
Zestaw .NET SDK zapewnia logiczną reprezentację po stronie klienta, aby uzyskać dostęp do Azure Cosmos DB interfejsu API SQL. W tym artykule opisano narzędzia i podejścia pomocne w przypadku napotkania jakichkolwiek problemów.

## <a name="checklist-for-troubleshooting-issues"></a>Lista kontrolna dotycząca rozwiązywania problemów:
Przed przeniesieniem aplikacji do środowiska produkcyjnego należy wziąć pod uwagę poniższą listę kontrolną. Korzystanie z listy kontrolnej uniemożliwi kilka typowych problemów, które mogą zostać wyświetlone. Można również szybko zdiagnozować w przypadku wystąpienia problemu:

*    Użyj najnowszego [zestawu SDK](sql-api-sdk-dotnet-standard.md). Zestawów SDK wersji zapoznawczych nie należy używać w środowisku produkcyjnym. Uniemożliwi to wyróżnianie znanych problemów, które zostały już naprawione.
*    Zapoznaj się z [poradami dotyczącymi wydajności](performance-tips.md)i postępuj zgodnie z zaleceniami. Pomoże to uniknąć skalowania, opóźnień i innych problemów z wydajnością.
*    Włącz rejestrowanie zestawu SDK, aby pomóc w rozwiązaniu problemu. Włączenie rejestrowania może mieć wpływ na wydajność, dlatego najlepiej je włączyć tylko w przypadku rozwiązywania problemów. Można włączyć następujące dzienniki:
    *    [Rejestruj metryki](monitor-accounts.md) przy użyciu Azure Portal. Metryki portalu pokazują Azure Cosmos DB dane telemetryczne, które ułatwiają określenie, czy problem odnosi się do Azure Cosmos DB, czy też jest po stronie klienta.
    *    Rejestruj [ciąg diagnostyczny](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) w zestawie SDK V2 lub [diagnostykę](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) w zestawie SDK v3 z odpowiedzi operacji punktu.
    *    Rejestruj [metryki zapytań SQL](sql-api-query-metrics.md) ze wszystkich odpowiedzi na zapytania 
    *    Postępuj zgodnie z konfiguracją [rejestrowania zestawu SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Zapoznaj się z sekcją [typowe problemy i obejścia](#common-issues-workarounds) w tym artykule.

Zapoznaj się z [sekcją problemów usługi GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) , która jest aktywnie monitorowana. Sprawdź, czy podobny problem z obejściem został już zgłoszony. Jeśli nie znajdziesz rozwiązania, zapoznaj się z tematem problemu z usługą GitHub. Możesz otworzyć cykl pomocy technicznej, aby uzyskać pilne problemy.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Typowe problemy i ich rozwiązania

### <a name="general-suggestions"></a>Ogólne sugestie
* Uruchom aplikację w tym samym regionie świadczenia usługi Azure jako konto Azure Cosmos DB, jeśli to możliwe. 
* Problemy z łącznością/dostępnością mogą być spowodowane brakiem zasobów na komputerze klienckim. Zalecamy monitorowanie użycia procesora CPU w węzłach z uruchomionym Azure Cosmos DB klienta i skalowanie w górę/w poziomie, jeśli są one uruchamiane przy dużym obciążeniu.

### <a name="check-the-portal-metrics"></a>Sprawdź metryki portalu
Sprawdzanie [metryk portalu](monitor-accounts.md) pomoże określić, czy jest to problem po stronie klienta, czy też występuje problem z usługą. Na przykład, jeśli metryki zawierają wysoką częstotliwość żądań (kod stanu HTTP 429), co oznacza, że żądanie jest ograniczone, sprawdź [częstotliwość żądań za dużą] sekcję. 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Limity czasu żądań
RequestTimeout zazwyczaj odbywa się przy użyciu protokołu Direct/TCP, ale może się zdarzyć w trybie bramy. Te błędy są typowymi znanymi przyczynami oraz sugestie dotyczące sposobu rozwiązania problemu.

* Użycie procesora CPU ma wysoką wartość, co spowoduje opóźnienia i/lub przekroczenie limitu czasu żądania. Klient może skalować w górę maszynę hosta w celu zapewnienia większej ilości zasobów, a obciążenie może być dystrybuowane na większej liczbie komputerów.
* Dostępność gniazda/portu może być niska. W przypadku uruchamiania na platformie Azure klienci korzystający z zestawu SDK platformy .NET mogą osiągać wyczerpanie portów z użyciem usługi Azure translatora adresów sieciowych. Aby zmniejszyć prawdopodobieństwo wystąpienia tego problemu, użyj najnowszej wersji 2. x lub 3. x zestawu .NET SDK. Jest to przykład, dlaczego zaleca się zawsze uruchomienie najnowszej wersji zestawu SDK.
* Tworzenie wielu wystąpień DocumentClient może prowadzić do problemów związanych z rywalizacją i limitem czasu. Postępuj zgodnie ze [wskazówkami dotyczącymi wydajności](performance-tips.md)i korzystaj z jednego wystąpienia DocumentClient w całym procesie.
* Czasami użytkownicy zobaczą podwyższony czas oczekiwania lub przekroczenia limitu czasu żądania, ponieważ ich kolekcje są w niewystarczający sposób używane jako ograniczenia wewnętrznej bazy danych żądań i ponowne próby klienta. Sprawdź [metryki portalu](monitor-accounts.md).
* Azure Cosmos DB dystrybuuje ogólnie zainicjowaną przepływność równomiernie między partycjami fizycznymi. Sprawdź metryki portalu, aby sprawdzić, czy w obciążeniu występuje [klucz partycji](partition-data.md)aktywnej. Spowoduje to, że agregowana przepływność (RU/s) będzie wyświetlana w ramach aprowizacji jednostek ru, ale użycie jednej partycji (RU/s) spowoduje przekroczenie alokowanej przepływności. 
* Ponadto zestaw SDK 2,0 dodaje semantykę kanału do połączeń bezpośrednich/TCP. Jedno połączenie TCP jest używane dla wielu żądań w tym samym czasie. Może to prowadzić do dwóch problemów w określonych przypadkach:
    * Wysoki stopień współbieżności może prowadzić do rywalizacji o kanał.
    * Duże żądania lub odpowiedzi mogą prowadzić do blokowania w kanale i zaostrzania rywalizacji nawet przy stosunkowo niskim stopniu współbieżności.
    * Jeśli przypadek występuje w jednej z tych dwóch kategorii (lub jeśli jest podejrzane wysokie wykorzystanie procesora CPU), są to możliwe środki zaradcze:
        * Spróbuj skalować aplikację w górę/w dół.
        * Ponadto dzienniki zestawu SDK mogą być przechwytywane przez [odbiornik śledzenia](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) , aby uzyskać więcej szczegółów.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Duże opóźnienie sieci
Duże opóźnienie sieci można zidentyfikować za pomocą [ciągu diagnostycznego](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) w zestawie SDK V2 w wersji 2 lub [Diagnostics](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) w zestawie SDK v3.

Jeśli nie ma [limitów czasu](#request-timeouts) , a Diagnostyka pokaże pojedyncze żądania, w przypadku których duże opóźnienie jest oczywiste na `ResponseTime` różnicy `RequestStartTime`między i, np. (>300 milisekund w tym przykładzie):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

To opóźnienie może mieć wiele przyczyn:

* Aplikacja nie jest uruchomiona w tym samym regionie co konto Azure Cosmos DB.
* Konfiguracja [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) lub [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) jest nieprawidłowa i podejmuje próbę nawiązania połączenia z innym regionem w lokalizacji, w której aplikacja jest aktualnie uruchomiona.
* W interfejsie sieciowym może występować wąskie gardło z powodu dużego ruchu. Jeśli aplikacja działa na platformie Azure Virtual Machines, możliwe jest obejście tego problemu:
    * Rozważ użycie [maszyny wirtualnej z włączoną obsługą przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Włącz [przyspieszone sieci na istniejącej maszynie wirtualnej](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Rozważ użycie [wyższej końcowej maszyny wirtualnej](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Wyczerpanie portów (z) na platformie Azure

Jeśli aplikacja jest wdrażana na [platformie azure Virtual Machines bez publicznego adresu IP](../load-balancer/load-balancer-outbound-connections.md#defaultsnat), domyślnie [porty usługi Azure](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) IPSec ustanawiają połączenia z dowolnym punktem końcowym poza maszyną wirtualną. Liczba połączeń dozwolonych między maszyną wirtualną a punktem końcowym Azure Cosmos DB jest ograniczona przez [konfigurację usługi Azure translatora adresów sieciowych](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Ta sytuacja może prowadzić do ograniczenia połączeń, zamknięcia połączenia lub powyżej wspomnianych [limitów czasu żądania](#request-timeouts).

 Porty protokołu IPSec platformy Azure są używane tylko wtedy, gdy maszyna wirtualna ma prywatny adres IP łączący się z publicznym adresem IP. Istnieją dwa obejścia, aby uniknąć ograniczenia dotyczącego translatora adresów sieciowych platformy Azure (pod warunkiem, że korzystasz już z pojedynczego wystąpienia klienta w całej aplikacji):

* Dodaj punkt końcowy usługi Azure Cosmos DB do podsieci sieci wirtualnej platformy Azure Virtual Machines. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Po włączeniu punktu końcowego usługi żądania nie są już wysyłane z publicznego adresu IP do Azure Cosmos DB. Zamiast tego jest wysyłana tożsamość sieci wirtualnej i podsieci. Ta zmiana może spowodować, że Zapora spadnie, jeśli dozwolone są tylko publiczne adresy IP. Jeśli używasz zapory, po włączeniu punktu końcowego usługi Dodaj podsieć do zapory przy użyciu [list acl Virtual Network](../virtual-network/virtual-networks-acl.md).
* Przypisz [publiczny adres IP do maszyny wirtualnej platformy Azure](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>Serwer proxy HTTP
W przypadku korzystania z serwera proxy HTTP upewnij się, że może on obsługiwać liczbę połączeń skonfigurowanych w zestawie `ConnectionPolicy`SDK.
W przeciwnym razie nastąpiły problemy z połączeniem.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Zbyt duży współczynnik żądań
"Częstotliwość żądań zbyt duża" lub kod błędu 429 wskazuje, że żądania są ograniczane, ponieważ wykorzystana przepływność (RU/s) przekroczyła [zainicjowaną przepływność](set-throughput.md). Zestaw SDK będzie automatycznie ponawiać próbę żądania na podstawie określonych [zasad ponawiania](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Jeśli ten błąd występuje często, należy rozważyć zwiększenie przepływności kolekcji. Sprawdź [metryki portalu](use-metrics.md) , aby sprawdzić, czy są wyświetlane błędy 429. Przejrzyj [klucz partycji](partitioning-overview.md#choose-partitionkey) , aby upewnić się, że jest to równomierny rozkład magazynu i woluminu żądania. 

### <a name="slow-query-performance"></a>Niska wydajność zapytań
[Metryki zapytań](sql-api-query-metrics.md) pomogą określić, gdzie zapytanie jest spędzane większością czasu. Z metryk zapytania można sprawdzić, jaka część jest używana na zapleczu programu vs a kliencie.
* Jeśli zapytanie zaplecza wraca szybko i spędza na tym dużą godzinę, sprawdź obciążenie maszyny. Prawdopodobnie nie ma wystarczającej ilości zasobów i zestaw SDK oczekuje na dostępność zasobów do obsługi odpowiedzi.
* Jeśli zapytanie zaplecza próbuje [zoptymalizować zapytanie](optimize-cost-queries.md) i przeszukać bieżące [zasady indeksowania](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Zbyt duży współczynnik żądań]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


