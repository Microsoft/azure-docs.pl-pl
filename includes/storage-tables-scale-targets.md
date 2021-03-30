---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78941807"
---
W poniższej tabeli opisano elementy docelowe pojemności, skalowalności i wydajności dla usługi Table Storage.

| Zasób | Cel |
|----------|---------------|
| Liczba tabel na koncie usługi Azure Storage | Ograniczone tylko pojemności konta magazynu |
| Liczba partycji w tabeli | Ograniczone tylko pojemności konta magazynu |
| Liczba jednostek w partycji | Ograniczone tylko pojemności konta magazynu |
| Maksymalny rozmiar pojedynczej tabeli | 500 TiB |
| Maksymalny rozmiar pojedynczej jednostki, w tym wszystkie wartości właściwości | 1 MiB |
| Maksymalna liczba właściwości w jednostce tabeli | 255 (w tym trzy właściwości systemu, **PartitionKey**, **RowKey** i **sygnatura czasowa**) |
| Maksymalny łączny rozmiar pojedynczej właściwości w jednostce | Różni się w zależności od typu właściwości. Aby uzyskać więcej informacji, zobacz **typy właściwości** w temacie [Omówienie modelu danych usługi Table Service](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Rozmiar **PartitionKey** | Ciąg o rozmiarze do 1 KiB |
| Rozmiar **RowKey** | Ciąg o rozmiarze do 1 KiB |
| Rozmiar transakcji grupy jednostek | Transakcja może obejmować maksymalnie 100 jednostek, a ładunek musi być krótszy niż 4 MiB. Transakcja grupy jednostek może zawierać aktualizację do jednostki tylko raz. |
| Maksymalna liczba przechowywanych zasad dostępu na tabelę | 5 |
| Maksymalna liczba żądań na konto magazynu | 20 000 transakcji na sekundę, które zakładają, że rozmiar jednostki wynosi 1 KiB |
| Docelowa przepływność dla pojedynczej partycji tabeli (1 KiB-Entities) | Do 2 000 jednostek na sekundę |