---
author: rothja
ms.service: iot-dps
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: bbe928084a758335e5fa3b0531726c77a6d599b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85838893"
---
W poniższej tabeli wymieniono limity dotyczące zasobów usługi Azure IoT Hub Device Provisioning Service.

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba usług aprowizacji urządzeń na subskrypcję platformy Azure | 10 |
| Maksymalna liczba rejestracji | 1 000 000 |
| Maksymalna liczba rejestracji | 1 000 000 |
| Maksymalna liczba grup rejestracji | 100 |
| Maksymalna liczba urzędów certyfikacji | 25 |
| Maksymalna liczba połączonych centrów IoT | 50 |
| Maksymalny rozmiar komunikatu | 96 KB|

> [!NOTE]
> Aby zwiększyć liczbę rejestracji i rejestracji w usłudze aprowizacji, skontaktuj się z firmą [Pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support/options/).

> [!NOTE]
> Zwiększenie maksymalnej liczby urzędów certyfikacji nie jest obsługiwane.

Usługa Device Provisioning ogranicza żądania w przypadku przekroczenia następujących przydziałów.

| Ograniczenie | Wartość na jednostkę |
| --- | --- |
| Operacje | 200/min/usługa |
| Rejestracje urządzeń | 200/min/usługa |
| Operacja sondowania urządzenia | 5/10 sek/urządzenie |
