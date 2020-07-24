---
title: Łączenie z pulpitem wirtualnym systemu Windows 2019 z systemu iOS — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta systemu iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 26c031710c96c42b1d5236332e445992de87616e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087139"
---
# <a name="connect-with-the-ios-client"></a>Nawiązywanie połączenia z klientem systemu iOS

> Dotyczy: iOS 13,0 lub nowszy. Zgodne z iPhone, iPad i iPod touch.

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../connect-ios.md).

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzenia z systemem iOS za pomocą klienta do pobrania. W tym przewodniku przedstawiono sposób konfigurowania klienta systemu iOS.

## <a name="install-the-ios-client"></a>Instalowanie klienta systemu iOS

Aby rozpocząć, [Pobierz](https://aka.ms/rdios) i zainstaluj klienta na urządzeniu z systemem iOS.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj źródło danych dostarczone przez administratora, aby uzyskać listę zasobów zarządzanych, do których możesz uzyskać dostęp na urządzeniu z systemem iOS.

Aby subskrybować źródło danych:

1. W centrum połączenia naciśnij pozycję **+** , a następnie naciśnij pozycję **Dodaj obszar roboczy**.
2. Wprowadź adres URL źródła danych w polu **adres URL źródła danych** . Adres URL źródła danych może być adresem URL lub adresem e-mail.
   - Jeśli używasz adresu URL, użyj tego administratora. Zwykle jest to adres URL <https://rdweb.wvd.microsoft.com> .
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
3. Naciśnij opcję **Dalej**.
4. Po wyświetleniu monitu podaj swoje poświadczenia.
   - W polu **Nazwa użytkownika**Nadaj nazwę użytkownikowi uprawnienia dostępu do zasobów.
   - W polu **hasło**Podaj hasło skojarzone z nazwą użytkownika.
   - Może być również wyświetlony monit o podanie dodatkowych czynników, jeśli administrator skonfigurował uwierzytelnianie w taki sposób.
5. Naciśnij pozycję **Zapisz**.

Po wykonaniu tej czynności centrum połączenia powinno wyświetlić zasoby zdalne.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta systemu iOS, zapoznaj się z dokumentacją dotyczącą rozpoczynania [pracy z klientem systemu iOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
