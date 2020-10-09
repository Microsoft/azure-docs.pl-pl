---
title: Segmenty sieci VMware HCX
description: W przypadku oprogramowania VMware HCX są dostępne cztery sieci.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91583332"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

W przypadku oprogramowania VMware HCX są dostępne cztery sieci:

- **Sieć zarządzania:** Zwykle jest to ta sama sieć zarządzania używana w klastrze vSphere.  Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX (w zależności od wdrożenia może być wymagana większa liczba).

- **Sieć vMotion:** Zwykle jest to ta sama sieć używana na potrzeby vMotion w klastrze vSphere.  Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX (w zależności od wdrożenia może być wymagana większa liczba).  

   Sieć vMotion musi być narażona na rozproszonym przełączniku wirtualnym lub vSwitch0. Jeśli tak nie jest, zmodyfikuj środowisko.

   > [!NOTE]
   > Jeśli ta sieć nie jest kierowana (prywatna), to jest OK.

- **Sieć pasma:** Chcesz utworzyć nową sieć dla pasma VMware HCX i zwiększyć ją do klastra vSphere za pośrednictwem grupy portów.  Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX (w zależności od wdrożenia może być wymagana większa liczba).  

   > [!NOTE]
   > Zalecaną metodą jest utworzenie sieci/29, ale będzie można wykonać dowolny rozmiar sieci.

- **Sieć replikacji:** Chcesz utworzyć nową sieć na potrzeby replikacji oprogramowania VMware HCX i zwiększyć tę sieć do klastra vSphere za pośrednictwem grupy portów.  Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX (w zależności od wdrożenia może być wymagana większa liczba).

   > [!NOTE]
   > Zalecaną metodą jest utworzenie sieci/29, ale będzie można wykonać dowolny rozmiar sieci.