---
title: Łączenie z pulpitem wirtualnym systemu Windows 10 lub 7 — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta pulpitu systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1f813d0ada516f6090b97e5858cefab110636f90
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077599"
---
# <a name="connect-with-the-windows-desktop-client"></a>Łączenie się z klientem klasycznym systemu Windows

> Dotyczy: Windows 7, Windows 10 i Windows 10 IoT Enterprise

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows na urządzeniach z systemem Windows 7, Windows 10 i Windows 10 IoT Enterprise przy użyciu klienta klasycznego systemu Windows. Klient nie obsługuje programu Window 8 ani Windows 8.1.

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
3. Wybierz przycisk **Dalej**.
4. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.
5. Zasoby powinny być wyświetlane w centrum połączeń pogrupowane według obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta klasycznego systemu Windows, zapoznaj się z tematem [wprowadzenie do klienta klasycznego systemu Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
