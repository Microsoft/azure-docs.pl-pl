---
title: Segmenty sieci VMware HCX
description: W przypadku oprogramowania VMware HCX są dostępne cztery sieci.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f2f5597471355bf4d74be7fbe370e6fa58770bf1
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251265"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Istnieją różne sposoby konfigurowania segmentów sieci VMware HCX w środowisku lokalnym. Poniżej przedstawiono prostą konfigurację, która obsługuje program pilotażowy lub niewielki w przypadku użycia w środowisku produkcyjnym.  Podczas projektowania dla setek lub tysięcy obciążeń może zajść konieczność zmiany tej konfiguracji, w zależności od potrzeb migracji.  

W przygotowaniu do wdrożenia programu VMware HCX w celu obsługi pilotażowej lub małego przypadku użycia w środowisku produkcyjnym należy określić następujące elementy:

- **Sieć zarządzania:** W przypadku wdrażania lokalnego programu VMware HCX należy zidentyfikować sieć zarządzania dla programu VMware HCX.  Zwykle jest to ta sama sieć zarządzania używana przez lokalny klaster programu VMware.  Należy określić co najmniej **dwa** adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. Może być potrzebna większa liczba, w zależności od skali wdrożenia poza testem pilotażowym lub małym przypadkiem użycia.

  > [!NOTE]
  > Przygotowanie do dużych środowisk, zamiast używać sieci zarządzania używanej przez lokalny klaster VMware, Utwórz nową sieć/26 i zaprezentowanie tej sieci jako grupy portów w lokalnym klastrze programu VMware.  Następnie można utworzyć maksymalnie 10 oczek usługi i rozszerzeń sieci 60 (-1 na siatkę usługi). Można rozciągnąć **osiem** sieci na rozszerzenie sieci przy użyciu chmur prywatnych rozwiązań VMware platformy Azure.
  >

- **Sieć pasma:** Podczas wdrażania lokalnego programu VMware HCX należy zidentyfikować sieć pasma dla programu VMware HCX. Użyj tej samej sieci, która będzie używana w sieci zarządzania. 

- **Sieć vMotion:** Podczas wdrażania lokalnego programu VMware HCX należy zidentyfikować sieć vMotion dla programu VMware HCX.  Zwykle jest to ta sama sieć, która jest używana na potrzeby vMotion przez lokalny klaster VMware.  Należy określić co najmniej **dwa** adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. Może być potrzebna większa liczba, w zależności od skali wdrożenia poza testem pilotażowym lub małym przypadkiem użycia.

   Sieć vMotion musi być narażona na rozproszonym przełączniku wirtualnym lub vSwitch0. Jeśli tak nie jest, zmodyfikuj środowisko, aby uwzględnić.

   > [!NOTE]
   > Wiele środowisk VMware używa niekierowanych segmentów sieci dla usługi vMotion, co nie powoduje problemów.
  
- **Sieć replikacji:** Podczas wdrażania lokalnego programu VMware HCX należy zdefiniować sieć replikacji. Użyj tej samej sieci, która jest używana dla sieci zarządzania i pasma.  Jeśli hosty klastra lokalnego używają dedykowanej sieci VMkernel, należy zarezerwować **dwa** adresy IP w tym segmencie sieci i użyć sieci VMkernel replikacji dla sieci replikacji.
