---
title: Nawiązywanie połączenia z programem Windows Virtual Desktop (klasyczny) Windows 10 lub 7 — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows (klasycznym) przy użyciu klienta pulpitu systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 420c507361e3e2437366e6ccf2d46a8b78684e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008364"
---
# <a name="connect-with-the-windows-desktop-classic-client"></a>Nawiązywanie połączenia z komputerem stacjonarnym z systemem Windows (klasycznym)

> Dotyczy: Windows 7, Windows 10 i Windows 10 IoT Enterprise

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../connect-windows-7-10.md).

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows na urządzeniach z systemem Windows 7, Windows 10 i Windows 10 IoT Enterprise przy użyciu klienta klasycznego systemu Windows. Klient nie obsługuje systemu Windows 8 ani Windows 8.1.

>[!NOTE]
>Klient systemu Windows automatycznie domyślnie używa pulpitu wirtualnego systemu Windows (klasycznego). Jeśli jednak klient wykryje, że użytkownik ma także zasoby Azure Resource Manager, automatycznie doda zasoby lub powiadomi użytkownika, że są one dostępne.

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
     - Pulpit wirtualny systemu Windows (klasyczny): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Pulpit wirtualny systemu Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - Jeśli zamiast tego używasz pola **e-mail** , wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował [odnajdywanie poczty e-mail](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Wybierz opcję **Dalej**.
4. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.
5. Zasoby powinny być wyświetlane w centrum połączeń pogrupowane według obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta klasycznego systemu Windows, zapoznaj się z tematem [wprowadzenie do klienta klasycznego systemu Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
