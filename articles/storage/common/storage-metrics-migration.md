---
title: Migracja metryk usługi Azure Storage | Microsoft Docs
description: Dowiedz się, jak przeprowadzić migrację starych metryk do nowych metryk, które są zarządzane przez Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 10768ca4c6fbe4afc322fa9a7045c7cc4fe6f175
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83681308"
---
# <a name="azure-storage-metrics-migration"></a>Migracja metryk usługi Azure Storage

Zgodnie z strategią ujednolicenia środowiska monitorowania na platformie Azure usługa Azure Storage integruje metryki z platformą Azure Monitor. W przyszłości usługa starych metryk będzie kończyć wczesne powiadomienia na podstawie Azure Policy. Jeśli korzystasz ze starych metryk magazynu, musisz przeprowadzić migrację przed datą zakończenia usługi, aby zachować informacje o metrykach.

W tym artykule pokazano, jak przeprowadzić migrację ze starych metryk do nowych metryk.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Informacje o starych metrykach zarządzanych przez usługę Azure Storage

Usługa Azure Storage zbiera stare wartości metryk i agreguje je i zapisuje w tabelach $Metric w ramach tego samego konta magazynu. Możesz użyć Azure Portal, aby skonfigurować wykres monitorowania. Korzystając z zestawów SDK usługi Azure Storage, można również odczytywać dane z tabel $Metric, które są oparte na schemacie. Aby uzyskać więcej informacji, zobacz [analityka magazynu](./storage-analytics.md).

Stare metryki zapewniają metryki pojemności tylko w usłudze Azure Blob Storage. Stare metryki zapewniają metryki transakcji na serwerze BLOB Storage, Table Storage, Azure Files i queue storage.

Stare metryki są zaprojektowane w schemacie płaskim. W projekcie powstaje wartość zero metryk, gdy nie masz wzorców ruchu wyzwalających metrykę. Na przykład wartość **ServerTimeoutError** jest ustawiona na 0 w tabelach $Metric, nawet jeśli nie otrzymasz żadnych błędów limitu czasu serwera z ruchu na żywo do konta magazynu.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Informacje o nowych metrykach zarządzanych przez usługę Azure Monitor

