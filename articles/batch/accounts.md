---
title: Konta usługi Batch i konta magazynu Azure
description: Dowiedz się więcej o kontach Azure Batch i sposobach ich użycia w przypadku rozwoju punktu widzenia.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 20a2a28d0eaa2c7997ea93e66d07ecb99bf297a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83791148"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Konta usługi Batch i konta magazynu Azure

Konto Azure Batch jest jednoznacznie identyfikowaną jednostką w ramach usługi Batch. Większość rozwiązań usługi Batch wykorzystuje [usługę Azure Storage](../storage/index.yml) do przechowywania plików zasobów i plików wyjściowych, dlatego każde konto usługi Batch jest zwykle skojarzone z odpowiednim kontem magazynu.

## <a name="batch-accounts"></a>Konta usługi Batch

Wszystkie procesy przetwarzania i zasobów są skojarzone z kontem wsadowym. Gdy aplikacja wykonuje żądanie względem usługi Batch, usługa ta uwierzytelnia żądanie przy użyciu nazwy konta usługi Batch, adresu URL konta oraz klucza dostępu albo tokenu usługi Azure Active Directory.

Można uruchomić wiele obciążeń wsadowych na jednym koncie w usłudze Batch. Możesz również dystrybuować obciążenia między kontami usługi Batch, które znajdują się w tej samej subskrypcji, ale znajdują się w różnych regionach platformy Azure.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

Konto usługi Batch można utworzyć przy użyciu [Azure Portal](batch-account-create-portal.md) lub programowo, na przykład w przypadku [biblioteki zarządzania usługą Batch dla platformy .NET](batch-management-dotnet.md). Podczas tworzenia konta możesz skojarzyć konto magazynu platformy Azure w celu przechowywania związanych z pracą danych wejściowych i wyjściowych lub aplikacji.

## <a name="azure-storage-accounts"></a>Konta usługi Azure Storage

Większość rozwiązań partii usługi Batch używa usługi Azure Storage do przechowywania plików zasobów i plików wyjściowych. Na przykład zadania podrzędne usługi Batch (w tym standardowe, uruchamiania oraz przygotowania i zwolnienia zadań) muszą określać pliki zasobów, które znajdują się na koncie magazynu. Konta magazynu również przechowują przetwarzane dane i wszystkie generowane dane wyjściowe.

Usługa Batch obsługuje następujące typy kont usługi Azure Storage:

- Konta ogólnego przeznaczenia, wersja 2 (GPv2)
- Konta ogólnego przeznaczenia, wersja 1 (GPv1)
- Konta magazynu obiektów blob (aktualnie obsługiwane w przypadku pul w konfiguracji maszyny wirtualnej)

Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](../storage/common/storage-account-overview.md).

Konto magazynu można skojarzyć z kontem usługi Batch podczas tworzenia konta usługi Batch lub później. Wybierając konto magazynu, należy wziąć pod uwagę wymagania dotyczące kosztów i wydajności. Na przykład opcje konta magazynu GPv2 i Blob Storage obsługują większe [limity wydajności i skalowalności](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) w porównaniu z wersją GPv1. (Skontaktuj się z pomocą techniczną platformy Azure, aby zażądać zwiększenia limitu magazynu). Te opcje konta mogą zwiększyć wydajność rozwiązań usługi Batch, które zawierają dużą liczbę zadań równoległych, które odczytują lub zapisują dane na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

- Poznaj [węzły i pule](nodes-and-pools.md).
- Dowiedz się, jak utworzyć konto usługi Batch przy użyciu [Azure Portal](batch-account-create-portal.md).
