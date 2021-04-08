---
title: Regionalna nadmiarowość i odzyskiwanie do trybu failover za pomocą usługi Azure HPC cache
description: Techniki zapewniające możliwości pracy awaryjnej w przypadku odzyskiwania po awarii z użyciem pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 9159807f55ae52393b8fccec339fcc94c3e4ebb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87061373"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Używanie wielu pamięci podręcznych dla regionalnego odzyskiwania trybu failover

Każde wystąpienie pamięci podręcznej platformy Azure HPC działa w ramach określonej subskrypcji i w jednym regionie. Oznacza to, że przepływ pracy pamięci podręcznej może być prawdopodobnie zakłócony, jeśli region ma pełną awarię.

W tym artykule opisano strategię zmniejszania ryzyka zakłócenia pracy przy użyciu drugiego regionu w celu przełączenia w tryb failover pamięci podręcznej.

Klucz używa magazynu zaplecza, który jest dostępny z wielu regionów. Ten magazyn może być lokalnym systemem NAS z odpowiednią obsługą systemu DNS lub magazynem obiektów blob platformy Azure, który znajduje się w innym regionie niż pamięć podręczna.

Gdy przepływ pracy zostanie przeprowadzony w regionie podstawowym, dane są zapisywane w magazynie długoterminowym poza regionem. Jeśli region pamięci podręcznej stanie się niedostępny, możesz utworzyć zduplikowane wystąpienie pamięci podręcznej platformy Azure HPC w regionie pomocniczym, nawiązać połączenie z tym samym magazynem i wznowić pracę z nowej pamięci podręcznej.

## <a name="planning-for-regional-failover"></a>Planowanie trybu failover dla regionu

Aby skonfigurować pamięć podręczną, która została przygotowana do możliwej pracy w trybie failover, wykonaj następujące kroki:

1. Upewnij się, że magazyn zaplecza jest dostępny w drugim regionie.
1. Planując utworzenie wystąpienia podstawowej pamięci podręcznej, należy również przygotować się do replikowania tego procesu konfiguracji w drugim regionie. Uwzględnij następujące elementy:

   1. Struktura sieci wirtualnej i podsieci
   1. Pojemność pamięci podręcznej
   1. Szczegóły lokalizacji docelowej magazynu, nazwy i ścieżki przestrzeni nazw
   1. Szczegółowe informacje o komputerach klienckich, jeśli znajdują się w tym samym regionie co pamięć podręczna
   1. Polecenie instalacji do użycia przez klientów pamięci podręcznej

   > [!NOTE]
   > Pamięć podręczną platformy Azure HPC można utworzyć programowo przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) lub przez bezpośredni dostęp do jego interfejsu API. Aby uzyskać szczegółowe informacje, skontaktuj się z zespołem usługi Azure HPC cache.

## <a name="failover-example"></a>Przykład pracy awaryjnej

Załóżmy na przykład, że chcesz zlokalizować pamięć podręczną platformy Azure HPC w regionie Wschodnie stany USA platformy Azure. Dostęp do danych przechowywanych w lokalnym centrum danych.

Możesz użyć pamięci podręcznej w regionie zachodnie stany USA 2 jako kopia zapasowa trybu failover.

Podczas tworzenia pamięci podręcznej w regionach Wschodnie stany USA Przygotuj drugą pamięć podręczną do wdrożenia w regionie zachodnie stany USA 2. W celu zautomatyzowania tego przygotowania można użyć skryptów lub szablonów.

W przypadku awarii całego regionu w regionach Wschodnie stany USA Utwórz pamięć podręczną przygotowaną w regionie zachodnie stany USA 2.

Po utworzeniu pamięci podręcznej Dodaj elementy docelowe magazynu wskazujące te same lokalne magazyny danych i Użyj tych samych zagregowanych ścieżek przestrzeni nazw jako obiektów docelowych magazynu starych pamięci podręcznej.

Jeśli ma to zastosowanie w przypadku oryginalnych klientów, Utwórz nowych klientów w regionie zachodnie stany USA 2, aby użyć jej z nową pamięcią podręczną.

Wszyscy klienci będą musieli zainstalować nową pamięć podręczną, nawet jeśli awaria regionu nie wpłynie na klientów. Nowa pamięć podręczna ma inne adresy instalacji od Starego.

## <a name="learn-more"></a>Więcej tutaj

Przewodnik po architekturze aplikacji platformy Azure zawiera więcej informacji o sposobach [odzyskiwania po przerwie między](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)działami w całym regionie.
