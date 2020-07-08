---
title: Łączenie z pulpitem wirtualnym systemu Windows 10 lub 7 — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta pulpitu systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d333b2f8bbb660e9fd71339159d942c0520f4f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201552"
---
# <a name="connect-with-the-windows-desktop-client"></a>Łączenie się z klientem klasycznym systemu Windows

> Dotyczy: Windows 7, Windows 10 i Windows 10 IoT Enterprise

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows na urządzeniach z systemem Windows 7, Windows 10 i Windows 10 IoT Enterprise przy użyciu klienta klasycznego systemu Windows.

>[!NOTE]
>Klient systemu Windows automatycznie domyślnie przyjmowana jest wersja 2019 pulpitu wirtualnego systemu Windows. Jeśli jednak klient wykryje, że użytkownik ma także zasoby Azure Resource Manager, automatycznie doda zasoby lub powiadomi użytkownika, że są one dostępne.

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows nie obsługuje klienta połączenia programów RemoteApp i pulpitu (RADC) ani klienta Podłączanie pulpitu zdalnego (MSTSC).

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows obecnie nie obsługuje klienta Pulpit zdalny ze sklepu Windows. Obsługa tego klienta zostanie dodana w przyszłym wydaniu.

## <a name="install-the-windows-desktop-client"></a>Instalowanie klienta klasycznego systemu Windows

Wybierz klienta zgodnego z Twoją wersją systemu Windows:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [ARM64 systemu Windows](https://go.microsoft.com/fwlink/?linkid=2098961)

Klienta programu można zainstalować dla bieżącego użytkownika, który nie wymaga uprawnień administratora, lub administrator może zainstalować i skonfigurować klienta tak, aby wszyscy użytkownicy na urządzeniu mogli uzyskać do niego dostęp.

Po zainstalowaniu klienta programu można uruchomić z menu Start, wyszukując **pulpit zdalny**.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Uzyskaj listę dostępnych zasobów zarządzanych przez zasubskrybowanie kanału informacyjnego dostarczonego przez administratora. Subskrybowanie sprawia, że zasoby są dostępne na komputerze lokalnym.

Aby subskrybować źródło danych:

1. Otwórz klienta pulpitu systemu Windows.
2. Wybierz pozycję **Subskrybuj** na stronie głównej, aby nawiązać połączenie z usługą i pobrać zasoby.
3. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.

Po pomyślnym zalogowaniu powinna zostać wyświetlona lista zasobów, do których można uzyskać dostęp.

Zasoby można uruchamiać przy użyciu jednej z dwóch metod.

- Na stronie głównej klienta kliknij dwukrotnie zasób, aby go uruchomić.
- Uruchom zasób, ponieważ inne aplikacje są zwykle z menu Start.
  - Możesz również wyszukać aplikacje na pasku wyszukiwania.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego jest regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta klasycznego systemu Windows, zapoznaj się z tematem [wprowadzenie do klienta klasycznego systemu Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
