---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67712395"
---
Źródłem jest moduł zbierający dane Open Source służący do rejestrowania ujednoliconego. `Fluentd`Ustawienia zarządzają połączeniem kontenera z serwerem pośrednim. [Fluentd](https://www.fluentd.org) Kontener obejmuje pozostały dostawca rejestrowania, który umożliwia kontenerowi zapisywanie dzienników i, opcjonalnie, danych metryk na serwerze pośrednim.

W poniższej tabeli opisano ustawienia konfiguracji obsługiwane w `Fluentd` sekcji.

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `Host` | Ciąg | Adres IP lub nazwa hosta DNS serwera Fluent. |
| `Port` | Liczba całkowita | Port serwera Fluent.<br/> Wartość domyślna to 24224. |
| `HeartbeatMs` | Liczba całkowita | Interwał pulsu (w milisekundach). Jeśli żaden ruch zdarzenia nie został wysłany przed upływem tego interwału, zostanie wysłany puls do serwera z systemem. Wartość domyślna to 60000 milisekund (1 minuta). |
| `SendBufferSize` | Liczba całkowita | Przestrzeń buforu sieciowego (w bajtach) przypisana do operacji wysyłania. Wartość domyślna to 32768 bajtów (32 kilobajtów). |
| `TlsConnectionEstablishmentTimeoutMs` | Liczba całkowita | Limit czasu (w milisekundach) w celu nawiązania połączenia SSL/TLS z serwerem Fluent. Wartość domyślna to 10000 milisekund (10 sekund).<br/> Jeśli `UseTLS` jest ustawiona na false, ta wartość jest ignorowana. |
| `UseTLS` | Boolean (wartość logiczna) | Wskazuje, czy kontener ma używać protokołu SSL/TLS do komunikacji z serwerem pośrednim. Wartość domyślna to false. |