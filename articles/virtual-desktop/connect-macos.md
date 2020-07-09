---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows z macOS — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b4696886c898eec5895c4ccb59347cf2a49f03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208998"
---
# <a name="connect-with-the-macos-client"></a>Nawiązywanie połączenia z klientem systemu macOS

> Dotyczy: macOS 10,12 lub nowszego

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/connect-macos-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzeń macOS za pomocą klienta do pobrania. Ten przewodnik zawiera informacje na temat konfigurowania klienta programu.

## <a name="install-the-client"></a>Instalacja klienta

Aby rozpocząć, [Pobierz](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)   i zainstaluj klienta programu na urządzeniu macOS.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj kanał informacyjny Twojego administratora, aby uzyskać listę zasobów zarządzanych dostępnych dla Ciebie na urządzeniu macOS.

Aby subskrybować źródło danych:

1. Wybierz pozycję **Dodaj obszar roboczy** na stronie głównej, aby nawiązać połączenie z usługą i pobrać zasoby.
2. Wprowadź adres URL źródła danych. Może to być adres URL lub adres e-mail:
   - Jeśli używasz adresu URL, użyj tego administratora. Zwykle jest to adres URL <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
3. Wybierz pozycję **Dodaj**.
4. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.

Po zalogowaniu powinna zostać wyświetlona lista dostępnych zasobów.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o kliencie macOS, zapoznaj się z dokumentacją dotyczącą rozpoczynania [pracy z klientem programu macOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .
