---
title: Wprowadzenie do kolejek platformy Azure — Azure Storage
description: Zobacz Wprowadzenie do kolejek platformy Azure — usługi do przechowywania dużej liczby komunikatów. Usługa kolejki zawiera format adresu URL, konto magazynu, kolejkę i komunikat.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: cb9d25bc9449c96ec7bf5ba11f8d64d59c8ddb4d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491948"
---
# <a name="what-are-azure-queues"></a>Czym są kolejki platformy Azure?

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Użytkownik uzyskuje dostęp do komunikatów z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego.

## <a name="queue-service-concepts"></a>Koncepcje usługa kolejki

Usługa kolejki zawiera następujące składniki:

![Diagram przedstawiający relację między kontem magazynu, kolejkami i komunikatami.](./media/storage-queues-introduction/queue1.png)

- **Format adresu URL:** adresy URL kolejek mają następujący format:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Następujący adres URL dotyczy kolejki w schemacie:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Konto magazynu:** Cały dostęp do usługi Azure Storage odbywa się za pomocą konta magazynu. Aby uzyskać informacje na temat pojemności konta magazynu, zobacz [cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Kolejka:** kolejka zawiera zestaw komunikatów. Nazwa kolejki **musi** zawierać tylko małe litery. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](/rest/api/storageservices/Naming-Queues-and-Metadata) (Nazewnictwo kolejek i metadanych).

- **Komunikat**: komunikat w dowolnym formacie, o maksymalnym rozmiarze 64 KB. Przed wersjami 2017-07-29 maksymalny dozwolony czas wygaśnięcia wynosi siedem dni. W wersji 2017-07-29 lub nowszej maksymalny czas wygaśnięcia może być dowolną liczbą dodatnią lub-1 oznacza, że komunikat nie wygasa. Jeśli ten parametr zostanie pominięty, domyślny czas wygaśnięcia wynosi siedem dni.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie konta magazynu](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Wprowadzenie do kolejek przy użyciu platformy .NET](storage-dotnet-how-to-use-queues.md)
