---
title: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen zarządzanych | Microsoft Docs
description: Dowiedz się, jak skonfigurować hybrydowe dołączanie do usługi Azure Active Directory dla domen zarządzanych.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4545e92767a427b8cd89af07ed4d06053685977a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578009"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Samouczek: konfigurowanie dołączania hybrydowego do usługi Azure Active Directory dla domen zarządzanych

W ramach tego samouczka nauczysz się konfigurować hybrydowe Azure Active Directory (Azure AD) Join dla Active Directory urządzeń przyłączonych do domeny. Ta metoda obsługuje środowisko zarządzane, które obejmuje zarówno lokalne Active Directory, jak i usługę Azure AD.

Podobnie jak w przypadku użytkownika w organizacji, urządzenie to podstawowa tożsamość, która ma być chroniona. Możesz użyć tożsamości urządzenia do ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji. Ten cel można osiągnąć przez Zarządzanie tożsamościami urządzeń w usłudze Azure AD. Użyj jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Ten artykuł koncentruje się na dołączeniu do hybrydowej usługi Azure AD.

Przełączenie urządzeń do usługi Azure AD maksymalizuje produktywność użytkowników dzięki logowaniem jednokrotnym (SSO) w chmurze i zasobach lokalnych. Jednocześnie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../conditional-access/howto-conditional-access-policy-compliant-device.md) .

Środowisko zarządzane można wdrożyć przy użyciu funkcji [synchronizacji skrótów haseł (PHS)](../hybrid/whatis-phs.md) lub [uwierzytelniania przekazywanego (PTA)](../hybrid/how-to-connect-pta.md) z [bezproblemowym logowaniem jednokrotnym](../hybrid/how-to-connect-sso.md). Te scenariusze nie wymagają konfigurowania serwera federacyjnego na potrzeby uwierzytelniania.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie dołączenia hybrydowego do usługi Azure AD
> * Włączanie urządzeń z systemem Windows niższego poziomu
> * Weryfikowanie dołączonych urządzeń
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 lub nowszy)
- Poświadczenia administratora globalnego dla dzierżawy usługi Azure AD
- Poświadczenia administratora przedsiębiorstwa dla każdego z lasów

Zapoznaj się z następującymi artykułami:

