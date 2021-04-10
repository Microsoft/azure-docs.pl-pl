---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows w systemie Android — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta systemu Android.
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 07e0754cc9fb940218999e2012a9cb87341557cb
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448087"
---
# <a name="connect-to-windows-virtual-desktop-with-the-android-client"></a>Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows przy użyciu klienta systemu Android

> Dotyczy: system Android 4,1 i nowsze, Chromebooks z ChromeOS 53 i nowszymi.

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/connect-android-2019.md).

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzenia z systemem Android za pomocą klienta do pobrania. Klienta systemu Android można także używać na urządzeniach Chromebook, które obsługują Sklep Google Play. W tym przewodniku przedstawiono sposób konfigurowania klienta systemu Android.

## <a name="install-the-android-client"></a>Instalowanie klienta systemu Android

Aby rozpocząć, [Pobierz](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) i zainstaluj klienta na urządzeniu z systemem Android.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj źródło danych dostarczone przez administratora, aby uzyskać listę zasobów zarządzanych, do których możesz uzyskać dostęp na urządzeniu z systemem Android.

Aby subskrybować źródło danych:

1. W centrum połączenia naciśnij pozycję **+** , a następnie naciśnij pozycję **zdalne źródło zasobów**.
2. Wprowadź adres URL źródła danych w polu **adres URL źródła danych** . Adres URL źródła danych może być adresem URL lub adresem e-mail.
   - Jeśli używasz adresu URL, użyj tego administratora, zwykle <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Klient będzie szukać adresu URL skojarzonego z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
   - Aby nawiązać połączenie za pomocą portalu US Gov, użyj <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Naciśnij pozycję **DALEJ**.
4. Po wyświetleniu monitu podaj swoje poświadczenia.
   - W polu **Nazwa użytkownika** Nadaj nazwę użytkownikowi uprawnienia dostępu do zasobów.
   - W polu **hasło** Podaj hasło skojarzone z nazwą użytkownika.
   - Może być również wyświetlony monit o podanie dodatkowych czynników, jeśli administrator skonfigurował uwierzytelnianie w taki sposób.

Po zasubskrybowaniu centrum połączeń powinno wyświetlić zasoby zdalne.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta systemu Android, zapoznaj się z tematem Rozpoczynanie [pracy z klientem systemu Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
