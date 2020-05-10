---
title: Podłączanie klienta sieci Web pulpitu wirtualnego systemu Windows — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta sieci Web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c264006d51f8f0f9ee2a56e994a0dc52d24bd7a8
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006037"
---
# <a name="connect-with-the-web-client"></a>Łączenie się z klientem internetowym

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../connect-web.md).

Klient sieci Web umożliwia dostęp do zasobów pulpitu wirtualnego systemu Windows z przeglądarki sieci Web bez długotrwałego procesu instalacji.

>[!NOTE]
>Klient sieci Web nie ma obecnie obsługi systemu operacyjnego dla urządzeń przenośnych.

## <a name="supported-operating-systems-and-browsers"></a>Obsługiwane systemy operacyjne i przeglądarki

Chociaż każda przeglądarka z obsługą języka HTML5 powinna działać, oficjalnie obsługujemy następujące systemy operacyjne i przeglądarki.

| Przeglądarka           | Obsługiwane systemy operacyjne                     | Uwagi               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Wersja 55 lub nowsza |
| Google Chrome     | Windows, macOS, Linux, system operacyjny Chrome |                     |

## <a name="access-remote-resources-feed"></a>Uzyskiwanie dostępu do źródeł danych zdalnych

W przeglądarce przejdź do [klienta sieci Web pulpitu wirtualnego systemu Windows](https://rdweb.wvd.microsoft.com/webclient) i zaloguj się przy użyciu konta użytkownika.

>[!NOTE]
>Jeśli używasz wersji 2020 pulpitu wirtualnego z systemem Windows, Użyj linku klient sieci Web w [tym artykule](../connect-web.md) , aby nawiązać połączenie z zasobami.

>[!NOTE]
>Jeśli zalogowano się już przy użyciu innego konta Azure Active Directory niż ten, który ma być używany dla pulpitu wirtualnego systemu Windows, należy się wylogować lub użyć prywatnego okna przeglądarki.

Po zalogowaniu powinna zostać wyświetlona lista zasobów. Zasoby można uruchamiać, wybierając takie jak normalna aplikacja na karcie **wszystkie zasoby** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o używaniu klienta sieci Web, zapoznaj się z tematem Rozpoczynanie [pracy z klientem sieci Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
