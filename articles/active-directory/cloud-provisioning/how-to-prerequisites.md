---
title: Wymagania wstępne dotyczące Azure AD Connect aprowizacji w chmurze w usłudze Azure AD
description: W tym artykule opisano warunki wstępne i wymagania sprzętowe wymagane do aprowizacji chmury.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f0c94ba6fb9ee5ab019458043095271123e325e
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97671016"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Wymagania wstępne aprowizacji w chmurze programu Azure AD Connect
Ten artykuł zawiera wskazówki dotyczące sposobu wybierania i używania usługi Azure Active Directory (Azure AD) w celu nawiązania połączenia z chmurą jako rozwiązania do obsługi tożsamości.

## <a name="cloud-provisioning-agent-requirements"></a>Wymagania dotyczące agenta aprowizacji w chmurze
Aby skorzystać z Azure AD Connect aprowizacji w chmurze, potrzebne są następujące elementy:

- Administrator domeny lub poświadczenia administratora przedsiębiorstwa aby utworzyć Azure AD Connect gMSA synchronizacji chmury (konto usługi zarządzane przez grupę), aby uruchomić usługę agenta. 
- Konto administratora tożsamości hybrydowej dla dzierżawy usługi Azure AD, które nie jest użytkownikiem-gościem.
- Serwer lokalny dla agenta aprowizacji z systemem Windows 2012 R2 lub nowszym.  Ten serwer powinien być serwerem warstwy 0 na podstawie [Active Directory modelu warstwy administracyjnej](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Lokalne konfiguracje zapory.

## <a name="group-managed-service-accounts"></a>Konta usług zarządzane przez grupę
Konto usługi zarządzane przez grupę to zarządzane konto domeny zapewniające automatyczne zarządzanie hasłami, uproszczone zarządzanie nazwami główna usługi (SPN), możliwość delegowania zarządzania do innych administratorów, a także rozszerza te funkcje na wiele serwerów.  Azure AD Connect Cloud Sync obsługuje i używa gMSA do uruchamiania agenta.  Podczas instalacji zostanie wyświetlony monit o podanie poświadczeń administracyjnych w celu utworzenia tego konta.  Konto będzie wyświetlane jako (domain\provAgentgMSA $).  Aby uzyskać więcej informacji na temat gMSA, zobacz [konta usług zarządzane przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 

### <a name="prerequisites-for-gmsa"></a>Wymagania wstępne dotyczące gMSA:
1.  Schemat Active Directory w lesie domeny gMSA należy zaktualizować do systemu Windows Server 2012
2.  [Moduły RSAT programu PowerShell](/windows-server/remote/remote-server-administration-tools) na kontrolerze domeny
3.  Co najmniej jeden kontroler domeny w domenie musi działać pod kontrolą systemu Windows Server 2012.
4.  Serwer przyłączony do domeny, na którym jest instalowany Agent, musi być w systemie Windows Server 2012 lub nowszym.

Aby zapoznać się z instrukcjami dotyczącymi sposobu uaktualniania istniejącego agenta do używania konta gMSA, zobacz [konta usług zarządzane przez grupę](how-to-install.md#group-managed-service-accounts).

### <a name="in-the-azure-active-directory-admin-center"></a>W centrum administracyjnym Azure Active Directory

1. Utwórz konto administratora tożsamości hybrydowej z tylko chmurą w dzierżawie usługi Azure AD. W ten sposób możesz zarządzać konfiguracją dzierżawy, jeśli usługi lokalne zakończą się niepowodzeniem lub staną się niedostępne. Dowiedz się [, jak dodać konto administratora tożsamości hybrydowej tylko do chmury](../fundamentals/add-users-azure-active-directory.md). Zakończenie tego kroku ma na celu upewnienie się, że dzierżawa nie została zablokowana.
1. Dodaj co najmniej jedną [niestandardową nazwę domeny](../fundamentals/add-custom-domain.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się przy użyciu jednej z tych nazw domen.

### <a name="in-your-directory-in-active-directory"></a>W katalogu w Active Directory

Uruchom [Narzędzie IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) , aby przygotować atrybuty katalogu do synchronizacji.

### <a name="in-your-on-premises-environment"></a>W środowisku lokalnym

1. Zidentyfikuj przyłączony do domeny serwer hosta z systemem Windows Server 2012 R2 lub nowszym z co najmniej 4 GB pamięci RAM i środowiskiem .NET 4.7.1 + Runtime.

    >[!NOTE]
    > Należy pamiętać, że zdefiniowanie filtru określania zakresu wiąże się z kosztem pamięci na serwerze hosta.  Jeśli nie jest używany filtr określania zakresu, nie ma dodatkowego kosztu pamięci. Minimum 4 GB będzie obsługiwać synchronizację dla maksymalnie 12 jednostek organizacyjnych zdefiniowanych w filtrze zakresu. Jeśli konieczne jest zsynchronizowanie dodatkowych jednostek organizacyjnych, należy zwiększyć minimalną ilość pamięci. W poniższej tabeli przedstawiono Przewodnik:
    >
    >
    > | Liczba jednostek organizacyjnych w filtrze zakresu| Minimalna wymagana pamięć|
    > | --- | --- |
    > | 12 | 4 GB |
    > | 18 | 5,5 GB|
    > | 28 | 10 + GB|

2. Zasady wykonywania programu PowerShell na serwerze lokalnym muszą mieć wartość undefined lub RemoteSigned.

3. Jeśli istnieje Zapora między serwerami i usługą Azure AD, skonfiguruj następujące elementy:
    - Upewnij się, że agenci mogą wykonywać żądania *wychodzące* do usługi Azure AD za pośrednictwem następujących portów:

      | Numer portu | Zastosowanie |
      | --- | --- |
      | **80** | Pobiera listy odwołania certyfikatów (CRL) podczas weryfikacji certyfikatu TLS/SSL.  |
      | **443** | Obsługuje całą komunikację wychodzącą z usługą. |
      |**8082**|Wymagane do instalacji i, jeśli chcesz skonfigurować swój administracyjny interfejs API.  Ten port można usunąć po zainstalowaniu agenta i jeśli nie planujesz korzystać z interfejsu API.   |
      | **8080** (opcjonalnie) | Agenci raportują swój stan co 10 minut przez port 8080, jeśli port 443 jest niedostępny. Ten stan jest wyświetlany w portalu usługi Azure AD. |

    - Jeśli Zapora wymusza reguły zależne od użytkowników inicjujących, należy otworzyć te porty dla ruchu z usług systemu Windows, które działają jako usługa sieciowa.
    - Jeśli zapora lub serwer proxy umożliwia określenie bezpiecznych sufiksów, Dodaj połączenia do \* . msappproxy.NET i \* . ServiceBus.Windows.NET. W przeciwnym razie Zezwól na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są aktualizowane co tydzień.
    - Twoje Agenci muszą mieć dostęp do login.windows.net i login.microsoftonline.com na potrzeby rejestracji wstępnej. Należy również otworzyć Zaporę dla tych adresów URL.
    - Aby sprawdzić poprawność certyfikatu, Odblokuj następujące adresy URL: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 i www \. Microsoft.com:80. Te adresy URL są używane do sprawdzania poprawności certyfikatu z innymi produktami firmy Microsoft, dzięki czemu te adresy URL mogą już być odblokowane.

    >[!NOTE]
    > Instalowanie agenta aprowizacji w chmurze w systemie Windows Server Core nie jest obsługiwane.

### <a name="additional-requirements"></a>Wymagania dodatkowe

- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Wymagania protokołu TLS

> [!NOTE]
> Transport Layer Security (TLS) to protokół, który zapewnia bezpieczną komunikację. Zmiana ustawień protokołu TLS ma wpływ na cały las. Aby uzyskać więcej informacji, zobacz [Aktualizacja umożliwiająca włączenie protokołu tls 1,1 i tls 1,2 jako domyślnych protokołów Secure w usłudze WinHTTP w systemie Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

W systemie Windows Server, który jest hostem agenta aprowizacji Azure AD Connect w chmurze, należy włączyć protokół TLS 1,2 przed jego zainstalowaniem.

Aby włączyć protokół TLS 1,2, wykonaj następujące kroki.

1. Ustaw następujące klucze rejestru:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Uruchom ponownie serwer.

## <a name="known-limitations"></a>Znane ograniczenia

Znane są następujące ograniczenia:

### <a name="delta-synchronization"></a>Synchronizacja zmian

- Filtrowanie zakresu grup dla synchronizacji różnicowej nie obsługuje więcej niż 1500 elementów członkowskich.
- Po usunięciu grupy, która jest używana jako część filtru określania zakresu grup, użytkownicy będący członkami tej grupy nie zostaną usunięci. 
- Po zmianie nazwy jednostki organizacyjnej lub grupy, która znajduje się w zakresie, synchronizacja różnicowa nie spowoduje usunięcia użytkowników.

### <a name="provisioning-logs"></a>Dzienniki aprowizacji
- Dzienniki aprowizacji nie różnią się wyraźnie od operacji tworzenia i aktualizowania.  Może zostać wyświetlona operacja tworzenia dla aktualizacji i operacji aktualizacji dla elementu Create.

### <a name="group-re-naming-or-ou-re-naming"></a>Zmiana nazwy grupy lub zmiana nazwy jednostki organizacyjnej
- W przypadku zmiany nazwy grupy lub jednostki organizacyjnej w usłudze AD, która znajduje się w zakresie dla danej konfiguracji, zadanie aprowizacji w chmurze nie będzie w stanie rozpoznać zmiany nazwy w usłudze AD. Zadanie nie zostanie objęte kwarantanną i pozostanie w dobrej kondycji.


## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)