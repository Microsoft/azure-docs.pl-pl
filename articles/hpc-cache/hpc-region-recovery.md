---
title: Regionalna redundancja i odzyskiwanie awaryjne za pomocą pamięci podręcznej HPC azure
description: Techniki zapewniania funkcji trybu failover w zakresie odzyskiwania po awarii za pomocą pamięci podręcznej HPC usługi Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 21074ae6bc4959da031bc7065cd7d0639ec2a14f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537274"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Używanie wielu pamięci podręcznych do regionalnego odzyskiwania trybu failover

Każde wystąpienie pamięci podręcznej HPC platformy Azure jest uruchamiane w ramach określonej subskrypcji i w jednym regionie. Oznacza to, że przepływ pracy pamięci podręcznej może ewentualnie zostać zakłócony, jeśli region ma pełną awarię.

W tym artykule opisano strategię, aby zmniejszyć ryzyko zakłóceń pracy przy użyciu drugiego regionu do pracy awaryjnej pamięci podręcznej.

Klucz jest przy użyciu magazynu zaplecza, który jest dostępny z wielu regionów. Ten magazyn może być lokalnym systemem NAS z odpowiednią obsługą dns lub magazynem obiektów Blob platformy Azure, który znajduje się w innym regionie niż pamięć podręczna.

W miarę postępu przepływu pracy w regionie podstawowym dane są zapisywane w długoterminowym magazynie poza regionem. Jeśli region pamięci podręcznej stanie się niedostępny, można utworzyć zduplikowane wystąpienie pamięci podręcznej HPC usługi Azure w regionie pomocniczym, połączyć się z tym samym magazynem i wznowić pracę z nowej pamięci podręcznej.

## <a name="planning-for-regional-failover"></a>Planowanie regionalnej pracy awaryjnej

Aby skonfigurować pamięć podręczną przygotowaną do ewentualnego pracy awaryjnej, wykonaj następujące kroki:

1. Upewnij się, że magazyn zaplecza jest dostępny w drugim regionie.
1. Planując utworzenie wystąpienia podstawowej pamięci podręcznej, należy również przygotować się do replikacji tego procesu instalacji w drugim regionie. Dołącz następujące elementy:

   1. Struktura sieci wirtualnej i podsieci
   1. Pojemność pamięci podręcznej
   1. Szczegóły docelowe magazynu, nazwy i ścieżki obszaru nazw
   1. Szczegółowe informacje o komputerach klienckich, jeśli znajdują się w tym samym regionie co pamięć podręczna
   1. Polecenie Montuj do użytku przez klientów pamięci podręcznej

   > [!NOTE]
   > Pamięć podręczną HPC platformy Azure można tworzyć programowo za pomocą [szablonu usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md) lub bezpośrednio uzyskując dostęp do jego interfejsu API. Aby uzyskać szczegółowe informacje, skontaktuj się z zespołem usługi Azure HPC Cache.

## <a name="failover-example"></a>Przykład trybu failover

Na przykład załóż, że chcesz zlokalizować pamięć podręczną HPC platformy Azure w regionie wschodnich stanów USA platformy Azure. Będzie uzyskiwać dostęp do danych przechowywanych w lokalnym centrum danych.

Można użyć pamięci podręcznej w regionie Zachodnie stany USA 2 jako kopii zapasowej trybu failover.

Podczas tworzenia pamięci podręcznej we wschodnich stanach zjednoczonych przygotuj drugą pamięć podręczną do wdrożenia w zachodnie stany USA 2. Do automatyzacji tego przygotowania można użyć skryptów lub szablonów.

W przypadku awarii całego regionu we wschodnich stanach USA utwórz pamięć podręczną przygotowaną w regionie Zachodnie stany USA 2.

Po utworzeniu pamięci podręcznej dodaj obiekty docelowe magazynu, które wskazują te same lokalne magazyny danych i użyj tych samych zagregowanych ścieżek obszaru nazw, co obiekty docelowe magazynu starej pamięci podręcznej.

Jeśli dotyczy to oryginalnych klientów, utwórz nowych klientów w regionie Zachodnie stany USA 2 do użycia z nową pamięcią podręczną.

Wszyscy klienci będą musieli zainstalować nową pamięć podręczną, nawet jeśli klienci nie zostali dotknięci awarią regionu. Nowa pamięć podręczna ma różne adresy instalacji niż stary.

## <a name="learn-more"></a>Dowiedz się więcej

Przewodnik architektury aplikacji platformy Azure zawiera więcej informacji na temat [odzyskiwania po zakłóceniu usługi w całym regionie.](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)
