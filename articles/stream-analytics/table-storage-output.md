---
title: Dane wyjściowe magazynu tabel z Azure Stream Analytics
description: W tym artykule opisano usługę Azure Table Storage jako dane wyjściowe dla Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 3e5b747e55c8f246167bcf8bbde3542146e69706
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891953"
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
| Rozmiar partii |Liczba rekordów dla operacji wsadowych. Wartość domyślna (100) jest wystarczająca dla większości zadań. Aby uzyskać więcej informacji na temat modyfikowania tego ustawienia, zobacz [specyfikację operacji wsadowej tabeli](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) . |

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
