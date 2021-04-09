---
title: Diagnostyka i monitorowanie usługi Azure servicefabric
description: W tym artykule opisano funkcje monitorowania wydajności w Service Fabric niezawodnego środowiska uruchomieniowego servicekomunikacja zdalnego, takie jak liczniki wydajności emitowane przez ten program.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 9c7d466d6e8fd36b4445966b92ee753becf96c64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791765"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostyka i monitorowanie wydajności dla niezawodnej komunikacji zdalnej usługi
Niezawodne środowisko uruchomieniowe servicekomunikacji zdalnej nie emituje  [liczników wydajności](/dotnet/api/system.diagnostics.performancecounter). Umożliwiają one wgląd w działanie usługi servicekomunikacja zdalna i rozwiązywanie problemów i monitorowanie wydajności.


## <a name="performance-counters"></a>Liczniki wydajności
Niezawodne środowisko uruchomieniowe servicekomunikacji zdalnej definiuje następujące kategorie liczników wydajności:

| Kategoria | Opis |
| --- | --- |
| Usługa Service Fabric |Liczniki specyficzne dla komunikacji zdalnej usługi Azure Service Fabric, na przykład średni czas przetwarzania żądania |
| Service Fabric metoda usługi |Liczniki specyficzne dla metod implementowanych przez usługę komunikacji zdalnej Service Fabric, na przykład częstotliwość wywoływania metody usługi |

Każda z powyższych kategorii ma jeden lub więcej liczników.

Aplikacja [Monitor wydajności systemu Windows](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) , która jest dostępna domyślnie w systemie operacyjnym Windows, może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) jest kolejną opcją do zbierania danych licznika wydajności i przekazywania ich do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień liczników wydajności
Klaster, który ma dużą liczbę usług zdalnej usługi lub partycji, ma dużą liczbę wystąpień liczników wydajności. Nazwy wystąpień licznika wydajności mogą pomóc w zidentyfikowaniu konkretnej metody partycji i usługi (jeśli dotyczy), z którą skojarzone jest wystąpienie licznika wydajności.

#### <a name="service-fabric-service-category"></a>Kategoria usługi Service Fabric
W przypadku kategorii `Service Fabric Service` nazwy wystąpień liczników mają następujący format:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* to ciąg reprezentujący identyfikator partycji Service Fabric, z którym jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentacja w postaci ciągu jest generowana za pomocą [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) metody ze specyfikatorem formatu "D".

*ServiceReplicaOrInstanceId* to ciąg reprezentujący identyfikator Service Fabric repliki/wystąpienia, z którym jest skojarzone wystąpienie licznika wydajności.

*ServiceRuntimeInternalID* to ciąg reprezentujący 64-bitową liczbę całkowitą wygenerowaną przez środowisko uruchomieniowe usługi sieci szkieletowej do użytku wewnętrznego. Ta wartość jest uwzględniona w nazwie wystąpienia licznika wydajności, aby zapewnić jej unikatowość i uniknąć konfliktu z innymi nazwami wystąpień liczników wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej znajduje się przykład nazwy wystąpienia licznika dla licznika, który należy do `Service Fabric Service` kategorii:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

W poprzednim przykładzie, `2740af29-78aa-44bc-a20b-7e60fb783264` jest reprezentacją ciągu Service Fabric identyfikator partycji, `635650083799324046` jest reprezentacją typu replika/InstanceId `5008379932` i jest 64-bitowym identyfikatorem, który jest generowany dla wewnętrznego użycia środowiska uruchomieniowego.

#### <a name="service-fabric-service-method-category"></a>Kategoria metody usługi Service Fabric
W przypadku kategorii `Service Fabric Service Method` nazwy wystąpień liczników mają następujący format:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* to nazwa metody usługi, z którą jest skojarzone wystąpienie licznika wydajności. Format nazwy metody jest określany na podstawie pewnej logiki w środowisku uruchomieniowym usługi sieci szkieletowej, która równoważy czytelność nazwy z ograniczeniami dotyczącymi maksymalnej długości nazw wystąpień liczników wydajności w systemie Windows.

