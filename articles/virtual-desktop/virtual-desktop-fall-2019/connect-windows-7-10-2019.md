---
title: Łączenie z pulpitem wirtualnym systemu Windows — 2019 Windows 10 lub 7 — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta pulpitu systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 78c400ffc6a84696a82c219294f133b729754259
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262030"
---
# <a name="connect-with-the-windows-desktop-client"></a>Łączenie się z klientem klasycznym systemu Windows

> Dotyczy: Windows 7, Windows 10 i Windows 10 IoT Enterprise

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../connect-windows-7-10.md).

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows na urządzeniach z systemem Windows 7, Windows 10 i Windows 10 IoT Enterprise przy użyciu klienta klasycznego systemu Windows.

>[!NOTE]
>Klient systemu Windows automatycznie domyślnie przyjmowana jest wersja 2019 pulpitu wirtualnego systemu Windows. Jeśli jednak klient wykryje, że użytkownik ma także zasoby Azure Resource Manager, automatycznie doda zasoby lub powiadomi użytkownika, że są one dostępne.

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows nie obsługuje klienta połączenia programów RemoteApp i pulpitu (RADC) ani klienta Podłączanie pulpitu zdalnego (MSTSC).

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows obecnie nie obsługuje klienta Pulpit zdalny ze sklepu Windows.

## <a name="install-the-windows-desktop-client"></a>Instalowanie klienta klasycznego systemu Windows

Wybierz klienta zgodnego z Twoją wersją systemu Windows:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [ARM64 systemu Windows](https://go.microsoft.com/fwlink/?linkid=2098961)

Klienta programu można zainstalować dla bieżącego użytkownika, który nie wymaga uprawnień administratora, lub administrator może zainstalować i skonfigurować klienta tak, aby wszyscy użytkownicy na urządzeniu mogli uzyskać do niego dostęp.

Po zainstalowaniu klienta programu można uruchomić z menu Start, wyszukując **pulpit zdalny**.

## <a name="subscribe-to-a-workspace"></a>Subskrybowanie obszaru roboczego

Istnieją dwa sposoby subskrybowania obszaru roboczego. Klient może próbować wykryć zasoby dostępne dla Ciebie z konta służbowego lub szkolnego lub bezpośrednio określić adres URL, pod którym są używane zasoby, w przypadku których klient nie może go znaleźć. Po zasubskrybowaniu obszaru roboczego można uruchamiać zasoby z jedną z następujących metod:

- Przejdź do centrum połączenia i kliknij dwukrotnie zasób, aby go uruchomić.
- Możesz również przejść do menu Start i poszukać folderu z nazwą obszaru roboczego lub wprowadzić nazwę zasobu na pasku wyszukiwania.

### <a name="subscribe-with-a-user-account"></a>Subskrybowanie przy użyciu konta użytkownika

1. Na stronie głównej klienta wybierz pozycję **Subskrybuj**.
2. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.
3. Zasoby będą widoczne w centrum połączenia i są pogrupowane według obszaru roboczego.

### <a name="subscribe-with-a-url"></a>Subskrybowanie przy użyciu adresu URL

1. Na stronie głównej klienta wybierz opcję **Subskrybuj z adresem URL**.
2. Wprowadź adres URL obszaru roboczego lub adres e-mail:
   - Jeśli używasz **adresu URL obszaru roboczego**, użyj tego administratora. W przypadku uzyskiwania dostępu do zasobów z pulpitu wirtualnego systemu Windows można użyć jednego z następujących adresów URL:
     - Pulpit wirtualny systemu Windows 2019:`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Windows Virtual Desktop wiosna 2020:`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - Jeśli zamiast tego używasz pola **e-mail** , wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował [odnajdywanie poczty e-mail](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Wybierz pozycję **Dalej**.
4. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.
5. Zasoby powinny być wyświetlane w centrum połączeń pogrupowane według obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta klasycznego systemu Windows, zapoznaj się z tematem [wprowadzenie do klienta klasycznego systemu Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
