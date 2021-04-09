---
title: Wprowadzenie do usługi Azure Queue Storage — Azure Storage
description: Zapoznaj się z wprowadzeniem do usługi Azure Queue Storage — usługą do przechowywania dużej liczby komunikatów. Usługa Queue Storage zawiera format adresu URL, konto magazynu, kolejkę i komunikat.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8c5c97fbb72934dd99ec784ccf8e08eec059c31b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590583"
---
# <a name="what-is-azure-queue-storage"></a>Co to jest Azure Queue Storage?

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Użytkownik uzyskuje dostęp do komunikatów z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego.

## <a name="queue-storage-concepts"></a>Koncepcje Queue Storage

Queue Storage zawiera następujące składniki:

![Diagram przedstawiający relację między kontem magazynu, kolejkami i komunikatami.](./media/storage-queues-introduction/queue1.png)

- **Format adresu URL:** adresy URL kolejek mają następujący format:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Następujący adres URL dotyczy kolejki w schemacie:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Konto magazynu:** Cały dostęp do usługi Azure Storage odbywa się za pomocą konta magazynu. Aby uzyskać informacje na temat pojemności konta magazynu, zobacz [cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Kolejka:** kolejka zawiera zestaw komunikatów. Nazwa kolejki **musi** zawierać tylko małe litery. Aby uzyskać informacje na temat nazewnictwa kolejek, zobacz [nazywanie kolejek i metadanych](/rest/api/storageservices/naming-queues-and-metadata).

- **Komunikat**: komunikat w dowolnym formacie, o maksymalnym rozmiarze 64 KB. Przed wersjami 2017-07-29 maksymalny dozwolony czas wygaśnięcia wynosi siedem dni. W wersji 2017-07-29 lub nowszej maksymalny czas wygaśnięcia może być dowolną liczbą dodatnią lub-1 oznacza, że komunikat nie wygasa. Jeśli ten parametr zostanie pominięty, domyślny czas wygaśnięcia wynosi siedem dni.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie konta magazynu](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Wprowadzenie do Queue Storage przy użyciu platformy .NET](storage-dotnet-how-to-use-queues.md)
- [Wprowadzenie do Queue Storage przy użyciu języka Java](storage-java-how-to-use-queue-storage.md)
- [Wprowadzenie do Queue Storage przy użyciu języka Python](storage-python-how-to-use-queue-storage.md)
- [Wprowadzenie do Queue Storage przy użyciu Node.js](storage-nodejs-how-to-use-queues.md)
