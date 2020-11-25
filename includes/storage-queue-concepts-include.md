---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 08/21/2020
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 45df30f4f1444b6148af9f3c7d47b94909ccef3d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026172"
---
## <a name="what-is-queue-storage"></a>Co to jest magazyn kolejek?

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu. Magazyn kolejek jest często używany do tworzenia zaległości prac do przetwarzania asynchronicznego.

## <a name="queue-service-concepts"></a>Koncepcje usługa kolejki

Usługa kolejki platformy Azure zawiera następujące składniki:

![Składniki usługi Azure usługa kolejki](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Konto magazynu:** cały dostęp do usługi Azure Storage odbywa się przez konto magazynu. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta magazynu](../articles/storage/common/storage-account-overview.md).
* **Kolejka:** kolejka zawiera zestaw komunikatów. Wszystkie komunikaty muszą być w kolejce. Pamiętaj, że nazwa kolejki może zawierać tylko małe litery. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](/rest/api/storageservices/Naming-Queues-and-Metadata) (Nazewnictwo kolejek i metadanych).
* **Komunikat**: komunikat w dowolnym formacie, o maksymalnym rozmiarze 64 KB. Maksymalny czas pozostawania komunikatu w kolejce wynosi 7 dni. W wersji 2017-07-29 lub nowszej maksymalny czas wygaśnięcia może być dowolną liczbą dodatnią lub-1 oznacza, że komunikat nie wygasa. Jeśli ten parametr zostanie pominięty, domyślny czas wygaśnięcia wynosi siedem dni.
* **Format adresu URL:** Kolejki są adresowane przy użyciu następującego formatu adresu URL: http:// `<storage account>` . Queue.Core.Windows.NET/`<queue>`

    Następujący adres URL dotyczy kolejki w schemacie:

    `http://myaccount.queue.core.windows.net/incoming-orders`