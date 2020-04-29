---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b8d60da507aa1eb312d1a8505f756ff337085ff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77592434"
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
