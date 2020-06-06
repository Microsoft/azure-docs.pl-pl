---
title: dołączanie pliku
description: dołączanie pliku
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: e9faea1d5913a19dfdeff662e26992529dc1b22d
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84466960"
---
Dla każdego typu obiektu dla Azure Policy istnieje maksymalna liczba. Wpis _Zakres_ oznacza subskrypcję lub [grupę zarządzania](../articles/governance/management-groups/overview.md).

| Lokalizacja | Elementy | Maksymalna liczba |
|---|---|---|
| Zakres | Definicje zasad | 500 |
| Zakres | Definicje inicjatyw | 100 |
| Dzierżawca | Definicje inicjatyw | 2500 |
| Zakres | Zasady lub przypisania inicjatywy | 100 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 100 |
| Definicja inicjatywy | Parametry | 100 |
| Zasady lub przypisania inicjatywy | Wykluczenia (notScopes) | 400 |
| Reguła zasad | Zagnieżdżone uwarunkowania | 512 |
| Zadanie korygowania | Zasoby | 500 |
