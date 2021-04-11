---
title: Łączenie z pulpitem wirtualnym systemu Windows 10 lub 7 — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta pulpitu systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 625662a6b67e7d30e6320fe7831e4fa7793b9c30
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447883"
---
# <a name="connect-with-the-windows-desktop-client"></a>Łączenie się z klientem klasycznym systemu Windows

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows na urządzeniach z systemem Windows 10, Windows 10 IoT Enterprise i Windows 7 przy użyciu klienta klasycznego systemu Windows. 

> [!IMPORTANT]
> Nie obsługuje to okna lub Windows 8.1.
> 
> Obsługuje to tylko Azure Resource Manager obiektów, aby obsługiwać obiekty bez Azure Resource Manager, zobacz [Connecting with Windows Desktop (klasyczny) Client](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
> 
> Nie obsługuje to klienta połączenia programów RemoteApp i pulpitu (RADC) ani klienta Podłączanie pulpitu zdalnego (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Instalowanie klienta klasycznego systemu Windows

Pobierz klienta na podstawie wersji systemu Windows:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [ARM64 systemu Windows](https://go.microsoft.com/fwlink/?linkid=2098961)

Podczas instalacji w celu określenia dostępu wybierz jedną z opcji:

- **Zainstaluj tylko dla Ciebie**
- **Zainstaluj dla wszystkich użytkowników tego komputera** (wymaga uprawnień administratora)

Aby uruchomić klienta po zakończeniu instalacji, należy użyć menu **Start** i wyszukać **pulpit zdalny**.

## <a name="subscribe-to-a-workspace"></a>Subskrybowanie obszaru roboczego

Aby subskrybować obszar roboczy, wybierz jedną z opcji:

- Korzystanie z konta służbowego i klient odnajdywania zasobów dostępnych dla Ciebie
- Użyj określonego adresu URL zasobu

Aby uruchomić zasób po zasubskrybowaniu, przejdź do **centrum połączenia** i kliknij dwukrotnie zasób.

> [!TIP]
> Aby uruchomić zasób z menu **Start** , można znaleźć folder z nazwą obszaru roboczego lub wprowadzić nazwę zasobu na pasku wyszukiwania.

### <a name="use-a-user-account"></a>Użyj konta użytkownika

1. Wybierz pozycję **Subskrybuj** na stronie głównej.
1. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.

Zasoby pogrupowane według obszaru roboczego będą widoczne w **centrum połączenia**.

   > [!NOTE]
   > Klient systemu Windows automatycznie domyślnie używa pulpitu wirtualnego systemu Windows (klasycznego). 
   > 
   > Jeśli jednak klient wykryje dodatkowe zasoby Azure Resource Manager, spowoduje to automatyczne dodanie lub powiadomienie użytkownika o tym, że są one dostępne.

### <a name="use-a-specific-url"></a>Użyj określonego adresu URL

1. Wybierz pozycję **Subskrybuj z adresem URL** na stronie głównej.
1. Wprowadź *adres URL obszaru roboczego* lub *adres e-mail*:
   - W polu **adres URL obszaru roboczego** Użyj adresu URL podanego przez administratora.

   |Dostępne zasoby|Adres URL|
   |-|-|
   |Usługa Windows Virtual Desktop (klasyczna)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Windows Virtual Desktop|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Pulpit wirtualny systemu Windows (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   
   - W przypadku **poczty e-mail** Użyj swojego adresu e-mail. 
      
   Klient znajdzie adres URL skojarzony z Twoim adresem e-mail, pod warunkiem, że administrator włączył [odnajdowanie poczty e-mail](/windows-server/remote/remote-desktop-services/rds-email-discovery).

1. Wybierz opcję **Dalej**.
1. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.

Zasoby pogrupowane według obszaru roboczego będą widoczne w **centrum połączenia**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta, zapoznaj się z tematem [wprowadzenie do klienta klasycznego systemu Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).

Jeśli jesteś administratorem, który interesuje Cię więcej o funkcjach klienta, sprawdź [klienta klasycznego systemu Windows dla administratorów](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).