*ServiceRuntimeMethodId* to ciąg reprezentujący 32-bitową liczbę całkowitą wygenerowaną przez środowisko uruchomieniowe usługi sieci szkieletowej do użytku wewnętrznego. Ta wartość jest uwzględniona w nazwie wystąpienia licznika wydajności, aby zapewnić jej unikatowość i uniknąć konfliktu z innymi nazwami wystąpień liczników wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

*ServiceFabricPartitionID* to ciąg reprezentujący identyfikator partycji Service Fabric, z którym jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentacja w postaci ciągu jest generowana za pomocą [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) metody ze specyfikatorem formatu "D".

*ServiceReplicaOrInstanceId* to ciąg reprezentujący identyfikator Service Fabric repliki/wystąpienia, z którym jest skojarzone wystąpienie licznika wydajności.

*ServiceRuntimeInternalID* to ciąg reprezentujący 64-bitową liczbę całkowitą wygenerowaną przez środowisko uruchomieniowe usługi sieci szkieletowej do użytku wewnętrznego. Ta wartość jest uwzględniona w nazwie wystąpienia licznika wydajności, aby zapewnić jej unikatowość i uniknąć konfliktu z innymi nazwami wystąpień liczników wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej znajduje się przykład nazwy wystąpienia licznika dla licznika, który należy do `Service Fabric Service Method` kategorii:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

W poprzednim przykładzie, `ivoicemailboxservice.leavemessageasync` jest nazwą metody, `2` jest 32-bitowy identyfikator wygenerowany dla wewnętrznego użytku środowiska uruchomieniowego, `89383d32-e57e-4a9b-a6ad-57c6792aa521` to ciąg reprezentujący identyfikator partycji Service Fabric, `635650083804480486` jest reprezentacją ciągu Service Fabric repliki/identyfikatora wystąpienia i `5008380` jest identyfikatorem 64-bitowym wygenerowanym dla wewnętrznego użycia środowiska uruchomieniowego.

## <a name="list-of-performance-counters"></a>Lista liczników wydajności
### <a name="service-method-performance-counters"></a>Liczniki wydajności metody usługi

Środowisko uruchomieniowe niezawodnej usługi publikuje następujące liczniki wydajności związane z wykonywaniem metod usługi.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Service Fabric metoda usługi |Wywołania/s |Liczba wywoływanych metod usługi na sekundę |
| Service Fabric metoda usługi |Średni czas dla wywołania |Czas wykonania metody usługi w milisekundach |
| Service Fabric metoda usługi |Zgłoszone wyjątki/s |Ile razy metoda usługi zgłosiła wyjątek na sekundę |

### <a name="service-request-processing-performance-counters"></a>Liczniki wydajności przetwarzania żądań obsługi
Gdy klient wywołuje metodę za pośrednictwem obiektu serwera proxy usługi, powoduje wysłanie komunikatu żądania przez sieć do usługi zdalnej. Usługa przetwarza komunikat żądania i wysyła odpowiedź z powrotem do klienta. Niezawodne środowisko uruchomieniowe servicekomunikacji zdalnej publikuje następujące liczniki wydajności związane z przetwarzaniem żądań obsługi.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Usługa Service Fabric |Liczba oczekujących żądań |Liczba żądań przetwarzanych w usłudze |
| Usługa Service Fabric |Średni czas na żądanie |Czas trwania (w milisekundach) przez usługę w celu przetworzenia żądania |
| Usługa Service Fabric |Średni czas deserializacji żądania (w milisekundach) |Czas (w milisekundach) deserializacji komunikatu żądania obsługi po odebraniu w usłudze |
| Usługa Service Fabric |Średni czas serializacji odpowiedzi (w milisekundach) |Czas (w milisekundach) serializacji komunikatu odpowiedzi usługi w usłudze przed wysłaniem odpowiedzi do klienta |

## <a name="next-steps"></a>Następne kroki
* [Przykładowy kod](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [Dostawcy EventSource w narzędzia PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
