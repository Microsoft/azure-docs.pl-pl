---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 47eae616ffc62e42448da23fb02152dae17aa548
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116682"
---
| Zasób | Cel | Limit sztywny |
|----------|--------------|------------|
| Usługi synchronizacji magazynu na region | Usługi synchronizacji magazynu 100 | Tak |
| Grupy synchronizacji na usługę synchronizacji magazynu | 200 grup synchronizacji | Tak |
| Zarejestrowane serwery na usługę synchronizacji magazynu | serwery 99 | Tak |
| Punkty końcowe chmury na grupę synchronizacji | 1 punkt końcowy w chmurze | Tak |
| Punkty końcowe serwera dla grupy synchronizacji | punkty końcowe serwera 50 | Nie |
| Punkty końcowe serwera na serwer | 30 punktów końcowych serwera | Tak |
| Obiekty systemu plików (Katalogi i pliki) dla każdej grupy synchronizacji | 100 000 000 obiektów | Nie |
| Maksymalna liczba obiektów systemu plików (katalogów i plików) w katalogu | 5 000 000 obiektów | Tak |
| Maksymalny rozmiar deskryptora zabezpieczeń obiektu (katalogów i plików) | 64 KiB | Tak |
| Rozmiar pliku | 100 GiB | Nie |
| Minimalny rozmiar pliku do warstwowego | V9 i nowsze: w zależności od rozmiaru klastra systemu plików (rozmiar klastra o podwójnym rozmiarze systemu plików). Jeśli na przykład rozmiar klastra systemu plików to 4 KB, minimalny rozmiar pliku to rozmiarze 8 KB.<br> V8 i starsze: 64 KiB  | Tak |

> [!Note]  
> Punkt końcowy Azure File Sync może skalować w górę do rozmiaru udziału plików platformy Azure. Jeśli osiągnięto limit rozmiaru udziału plików platformy Azure, synchronizacja nie będzie mogła działać.
