---
title: Reagowanie na zdarzenia usługi Azure Blob Storage | Microsoft Docs
description: Użyj Azure Event Grid, aby subskrybować zdarzenia usługi BLOB Storage i reagować na nie. Zrozumienie modelu zdarzeń, zdarzeń filtrowania i praktyk związanych z zużywaniem zdarzeń.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: dineshm
ms.openlocfilehash: f07c249e3b7cb54283959df410d51ca18998f2cf
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181520"
---
# <a name="reacting-to-blob-storage-events"></a>Reagowanie na zdarzenia usługi Blob Storage

Zdarzenia usługi Azure Storage umożliwiają aplikacjom reagowanie na zdarzenia, takie jak tworzenie i usuwanie obiektów BLOB. Robi to bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. W najlepszej części płacisz tylko za to, czego używasz.

Zdarzenia usługi BLOB Storage są wypychane przy użyciu [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak Azure Functions, Azure Logic Apps lub nawet do własnego odbiornika http. Event Grid zapewnia niezawodne dostarczanie zdarzeń do aplikacji przez rozbudowane zasady ponawiania prób i utraconych wiadomości.

Zobacz artykuł [schemat zdarzeń magazynu obiektów BLOB](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) , aby wyświetlić pełną listę zdarzeń obsługiwanych przez magazyn obiektów BLOB.

Typowe scenariusze zdarzeń magazynu obiektów BLOB obejmują przetwarzanie obrazów lub wideo, indeksowanie wyszukiwania lub wszelkie przepływy pracy zorientowane na pliki. Asynchroniczne przekazywanie plików to doskonałe dopasowanie do zdarzeń. Gdy zmiany są rzadko wykonywane, ale scenariusz wymaga natychmiastowej reakcji, Architektura oparta na zdarzeniach może być szczególnie wydajna.

Jeśli chcesz wypróbować zdarzenia magazynu obiektów blob, zapoznaj się z dowolnym z tych artykułów szybkiego startu:

|Jeśli chcesz użyć tego narzędzia:    |Zobacz ten artykuł: |
|--|-|
|Azure Portal    |[Szybki Start: kierowanie zdarzeń magazynu obiektów BLOB do punktu końcowego sieci Web za pomocą Azure Portal](../../event-grid/blob-event-quickstart-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Szybki Start: kierowanie zdarzeń magazynu do punktu końcowego w sieci Web przy użyciu programu PowerShell](./storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Interfejs wiersza polecenia platformy Azure    |[Szybki Start: kierowanie zdarzeń magazynu do punktu końcowego sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure](./storage-blob-event-quickstart.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Aby zapoznać się z szczegółowymi przykładami odwołującymi się do zdarzeń magazynu obiektów BLOB za pomocą usługi Azure Functions, zobacz następujące artykuły:

- [Samouczek: Użyj zdarzeń Azure Data Lake Storage Gen2, aby zaktualizować tabelę różnicową](data-lake-storage-events.md)danych.
- [Samouczek: Automatyzowanie zmiany rozmiarów załadowanych obrazów przy użyciu Event Grid](../../event-grid/resize-images-on-storage-blob-upload-event.md?tabs=dotnet)

>[!NOTE]
> Tylko konta magazynu typu **StorageV2 (ogólnego przeznaczenia w wersji 2)**, **BlockBlobStorage** i **BlobStorage** obsługują integrację zdarzeń. **Magazyn (ogólnego przeznaczenia w wersji 1)** *nie obsługuje integracji* z programem Event Grid.

## <a name="the-event-model"></a>Model zdarzenia

Event Grid używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Ten obraz przedstawia relację między wydawcami zdarzeń, subskrypcjami zdarzeń i programami obsługi zdarzeń.

![Model Event Grid](./media/storage-blob-event-overview/event-grid-functional-model.png)

Najpierw Zasubskrybuj punkt końcowy do zdarzenia. Następnie po wyzwoleniu zdarzenia usługa Event Grid wyśle dane dotyczące tego zdarzenia do punktu końcowego.

Zobacz artykuł [schemat zdarzeń magazynu obiektów BLOB](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) , aby wyświetlić:

> [!div class="checklist"]
> * Pełna lista zdarzeń magazynu obiektów blob oraz sposób wyzwalania poszczególnych zdarzeń.
> * Przykład danych wysyłanych przez Event Grid dla każdego z tych zdarzeń.
> * Przeznaczenie poszczególnych par klucz-wartość, które pojawiają się w danych.

## <a name="filtering-events"></a>Filtrowanie zdarzeń

Zdarzenia obiektów BLOB [można filtrować](/cli/azure/eventgrid/event-subscription) według typu zdarzenia, nazwy kontenera lub nazwy obiektu, który został utworzony/usunięty. Filtry w Event Grid są zgodne z początkiem lub końcem podmiotu, dlatego zdarzenia z odpowiednim podmiotem przejdą do subskrybenta.

Aby dowiedzieć się więcej na temat stosowania filtrów, zobacz [filtrowanie zdarzeń dla Event Grid](../../event-grid/how-to-filter-events.md).

W temacie zdarzeń magazynu obiektów BLOB jest stosowany format:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Aby dopasować wszystkie zdarzenia dla konta magazynu, można pozostawić puste filtry podmiotu.

Aby dopasować zdarzenia z obiektów BLOB utworzonych w zestawie kontenerów, które współużytkują prefiks, należy użyć `subjectBeginsWith` filtru, takiego jak:

```
/blobServices/default/containers/containerprefix
```

Aby dopasować zdarzenia z obiektów BLOB utworzonych w określonym kontenerze, użyj `subjectBeginsWith` filtru, takiego jak:

```
/blobServices/default/containers/containername/
```

Aby dopasować zdarzenia z obiektów BLOB utworzonych w określonym kontenerze, udostępniając prefiks nazwy obiektu BLOB, użyj `subjectBeginsWith` filtru, takiego jak:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Aby dopasować zdarzenia z obiektów BLOB utworzonych w określonym kontenerze, udostępniając sufiks obiektu BLOB, użyj `subjectEndsWith` filtru, takiego jak ". log" lub ". jpg". Aby uzyskać więcej informacji, zobacz temat [Event Grid pojęcia](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Praktyki związane z zużywaniem zdarzeń

Aplikacje, które obsługują zdarzenia magazynu obiektów blob, powinny spełniać kilka zalecanych praktyk:
> [!div class="checklist"]
> * Ponieważ można skonfigurować wiele subskrypcji do kierowania zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, aby nie przyjmować zdarzeń z określonego źródła, ale w celu sprawdzenia, czy pochodzi ona z konta magazynu, którego oczekujesz.
> * Podobnie Sprawdź, czy typ zdarzenia jest przygotowana do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwanymi typami.
> * Ponieważ komunikaty mogą trafiać po pewnym opóźnieniu, Użyj pól ETag, aby zrozumieć, czy informacje o obiektach są nadal aktualne. Aby dowiedzieć się, jak używać pola ETag, zobacz [Zarządzanie współbieżnością w magazynie obiektów BLOB](./concurrency-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage).
> * Ponieważ komunikaty mogą się pojawiać poza kolejnością, Użyj pól programu Sequencer do zrozumienia kolejności zdarzeń dla każdego określonego obiektu. Pole Sequencer jest wartością ciągu reprezentującą logiczną sekwencję zdarzeń dla każdej konkretnej nazwy obiektu BLOB. Można użyć standardowego porównania ciągów, aby zrozumieć względną sekwencję dwóch zdarzeń dla tej samej nazwy obiektu BLOB.
> * Zdarzenia magazynu gwarantują co najmniej jednokrotne dostarczanie do subskrybentów, co gwarantuje, że wszystkie komunikaty są zwracane. Jednak ze względu na ponowną próbę między węzłami zaplecza a usługami lub dostępnością subskrypcji mogą wystąpić duplikaty komunikatów. Aby dowiedzieć się więcej na temat dostarczania komunikatów i ponawiania prób, zobacz [Event Grid dostarczania komunikatów i ponów próbę](../../event-grid/delivery-and-retry.md).
> * Użyj pola blobtype, aby zrozumieć, jaki typ operacji jest dozwolony dla obiektu BLOB, a także typy bibliotek klientów, które powinny być używane w celu uzyskania dostępu do obiektu BLOB. Prawidłowe wartości to `BlockBlob` lub `PageBlob` . 
> * Użyj pola adresu URL z `CloudBlockBlob` `CloudAppendBlob` konstruktorami i, aby uzyskać dostęp do obiektu BLOB.
> * Ignoruj pola, które nie są zrozumiałe. Ta metoda pomaga w zachowaniu odporności na nowe funkcje, które mogą zostać dodane w przyszłości.
> * Jeśli chcesz się upewnić, że zdarzenie **Microsoft. Storage. BlobCreated** jest wyzwalane tylko wtedy, gdy blokowy obiekt BLOB jest całkowicie zatwierdzony, przefiltruj zdarzenie dla `CopyBlob` `PutBlob` `PutBlockList` `FlushWithClose` wywołań interfejsu API, lub protokołu REST. Te wywołania interfejsu API wyzwalają zdarzenie **Microsoft. Storage. BlobCreated** tylko wtedy, gdy dane są w pełni zatwierdzone do blokowego obiektu BLOB. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń dla Event Grid](../../event-grid/how-to-filter-events.md).


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i nadaj zdarzeniom magazynu obiektów blob:

- [Event Grid — informacje](../../event-grid/overview.md)
- [Schemat zdarzeń magazynu obiektów BLOB](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Kierowanie zdarzeń magazynu obiektów BLOB do niestandardowego internetowego punktu końcowego](storage-blob-event-quickstart.md)
