---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 14ac8ff54f5627509fe10a90bfdd0a01c07762d9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026159"
---
Przed utworzeniem jakichkolwiek elementów zapoznajmy się z pojęciem zakresu. Platforma Azure oferuje cztery poziomy zarządzania: grupy zarządzania, subskrypcję, grupy zasobów i zasób. [Grupy zarządzania](../articles/governance/management-groups/overview.md) są w wersji zapoznawczej. Na poniższej ilustracji przedstawiono takie przykładowe warstwy.

![Zakres](./media/resource-manager-governance-scope/scope-levels.png)

Ustawienia zarządzania są stosowane na dowolnych z tych poziomów zakresu. Zasięg zastosowania ustawienia jest określany na podstawie wybranego poziomu. Niższe poziomy dziedziczą ustawienia z wyższych poziomów. Po zastosowaniu ustawienia do subskrypcji to ustawienie jest stosowane do wszystkich grup zasobów i zasobów w ramach subskrypcji. Kiedy ustawienie jest stosowane do grupy zasobów, jest ono stosowane do grupy zasobów i wszystkich zasobów, które się w niej znajdują. Takie ustawienie nie ma jednak wpływu na inną grupę zasobów.

Krytyczne ustawienia zwykle warto stosować na wyższych poziomach, a wymagania specyficzne dla projektu na niższych poziomach. Może na przykład zajść potrzeba upewnienia się, że wszystkie zasoby w organizacji zostaną wdrożone w określonych regionach. Aby sprostać temu wymaganiu, należy do subskrypcji zastosować zasady, które określą dozwolone lokalizacje. Kiedy inni użytkownicy w organizacji będą dodawać nowe grupy zasobów i zasoby, dozwolone lokalizacje będą automatycznie wymuszane.