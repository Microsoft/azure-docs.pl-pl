---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows przy użyciu klienta sieci Web — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta sieci Web.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7c97009a69c937863a5776d63bab1c994a8a9160
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007611"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows przy użyciu klienta sieci Web

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/connect-web-2019.md).

Klient sieci Web umożliwia dostęp do zasobów pulpitu wirtualnego systemu Windows z przeglądarki sieci Web bez długotrwałego procesu instalacji.

>[!NOTE]
>Klient sieci Web nie ma obecnie obsługi systemu operacyjnego dla urządzeń przenośnych.

## <a name="supported-operating-systems-and-browsers"></a>Obsługiwane systemy operacyjne i przeglądarki

Chociaż każda przeglądarka z obsługą języka HTML5 powinna działać, oficjalnie obsługujemy następujące systemy operacyjne i przeglądarki.

| Przeglądarka           | Obsługiwane systemy operacyjne                     | Uwagi               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Wersja 11 lub nowsza |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Wersja 55 lub nowsza |
| Google Chrome     | Windows, macOS, Linux, system operacyjny Chrome |                     |

## <a name="access-remote-resources-feed"></a>Uzyskiwanie dostępu do źródeł danych zdalnych

W przeglądarce przejdź do Azure Resource Manager zintegrowanej wersji klienta sieci Web systemu Windows na komputerze <https://rdweb.wvd.microsoft.com/arm/webclient> i zaloguj się przy użyciu konta użytkownika.

>[!NOTE]
>Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez integracji Azure Resource Manager, w zamian Połącz się z zasobami <https://rdweb.wvd.microsoft.com/webclient> .

>[!NOTE]
>Jeśli zalogowano się już przy użyciu innego konta Azure Active Directory niż ten, który ma być używany dla pulpitu wirtualnego systemu Windows, należy się wylogować lub użyć prywatnego okna przeglądarki.

Po zalogowaniu powinna zostać wyświetlona lista zasobów. Zasoby można uruchamiać, wybierając takie jak normalna aplikacja na karcie **wszystkie zasoby** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o używaniu klienta sieci Web, zapoznaj się z tematem Rozpoczynanie [pracy z klientem sieci Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
