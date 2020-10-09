---
title: plik dołączania
description: plik dołączania
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d1e7fa1ed1649508f0d4923db8817d17ad556ca1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183161"
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

W tym artykule skoncentrowano się na zadaniach, które należy wykonać w celu zaimplementowania ładu. Aby uzyskać bardziej szczegółowe omówienie pojęć, zobacz [Governance in Azure](../articles/security/governance-in-azure.md) (Ład na platformie Azure). 
