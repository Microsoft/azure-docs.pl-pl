---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75477150"
---
Ta dokumentacja zawiera szczegóły dotyczące skalowalności i wydajności dla usługi Azure Storage. Elementy docelowe skalowalności i wydajności wymienione w tym miejscu są obiektami docelowymi wysokiej klasy, ale są osiągalne. We wszystkich przypadkach częstotliwość żądań i przepustowość osiągnięta przez konto magazynu zależy od rozmiaru przechowywanych obiektów, użytych wzorców dostępu oraz typu obciążenia wykonywanego przez aplikację.

Upewnij się, że usługa została przetestowana, aby określić, czy jej wydajność spełnia Twoje wymagania. Jeśli to możliwe, unikaj nagłych skoków natężenia ruchu i upewnij się, że ruch jest prawidłowo dystrybuowany między partycjami.

Gdy aplikacja osiągnie limit, co może obsłużyć partycja w obciążeniu, usługa Azure Storage zaczyna zwracać kod błędu 503 (serwer zajęty) lub kod błędu 500 (limit czasu operacji). Jeśli wystąpią błędy 503, Rozważ zmodyfikowanie aplikacji tak, aby korzystała z zasad wycofywania wykładniczych na potrzeby ponownych prób. Wykładniczy wycofywania umożliwia zmniejszenie obciążenia partycji i ułatwienie wzrostu ruchu do tej partycji.
