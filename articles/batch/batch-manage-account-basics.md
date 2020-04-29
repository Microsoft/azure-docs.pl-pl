---
title: Zarządzanie kontem
description: Dowiedz się, co obejmuje konto Azure Batch
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce0c612e3434249c0c7d078949b10e0b9f6b1f10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116013"
---
# <a name="manage-your-batch-account"></a>Zarządzaj kontem w usłudze Batch

Konto usługi Batch jest jednoznacznie zdefiniowanym obiektem w ramach usługi Batch. Całe przetwarzanie jest skojarzone z kontem usługi Batch.

Konto usługi Azure Batch możesz utworzyć za pomocą witryny [Azure Portal](batch-account-create-portal.md) lub programowo, na przykład za pomocą [biblioteki Batch Management .NET](batch-management-dotnet.md). Podczas tworzenia konta możesz skojarzyć konto magazynu platformy Azure w celu przechowywania związanych z pracą danych wejściowych i wyjściowych lub aplikacji.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozdzielić obciążenia pomiędzy konta tej usługi znajdujące się w jednej subskrypcji, ale różnych regionach świadczenia usługi Azure.

## <a name="components-of-the-batch-account"></a>Składniki konta wsadowego

Konto usługi Batch umożliwia wydajne Uruchamianie zadań wsadowych o dużej skali równoległych i o wysokiej wydajności (HPC) na platformie Azure. Na zarządzanym koncie:

- Aplikacje, z których korzystasz

- Alokacja pul i węzłów w pulach

- Liczba i typy zadań 

- Dane wejściowe i wyjściowe. Nie musisz instalować dodatkowego oprogramowania, aby zarządzać zadaniami.

- Po utworzeniu konta w usłudze Batch zostanie wyświetlony monit o przypisanie jego nazwy. Ta nazwa jest jego IDENTYFIKATORem i po przypisaniu nie można zmienić.

- Aby zmienić nazwę konta, należy usunąć je i utworzyć nowe konto w usłudze Batch.

- Konto jest tworzone w ramach subskrypcji, której chcesz użyć.

- Użyj konta, aby identyfikować i pobierać podstawowe i pomocnicze klucze kont z dowolnego konta usługi Batch w ramach subskrypcji.

- Konto utrzymuje informacje o alokacji puli i przydziałach podstawowych.  

- Konto zawiera informacje o lokalizacji.

- Konto identyfikuje konto magazynu.

## <a name="next-steps"></a>Następne kroki

- Utwórz konto usługi Batch przy użyciu [Azure Portal](batch-account-create-portal.md).
- Utwórz konto wsadowe programowo, takie jak w przypadku [biblioteki zarządzania usługą Batch dla platformy .NET](batch-management-dotnet.md).
- [Skonfiguruj lub wyłącz dostęp zdalny do węzłów obliczeniowych w puli Azure Batch](pool-endpoint-configuration.md).
- [Uruchamianie zadań przygotowania i zwolnienia zadań w węzłach obliczeniowych wsadowych](batch-job-prep-release.md)

