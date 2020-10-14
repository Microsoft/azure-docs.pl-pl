---
title: Wysoka dostępność i odzyskiwanie po awarii
description: Dowiedz się, jak zaprojektować aplikację usługi Batch pod kątem regionalnej awarii.
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: c77e6fa672d26545af47b262a982a7d0d7303eec
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058016"
---
# <a name="design-your-application-for-high-availability"></a>Projektowanie aplikacji wysokiej dostępności

Azure Batch to usługa regionalna. Usługa Batch jest dostępna we wszystkich regionach platformy Azure, ale po utworzeniu konta usługi Batch musi ono być skojarzone z regionem. Wszystkie operacje na koncie usługi Batch są następnie stosowane do tego regionu. Na przykład pule i skojarzone maszyny wirtualne są tworzone w tym samym regionie co konto usługi Batch.

Podczas projektowania aplikacji korzystającej z programu Batch należy rozważyć możliwość, że partia nie jest dostępna w regionie. Istnieje możliwość wystąpienia rzadkich sytuacji, gdy występuje problem z regionem jako całość, cała usługa Batch w regionie lub problem z konkretnym kontem usługi Batch.

Jeśli aplikacja lub rozwiązanie korzystające z usługi Batch zawsze musi być dostępne, powinno być przeznaczone do pracy w trybie failover z innym regionem lub zawsze mają podział obciążenia między dwa lub więcej regionów. Oba podejścia wymagają co najmniej dwóch kont usługi Batch, przy czym każde konto znajduje się w innym regionie.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Wiele kont wsadowych w wielu regionach

Używanie wielu kont usługi Batch w różnych regionach zapewnia możliwość kontynuowania działania aplikacji w przypadku niedostępności konta usługi Batch w innym regionie. Używanie wielu kont jest szczególnie ważne, jeśli aplikacja musi być wysoce dostępna.

W niektórych przypadkach aplikacja może być zaprojektowana tak, aby zawsze korzystała z dwóch lub więcej regionów. Na przykład gdy potrzebna jest znaczna ilość pojemności, może być konieczne użycie wielu regionów w celu obsługi aplikacji o dużej skali lub zajęcie ich w przyszłości.

## <a name="design-considerations-for-providing-failover"></a>Zagadnienia dotyczące projektowania związane z udostępnianiem trybu failover

Kluczowym punktem, który należy wziąć pod uwagę w przypadku przejścia w tryb failover do alternatywnego regionu, jest to, że wszystkie składniki w rozwiązaniu należy rozważyć; nie wystarczy, aby po prostu mieć drugie konto w usłudze Batch. Na przykład w większości aplikacji wsadowych wymagane jest konto magazynu platformy Azure, a konto magazynu i konto usługi Batch muszą znajdować się w tym samym regionie, aby można było uzyskać akceptowalną wydajność.

Podczas projektowania rozwiązania, które może przejść w tryb failover, należy wziąć pod uwagę następujące kwestie:

- Przed utworzeniem wszystkich wymaganych kont w każdym regionie, takich jak konto i konto magazynu w usłudze Batch. W przypadku tworzenia kont często nie jest naliczana żadna opłata, tylko wtedy, gdy są przechowywane dane lub konto jest używane.
- Upewnij się, że przydziały są ustawione na kontach z wyprzedzeniem, aby można było przydzielić wymaganą liczbę rdzeni przy użyciu konta usługi Batch.
- Aby zautomatyzować wdrażanie aplikacji w regionie, użyj szablonów i/lub skryptów.
- Utrzymywanie Aktualności plików binarnych aplikacji i danych referencyjnych we wszystkich regionach. Zadbaj o to, aby region można było szybko przełączyć do trybu online bez konieczności oczekiwania na przekazywanie i wdrażanie plików. Na przykład jeśli aplikacja niestandardowa do zainstalowania w węzłach puli jest przechowywana i przywoływana przy użyciu pakietów aplikacji usługi Batch, wtedy, gdy zostanie utworzona nowa wersja aplikacji, należy ją przekazać do każdego konta usługi Batch i odwołać się do niej zgodnie z konfiguracją puli (lub wprowadzić nową wersję domyślną).
- W aplikacji przetwarzanie wsadowe, magazyn i inne usługi można łatwo przełączeć klientów lub obciążenie do innego regionu.
- Najlepszym rozwiązaniem w celu zapewnienia pomyślnego przełączenia w tryb failover będzie częste przełączenie do alternatywnego regionu w ramach normalnej operacji. Na przykład w przypadku dwóch wdrożeń w osobnych regionach przełączenie do regionu alternatywnego co miesiąc.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat tworzenia kont usługi Batch za pomocą [Azure Portal](batch-account-create-portal.md), interfejsu [wiersza polecenia platformy Azure](./scripts/batch-cli-sample-create-account.md), [programu PowerShell](batch-powershell-cmdlets-get-started.md)lub [API zarządzania usługą Batch](batch-management-dotnet.md).
- Domyślne przydziały są skojarzone z kontem wsadowym; [ten artykuł](batch-quota-limit.md) zawiera szczegółowe informacje o domyślnych wartościach przydziału i opisuje, jak można zwiększyć limity przydziałów.
