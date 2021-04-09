---
title: Dane wyjściowe magazynu tabel z Azure Stream Analytics
description: W tym artykule opisano usługę Azure Table Storage jako dane wyjściowe dla Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 558ddf065d33a552034c5b129ea70bc144c494ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013893"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Dane wyjściowe magazynu tabel z Azure Stream Analytics

[Usługa Azure Table Storage](../storage/common/storage-introduction.md) oferuje wysoką dostępność i skalowalność magazynu, dzięki czemu aplikacja może automatycznie skalować się w celu spełnienia wymagań użytkownika. Magazyn tabel to Magazyn NoSQL Key/Attribute firmy Microsoft, którego można użyć dla danych ze strukturą z mniejszą liczbą ograniczeń w schemacie. Usługa Azure Table Storage może służyć do przechowywania danych w celu zapewnienia trwałości i wydajnego pobierania.

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia danych wyjściowych tabeli.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego magazynu tabel. |
| Konto magazynu |Nazwa konta magazynu, do którego wysyłane są dane wyjściowe. |
| Klucz konta magazynu |Klucz dostępu skojarzony z kontem magazynu. |
| Nazwa tabeli |Nazwa tabeli. Tabela zostanie utworzona, jeśli nie istnieje. |
| Klucz partycji |Nazwa kolumny wyjściowej zawierającej klucz partycji. Klucz partycji jest unikatowym identyfikatorem partycji w tabeli, która stanowi pierwszą część klucza podstawowego jednostki. Jest to wartość ciągu, która może mieć długość do 1 KB. |
| Klucz wiersza |Nazwa kolumny wyjściowej zawierającej klucz wiersza. Klucz wiersza jest unikatowym identyfikatorem dla jednostki w ramach partycji. Stanowi on drugą część klucza podstawowego jednostki. Klucz wiersza jest wartością ciągu, która może mieć długość do 1 KB. |
| Rozmiar partii |Liczba rekordów dla operacji wsadowych. Wartość domyślna (100) jest wystarczająca dla większości zadań. Aby uzyskać więcej informacji na temat modyfikowania tego ustawienia, zobacz [specyfikację operacji wsadowej tabeli](/java/api/com.microsoft.azure.storage.table.tablebatchoperation) . |

## <a name="partitioning"></a>Partycjonowanie

Klucz partycji to dowolna kolumna wyjściowa. Liczba składników zapisywania danych wyjściowych następuje po partycjonowaniu danych wejściowych dla w [pełni równoległych zapytań](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

Maksymalny rozmiar komunikatu znajduje się w temacie [limity usługi Azure Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Wartość domyślna to 100 jednostek na pojedynczą transakcję, ale w razie potrzeby można ją skonfigurować na mniejszą wartość.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-azure-cli.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu szablonu ARM](quick-create-azure-resource-manager.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics w programie Visual Studio Code](quick-create-visual-studio-code.md)