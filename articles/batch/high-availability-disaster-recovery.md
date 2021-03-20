---
title: Wysoka dostępność i odzyskiwanie po awarii
description: Dowiedz się, jak zaprojektować aplikację usługi Batch pod kątem regionalnej awarii.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831010"
---
# <a name="design-your-batch-application-for-high-availability"></a>Projektowanie aplikacji usługi Batch w celu zapewnienia wysokiej dostępności

Azure Batch jest dostępna we wszystkich regionach świadczenia usługi Azure, ale po utworzeniu konta w usłudze Batch należy je skojarzyć z jednym określonym regionem. Wszystkie operacje na koncie usługi Batch są następnie stosowane do tego regionu. Na przykład pule i skojarzone maszyny wirtualne są tworzone w tym samym regionie co konto usługi Batch.

Podczas projektowania aplikacji korzystającej z programu Batch należy rozważyć możliwość, że partia nie jest dostępna w regionie. Istnieje możliwość wystąpienia rzadkich sytuacji, gdy występuje problem z regionem jako całości, cała usługa Batch w regionie lub określone konto usługi Batch.

Jeśli aplikacja lub rozwiązanie korzystające z usługi Batch zawsze musi być dostępne, powinno być przeznaczone do pracy w trybie failover z innym regionem lub zawsze mają podział obciążenia między dwa lub więcej regionów. Oba podejścia wymagają co najmniej dwóch kont usługi Batch, przy czym każde konto znajduje się w innym regionie.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Wiele kont wsadowych w wielu regionach

Używanie wielu kont usługi Batch w różnych regionach umożliwia kontynuowanie działania aplikacji, jeśli konto usługi Batch w jednym regionie przestanie być dostępne. Jeśli aplikacja musi być wysoce dostępna, posiadanie wielu kont jest szczególnie ważne.

W niektórych przypadkach aplikacje można zaprojektować, aby celowo korzystać z dwóch lub więcej regionów. Na przykład gdy potrzebna jest znaczna ilość pojemności, może być konieczne użycie wielu regionów w celu obsługi aplikacji o dużej skali lub zajęcie ich w przyszłości. Te aplikacje będą również wymagały wielu kont wsadowych (jednego na region).

## <a name="design-considerations-for-providing-failover"></a>Zagadnienia dotyczące projektowania związane z udostępnianiem trybu failover

Aby zapewnić możliwość przejścia w tryb failover do alternatywnego regionu, należy rozważyć wszystkie składniki w rozwiązaniu. nie wystarczy, aby po prostu mieć drugie konto w usłudze Batch. Na przykład w większości aplikacji wsadowych wymagane jest konto magazynu platformy Azure, a konto magazynu i konto usługi Batch muszą znajdować się w tym samym regionie, aby można było uzyskać akceptowalną wydajność.

Podczas projektowania rozwiązania, które może przejść w tryb failover, należy wziąć pod uwagę następujące kwestie:

- Przed utworzeniem wszystkich wymaganych kont w każdym regionie, takich jak konto i konto magazynu w usłudze Batch. W przypadku tworzenia kont często nie są naliczane opłaty, a opłaty są naliczane tylko wtedy, gdy konto jest używane lub gdy dane są przechowywane.
- Upewnij się, że odpowiednie [przydziały](batch-quota-limit.md) zostały ustawione na wszystkie konta przed czasem, aby można było przydzielić wymaganą liczbę rdzeni przy użyciu konta usługi Batch.
- Aby zautomatyzować wdrażanie aplikacji w regionie, użyj szablonów i/lub skryptów.
- Utrzymywanie Aktualności plików binarnych aplikacji i danych referencyjnych we wszystkich regionach. Zadbaj o to, aby region można było szybko przełączyć do trybu online bez konieczności oczekiwania na przekazywanie i wdrażanie plików. Na przykład jeśli aplikacja niestandardowa do zainstalowania w węzłach puli jest przechowywana i przywoływana przy użyciu pakietów aplikacji usługi Batch, wtedy, gdy zostanie utworzona nowa wersja aplikacji, należy ją przekazać do każdego konta usługi Batch i odwołać się do niej zgodnie z konfiguracją puli (lub wprowadzić nową wersję domyślną).
- W aplikacji wywołującej partię, magazyn i inne usługi można łatwo przełączać klientów lub ładować je do różnych regionów.
- Należy wziąć pod uwagę częste przełączanie do alternatywnego regionu w ramach normalnej operacji. Na przykład w przypadku dwóch wdrożeń w oddzielnych regionach Przełącz się do regionu alternatywnego co miesiąc.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat tworzenia kont usługi Batch za pomocą [Azure Portal](batch-account-create-portal.md), interfejsu [wiersza polecenia platformy Azure](./scripts/batch-cli-sample-create-account.md), [programu PowerShell](batch-powershell-cmdlets-get-started.md)lub [API zarządzania usługą Batch](batch-management-dotnet.md).
- Dowiedz się więcej o [limitach przydziału domyślnego skojarzonych z kontem w usłudze Batch](batch-quota-limit.md) oraz o sposobie zwiększania limitów przydziału.