- [Co to jest tożsamość urządzenia?](overview.md)
- [Instrukcje: planowanie implementacji dołączania hybrydowego Azure Active Directory](hybrid-azuread-join-plan.md)
- [Kontrolowana walidacja przyłączenia do hybrydowej usługi Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Usługa Azure AD nie obsługuje kart inteligentnych ani certyfikatów w domenach zarządzanych.

Sprawdź, czy Azure AD Connect synchronizuje obiekty komputerów urządzeń, które mają być przyłączone do usługi Azure AD jako hybrydowe. Jeśli obiekty komputera należą do określonych jednostek organizacyjnych (OU), należy skonfigurować jednostki organizacyjne do synchronizacji w Azure AD Connect. Aby dowiedzieć się więcej o synchronizowaniu obiektów komputerów przy użyciu Azure AD Connect, zobacz [filtrowanie na podstawie jednostki organizacyjnej](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Aby uzyskać powodzenie przyłączenia synchronizacji rejestracji urządzeń w ramach konfiguracji rejestracji urządzenia, nie należy wykluczać domyślnych atrybutów urządzenia z konfiguracji synchronizacji Azure AD Connect. Aby dowiedzieć się więcej o domyślnych atrybutach urządzenia synchronizowanych do usługi AAD, zobacz [atrybuty synchronizowane przez Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Począwszy od wersji 1.1.819.0, Azure AD Connect zawiera Kreatora konfiguracji hybrydowego sprzężenia usługi Azure AD. Kreator znacznie upraszcza proces konfiguracji. Kreator konfiguruje punkty połączenia usługi (punktu SCP) na potrzeby rejestracji urządzeń.

Kroki konfiguracji opisane w tym artykule opierają się na użyciu kreatora w Azure AD Connect.

Hybrydowe dołączenie usługi Azure AD wymaga, aby urządzenia miały dostęp do następujących zasobów firmy Microsoft z sieci organizacji:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Jeśli używasz lub planujesz korzystanie z bezproblemowego logowania jednokrotnego)

> [!WARNING]
> Jeśli Twoja organizacja korzysta z serwerów proxy, które przechwytuje ruch SSL w scenariuszach takich jak ochrona przed utratą danych lub ograniczenia dzierżawy usługi Azure AD, upewnij się, że ruch do elementu " https://device.login.microsoftonline.com " jest wykluczony z funkcji Break-and-Inspekcja TLS. Niepowodzenie wykluczenia " https://device.login.microsoftonline.com " może spowodować zakłócenia przy użyciu uwierzytelniania certyfikatu klienta, powodując problemy dotyczące rejestracji urządzeń i dostępu warunkowego opartego na urządzeniach.

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem wychodzącego serwera proxy, można użyć [implementującego funkcję autowykrywania serwera proxy sieci Web (WPAD)](/previous-versions/tn-archive/cc995261(v=technet.10)) w celu umożliwienia komputerom z systemem Windows 10 rejestracji urządzeń w usłudze Azure AD. Aby rozwiązać problemy dotyczące konfigurowania usługi WPAD i zarządzania nią, zobacz [Rozwiązywanie problemów z automatycznym wykryciem](/previous-versions/tn-archive/cc302643(v=technet.10)). W przypadku urządzeń z systemem Windows 10 przed aktualizacją 1709 usługa WPAD jest jedyną dostępną opcją w celu skonfigurowania serwera proxy do pracy z hybrydowym sprzężeniem usługi Azure AD. 

Jeśli nie korzystasz z usługi WPAD, możesz skonfigurować ustawienia serwera proxy WinHTTP na komputerze, rozpoczynając od systemu Windows 10 1709. Aby uzyskać więcej informacji, zobacz [Ustawienia serwera proxy WinHTTP wdrożone przez obiekt zasad grupy](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> W przypadku skonfigurowania ustawień serwera proxy na komputerze przy użyciu ustawień usługi WinHTTP wszystkie komputery, które nie mogą nawiązać połączenia ze skonfigurowanym serwerem proxy, nie będą mogły połączyć się z Internetem.

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego wychodzącego serwera proxy, upewnij się, że komputery z systemem Windows 10 mogą pomyślnie uwierzytelniać się na wychodzącym serwerze proxy. Ponieważ komputery z systemem Windows 10 uruchamiają rejestrację urządzeń przy użyciu kontekstu komputera, Skonfiguruj uwierzytelnianie wychodzącego serwera proxy przy użyciu kontekstu komputera. Skontaktuj się z dostawcą serwera proxy ruchu wychodzącego, aby uzyskać informacje na temat wymagań dotyczących konfiguracji.

Sprawdź, czy urządzenie może uzyskać dostęp do powyższych zasobów firmy Microsoft w ramach konta system przy użyciu skryptu [łączności usługi rejestracji urządzeń testowych](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurowanie dołączenia hybrydowego do usługi Azure AD

Aby skonfigurować sprzężenie hybrydowe usługi Azure AD przy użyciu Azure AD Connect:

1. Uruchom Azure AD Connect, a następnie wybierz pozycję **Konfiguruj**.

1. W obszarze **dodatkowe zadania** wybierz pozycję **Konfiguruj opcje urządzenia**, a następnie wybierz przycisk **dalej**.

   ![Dodatkowe zadania](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. W obszarze **Przegląd** wybierz pozycję **dalej**.

1. W obszarze **nawiązywanie połączenia z usługą Azure AD** wprowadź poświadczenia administratora globalnego dla dzierżawy usługi Azure AD.  

1. W obszarze **Opcje urządzenia** wybierz pozycję **Konfiguruj sprzężenie hybrydowe usługi Azure AD**, a następnie wybierz pozycję **dalej**.

   ![Opcje urządzenia](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. W obszarze **systemy operacyjne urządzeń** wybierz systemy operacyjne używane przez urządzenia w środowisku Active Directory, a następnie wybierz przycisk **dalej**.

   ![System operacyjny urządzenia](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. W obszarze **Konfiguracja punktu połączenia** usługi dla każdego lasu, w którym ma Azure AD Connect skonfigurować punkt połączenia usługi, wykonaj następujące czynności, a następnie wybierz przycisk **dalej**.

   1. Wybierz **Las**.
   1. Wybierz **usługę uwierzytelniania**.
   1. Wybierz pozycję **Dodaj** , aby wprowadzić poświadczenia administratora przedsiębiorstwa.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. W obszarze **gotowe do skonfigurowania** wybierz pozycję **Konfiguruj**.

1. W obszarze **Konfiguracja ukończona** wybierz pozycję **Zakończ**.

## <a name="enable-windows-down-level-devices"></a>Włączanie urządzeń z systemem Windows niższego poziomu

Jeśli niektóre urządzenia dołączone do domeny są urządzeniami niskiego poziomu systemu Windows, musisz:

- Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń
- Konfigurowanie bezproblemowego logowania jednokrotnego
- Zainstaluj program Microsoft Workplace Join dla komputerów niskiego poziomu systemu Windows

> [!NOTE]
> Obsługa systemu Windows 7 zakończyła się 14 stycznia 2020. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna systemu Windows 7](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

Aby zakończyć dołączanie hybrydowej usługi Azure AD na urządzeniach z systemem Windows niższego poziomu i uniknąć wyświetlania wierszy przy uwierzytelnianiu urządzeń w usłudze Azure AD, można wypchnąć zasady do urządzeń przyłączonych do domeny w celu dodania następujących adresów URL do strefy Lokalny intranet w programie Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Należy również włączyć opcję **Zezwalaj na aktualizacje na pasku stanu za pomocą skryptu** w strefie Lokalny intranet użytkownika.

### <a name="configure-seamless-sso"></a>Konfigurowanie bezproblemowego logowania jednokrotnego

Aby ukończyć sprzężenie hybrydowe urządzeń z systemem Windows w domenie zarządzanej, która używa [synchronizacji skrótów haseł](../hybrid/whatis-phs.md) lub [uwierzytelniania przekazywanego](../hybrid/how-to-connect-pta.md) jako metody uwierzytelniania w chmurze usługi Azure AD, należy również [skonfigurować bezproblemowe logowanie jednokrotne](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Zainstaluj program Microsoft Workplace Join dla komputerów niskiego poziomu systemu Windows

Aby zarejestrować urządzenia niskiego poziomu systemu Windows, organizacje muszą zainstalować [Workplace Join firmy Microsoft dla komputerów z systemem innym niż Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Program Microsoft Workplace Join dla komputerów z systemem innym niż Windows 10 jest dostępny w centrum pobierania Microsoft.

Pakiet można wdrożyć przy użyciu systemu dystrybucji oprogramowania, takiego jak [Microsoft Endpoint Configuration Manager](/configmgr/). Pakiet obsługuje standardowe opcje instalacji dyskretnej z `quiet` parametrem. Bieżąca wersja Configuration Manager oferuje korzyści w porównaniu z wcześniejszymi wersjami, takie jak możliwość śledzenia zakończonych rejestracji.

Instalator tworzy zaplanowane zadanie w systemie, który działa w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie dołącza urządzenie do usługi Azure AD przy użyciu poświadczeń użytkownika po uwierzytelnieniu w usłudze Azure AD.

## <a name="verify-the-registration"></a>Weryfikacja rejestracji

Oto trzy sposoby lokalizowania i weryfikowania stanu urządzenia:

### <a name="locally-on-the-device"></a>Lokalnie na urządzeniu

1. Otwórz program Windows PowerShell.
2. Wprowadź `dsregcmd /status`.
3. Sprawdź, czy dla obu **AzureAdJoined** i **DomainJoined** ustawiono wartość **tak**.
4. Można użyć **DeviceID** i porównać stan usługi przy użyciu Azure Portal lub programu PowerShell.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Przejdź do strony urządzenia za pomocą [linku bezpośredniego](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informacje dotyczące sposobu lokalizowania urządzenia można znaleźć w temacie [jak zarządzać tożsamościami urządzeń za pomocą Azure Portal](./device-management-azure-portal.md).
3. Jeśli **zarejestrowana** kolumna znajduje się w **stanie oczekiwania**, wówczas hybrydowe dołączenie do usługi Azure AD nie zostało ukończone.
4. Jeśli **zarejestrowana** kolumna zawiera **datę/godzinę**, dołączenie do hybrydowej usługi Azure AD zostało zakończone.

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

Sprawdź stan rejestracji urządzenia w dzierżawie platformy Azure przy użyciu polecenia **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)**. To polecenie cmdlet znajduje się w [module Azure Active Directory PowerShell](/powershell/azure/active-directory/install-msonlinev1).

Korzystając z polecenia cmdlet **Get-MSolDevice** , można sprawdzić szczegóły usługi:

- Obiekt z **identyfikatorem urządzenia** , który jest zgodny z identyfikatorem na kliencie systemu Windows, musi istnieć.
- Wartość **DeviceTrustType** jest **przyłączona do domeny**. To ustawienie jest równoważne ze stanem **przyłączonym do hybrydowej usługi Azure AD** na stronie **urządzenia** w portalu usługi Azure AD.
- W przypadku urządzeń, które są używane w dostęp warunkowy, wartość **włączone** to **true** , a **DeviceTrustLevel** jest **zarządzana**.

1. Uruchom program Windows PowerShell jako administrator.
2. Wprowadź `Connect-MsolService` , aby nawiązać połączenie z dzierżawcą platformy Azure.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Liczba wszystkich urządzeń przyłączonych do hybrydowej usługi Azure AD (z wyłączeniem stanu **oczekiwania** )

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Liczba wszystkich urządzeń przyłączonych do hybrydowej usługi Azure AD ze stanem **oczekiwanie**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Wyświetl listę wszystkich urządzeń przyłączonych do hybrydowej usługi Azure AD

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Wyświetl listę wszystkich urządzeń przyłączonych do hybrydowej usługi Azure AD ze stanem **oczekującym**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Wyświetl szczegóły pojedynczego urządzenia:

1. Wprowadź `get-msoldevice -deviceId <deviceId>` (jest to identyfikator **DeviceID** uzyskany lokalnie na urządzeniu).
2. Upewnij się, że opcja **Włączone** ma wartość **Prawda**.

## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacją

Jeśli wystąpią problemy z ukończeniem hybrydowego łączenia z usługą Azure AD dla przyłączonych do domeny urządzeń z systemem Windows, zobacz:

- [Rozwiązywanie problemów z urządzeniami za pomocą polecenia dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Rozwiązywanie problemów dotyczących urządzeń dołączonych hybrydowo do usługi Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z Azure Active Directory hybrydowymi podłączonymi do urządzeń niższego poziomu](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać tożsamościami urządzeń za pomocą Azure Portal.
> [!div class="nextstepaction"]
> [Zarządzanie tożsamościami urządzeń](device-management-azure-portal.md)
