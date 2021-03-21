---
title: Diagnostyka i monitorowanie aktorów
description: W tym artykule opisano funkcje diagnostyki i monitorowania wydajności w środowisku uruchomieniowym Reliable Actors Service Fabric, w tym zdarzenia i liczniki wydajności emitowane przez nią.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: ebaedb5369f3b39372262bfde526706e8d069418
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789620"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostyka i monitorowanie wydajności struktury Reliable Actors
Środowisko uruchomieniowe Reliable Actors emituje zdarzenia [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) i [liczniki wydajności](/dotnet/api/system.diagnostics.performancecounter). Zapewniają one wgląd w działanie środowiska uruchomieniowego i ułatwiają rozwiązywanie problemów i monitorowanie wydajności.

## <a name="eventsource-events"></a>Zdarzenia EventSource
Nazwa dostawcy EventSource dla środowiska uruchomieniowego Reliable Actors to "Microsoft-servicefabric-Aktors". Zdarzenia z tego źródła zdarzeń pojawiają się w oknie [zdarzenia diagnostyczne](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) , gdy aplikacja aktora jest [debugowana w programie Visual Studio](service-fabric-debugging-your-application.md).

Przykłady narzędzi i technologii, które pomagają zbierać i/lub wyświetlać zdarzenia EventSource, to [Narzędzia PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [Rejestrowanie semantyczne](/previous-versions/msp-n-p/dn774980(v=pandp.10))i [Biblioteka Microsoft zdarzenie śledzenia Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Słowa kluczowe
Wszystkie zdarzenia należące do Reliable Actors EventSource są skojarzone z co najmniej jednym słowami kluczowymi. Umożliwia to filtrowanie zdarzeń, które są zbierane. Zdefiniowane są następujące bity słowa kluczowego.

| Bit | Opis |
| --- | --- |
| 0x1 |Zestaw ważnych zdarzeń, które podsumowują działanie środowiska uruchomieniowego aktorów sieci szkieletowej. |
| 0x2 |Zestaw zdarzeń opisujących wywołania metod aktora. Aby uzyskać więcej informacji, zobacz [temat wprowadzający w aktorach](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Zestaw zdarzeń związanych ze stanem aktora. Aby uzyskać więcej informacji, zobacz temat dotyczący [zarządzania stanem aktora](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Zestaw zdarzeń związanych z przekształcaniem współbieżności w aktorze. Aby uzyskać więcej informacji, zobacz temat dotyczący [współbieżności](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe Reliable Actors definiuje następujące kategorie liczników wydajności.

| Kategoria | Opis |
| --- | --- |
| Aktor Service Fabric |Liczniki specyficzne dla uczestników platformy Azure Service Fabric, np. czas zapisywania stanu aktora |
| Metoda aktora Service Fabric |Liczniki specyficzne dla metod implementowanych przez Service Fabric aktorów, np. częstotliwość wywoływania metody aktora |

Każda z powyższych kategorii ma jeden lub więcej liczników.

Aplikacja [Monitor wydajności systemu Windows](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) , która jest dostępna domyślnie w systemie operacyjnym Windows, może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) jest kolejną opcją do zbierania danych licznika wydajności i przekazywania ich do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień liczników wydajności
Klaster, który ma dużą liczbę usług aktora lub partycji usługi aktora, będzie miał dużą liczbę wystąpień liczników wydajności aktora. Nazwy wystąpień licznika wydajności mogą pomóc w zidentyfikowaniu określonej [partycji](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) i metody aktora (jeśli dotyczy), z którym jest skojarzone wystąpienie licznika wydajności.

#### <a name="service-fabric-actor-category"></a>Kategoria aktora Service Fabric
W przypadku kategorii `Service Fabric Actor` nazwy wystąpień liczników mają następujący format:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* to ciąg reprezentujący identyfikator partycji Service Fabric, z którym jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentacja w postaci ciągu jest generowana za pomocą [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) metody ze specyfikatorem formatu "D".

*ActorRuntimeInternalID* to ciąg reprezentujący 64-bitową liczbę całkowitą, która jest generowana przez środowisko uruchomieniowe aktorów do użytku wewnętrznego. Ta wartość jest uwzględniona w nazwie wystąpienia licznika wydajności, aby zapewnić jej unikatowość i uniknąć konfliktu z innymi nazwami wystąpień liczników wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej znajduje się przykład nazwy wystąpienia licznika dla licznika, który należy do `Service Fabric Actor` kategorii:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

W powyższym przykładzie `2740af29-78aa-44bc-a20b-7e60fb783264` jest reprezentacją ciągu Service Fabric identyfikator partycji i `635650083799324046` jest 64-bitowy identyfikator, który jest generowany dla wewnętrznego użycia środowiska uruchomieniowego.

#### <a name="service-fabric-actor-method-category"></a>Kategoria metody aktora Service Fabric
W przypadku kategorii `Service Fabric Actor Method` nazwy wystąpień liczników mają następujący format:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* to nazwa metody aktora, z którą jest skojarzone wystąpienie licznika wydajności. Format nazwy metody jest określany na podstawie pewnej logiki w środowisku uruchomieniowym aktorów, która równoważy czytelność nazwy z ograniczeniami dotyczącymi maksymalnej długości nazw wystąpień licznika wydajności w systemie Windows.

*ActorsRuntimeMethodId* to ciąg reprezentujący 32-bitową liczbę całkowitą, która jest generowana przez środowisko uruchomieniowe aktorów do użytku wewnętrznego. Ta wartość jest uwzględniona w nazwie wystąpienia licznika wydajności, aby zapewnić jej unikatowość i uniknąć konfliktu z innymi nazwami wystąpień liczników wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

*ServiceFabricPartitionID* to ciąg reprezentujący identyfikator partycji Service Fabric, z którym jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentacja w postaci ciągu jest generowana za pomocą [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) metody ze specyfikatorem formatu "D".

*ActorRuntimeInternalID* to ciąg reprezentujący 64-bitową liczbę całkowitą, która jest generowana przez środowisko uruchomieniowe aktorów do użytku wewnętrznego. Ta wartość jest uwzględniona w nazwie wystąpienia licznika wydajności, aby zapewnić jej unikatowość i uniknąć konfliktu z innymi nazwami wystąpień liczników wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej znajduje się przykład nazwy wystąpienia licznika dla licznika, który należy do `Service Fabric Actor Method` kategorii:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

W powyższym przykładzie `ivoicemailboxactor.leavemessageasync` jest nazwą metody, `2` jest 32-bitowy identyfikator wygenerowany dla wewnętrznego użycia środowiska uruchomieniowego, `89383d32-e57e-4a9b-a6ad-57c6792aa521` to ciąg reprezentujący identyfikator partycji Service Fabric i `635650083804480486` jest identyfikatorem 64-bitowym wygenerowanym do użytku wewnętrznego środowiska uruchomieniowego.

## <a name="list-of-events-and-performance-counters"></a>Lista zdarzeń i liczników wydajności
### <a name="actor-method-events-and-performance-counters"></a>Zdarzenia metod aktora i liczniki wydajności
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [metodami aktora](service-fabric-reliable-actors-introduction.md).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Pełny |0x2 |Środowisko uruchomieniowe aktorów ma na celu wywołanie metody aktora. |
| ActorMethodStop |8 |Pełny |0x2 |Metoda aktora zakończyła wykonywanie. Oznacza to, że wywołanie asynchroniczne środowiska uruchomieniowego do metody aktora zostało zwrócone, a zadanie zwrócone przez metodę aktora zostało zakończone. |
| ActorMethodThrewException |9 |Ostrzeżenie |0x3 |Zgłoszono wyjątek podczas wykonywania metody aktora, podczas wywołania asynchronicznego środowiska uruchomieniowego do metody aktora lub podczas wykonywania zadania zwróconego przez metodę aktora. To zdarzenie wskazuje niepowodzenie w kodzie aktora, który wymaga badania. |

Środowisko uruchomieniowe Reliable Actors publikuje następujące liczniki wydajności związane z wykonywaniem metod aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Metoda aktora Service Fabric |Wywołania/s |Liczba przypadków wywołania metody usługi aktora na sekundę |
| Metoda aktora Service Fabric |Średni czas dla wywołania |Czas wykonania metody usługi aktora w milisekundach |
| Metoda aktora Service Fabric |Zgłoszone wyjątki/s |Ile razy metoda usługi aktora zgłosiła wyjątek na sekundę |

### <a name="concurrency-events-and-performance-counters"></a>Zdarzenia współbieżności i liczniki wydajności
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [współbieżnością](service-fabric-reliable-actors-introduction.md#concurrency).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Pełny |0x8 |To zdarzenie jest zapisywane na początku każdego nowego, Włącz aktora. Zawiera ona liczbę oczekujących wywołań aktora, które oczekują na uzyskanie blokady aktora, która wymusza współbieżność opartą na włączeniu. |

Środowisko uruchomieniowe Reliable Actors publikuje następujące liczniki wydajności dotyczące współbieżności.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor Service Fabric |Liczba wywołań aktora oczekujących na blokadę aktora |Liczba oczekujących wywołań aktora oczekujących na uzyskanie blokady aktora, która wymusza współbieżność opartą na włączeniu |
| Aktor Service Fabric |Średni czas oczekiwania na blokadę |Czas trwania (w milisekundach), aby uzyskać blokadę dla aktora, która wymusza współbieżność opartą na włączeniu |
| Aktor Service Fabric |Średnia liczba przechowywanych blokad aktora w milisekundach |Czas (w milisekundach), dla którego jest utrzymywana blokada dla aktora |

### <a name="actor-state-management-events-and-performance-counters"></a>Zdarzenia i liczniki wydajności zarządzania stanem aktora
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [zarządzaniem stanem aktora](service-fabric-reliable-actors-state-management.md).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Pełny |0x4 |Środowisko uruchomieniowe aktorów ma na celu zapisanie stanu aktora. |
| ActorSaveStateStop |11 |Pełny |0x4 |Środowisko uruchomieniowe aktorów zakończyło Zapisywanie stanu aktora. |

Środowisko uruchomieniowe Reliable Actors publikuje następujące liczniki wydajności związane z zarządzaniem stanem aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor Service Fabric |Średnia liczba milisekund dla operacji zapisu stanu |Czas zapisywania stanu aktora w milisekundach |
| Aktor Service Fabric |Średni czas operacji ładowania stanu (milisekundy) |Czas ładowania stanu aktora w milisekundach |

### <a name="events-related-to-actor-replicas"></a>Zdarzenia związane z replikami aktorów
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [replikami aktorów](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informacyjne |0x1 |Replika aktora zmieniła rolę na podstawową. Oznacza to, że aktory dla tej partycji zostaną utworzone w tej replice. |
| ReplicaChangeRoleFromPrimary |2 |Informacyjne |0x1 |Replika aktora zmieniła rolę na niepodstawową. Oznacza to, że aktory dla tej partycji nie będą już tworzone w tej replice. Żadne nowe żądania nie zostaną dostarczone do aktorów już utworzonych w tej replice. Aktory zostaną zniszczone po zakończeniu wszelkich żądań w toku. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Zdarzenia aktywacji aktora i dezaktywacji oraz liczniki wydajności
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [aktywacją aktora i](service-fabric-reliable-actors-lifecycle.md)dezaktywacją.

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informacyjne |0x1 |Aktor został aktywowany. |
| ActorDeactivated |6 |Informacyjne |0x1 |Aktor został zdezaktywowany. |

Środowisko uruchomieniowe Reliable Actors publikuje następujące liczniki wydajności związane z aktywacją aktora i dezaktywacją.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor Service Fabric |Średnia OnActivateAsync milisekund |Czas wykonania metody OnActivateAsync w milisekundach |

### <a name="actor-request-processing-performance-counters"></a>Liczniki wydajności przetwarzania żądań aktora
Gdy klient wywołuje metodę za pośrednictwem obiektu serwera proxy aktora, powoduje wysłanie komunikatu żądania przez sieć do usługi aktora. Usługa przetwarza komunikat żądania i wysyła odpowiedź z powrotem do klienta. Środowisko uruchomieniowe Reliable Actors publikuje następujące liczniki wydajności związane z przetwarzaniem żądań aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor Service Fabric |Liczba oczekujących żądań |Liczba żądań przetwarzanych w usłudze |
| Aktor Service Fabric |Średni czas na żądanie |Czas trwania (w milisekundach) przez usługę w celu przetworzenia żądania |
| Aktor Service Fabric |Średni czas deserializacji żądania (w milisekundach) |Czas (w milisekundach) deserializacji komunikatu żądania aktora po odebraniu w usłudze |
| Aktor Service Fabric |Średni czas serializacji odpowiedzi (w milisekundach) |Czas (w milisekundach) serializacji komunikatu odpowiedzi aktora w usłudze przed wysłaniem odpowiedzi do klienta |

## <a name="next-steps"></a>Następne kroki
* [Jak Reliable Actors używać platformy Service Fabric](service-fabric-reliable-actors-platform.md)
* [Dokumentacja interfejsu API aktora](/previous-versions/azure/dn971626(v=azure.100))
* [Przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Dostawcy EventSource w narzędzia PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
