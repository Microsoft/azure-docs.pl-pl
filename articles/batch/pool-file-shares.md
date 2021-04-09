---
title: Udział plików platformy Azure dla pul Azure Batch
description: Jak zainstalować udział Azure Files z węzłów obliczeniowych w puli systemu Linux lub Windows w Azure Batch.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: e5682e7ba853973592c3a650a06ce72615cec7b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735498"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Korzystanie z udziału plików platformy Azure z pulą usługi Batch

[Azure Files](../storage/files/storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołu bloku komunikatów serwera (SMB). Ten artykuł zawiera informacje i przykłady kodu służące do instalowania i używania udziału plików platformy Azure w węzłach obliczeniowych puli.

## <a name="considerations-for-use-with-batch"></a>Zagadnienia dotyczące korzystania z usługi Batch

* Należy rozważyć użycie udziału plików platformy Azure, jeśli masz pule, które uruchamiają stosunkowo niską liczbę równoległych zadań, jeśli używasz Azure Files innej niż Premium. Przejrzyj [elementy docelowe wydajności i skalowania](../storage/files/storage-files-scale-targets.md) , aby określić, czy należy używać Azure Files (używających konta usługi Azure Storage), uwzględniając rozmiar oczekiwanej puli i liczbę plików zasobów. 

* Udziały plików platformy Azure są [opłacalne](https://azure.microsoft.com/pricing/details/storage/files/) i można je skonfigurować za pomocą replikacji danych w innym regionie, tak aby były globalnie nadmiarowe. 

* Udział plików platformy Azure można zainstalować jednocześnie na komputerze lokalnym. Należy jednak pamiętać o [konsekwencjach współbieżności](../storage/blobs/concurrency-manage.md) szczególnie w przypadku korzystania z interfejsów API REST.

* Zobacz również ogólne [zagadnienia dotyczące planowania](../storage/files/storage-files-planning.md) udziałów plików platformy Azure.


## <a name="create-a-file-share"></a>Tworzenie udziału plików

[Utwórz udział plików](../storage/files/storage-how-to-create-file-share.md) na koncie magazynu, które jest połączone z kontem w usłudze Batch, lub na osobnym koncie magazynu.

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Instalowanie udziału plików platformy Azure w puli usługi Batch

Zapoznaj się z dokumentacją dotyczącą sposobu [instalowania wirtualnego systemu plików w puli wsadowej](virtual-file-mount.md).

## <a name="next-steps"></a>Następne kroki

* Aby poznać inne opcje odczytywania i zapisywania danych w usłudze Batch, zobacz [trwałe zadanie i dane wyjściowe zadania](batch-task-output.md).
* Zobacz również zestaw narzędzi do tworzenia [pakietów wsadowych usługi Batch](https://github.com/Azure/batch-shipyard) , który obejmuje [przepisy stoczniowe](https://github.com/Azure/batch-shipyard/tree/master/recipes) do wdrażania systemów plików dla obciążeń kontenerów usługi Batch.