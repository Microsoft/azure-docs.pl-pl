---
title: Nawiązywanie połączenia z programem Windows Virtual Desktop (klasyczny) z systemu Android — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows (klasycznym) przy użyciu klienta systemu Android.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b0fadcc592f5dd52bf1b72dc9d5658866ffba7f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008478"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-android-client"></a>Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows (klasycznym) przy użyciu klienta systemu Android

> Dotyczy: system Android 4,1 i nowsze, Chromebooks z ChromeOS 53 i nowszymi.

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../connect-android.md).

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzenia z systemem Android za pomocą klienta do pobrania. Klienta systemu Android można także używać na urządzeniach Chromebook, które obsługują Sklep Google Play. W tym przewodniku przedstawiono sposób konfigurowania klienta systemu Android.

## <a name="install-the-android-client"></a>Instalowanie klienta systemu Android

Aby rozpocząć, [Pobierz](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) i zainstaluj klienta na urządzeniu z systemem Android.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj źródło danych dostarczone przez administratora, aby uzyskać listę zasobów zarządzanych, do których możesz uzyskać dostęp na urządzeniu z systemem Android.

Aby subskrybować źródło danych:

1. W centrum połączenia naciśnij pozycję **+** , a następnie naciśnij pozycję **zdalne źródło zasobów**.
2. Wprowadź adres URL źródła danych w polu **adres URL źródła danych** . Adres URL źródła danych może być adresem URL lub adresem e-mail.
   - Jeśli używasz adresu URL, użyj tego administratora, zwykle <https://rdweb.wvd.microsoft.com> .
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Klient będzie szukać adresu URL skojarzonego z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
3. Naciśnij pozycję **DALEJ**.
4. Po wyświetleniu monitu podaj swoje poświadczenia.
   - W polu **Nazwa użytkownika** Nadaj nazwę użytkownikowi uprawnienia dostępu do zasobów.
   - W polu **hasło** Podaj hasło skojarzone z nazwą użytkownika.
   - Może być również wyświetlony monit o podanie dodatkowych czynników, jeśli administrator skonfigurował uwierzytelnianie w taki sposób.

Po zasubskrybowaniu centrum połączeń powinno wyświetlić zasoby zdalne.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta systemu Android, zapoznaj się z tematem Rozpoczynanie [pracy z klientem systemu Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).