---
title: Segmenty sieci VMware HCX
description: W przypadku oprogramowania VMware HCX są dostępne cztery sieci.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173631"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

W przypadku oprogramowania VMware HCX są cztery sieci:

- **Sieć zarządzania:** Zwykle jest to ta sama sieć zarządzania używana w klastrze vSphere. Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. (Może być potrzebna większa liczba, w zależności od wdrożenia).

- **Sieć vMotion:** Zwykle jest to ta sama sieć używana na potrzeby vMotion w klastrze vSphere.  Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. (Może być potrzebna większa liczba, w zależności od wdrożenia).  

   Sieć vMotion musi być narażona na rozproszonym przełączniku wirtualnym lub vSwitch0. Jeśli tak nie jest, zmodyfikuj środowisko.

   > [!NOTE]
   > Jeśli ta sieć nie jest kierowana (prywatna), oznacza to, że jest to poprawne.

- **Sieć pasma:** Chcesz utworzyć nową sieć dla pasma VMware HCX i zwiększyć ją do klastra vSphere za pośrednictwem grupy portów. Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. (Może być potrzebna większa liczba, w zależności od wdrożenia).  

   > [!NOTE]
   > Zalecaną metodą jest utworzenie sieci/29, ale będzie można wykonać dowolny rozmiar sieci.

- **Sieć replikacji:** Chcesz utworzyć nową sieć na potrzeby replikacji oprogramowania VMware HCX i zwiększyć tę sieć do klastra vSphere za pośrednictwem grupy portów. Należy określić co najmniej dwa adresy IP w tym segmencie sieci dla oprogramowania VMware HCX. (Może być potrzebna większa liczba, w zależności od wdrożenia).

   > [!NOTE]
   > Zalecaną metodą jest utworzenie sieci/29, ale będzie można wykonać dowolny rozmiar sieci.