W przypadku nowych metryk magazynu usługa Azure Storage emituje dane metryk do zaplecza Azure Monitor. Azure Monitor zapewnia ujednolicone środowisko monitorowania, w tym dane z portalu oraz pozyskiwanie danych. Więcej szczegółów można znaleźć w tym [artykule](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nowe metryki zapewniają metryki wydajności i metryki transakcji dla obiektów blob, tabel, plików, kolejek i Premium Storage.

Wiele wymiarów to jedna z funkcji udostępnianych przez Azure Monitor. Usługa Azure Storage przyjmuje projekt w celu zdefiniowania nowego schematu metryki. Aby uzyskać informacje o obsługiwanych wymiarach w metrykach, można znaleźć szczegóły w temacie [metryki usługi Azure Storage w Azure monitor](./storage-metrics-in-azure-monitor.md). Projekt z obsługą wielowymiarowego zapewnia wydajność kosztów na podstawie pozyskiwania i zdolności do przechowywania metryk. W związku z tym, jeśli ruch nie wyzwolił powiązanych metryk, powiązane dane metryk nie zostaną wygenerowane. Na przykład, jeśli ruch nie wywołał żadnych błędów przekroczenia limitu czasu serwera, Azure Monitor nie zwróci żadnych danych podczas wykonywania zapytania o wartość **transakcji** metryki z atrybutem **responsetype** równym **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapowanie metryk między starymi i nowymi metrykami

Jeśli dane metryk są odczytywane programowo, należy zastosować nowy schemat metryki w swoich programach. Aby lepiej zrozumieć zmiany, można odwołać się do mapowania wymienionego w poniższej tabeli:

**Metryki pojemności**

| Stara Metryka | Nowa Metryka |
| ------------------- | ----------------- |
| **Pojemność**            | **BlobCapacity** z wymiarem **blobtype** równym **BlockBlob** lub **PageBlob** |
| **ObjectCount**        | **BlobCount** z wymiarem **blobtype** równym **BlockBlob** lub **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Następujące metryki to nowe oferty, które nie obsługują starych metryk:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Metryki transakcji**

| Stara Metryka | Nowa Metryka |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transakcje z atrybutem **responsetype** równym **AuthorizationError** i **uwierzytelnianiem** wymiaru równe **anonimowi** |
| **AnonymousClientOtherError** | Transakcje z atrybutem **responsetype** równym **ClientOtherError** i **uwierzytelnianiem** wymiaru równe **anonimowi** |
| **AnonymousClientTimeoutError** | Transakcje z atrybutem **responsetype** równym **ClientTimeoutError** i **uwierzytelnianiem** wymiaru równe **anonimowi** |
| **AnonymousNetworkError** | Transakcje z atrybutem **responsetype** równym **NetworkError** i **uwierzytelnianiem** wymiaru równe **anonimowi** |
| **AnonymousServerOtherError** | Transakcje z atrybutem **responsetype** równym **ServerOtherError** i **uwierzytelnianiem** wymiaru równe **anonimowi** |
| **AnonymousServerTimeoutError** | Transakcje z atrybutem **responsetype** równym **ServerTimeoutError** i **uwierzytelnianiem** wymiaru równe **anonimowi** |
| **AnonymousSuccess** | Transakcje z identyfikatorem **odpowiedzi** wymiaru równym **powodzeniu** i **uwierzytelnianiu** wymiarów równe **anonimowi** |
| **AnonymousThrottlingError** | Transakcje z atrybutem **responsetype** równym **ClientThrottlingError** lub **ServerBusyError** i **uwierzytelnianiem** wymiaru równe **anonimowi** |
| **AuthorizationError** | Transakcje o wymiarze **responsetype** równym **AuthorizationError** |
| **Dostępność** | **Dostępność** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transakcje o wymiarze **responsetype** równym **ClientOtherError** |
| **ClientTimeoutError** | Transakcje o wymiarze **responsetype** równym **ClientTimeoutError** |
| **NetworkError** | Transakcje o wymiarze **responsetype** równym **NetworkError** |
| **PercentAuthorizationError** | Transakcje o wymiarze **responsetype** równym **AuthorizationError** |
| **PercentClientOtherError** | Transakcje o wymiarze **responsetype** równym **ClientOtherError** |
| **Wzrost percentnetworkerror** | Transakcje o wymiarze **responsetype** równym **NetworkError** |
| **PercentServerOtherError** | Transakcje o wymiarze **responsetype** równym **ServerOtherError** |
| **PercentSuccess** | Transakcje z identyfikatorem **odpowiedzi** wymiaru równym **powodzeniu** |
| **Wzrost percentthrottlingerror** | Transakcje z atrybutem **responsetype** równym **ClientThrottlingError** lub **ServerBusyError** |
| **Wzrost percenttimeouterror** | Transakcje o wymiarze **responsetype** równym **ServerTimeoutError** lub **responsetype** równym **ClientTimeoutError** |
| **SASAuthorizationError** | Transakcje z atrybutem **responsetype** równym **AuthorizationError** i **uwierzytelnianiem** wymiarów równym **sygnaturze SAS** |
| **SASClientOtherError** | Transakcje z atrybutem **responsetype** równym **ClientOtherError** i **uwierzytelnianiem** wymiarów równym **sygnaturze SAS** |
| **SASClientTimeoutError** | Transakcje z atrybutem **responsetype** równym **ClientTimeoutError** i **uwierzytelnianiem** wymiarów równym **sygnaturze SAS** |
| **SASNetworkError** | Transakcje z atrybutem **responsetype** równym **NetworkError** i **uwierzytelnianiem** wymiarów równym **sygnaturze SAS** |
| **SASServerOtherError** | Transakcje z atrybutem **responsetype** równym **ServerOtherError** i **uwierzytelnianiem** wymiarów równym **sygnaturze SAS** |
| **SASServerTimeoutError** | Transakcje z atrybutem **responsetype** równym **ServerTimeoutError** i **uwierzytelnianiem** wymiarów równym **sygnaturze SAS** |
| **SASSuccess** | Transakcje z identyfikatorem **odpowiedź** wymiarutype równe **sukcesowi** i **uwierzytelnianiu** wymiarów równym **sygnaturze SAS** |
| **SASThrottlingError** | Transakcje z atrybutem **responsetype** równym **ClientThrottlingError** lub **ServerBusyError** i **uwierzytelnianiem** w wymiarze równym **sygnaturze SAS** |
| **ServerOtherError** | Transakcje o wymiarze **responsetype** równym **ServerOtherError** |
| **ServerTimeoutError** | Transakcje o wymiarze **responsetype** równym **ServerTimeoutError** |
| **Prawnego** | Transakcje z identyfikatorem **odpowiedzi** wymiaru równym **powodzeniu** |
| **ThrottlingError** | **Transakcje** z atrybutem **responsetype** równym **ClientThrottlingError** lub **ServerBusyError**|
| **TotalBillableRequests** | **Transakcje** |
| **TotalEgress** | **Ruch wychodzący** |
| **TotalIngress** | **Ruch przychodzący** |
| **TotalRequests** | **Transakcje** |

## <a name="faq"></a>Często zadawane pytania

### <a name="how-should-i-migrate-existing-alert-rules"></a>Jak należy migrować istniejące reguły alertów?

Jeśli utworzono klasyczne reguły alertów na podstawie starych metryk magazynu, należy utworzyć nowe reguły alertów na podstawie nowego schematu metryki.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Czy nowe dane metryk są przechowywane domyślnie na tym samym koncie magazynu?

Nie. Aby zarchiwizować dane metryki na koncie magazynu, użyj [interfejsu API Azure monitor ustawień diagnostycznych](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Następne kroki

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metryki magazynu w Azure Monitor](./storage-metrics-in-azure-monitor.md)
