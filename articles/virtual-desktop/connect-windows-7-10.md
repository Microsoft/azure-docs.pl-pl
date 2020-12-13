---
title: Łączenie z pulpitem wirtualnym systemu Windows 10 lub 7 — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta pulpitu systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 59379dd9c3a41729466de269b52dfd3fb206eea9
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368719"
---
# <a name="connect-with-the-windows-desktop-client"></a>Łączenie się z klientem klasycznym systemu Windows

> Dotyczy: Windows 10, Windows 10 IoT Enterprise i Windows 7

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows na urządzeniach z systemem Windows 10, Windows 10 IoT Enterprise i Windows 7 przy użyciu klienta klasycznego systemu Windows. Klient nie obsługuje programu Window 8 ani Windows 8.1.

## <a name="install-the-windows-desktop-client"></a>Instalowanie klienta klasycznego systemu Windows

Wybierz klienta zgodnego z Twoją wersją systemu Windows:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [ARM64 systemu Windows](https://go.microsoft.com/fwlink/?linkid=2098961)

Klienta programu można zainstalować dla bieżącego użytkownika, który nie wymaga uprawnień administratora, lub administrator może zainstalować i skonfigurować klienta tak, aby wszyscy użytkownicy na urządzeniu mogli uzyskać do niego dostęp.

Po zainstalowaniu klienta programu można uruchomić z menu Start, wyszukując **pulpit zdalny**.

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows nie obsługuje klienta połączenia programów RemoteApp i pulpitu (RADC) ani klienta Podłączanie pulpitu zdalnego (MSTSC).

## <a name="subscribe-to-a-workspace"></a>Subskrybowanie obszaru roboczego

Istnieją dwa sposoby subskrybowania obszaru roboczego. Klient może próbować wykryć zasoby dostępne dla Ciebie z konta służbowego lub szkolnego lub bezpośrednio określić adres URL, pod którym są używane zasoby, w przypadku których klient nie może go znaleźć. Po zasubskrybowaniu obszaru roboczego można uruchamiać zasoby z jedną z następujących metod:

- Przejdź do centrum połączenia i kliknij dwukrotnie zasób, aby go uruchomić.
- Możesz również przejść do menu Start i poszukać folderu z nazwą obszaru roboczego lub wprowadzić nazwę zasobu na pasku wyszukiwania.

### <a name="subscribe-with-a-user-account"></a>Subskrybowanie przy użyciu konta użytkownika

1. Na stronie głównej klienta wybierz pozycję **Subskrybuj**.
2. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.
3. Zasoby będą widoczne w centrum połączenia i są pogrupowane według obszaru roboczego.

>[!NOTE]
>Klient systemu Windows automatycznie domyślnie używa pulpitu wirtualnego systemu Windows (klasycznego). Jeśli jednak klient wykryje, że użytkownik ma także zasoby Azure Resource Manager, automatycznie doda zasoby lub powiadomi użytkownika, że są one dostępne.

### <a name="subscribe-with-a-url"></a>Subskrybowanie przy użyciu adresu URL

1. Na stronie głównej klienta wybierz opcję **Subskrybuj z adresem URL**.
2. Wprowadź adres URL obszaru roboczego lub adres e-mail:
   - Jeśli używasz **adresu URL obszaru roboczego**, użyj tego administratora. W przypadku uzyskiwania dostępu do zasobów z pulpitu wirtualnego systemu Windows można użyć jednego z następujących adresów URL:
     - Pulpit wirtualny systemu Windows (klasyczny): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Pulpit wirtualny systemu Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
     - Pulpit wirtualny systemu Windows (US Gov): `https://rdweb.wvd.azure.us/api/arm/feeddiscovery`
   - Jeśli zamiast tego używasz pola **e-mail** , wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował [odnajdywanie poczty e-mail](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Wybierz pozycję **Dalej**.
4. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.
5. Zasoby powinny być wyświetlane w centrum połączeń pogrupowane według obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta klasycznego systemu Windows, zapoznaj się z tematem [wprowadzenie do klienta klasycznego systemu Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).

Jeśli jesteś administratorem, który interesuje więcej szczegółowych informacji na temat sposobu korzystania z pulpitu systemu Windows, sprawdź [klienta klasycznego systemu Windows dla administratorów](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).
