---
title: plik dołączany
description: plik dołączany
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 95c553d26a3e79b53106b933c629c5884c3e004c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84466862"
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
| Minimalny rozmiar pliku do warstwowego | V9: na podstawie rozmiaru klastra systemu plików (rozmiar klastra o podwójnym rozmiarze). Jeśli na przykład rozmiar klastra systemu plików to 4 KB, minimalny rozmiar pliku to rozmiarze 8 KB.<br> V8 i starsze: 64 KiB  | Tak |

> [!Note]  
> Punkt końcowy Azure File Sync może skalować w górę do rozmiaru udziału plików platformy Azure. Jeśli osiągnięto limit rozmiaru udziału plików platformy Azure, synchronizacja nie będzie mogła działać.
