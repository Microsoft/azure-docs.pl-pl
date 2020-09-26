---
title: Co to jest magazyn OPC — platforma Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie magazynu OPC. Może on konfigurować, rejestrować i zarządzać cyklem życia certyfikatów dla aplikacji OPC UA w chmurze.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 715ed204e28d6260c28fa099b40fc78aa12de44d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281545"
---
# <a name="what-is-opc-vault"></a>Co to jest magazyn OPC?

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

Magazyn OPC to mikrousługa, która pozwala konfigurować, rejestrować i zarządzać cyklem życia certyfikatu dla serwera OPC UA i aplikacji klienckich w chmurze. W tym artykule opisano proste przypadki użycia magazynu OPC.

## <a name="certificate-management"></a>Zarządzanie certyfikatami

Na przykład firma produkcyjna musi podłączyć swój komputer serwera OPC UA do nowo skompilowanej aplikacji klienckiej. Gdy producent dokonuje początkowego dostępu do komputera serwera, komunikat o błędzie jest natychmiast wyświetlany w aplikacji serwera OPC UA, aby wskazać, że aplikacja kliencka nie jest zabezpieczona. Ten mechanizm jest zbudowany na komputerze serwera OPC UA, aby zapobiec nieautoryzowanemu dostępowi do aplikacji, co uniemożliwia Vicious działanie hakerskie w pracy warsztatowej.

## <a name="application-security-management"></a>Zarządzanie zabezpieczeniami aplikacji
Specjalista ds. zabezpieczeń korzysta z mikrousługi OPC Storage, aby łatwo włączyć serwer OPC UA do komunikowania się z dowolnymi aplikacjami klienckimi, ponieważ magazyn OPC ma wszystkie funkcje dla rejestru certyfikatów, magazynu i zarządzania cyklem życia. Teraz serwer OPC UA jest bezpiecznie połączony, może komunikować się z nowo zbudowaną aplikacją kliencką

## <a name="the-complete-opc-vault-architecture"></a>Kompletna architektura magazynu OPC
Na poniższym diagramie przedstawiono kompletną architekturę magazynu OPC.

![Architektura magazynu OPC](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy znasz już magazyn OPC i jego zastosowania, Oto sugerowany następny krok:

[Architektura magazynu OPC](overview-opc-vault-architecture.md)