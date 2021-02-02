---
title: Stany subskrypcji platformy Azure
description: W tym artykuł opisano różne stany subskrypcji platformy Azure.
keywords: stan subskrypcji platformy azure
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: andalmia
ms.openlocfilehash: 5267b333e66a0ae7b2ad05399406fecc32af74b0
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430130"
---
# <a name="azure-subscription-states"></a>Stany subskrypcji platformy Azure

W tym artykule opisano różne możliwe stany subskrypcji platformy Azure. Te stany są wyświetlane jako **Stan** w obszarach subskrypcji witryny Azure Portal.

| Stan subskrypcji | Opis |
|-------------| ----------------|
| **Aktywna**/**Włączona** | Twoja subskrypcja platformy Azure jest aktywna. Możesz używać subskrypcji do wdrażania nowych zasobów i zarządzania istniejącymi.<br><br>Wszystkie operacje (PUT, PATCH, DELETE, POST i GET) są dostępne dla dostawców zasobów, którzy są [zarejestrowani dla Twojej subskrypcji](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Usunięta** | Twoja subskrypcja platformy Azure została usunięta wraz z wszystkimi zasobami/danymi bazowymi.<br><br>Żadne operacje nie są dostępne. |
| **Disabled (Wyłączone)** | Twoja subskrypcja platformy Azure jest wyłączona i nie można już używać jej do tworzenia zasobów platformy Azure i zarządzania nimi. W tym stanie przydziały maszyn wirtualnych są wycofane, tymczasowe adresy IP są zwolnione, magazyn jest dostępny tylko do odczytu, a inne usługi są wyłączone. Subskrypcja może zostać wyłączona z następujących powodów: Twoje środki mogły wygasnąć. Prawdopodobnie osiągnięto limit wydatków. Masz zaległy rachunek. Przekroczono limit karty kredytowej. Subskrypcja została jawnie wyłączona lub anulowana. W zależności od typu subskrypcji subskrypcja może pozostać wyłączona od 1 do 90 dni. Po upływie tego czasu zostanie ona trwale usunięta. Aby uzyskać więcej informacji, zobacz [Ponowne uaktywnianie wyłączonej subskrypcji platformy Azure](subscription-disabled.md).<br><br>Operacje tworzenia lub aktualizowania zasobów (PUT, PATCH) są wyłączone. Operacje, które wykonują akcję (POST), również są wyłączone. Możesz pobierać lub usuwać zasoby (GET, DELETE). Twoje zasoby są nadal dostępne. |
| **Wygasła** | Twoja subskrypcja platformy Azure wygasła, ponieważ została anulowana. Możesz ponownie aktywować wygasłą subskrypcję. Aby uzyskać więcej informacji, zobacz [Ponowne uaktywnianie wyłączonej subskrypcji platformy Azure](subscription-disabled.md).<br><br>Operacje tworzenia lub aktualizowania zasobów (PUT, PATCH) są wyłączone. Operacje, które wykonują akcję (POST), również są wyłączone. Możesz pobierać lub usuwać zasoby (GET, DELETE).|
| **Minął termin płatności** | Istnieje zaległa płatność za Twoją subskrypcję platformy Azure. Subskrypcja jest nadal aktywna, ale nieopłacenie zaległych płatności może spowodować wyłączenie subskrypcji. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemu z zaległym saldem za subskrypcję platformy Azure](resolve-past-due-balance.md).<br><br>Wszystkie operacje są dostępne. |
| **Ostrzeżenie** | Twoja subskrypcja platformy Azure jest w stanie ostrzegania i zostanie wkrótce wyłączona, jeśli powód ostrzeżenia nie zostanie rozkierowany. Subskrypcja może być w stanie ostrzeżenia, jeśli minął jej termin płatności, została anulowana przez użytkownika, wygasła itp.<br><br>Możesz pobrać lub usunąć zasoby (pobieranie/usuwanie), ale nie można utworzyć żadnych zasobów (PUT/PATCH/POST) |
