---
title: Segmenty sieci VMware HCX
description: W przypadku oprogramowania VMware HCX są dostępne cztery sieci.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 1f0fcc1ddeeb26702a297035e6ae2a2f73dd71d6
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462334"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

W przypadku oprogramowania VMware HCX są cztery sieci:

- **Sieć zarządzania:** Zwykle jest to ta sama sieć zarządzania, która jest używana przez klaster rozwiązań VMware platformy Azure. Należy określić co najmniej **dwa** adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. W zależności od wdrożenia mogą być potrzebne większe liczby.

   > [!NOTE]
   > Zalecaną metodą jest utworzenie sieci/26, ponieważ można użyć maksymalnie 10 siatek usług i 60 sieci rozszerzeń (-1 na siatkę usług). Można rozciągnąć **osiem** sieci na rozszerzenie sieci przy użyciu chmur prywatnych rozwiązań VMware platformy Azure.
   >
   
- **Sieć vMotion:** Zwykle jest to ta sama sieć, która jest używana na potrzeby vMotion w klastrze rozwiązań VMware platformy Azure.  Należy określić co najmniej **dwa** adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. W zależności od wdrożenia mogą być potrzebne większe liczby.  

   Sieć vMotion musi być narażona na rozproszonym przełączniku wirtualnym lub vSwitch0. Jeśli tak nie jest, zmodyfikuj środowisko.

   > [!NOTE]
   > Ta sieć może być prywatna (nie kierowana).

- **Sieć pasma:** Chcesz utworzyć nową sieć dla pasma VMware HCX i zwiększyć ją do klastra vSphere za pośrednictwem grupy portów. Należy określić co najmniej **dwa** adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. W zależności od wdrożenia mogą być potrzebne większe liczby.  

   > [!NOTE]
   > Zalecaną metodą jest utworzenie sieci/26, ponieważ można użyć maksymalnie 10 siatek usług i 60 sieci rozszerzeń (-1 na siatkę usług). Można rozciągnąć **osiem** sieci na rozszerzenie sieci przy użyciu chmur prywatnych rozwiązań VMware platformy Azure.
   >
   
- **Sieć replikacji:** Jest to opcjonalne. Chcesz utworzyć nową sieć na potrzeby replikacji oprogramowania VMware HCX i zwiększyć tę sieć do klastra vSphere za pośrednictwem grupy portów. Należy określić co najmniej **dwa** adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. W zależności od wdrożenia mogą być potrzebne większe liczby.

   > [!NOTE]
   > Ta konfiguracja jest możliwa tylko wtedy, gdy lokalne hosty klastra używają dedykowanej sieci VMkernel replikacji.  Jeśli klaster lokalny nie ma zdefiniowanej dedykowanej sieci VMkernel replikacji, nie ma potrzeby tworzenia tej sieci.
