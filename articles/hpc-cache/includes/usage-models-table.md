---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563247"
---
| Model użycia | Tryb buforowania | Weryfikacja zaplecza | Maksymalne opóźnienie zapisu |
|--|--|--|--|
| Czytaj duże, rzadko występujące zapisy | Odczyt | Nigdy | Brak |
| Ponad 15% zapisów | Odczyt/zapis | 8 godzin | 20 minut |
| Klienci pomijają pamięć podręczną | Odczyt | 30 sekund | Brak |
| Więcej niż 15% zapisów, częste sprawdzanie zaplecza (30 sekund) | Odczyt/zapis | 30 sekund | 20 minut |
| Więcej niż 15% zapisów, częste sprawdzanie zaplecza (60 sekund) | Odczyt/zapis | 60 sekund | 20 minut |
| Większe niż 15% zapisów, częste zapisywanie | Odczyt/zapis | 30 sekund | 30 sekund |
| Czytaj duże i sprawdzaj serwer zapasowy co 3 godziny | Odczyt | 3 godziny | Brak |
