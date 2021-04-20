---
title: Nawiązywanie połączenia Windows Virtual Desktop za pomocą klienta internetowego — Azure
description: Jak nawiązać połączenie z Windows Virtual Desktop przy użyciu klienta internetowego.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 80a090abee45f9cb3ec6ee5406aad6abf1d73a59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725012"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Nawiązywanie połączenia Windows Virtual Desktop z klientem internetowym

>[!IMPORTANT]
>Ta zawartość dotyczy Windows Virtual Desktop z Azure Resource Manager Windows Virtual Desktop obiektów. Jeśli używasz klasy Windows Virtual Desktop (klasycznej) bez obiektów Azure Resource Manager, zobacz [ten artykuł.](./virtual-desktop-fall-2019/connect-web-2019.md)

Klient internetowy umożliwia dostęp do zasobów Windows Virtual Desktop z przeglądarki internetowej bez długiego procesu instalacji.

>[!NOTE]
>Klient internetowy nie ma obecnie obsługi systemu operacyjnego dla urządzeń przenośnych.

## <a name="supported-operating-systems-and-browsers"></a>Obsługiwane systemy operacyjne i przeglądarki

Chociaż każda przeglądarka obsługująca język HTML5 powinna działać, oficjalnie obsługujemy następujące systemy operacyjne i przeglądarki.

| Przeglądarka           | Obsługiwany system operacyjny                     | Uwagi               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Wersja 11 lub nowsza |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Wersja 55 lub nowsza |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Uzyskiwanie dostępu do źródła danych zasobów zdalnych

W przeglądarce przejdź do zintegrowanej Azure Resource Manager klienta internetowego usługi Windows Virtual Desktop na stronie i zaloguj się <https://rdweb.wvd.microsoft.com/arm/webclient> przy użyciu konta użytkownika.

>[!NOTE]
>Jeśli korzystasz z usługi Windows Virtual Desktop (klasycznej) bez integracji Azure Resource Manager, zamiast tego połącz się ze swoimi zasobami na <https://rdweb.wvd.microsoft.com/webclient> stronie .
>
> Jeśli używasz portalu US Gov, użyj . <https://rdweb.wvd.azure.us/arm/webclient/index.html>

>[!NOTE]
>Jeśli już zalogowano się przy użyciu innego konta Azure Active Directory niż konto, którego chcesz użyć na Windows Virtual Desktop, wyloguj się lub użyj prywatnego okna przeglądarki.

Po zalogowaniu powinna zostać wyświetlona lista zasobów. Zasoby można uruchamiać, wybierając je w taki sposób, jak w przypadku zwykłej aplikacji na **karcie Wszystkie** zasoby.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta internetowego, zapoznaj się [z tematem Rozpoczynanie pracy z klientem internetowym.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)
