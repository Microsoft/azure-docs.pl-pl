---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 55b545471cbe45fe35e28879270e4340cf9d3834
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027768"
---
Wdrażanie zasobów na platformie Azure jest procesem niezwykle elastycznym. Można zdecydować, jakiego rodzaju zasoby będą wdrażane, gdzie się one znajdują oraz jak należy je skonfigurować. Ta elastyczność może jednak spowodować, że dostępnych będzie więcej opcji, niż powinno być dozwolonych w organizacji. Podczas planowania wdrażania zasobów na platformie Azure mogą wyniknąć następujące kwestie:

* Jak mogę spełniają wymagania prawne dotyczące suwerenności danych w niektórych krajach/regionach?
* Jak mogę kontrolować koszty?
* Jak mogę zagwarantować, że nikt nieodwracalnie nie zmodyfikuje krytycznego systemu?
* Jak mogę śledzić koszty zasobów i odpowiednio je rozliczać?

W tym artykule znajdują się odpowiedzi na te pytania. W szczególności opisano wykonywanie następujących czynności:

> [!div class="checklist"]
> * Przypisywanie użytkowników do ról i przypisywanie ról do zakresu, dzięki czemu użytkownicy mają uprawnienia do wykonywania tylko i wyłącznie oczekiwanych akcji.
> * Stosowanie zasad, które określają konwencje dla zasobów w ramach subskrypcji.
> * Blokowanie zasobów, które są krytyczne dla systemu.
> * Oznaczanie zasobów tagami, dzięki czemu można je śledzić według wartości, które są istotne dla organizacji.

W tym artykule skoncentrowano się na zadaniach, które należy wykonać w celu zaimplementowania ładu. Aby uzyskać bardziej szczegółowe omówienie pojęć, zobacz [Governance in Azure](../articles/governance/index.yml) (Ład na platformie Azure).