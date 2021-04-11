---
title: Segmenty sieci VMware HCX
description: W przypadku oprogramowania VMware HCX są dostępne cztery sieci.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622140"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Istnieją różne sposoby konfigurowania segmentów sieci VMware HCX w środowisku lokalnym. Poniżej przedstawiono prostą konfigurację, która obsługuje program pilotażowy lub niewielki w przypadku użycia w środowisku produkcyjnym.  Podczas projektowania dla setek lub tysięcy obciążeń może zajść konieczność zmiany tej konfiguracji, w zależności od potrzeb migracji.  

W przygotowaniu do wdrożenia programu VMware HCX w celu obsługi pilotażowej lub małego przypadku użycia w środowisku produkcyjnym należy określić następujące elementy:

- **Sieć zarządzania:** Podczas wdrażania lokalnego programu VMware HCX należy zdefiniować sieć zarządzania.  Zwykle jest to ta sama sieć zarządzania używana przez lokalny klaster programu VMware.  Należy określić co najmniej **dwa** adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. Może być potrzebna większa liczba, w zależności od skali wdrożenia poza testem pilotażowym lub małym przypadkiem użycia.

> [!NOTE]
   > Zalecaną metodą jest utworzenie sieci/26, ponieważ można użyć maksymalnie 10 siatek usług i 60 sieci rozszerzeń (-1 na siatkę usług). Można rozciągnąć **osiem** sieci na rozszerzenie sieci przy użyciu chmur prywatnych rozwiązań VMware platformy Azure.
   >
   
- **Sieć vMotion:** Podczas wdrażania lokalnego programu VMware HCX należy zdefiniować sieć vMotion.  Zwykle jest to ta sama sieć, która jest używana na potrzeby vMotion przez lokalny klaster VMware.  Należy określić co najmniej **dwa** adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. Może być potrzebna większa liczba, w zależności od skali wdrożenia poza testem pilotażowym lub małym przypadkiem użycia.

   Sieć vMotion musi być narażona na rozproszonym przełączniku wirtualnym lub vSwitch0. Jeśli tak nie jest, zmodyfikuj środowisko, aby uwzględnić.

   > [!NOTE]
   > Wiele środowisk VMware używa niekierowanych segmentów sieci dla usługi vMotion, co nie powoduje problemów.

- **Sieć pasma:** Podczas wdrażania lokalnego programu VMware HCX należy zdefiniować sieć pasma. Użyj sieci zarządzania zdefiniowanej jako sieci pasma.
   
- **Sieć replikacji:** Podczas wdrażania lokalnego programu VMware HCX należy zdefiniować sieć replikacji. Użyj sieci zarządzania zdefiniowanej jako sieć replikacji.  Jeśli hosty klastra lokalnego używają dedykowanej sieci VMkernel, należy zarezerwować **dwa** adresy IP w tym segmencie sieci i użyć sieci VMkernel replikacji dla sieci replikacji.
