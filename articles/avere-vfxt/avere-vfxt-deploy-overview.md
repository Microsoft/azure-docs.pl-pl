---
title: Przegląd wdrożenia — avere vFXT dla platformy Azure
description: Dowiedz się, jak wdrożyć avere vFXT dla klastra platformy Azure przy użyciu tego omówienia. Powiązane artykuły mają określone instrukcje dotyczące wdrażania.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88271230"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure — omówienie wdrożenia

Ten artykuł zawiera omówienie kroków niezbędnych do uzyskania avere vFXT dla klastra platformy Azure.

Przed utworzeniem klastra vFXT z poziomu portalu Azure Marketplace potrzebne są kilka zadań. Wyraźne zrozumienie procesu rozpoczęcia do zakończenia pomoże Ci określić zakres potrzebnego nakładu pracy.

## <a name="deployment-steps"></a>Kroki wdrażania

Po [zaplanowaniu systemu](avere-vfxt-deploy-plan.md)możesz rozpocząć tworzenie klastra avere vFXT.

Szablon Azure Resource Manager w portalu Azure Marketplace zbiera niezbędne informacje i automatycznie wdraża cały klaster.

Gdy klaster vFXT jest uruchomiony, nadal istnieją pewne czynności konfiguracyjne, które należy wykonać przed jego użyciem. W przypadku utworzenia nowego kontenera magazynu obiektów BLOB należy przenieść do niego dane. W przypadku korzystania z systemu magazynu NAS należy dodać go po utworzeniu klastra. Chcesz połączyć klientów z klastrem.

Poniżej przedstawiono przegląd wszystkich kroków.

1. Konfigurowanie wymagań wstępnych

   Przed utworzeniem maszyny wirtualnej należy utworzyć nową subskrypcję dla projektu avere vFXT, skonfigurować własność subskrypcji, sprawdzić przydziały i zażądać zwiększenia w razie potrzeby, a następnie zaakceptować warunki korzystania z oprogramowania vFXT avere. Aby uzyskać szczegółowe instrukcje, zapoznaj [się z artykułem przygotowywanie do utworzenia avere vFXT](avere-vfxt-prereqs.md) .

1. Tworzenie klastra avere vFXT

   Skorzystaj z portalu Azure Marketplace, aby utworzyć avere vFXT dla klastra platformy Azure. Szablon zbiera wymagane informacje i wykonuje skrypty w celu utworzenia produktu końcowego.

   Tworzenie klastra obejmuje te kroki, które są wykonywane przez szablon Marketplace:

   * Utwórz nową infrastrukturę sieci i grupy zasobów, w razie konieczności
   * Tworzenie kontrolera klastra

     Kontroler klastra jest prostą MASZYNą wirtualną, która znajduje się w tej samej sieci wirtualnej co klaster avere vFXT i ma niestandardowe oprogramowanie, które jest konieczne do utworzenia klastra i zarządzania nim. Kontroler tworzy węzły vFXT i tworzą klaster, a także udostępnia interfejs wiersza polecenia do zarządzania klastrem w trakcie jego istnienia.

     Jeśli podczas wdrażania zostanie utworzona nowa sieć wirtualna lub podsieć, kontroler będzie miał publiczny adres IP. Oznacza to, że kontroler może działać jako host skoku do łączenia się z klastrem spoza sieci wirtualnej.

   * Tworzenie maszyn wirtualnych węzłów klastra

   * Tworzenie klastra z poziomu poszczególnych węzłów

   * Opcjonalnie można utworzyć nowy kontener obiektów blob i skonfigurować go jako magazyn zaplecza dla klastra

   Tworzenie klastra zostało szczegółowo opisane w artykule [wdrażanie klastra vFXT](avere-vfxt-deploy.md).

1. Konfigurowanie klastra

   Połącz się z interfejsem avere vFXT Configuration Interface (avere Panel sterowania), aby dostosować ustawienia klastra. Zezwól na monitorowanie pomocy technicznej i Dodaj system magazynu, jeśli używasz magazynu sprzętowego lub dodatkowych kontenerów obiektów BLOB.

   * [Uzyskiwanie dostępu do klastra vFXT](avere-vfxt-cluster-gui.md)
   * [Włącz obsługę](avere-vfxt-enable-support.md)
   * [Konfigurowanie magazynu](avere-vfxt-add-storage.md) (w razie konieczności)

1. Instalowanie klientów

   Postępuj zgodnie z wytycznymi w temacie [Instalowanie klastra avere vFXT](avere-vfxt-mount-clients.md) , aby dowiedzieć się więcej na temat równoważenia obciążenia i sposobu instalowania klastra przez komputery klienckie.

1. Dodaj dane (w razie konieczności)

   Ponieważ avere vFXT to skalowalna pamięć podręczna z obsługą kilku klientów, najlepszym sposobem przenoszenia danych do nowego kontenera magazynu zaplecza jest wielosesyjna, wielowątkowa strategia kopiowania plików.

   Jeśli musisz przenieść dane zestawu roboczego do nowego kontenera obiektów blob lub innego systemu magazynu zaplecza, postępuj zgodnie z instrukcjami w temacie [przenoszenie danych do klastra vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Następne kroki

Kontynuuj [Przygotowywanie do utworzenia avere vFXT](avere-vfxt-prereqs.md) w celu ukończenia zadań wymaganych wstępnie.
