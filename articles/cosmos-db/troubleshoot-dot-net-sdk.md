---
title: Diagnozowanie i rozwiązywanie problemów podczas korzystania z zestawu .NET SDK usługi Azure Cosmos DB
description: Korzystaj z funkcji, takich jak rejestrowanie po stronie klienta i innych narzędzi innych firm, aby identyfikować, diagnozować i rozwiązywać problemy Azure Cosmos DB podczas korzystania z zestawu .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 03/05/2021
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1f7548b355353eb77419f4d1760b40ba02eeddda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442200"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnozowanie i rozwiązywanie problemów podczas korzystania z zestawu .NET SDK usługi Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK 4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK 2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

W tym artykule opisano typowe problemy, obejścia, kroki diagnostyczne i narzędzia używane w przypadku korzystania z [zestawu .NET SDK](sql-api-sdk-dotnet.md) z kontami interfejsu API SQL Azure Cosmos DB.
Zestaw .NET SDK zapewnia logiczną reprezentację po stronie klienta, aby uzyskać dostęp do Azure Cosmos DB interfejsu API SQL. W tym artykule opisano narzędzia i podejścia pomocne w przypadku napotkania jakichkolwiek problemów.

## <a name="checklist-for-troubleshooting-issues"></a>Lista kontrolna dotycząca rozwiązywania problemów

Przed przeniesieniem aplikacji do środowiska produkcyjnego należy wziąć pod uwagę poniższą listę kontrolną. Korzystanie z listy kontrolnej uniemożliwi kilka typowych problemów, które mogą zostać wyświetlone. Można również szybko zdiagnozować w przypadku wystąpienia problemu:

