---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows z macOS — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 79eaefb5cd8181bd471618761255bf38f464d735
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614917"
---
# <a name="connect-with-the-macos-client"></a>Nawiązywanie połączenia z klientem systemu macOS

> Dotyczy: macOS 10,12 lub nowszego

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../connect-macos.md).

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzeń macOS za pomocą klienta do pobrania. Ten przewodnik zawiera informacje na temat konfigurowania klienta programu.

## <a name="install-the-client"></a>Instalacja klienta

Aby rozpocząć, [Pobierz](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) i zainstaluj klienta programu na urządzeniu macOS.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj kanał informacyjny Twojego administratora, aby uzyskać listę zasobów zarządzanych dostępnych dla Ciebie na urządzeniu macOS.

Aby subskrybować źródło danych:

1. Wybierz pozycję **Dodaj obszar roboczy** na stronie głównej, aby nawiązać połączenie z usługą i pobrać zasoby.
2. Wprowadź adres URL źródła danych. Może to być adres URL lub adres e-mail:
   - Jeśli używasz adresu URL, użyj tego administratora. Zwykle jest <https://rdweb.wvd.microsoft.com>to adres URL.
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
3. Wybierz pozycję **Dodaj**.
4. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.

Po zalogowaniu powinna zostać wyświetlona lista dostępnych zasobów.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o kliencie macOS, zapoznaj się z dokumentacją dotyczącą rozpoczynania [pracy z klientem programu macOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .