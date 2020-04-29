---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows w systemie iOS — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta systemu iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128256"
---
# <a name="connect-with-the-ios-client"></a>Nawiązywanie połączenia z klientem systemu iOS

> Dotyczy: iOS 13,0 lub nowszy. Zgodne z iPhone, iPad i iPod touch.

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzenia z systemem iOS za pomocą klienta do pobrania. W tym przewodniku przedstawiono sposób konfigurowania klienta systemu iOS.

## <a name="install-the-ios-client"></a>Instalowanie klienta systemu iOS

Aby rozpocząć, [Pobierz](https://aka.ms/rdios) i zainstaluj klienta na urządzeniu z systemem iOS.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj źródło danych dostarczone przez administratora, aby uzyskać listę zasobów zarządzanych, do których możesz uzyskać dostęp na urządzeniu z systemem iOS.

Aby subskrybować źródło danych:

1. W centrum połączenia naciśnij **+** pozycję, a następnie naciśnij pozycję **Dodaj obszar roboczy**.
2. Wprowadź adres URL źródła danych w polu **adres URL źródła danych** . Adres URL źródła danych może być adresem URL lub adresem e-mail.
   - Jeśli używasz adresu URL, użyj tego administratora. Zwykle jest <https://rdweb.wvd.microsoft.com>to adres URL.
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
3. Naciśnij przycisk **dalej**.
4. Po wyświetleniu monitu podaj swoje poświadczenia.
   - W polu **Nazwa użytkownika**Nadaj nazwę użytkownikowi uprawnienia dostępu do zasobów.
   - W polu **hasło**Podaj hasło skojarzone z nazwą użytkownika.
   - Może być również wyświetlony monit o podanie dodatkowych czynników, jeśli administrator skonfigurował uwierzytelnianie w taki sposób.
5. Naciśnij pozycję **Zapisz**.

Po wykonaniu tej czynności centrum połączenia powinno wyświetlić zasoby zdalne.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta systemu iOS, zapoznaj się z dokumentacją dotyczącą rozpoczynania [pracy z klientem systemu iOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
