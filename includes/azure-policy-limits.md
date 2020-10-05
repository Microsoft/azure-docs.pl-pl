---
title: plik dołączania
description: plik dołączania
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91605927"
---
Dla każdego typu obiektu dla Azure Policy istnieje maksymalna liczba. W przypadku definicji wpis _zakresu_ oznacza grupę lub subskrypcję [zarządzania](../articles/governance/management-groups/overview.md) .
W przypadku przypisań i wykluczeń, wpis _zakresu_ oznacza [grupę zarządzania](../articles/governance/management-groups/overview.md), subskrypcję, grupę zasobów lub pojedynczy zasób.

| Lokalizacja | Co | Maksymalna liczba |
|---|---|---|
| Zakres | Definicje zasad | 500 |
| Zakres | Definicje inicjatyw | 200 |
| Dzierżawa | Definicje inicjatyw | 2500 |
| Zakres | Zasady lub przypisania inicjatywy | 200 |
| Zakres | Wykluczenia | 1000 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 1000 |
| Definicja inicjatywy | Parametry | 100 |
| Zasady lub przypisania inicjatywy | Wykluczenia (notScopes) | 400 |
| Reguła zasad | Zagnieżdżone uwarunkowania | 512 |
| Zadanie korygowania | Zasoby | 500 |
