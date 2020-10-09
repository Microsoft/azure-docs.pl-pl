---
title: Diagnostyka Reliable Services Stanów Service Fabric platformy Azure
description: Funkcje diagnostyczne dla stanowych Reliable Services na platformie Azure Service Fabric
ms.topic: conceptual
ms.date: 8/24/2018
ms.openlocfilehash: 5a3831dd4f8d5402980fac3daf8c35d9884c852d
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840765"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funkcja diagnostyki dla stanowych usług Reliable Services
Service Fabric stanowa platformy Azure Reliable Services Klasa StatefulServiceBase emituje zdarzenia [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1) , które mogą być używane do debugowania usługi, zapewnia wgląd w sposób działania środowiska uruchomieniowego i pomaga w rozwiązywaniu problemów.

## <a name="eventsource-events"></a>Zdarzenia EventSource
Nazwa elementu EventSource dla klasy stanowej Reliable Services StatefulServiceBase to "Microsoft-servicefabric-Services". Zdarzenia z tego źródła zdarzeń pojawiają się w oknie [zdarzenia diagnostyczne](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) , gdy usługa jest [debugowana w programie Visual Studio](service-fabric-debugging-your-application.md).

Przykłady narzędzi i technologii, które pomagają zbierać i/lub wyświetlać zdarzenia EventSource, to [Narzędzia PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)i [Biblioteka Microsoft zdarzenie śledzenia Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Zdarzenia
| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Opis zdarzenia |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informacyjne |Emitowane po rozpoczęciu zadania RunAsync usługi |
| StatefulRunAsyncCancellation |2 |Informacyjne |Emitowane po anulowaniu zadania RunAsync usługi |
| StatefulRunAsyncCompletion |3 |Informacyjne |Emitowane po zakończeniu zadania RunAsync usługi |
| StatefulRunAsyncSlowCancellation |4 |Ostrzeżenie |Emitowane, gdy zadanie RunAsync usługi trwa zbyt długo, aby można było ukończyć anulowanie |
| StatefulRunAsyncFailure |5 |Error |Emitowane, gdy zadanie RunAsync usługi zgłasza wyjątek |

## <a name="interpret-events"></a>Interpretuj zdarzenia
Zdarzenia StatefulRunAsyncInvocation, StatefulRunAsyncCompletion i StatefulRunAsyncCancellation są przydatne dla składnika zapisywania usług, aby zrozumieć cykl życia usługi, a także chronometraż czasu uruchomienia usługi, anulowania lub zakończenia. Te informacje mogą być przydatne w przypadku debugowania problemów z usługą lub zapoznania się z cyklem życia usługi.

Autorzy usług powinni zwrócić szczególną uwagę na zdarzenia StatefulRunAsyncSlowCancellation i StatefulRunAsyncFailure, ponieważ wskazują one problemy z usługą.

StatefulRunAsyncFailure jest emitowany za każdym razem, gdy zadanie usługi RunAsync () zgłasza wyjątek. Zazwyczaj zgłoszony wyjątek wskazuje błąd lub usterkę w usłudze. Ponadto wyjątek powoduje niepowodzenie usługi, dlatego jest przenoszony do innego węzła. Ta operacja może być kosztowna i może opóźniać przychodzące żądania podczas przenoszenia usługi. Autorzy usług powinni określić przyczynę wyjątku i, jeśli to możliwe, rozwiązać ten problem.

StatefulRunAsyncSlowCancellation jest emitowane za każdym razem, gdy żądanie anulowania dla zadania RunAsync trwa dłużej niż cztery sekundy. Zakończenie anulowania usługi trwa zbyt długo, ma wpływ na możliwość szybkiego ponownego uruchomienia usługi w innym węźle. Ten scenariusz może mieć wpływ na ogólną dostępność usługi.

## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe Reliable Services definiuje następujące kategorie liczników wydajności:

| Kategoria | Opis |
| --- | --- |
| Service Fabric Replikator transakcyjny |Liczniki specyficzne dla replikatora transakcyjnego usługi Azure Service Fabric |
| Service Fabric TStore |Liczniki specyficzne dla platformy Azure Service Fabric TStore |

Service Fabric transakcyjny jest używany przez [Menedżera niezawodnego stanu](./service-fabric-reliable-services-introduction.md) do replikowania transakcji w ramach danego zestawu [replik](service-fabric-concepts-replica-lifecycle.md).

Service Fabric TStore jest składnikiem używanym w [niezawodnych kolekcjach](./service-fabric-reliable-services-introduction.md) do przechowywania i pobierania par klucz-wartość.

Aplikacja [Monitor wydajności systemu Windows](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) , która jest dostępna domyślnie w systemie operacyjnym Windows, może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) jest kolejną opcją do zbierania danych licznika wydajności i przekazywania ich do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień liczników wydajności
Klaster, który ma dużą liczbę niezawodnych usług lub niezawodnych partycji usługi, będzie miał dużą liczbę wystąpień liczników wydajności dla replikatorów transakcyjnych. Jest to również przypadek dla liczników wydajności TStore, ale jest również mnożony przez liczbę niezawodnych słowników i używanych niezawodnych kolejek. Nazwy wystąpień licznika wydajności mogą pomóc w zidentyfikowaniu konkretnej [partycji](service-fabric-concepts-partitioning.md), repliki usługi i dostawcy stanu w przypadku TStore, z którym jest skojarzone wystąpienie licznika wydajności.

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric kategorii replikatorów transakcyjnych
W przypadku kategorii `Service Fabric Transactional Replicator` nazwy wystąpień liczników mają następujący format:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* to ciąg reprezentujący identyfikator partycji Service Fabric, z którym jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentacja w postaci ciągu jest generowana za pomocą [`Guid.ToString`](/dotnet/api/system.guid.tostring?view=netcore-3.1#System_Guid_ToString_System_String_) specyfikatora formatu "D".

*ServiceFabricReplicaId* to identyfikator skojarzony z daną repliką niezawodnej usługi. IDENTYFIKATOR repliki jest dołączany do nazwy wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi wystąpieniami liczników wydajności generowanymi przez tę samą partycję. Więcej informacji o replikach i ich roli w niezawodnych usługach można znaleźć [tutaj](service-fabric-concepts-replica-lifecycle.md).

Następująca nazwa wystąpienia licznika jest typowa dla licznika w `Service Fabric Transactional Replicator` kategorii:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

W poprzednim przykładzie `00d0126d-3e36-4d68-98da-cc4f7195d85e` jest reprezentacją ciągu Service Fabric identyfikator partycji i `131652217797162571` jest identyfikatorem repliki.

#### <a name="service-fabric-tstore-category"></a>Service Fabric kategorii TStore
W przypadku kategorii `Service Fabric TStore` nazwy wystąpień liczników mają następujący format:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* to ciąg reprezentujący identyfikator partycji Service Fabric, z którym jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentacja w postaci ciągu jest generowana za pomocą [`Guid.ToString`](/dotnet/api/system.guid.tostring?view=netcore-3.1#System_Guid_ToString_System_String_) specyfikatora formatu "D".

*ServiceFabricReplicaId* to identyfikator skojarzony z daną repliką niezawodnej usługi. IDENTYFIKATOR repliki jest dołączany do nazwy wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi wystąpieniami liczników wydajności generowanymi przez tę samą partycję. Więcej informacji o replikach i ich roli w niezawodnych usługach można znaleźć [tutaj](service-fabric-concepts-replica-lifecycle.md).

*StateProviderId* jest identyfikatorem skojarzonym z dostawcą stanu w ramach niezawodnej usługi. Identyfikator dostawcy stanu jest dołączany do nazwy wystąpienia licznika wydajności w celu odróżnienia TStore od innego.

*PerformanceCounterInstanceDifferentiator* jest identyfikatorem odróżnienia skojarzonym z wystąpieniem licznika wydajności w ramach dostawcy stanu. Ten odróżniający jest uwzględniany w nazwie wystąpienia licznika wydajności w celu zapewnienia jego unikatowości i uniknięcia konfliktu z innymi wystąpieniami liczników wydajności generowanymi przez tego samego dostawcę stanu.

*StateProviderName* jest nazwą skojarzoną z dostawcą stanu w ramach niezawodnej usługi. Nazwa dostawcy stanu jest dołączana do nazwy wystąpienia licznika wydajności, aby użytkownicy mogli łatwo określić, jaki stan zapewnia.

Następująca nazwa wystąpienia licznika jest typowa dla licznika w `Service Fabric TStore` kategorii:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

W poprzednim przykładzie, `00d0126d-3e36-4d68-98da-cc4f7195d85e` jest reprezentacją ciągu Service Fabric identyfikator partycji, `131652217797162571` jest identyfikatorem repliki, `142652217797162571` jest identyfikatorem dostawcy stanu i `1337` jest odróżniany od wystąpienia licznika wydajności. `urn:MyReliableDictionary/dataStore` jest nazwą dostawcy stanu, który przechowuje dane dla kolekcji o nazwie `urn:MyReliableDictionary` .

### <a name="transactional-replicator-performance-counters"></a>Liczniki wydajności transakcyjnych replikatorów

Środowisko uruchomieniowe Reliable Services emituje następujące zdarzenia w `Service Fabric Transactional Replicator` kategorii

 Nazwa licznika | Opis |
| --- | --- |
| Liczba operacji BEGIN transakcja/s | Liczba utworzonych nowych transakcji zapisu na sekundę.|
| Transakcja operacji/s | Liczba operacji dodawania/aktualizowania/usuwania wykonanych dla niezawodnych kolekcji na sekundę.|
| Bajty opróżniania dziennika/s | Liczba bajtów opróżnianych na dysk przez Replikator transakcyjny na sekundę |
| Operacje ograniczające/s | Liczba operacji odrzuconych co sekundę przez Replikator transakcyjny ze względu na ograniczenie przepustowości. |
| Średnia transakcja MS/Commit | Średnie opóźnienie zatwierdzania na transakcję w milisekundach |
| Średnie opóźnienie operacji opróżniania (MS) | Średni czas trwania operacji opróżniania dysku inicjowanych przez Replikator transakcyjny w milisekundach |

### <a name="tstore-performance-counters"></a>Liczniki wydajności TStore

Środowisko uruchomieniowe Reliable Services emituje następujące zdarzenia w `Service Fabric TStore` kategorii

 Nazwa licznika | Opis |
| --- | --- |
| Liczba elementów | Liczba elementów w sklepie.|
| Rozmiar dysku | Łączny rozmiar dysku (w bajtach) plików punktów kontrolnych dla magazynu.|
| Bajty zapisu pliku punktu kontrolnego/s | Liczba bajtów zapisanych na sekundę dla ostatniego pliku punktu kontrolnego.|
| Bajty transferu dysku/s | Liczba bajtów dysku odczytanych (w replice podstawowej) lub zapisana (w replice pomocniczej) na sekundę podczas kopiowania magazynu.|

## <a name="next-steps"></a>Następne kroki
[Dostawcy EventSource w narzędzia PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
