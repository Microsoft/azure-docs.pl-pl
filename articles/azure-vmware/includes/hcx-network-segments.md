---
title: Segmenty sieci VMware HCX
description: W przypadku oprogramowania VMware HCX są dostępne cztery sieci.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95529749"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

W przypadku oprogramowania VMware HCX są cztery sieci:

- **Sieć zarządzania:** Zwykle jest to ta sama sieć zarządzania używana w klastrze vSphere. Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. W zależności od wdrożenia mogą być potrzebne większe liczby.

   > [!NOTE]
   > Zalecaną metodą jest utworzenie sieci/26. W przypadku sieci/26 można użyć maksymalnie 10 siatek usługi i rozszerzeń sieci 60 (-1 na siatkę usługi). Można rozciągnąć osiem sieci na rozszerzenie sieci przy użyciu chmur prywatnych rozwiązań VMware platformy Azure.
   >
   
- **Sieć vMotion:** Zwykle jest to ta sama sieć używana na potrzeby vMotion w klastrze vSphere.  Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. W zależności od wdrożenia mogą być potrzebne większe liczby.  

   Sieć vMotion musi być narażona na rozproszonym przełączniku wirtualnym lub vSwitch0. Jeśli tak nie jest, zmodyfikuj środowisko.

   > [!NOTE]
   > Ta sieć może być prywatna (nie kierowana).

- **Sieć pasma:** Chcesz utworzyć nową sieć dla pasma VMware HCX i zwiększyć ją do klastra vSphere za pośrednictwem grupy portów. Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. W zależności od wdrożenia mogą być potrzebne większe liczby.  

   > [!NOTE]
   > Zalecaną metodą jest utworzenie sieci/26. W przypadku sieci/26 można użyć maksymalnie 10 siatek usługi i rozszerzeń sieci 60 (-1 na siatkę usługi). Można rozciągnąć osiem sieci na rozszerzenie sieci przy użyciu chmur prywatnych rozwiązań VMware platformy Azure.
   >
   
- **Sieć replikacji:** Jest to opcjonalne. Chcesz utworzyć nową sieć na potrzeby replikacji oprogramowania VMware HCX i zwiększyć tę sieć do klastra vSphere za pośrednictwem grupy portów. Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. W zależności od wdrożenia mogą być potrzebne większe liczby.

   > [!NOTE]
   > Ta konfiguracja jest możliwa tylko wtedy, gdy lokalne hosty klastra używają dedykowanej sieci VMkernel replikacji.  Jeśli klaster lokalny nie ma zdefiniowanej dedykowanej sieci VMkernel replikacji, nie ma potrzeby tworzenia tej sieci.