*    Użyj najnowszego [zestawu SDK](sql-api-sdk-dotnet-standard.md). Zestawów SDK wersji zapoznawczych nie należy używać w środowisku produkcyjnym. Uniemożliwi to wyróżnianie znanych problemów, które zostały już naprawione.
*    Zapoznaj się z [poradami dotyczącymi wydajności](performance-tips.md)i postępuj zgodnie z zaleceniami. Pomoże to uniknąć skalowania, opóźnień i innych problemów z wydajnością.
*    Włącz rejestrowanie zestawu SDK, aby pomóc w rozwiązaniu problemu. Włączenie rejestrowania może mieć wpływ na wydajność, dlatego najlepiej je włączyć tylko w przypadku rozwiązywania problemów. Można włączyć następujące dzienniki:
*    [Rejestruj metryki](./monitor-cosmos-db.md) przy użyciu Azure Portal. Metryki portalu pokazują Azure Cosmos DB dane telemetryczne, które ułatwiają określenie, czy problem odnosi się do Azure Cosmos DB, czy też jest po stronie klienta.
*    Rejestruj [ciąg diagnostyczny](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) w zestawie SDK V2 lub [diagnostykę](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) w zestawie SDK v3 z odpowiedzi operacji punktu.
*    Rejestruj [metryki zapytań SQL](sql-api-query-metrics.md) ze wszystkich odpowiedzi na zapytania 
*    Postępuj zgodnie z konfiguracją [rejestrowania zestawu SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Zapoznaj się z sekcją [typowe problemy i obejścia](#common-issues-workarounds) w tym artykule.

Zapoznaj się z [sekcją problemów usługi GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) , która jest aktywnie monitorowana. Sprawdź, czy podobny problem z obejściem został już zgłoszony. Jeśli nie znajdziesz rozwiązania, zapoznaj się z tematem problemu z usługą GitHub. Możesz otworzyć cykl pomocy technicznej, aby uzyskać pilne problemy.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Typowe problemy i ich rozwiązania

### <a name="general-suggestions"></a>Ogólne sugestie
* Uruchom aplikację w tym samym regionie świadczenia usługi Azure jako konto Azure Cosmos DB, jeśli to możliwe. 
* Problemy z łącznością/dostępnością mogą być spowodowane brakiem zasobów na komputerze klienckim. Zalecamy monitorowanie użycia procesora CPU w węzłach z uruchomionym Azure Cosmos DB klienta i skalowanie w górę/w poziomie, jeśli są one uruchamiane przy dużym obciążeniu.

### <a name="check-the-portal-metrics"></a>Sprawdź metryki portalu
Sprawdzanie [metryk portalu](./monitor-cosmos-db.md) pomoże określić, czy jest to problem po stronie klienta, czy też występuje problem z usługą. Na przykład, jeśli metryki zawierają wysoką częstotliwość żądań (kod stanu HTTP 429), co oznacza, że żądanie jest ograniczone, sprawdź [częstotliwość żądań za dużą](troubleshoot-request-rate-too-large.md) sekcję. 

## <a name="retry-logic"></a>Logika ponawiania <a id="retry-logics"></a>
Zestaw Cosmos DB SDK w przypadku dowolnego błędy we/wy podejmie próbę ponowienia operacji zakończonej niepowodzeniem, jeśli jest możliwe jej ponowienie. Ponowienie próby w przypadku jakiegokolwiek błędu jest dobrym sposobem, ale w przypadku niepowodzenia przetwarzania/ponawiania próby zapisu jest to konieczne. Zalecane jest korzystanie z najnowszego zestawu SDK, ponieważ logika ponowień jest ciągle ulepszana.

1. Błędy we/wy odczytu i zapytania zostaną ponowione przez zestaw SDK bez obsłużynia ich użytkownikowi końcowemu.
2. Operacje zapisu (Create, Upsert, Replace, Delete) nie są idempotentne, a tym samym zestaw SDK nie zawsze może niemniej ponowić próbę wykonania nieudanych operacji zapisywania. Wymagane jest, aby logika aplikacji użytkownika mogła obsłużyć błąd, i ponowić próbę.
3. Problemy związane z uzyskaniem [dostępności zestawu SDK](troubleshoot-sdk-availability.md) objaśniają ponawianie prób dla wieloregionowych kont Cosmos DB.

## <a name="common-error-status-codes"></a>Kody stanu typowego błędu <a id="error-codes"></a>

| Kod stanu | Opis | 
|----------|-------------|
| 400 | Złe żądanie (zależy od komunikatu o błędzie)| 
| 401 | [Brak autoryzacji](troubleshoot-unauthorized.md) | 
| 403 | [Zabronione](troubleshoot-forbidden.md) |
| 404 | [Nie znaleziono zasobu](troubleshoot-not-found.md) |
| 408 | [Przekroczono limit czasu żądania](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | Błąd konfliktu polega na tym, że identyfikator podany dla zasobu w operacji zapisu został podjęty przez istniejący zasób. Użyj innego identyfikatora dla zasobu, aby rozwiązać ten problem, ponieważ identyfikator musi być unikatowy w obrębie wszystkich dokumentów z tą samą wartością klucza partycji. |
| 410 | Usunięte wyjątki (błąd przejściowy, który nie powinien naruszać umowy SLA) |
| 412 | Niepowodzenie warunku wstępnego polega na tym, że operacja określiła element eTag, który jest inny niż wersja dostępna na serwerze. Jest to optymistyczny błąd współbieżności. Ponów żądanie po odczytaniu najnowszej wersji zasobu i zaktualizowaniu elementu eTag dla żądania.
| 413 | [Jednostka żądania jest zbyt duża](concepts-limits.md#per-item-limits) |
| 429 | [Zbyt wiele żądań](troubleshoot-request-rate-too-large.md) |
| 449 | Błąd przejściowy występujący tylko w operacjach zapisu i czy można bezpiecznie ponowić próbę |
| 500 | Operacja nie powiodła się z powodu nieoczekiwanego błędu usługi. Skontaktuj się z pomocą techniczną. Zobacz temat zgłaszanie [problemu pomocy technicznej platformy Azure](https://aka.ms/azure-support). |
| 503 | [Usługa niedostępna](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Wyczerpanie portów (z) na platformie Azure

Jeśli aplikacja jest wdrażana na [platformie azure Virtual Machines bez publicznego adresu IP](../load-balancer/load-balancer-outbound-connections.md), domyślnie [porty usługi Azure](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) IPSec ustanawiają połączenia z dowolnym punktem końcowym poza maszyną wirtualną. Liczba połączeń dozwolonych między maszyną wirtualną a punktem końcowym Azure Cosmos DB jest ograniczona przez [konfigurację usługi Azure translatora adresów sieciowych](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Ta sytuacja może prowadzić do ograniczenia połączeń, zamknięcia połączenia lub powyżej wspomnianych [limitów czasu żądania](troubleshoot-dot-net-sdk-request-timeout.md).

 Porty protokołu IPSec platformy Azure są używane tylko wtedy, gdy maszyna wirtualna ma prywatny adres IP łączący się z publicznym adresem IP. Istnieją dwa obejścia, aby uniknąć ograniczenia dotyczącego translatora adresów sieciowych platformy Azure (pod warunkiem, że korzystasz już z pojedynczego wystąpienia klienta w całej aplikacji):

* Dodaj punkt końcowy usługi Azure Cosmos DB do podsieci sieci wirtualnej platformy Azure Virtual Machines. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Po włączeniu punktu końcowego usługi żądania nie są już wysyłane z publicznego adresu IP do Azure Cosmos DB. Zamiast tego jest wysyłana tożsamość sieci wirtualnej i podsieci. Ta zmiana może spowodować, że Zapora spadnie, jeśli dozwolone są tylko publiczne adresy IP. Jeśli używasz zapory, po włączeniu punktu końcowego usługi Dodaj podsieć do zapory przy użyciu [list acl Virtual Network](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Przypisz [publiczny adres IP do maszyny wirtualnej platformy Azure](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Duże opóźnienie sieci
Duże opóźnienie sieci można zidentyfikować za pomocą [ciągu diagnostycznego](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) w zestawie SDK V2 w wersji 2 lub [Diagnostics](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) w zestawie SDK v3.

Jeśli nie ma żadnych [limitów czasu](troubleshoot-dot-net-sdk-request-timeout.md) , a Diagnostyka pokaże pojedyncze żądania, w przypadku których duże opóźnienie jest oczywiste.

# <a name="v3-sdk"></a>[ZESTAW V3 SDK](#tab/diagnostics-v3)

Diagnostyka może być uzyskana z dowolnego `ResponseMessage` , `ItemResponse` , `FeedResponse` lub `CosmosException` przez `Diagnostics` Właściwość:

```csharp
ItemResponse<MyItem> response = await container.CreateItemAsync<MyItem>(item);
Console.WriteLine(response.Diagnostics.ToString());
```

Interakcje sieciowe w diagnostyce będą na przykład następujące:

```json
{
    "name": "Microsoft.Azure.Documents.ServerStoreModel Transport Request",
    "id": "0e026cca-15d3-4cf6-bb07-48be02e1e82e",
    "component": "Transport",
    "start time": "12: 58: 20: 032",
    "duration in milliseconds": 1638.5957
}
```

Gdzie zostanie `duration in milliseconds` wyświetlone opóźnienie.

# <a name="v2-sdk"></a>[ZESTAW V2 SDK](#tab/diagnostics-v2)

Diagnostyka jest dostępna, gdy klient jest skonfigurowany w [trybie bezpośrednim](sql-sdk-connection-modes.md), przez `RequestDiagnosticsString` Właściwość:

```csharp
ResourceResponse<Document> response = await client.ReadDocumentAsync(documentLink, new RequestOptions() { PartitionKey = new PartitionKey(partitionKey) });
Console.WriteLine(response.RequestDiagnosticsString);
```

Czas oczekiwania na różnicę między `ResponseTime` i `RequestStartTime` :

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```
--- 

To opóźnienie może mieć wiele przyczyn:

* Aplikacja nie jest uruchomiona w tym samym regionie co konto Azure Cosmos DB.
* Konfiguracja [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) lub [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) jest nieprawidłowa i podejmuje próbę nawiązania połączenia z innym regionem w lokalizacji, w której aplikacja jest aktualnie uruchomiona.
* W interfejsie sieciowym może występować wąskie gardło z powodu dużego ruchu. Jeśli aplikacja działa na platformie Azure Virtual Machines, możliwe jest obejście tego problemu:
    * Rozważ użycie [maszyny wirtualnej z włączoną obsługą przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Włącz [przyspieszone sieci na istniejącej maszynie wirtualnej](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Rozważ użycie [wyższej końcowej maszyny wirtualnej](../virtual-machines/sizes.md).

### <a name="common-query-issues"></a>Typowe problemy z zapytaniami

[Metryki zapytań](sql-api-query-metrics.md) pomogą określić, gdzie zapytanie jest spędzane większością czasu. Z metryk zapytania można sprawdzić, jaka część jest używana na zapleczu programu vs a kliencie. Dowiedz się więcej o [rozwiązywaniu problemów z wydajnością zapytań](troubleshoot-query-performance.md).

* Jeśli zapytanie zaplecza wraca szybko i spędza na tym dużą godzinę, sprawdź obciążenie maszyny. Prawdopodobnie nie ma wystarczającej ilości zasobów i zestaw SDK oczekuje na dostępność zasobów do obsługi odpowiedzi.
* Jeśli zapytanie zaplecza jest powolne, spróbuj [zoptymalizować zapytanie](troubleshoot-query-performance.md) i przejrzeć bieżące [zasady indeksowania](index-overview.md)

    > [!NOTE]
    > Aby zwiększyć wydajność, zalecamy przetwarzanie hosta Windows 64-bitowego. Zestaw SDK SQL zawiera natywną ServiceInterop.dll do analizy i optymalizowania zapytań lokalnie. ServiceInterop.dll jest obsługiwana tylko na platformie Windows x64. W przypadku systemu Linux i innych nieobsługiwanych platform, w których ServiceInterop.dll nie jest dostępna, do bramy zostanie nawiązane dodatkowe połączenie sieciowe w celu uzyskania zoptymalizowanego zapytania.

Jeśli wystąpi następujący błąd: `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` i używa systemu Windows, należy przeprowadzić uaktualnienie do najnowszej wersji systemu Windows.

## <a name="next-steps"></a>Następne kroki

* Informacje o wskazówkach dotyczących wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md)
* Dowiedz się więcej na temat [zestawów SDK Java opartych na](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) replikach

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list