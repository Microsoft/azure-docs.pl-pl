---
title: dołączanie pliku
description: dołączanie pliku
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: 08183cc2cbe2fce83bc7347da22308931b99dcc1
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010939"
---
Dla każdego typu obiektu dla Azure Policy istnieje maksymalna liczba. Wpis _Zakres_ oznacza subskrypcję lub [grupę zarządzania](../articles/governance/management-groups/overview.md).

| Lokalizacja | Co | Maksymalna liczba |
|---|---|---|
| Zakres | Definicje zasad | 500 |
| Zakres | Definicje inicjatyw | 200 |
| Dzierżawa | Definicje inicjatyw | 2500 |
| Zakres | Zasady lub przypisania inicjatywy | 200 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 1000 |
| Definicja inicjatywy | Parametry | 100 |
| Zasady lub przypisania inicjatywy | Wykluczenia (notScopes) | 400 |
| Reguła zasad | Zagnieżdżone uwarunkowania | 512 |
| Zadanie korygowania | Zasoby | 500 |
