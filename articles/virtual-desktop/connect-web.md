---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows przy użyciu klienta sieci Web — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta sieci Web.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a4b22966c3d4db268e212bb3f2d1bbb78fee74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400640"
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
>
> Jeśli używasz portalu US Gov, użyj polecenia <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Jeśli zalogowano się już przy użyciu innego konta Azure Active Directory niż ten, który ma być używany dla pulpitu wirtualnego systemu Windows, należy się wylogować lub użyć prywatnego okna przeglądarki.

Po zalogowaniu powinna zostać wyświetlona lista zasobów. Zasoby można uruchamiać, wybierając takie jak normalna aplikacja na karcie **wszystkie zasoby** .

## <a name="using-an-input-method-editor"></a>Korzystanie z edytora metody wejściowej

Klient sieci Web obsługuje używanie edytora IME (Input Method Editor) w sesji zdalnej w wersji **1.0.21.16 lub nowszej**. Pakiet językowy dla klawiatury, który ma być używany w sesji zdalnej, musi być zainstalowany na maszynie wirtualnej hosta. Aby dowiedzieć się więcej o konfigurowaniu pakietów językowych w sesji zdalnej, zapoznaj [się z tematem dodawanie pakietów językowych do obrazu z wieloma sesjami systemu Windows 10](language-packs.md).

Aby włączyć dane wejściowe edytora IME przy użyciu klienta sieci Web:

1. Przed nawiązaniem połączenia z sesją zdalną przejdź do panelu **ustawień** klienta sieci Web.

2. Przełącz ustawienie **Włącz Edytor metody wejściowej** na wartość **włączone**.

3. Z menu rozwijanego wybierz klawiaturę, która będzie używana w sesji zdalnej.

4. Nawiąż połączenie z sesją zdalną.

Klient sieci Web pomija lokalne okno edytora IME, gdy użytkownik nawiąże się z sesją zdalną. Zmiana ustawień edytora IME po nawiązaniu połączenia z sesją zdalną nie będzie miała żadnego efektu.

>[!NOTE]
>Jeśli pakiet językowy nie jest zainstalowany na maszynie wirtualnej hosta, Sesja zdalna będzie domyślnie w języku angielskim (Stany Zjednoczone).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o używaniu klienta sieci Web, zapoznaj się z tematem Rozpoczynanie [pracy z klientem sieci Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
