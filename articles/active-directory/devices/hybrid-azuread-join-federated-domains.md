---
title: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen federacyjnych | Microsoft Docs
description: Dowiedz się, jak konfigurować dołączanie hybrydowe do usługi Azure Active Directory dla domen federacyjnych.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2a455e1ee6f8f714cf50ebdf6a59dab568489ca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646303"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Samouczek: konfigurowanie dołączania hybrydowego do usługi Azure Active Directory dla domen federacyjnych

Podobnie jak w przypadku użytkownika w organizacji, urządzenie to podstawowa tożsamość, która ma być chroniona. Możesz użyć tożsamości urządzenia do ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji. Ten cel można osiągnąć przez umieszczenie tożsamości urządzeń i zarządzanie nimi w Azure Active Directory (Azure AD) za pomocą jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przełączenie urządzeń do usługi Azure AD maksymalizuje produktywność użytkowników dzięki logowaniem jednokrotnym (SSO) w chmurze i zasobach lokalnych. Jednocześnie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../conditional-access/howto-conditional-access-policy-compliant-device.md) .

Środowisko federacyjne powinno mieć dostawcę tożsamości, który obsługuje poniższe wymagania. Jeśli masz środowisko federacyjne wykorzystujące Active Directory Federation Services (AD FS), poniższe wymagania są już obsługiwane.

- **WIAORMULTIAUTHN:** To żądanie jest wymagane do hybrydowego przyłączenia do usługi Azure AD dla urządzeń niższego poziomu systemu Windows.
- **Protokół WS-Trust:** Ten protokół jest wymagany do uwierzytelniania bieżących urządzeń przyłączonych hybrydowo usługi Azure AD za pomocą usługi Azure AD.
  W przypadku korzystania z AD FS należy włączyć następujące punkty końcowe WS-Trust: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **Usługi ADFS/Services/Trust/2005/windowstransport** oraz **ADFS/Services/Trust/13/windowstransport** powinny być włączone tylko jako punkty końcowe dostępne dla intranetu i nie mogą być uwidocznione jako punkty końcowe dla ekstranetu za pośrednictwem serwera proxy aplikacji sieci Web. Aby dowiedzieć się więcej o tym, jak wyłączyć WS-Trust punkty końcowe systemu Windows, zobacz temat [wyłączanie WS-Trust punktów końcowych systemu Windows na serwerze proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Punkty końcowe można sprawdzić za pomocą konsoli zarządzania AD FS w obszarze   >  **punkty końcowe** usługi.

W tym samouczku dowiesz się, jak skonfigurować sprzężenie hybrydowe usługi Azure AD dla Active Directory urządzeń przyłączonych do domeny w środowisku federacyjnym przy użyciu AD FS.

Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie dołączenia hybrydowego do usługi Azure AD
> * Włącz urządzenia niskiego poziomu systemu Windows
> * Weryfikacja rejestracji
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że znasz następujące artykuły:

- [Co to jest tożsamość urządzenia?](overview.md)
- [Jak zaplanować implementację hybrydowej funkcji dołączania do usługi Azure AD](hybrid-azuread-join-plan.md)
- [Jak sprawdzać poprawność funkcji dołączania hybrydowego usługi Azure AD](hybrid-azuread-join-control.md)

Aby skonfigurować scenariusz zaprezentowany w tym samouczku, potrzebujesz:

- Windows Server 2012 R2 z usługami AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) w wersji 1.1.819.0 lub nowszej

Począwszy od wersji 1.1.819.0, Azure AD Connect zawiera kreatora, którego można użyć do skonfigurowania hybrydowego sprzężenia usługi Azure AD. Kreator znacznie upraszcza proces konfiguracji. Powiązany kreator:

- Konfiguruje punkty połączenia usługi (punktu SCP) na potrzeby rejestracji urządzeń
- Tworzy kopię zapasową istniejącej relacji zaufania dla jednostki zależnej usługi Azure AD.
- Aktualizuje reguły dotyczące oświadczeń w relacji zaufania usługi Azure AD.

Kroki konfiguracji opisane w tym artykule opierają się na użyciu kreatora Azure AD Connect. Jeśli masz zainstalowaną wcześniejszą wersję programu Azure AD Connect, musisz uaktualnić ją do wersji 1.1.819 lub nowszej, aby użyć kreatora. Jeśli nie jest zainstalowana najnowsza wersja Azure AD Connect nie jest to opcja, zobacz [Ręczne konfigurowanie hybrydowego dołączania do usługi Azure AD](hybrid-azuread-join-manual.md).

Hybrydowe dołączenie usługi Azure AD wymaga, aby urządzenia miały dostęp do następujących zasobów firmy Microsoft z sieci organizacji:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Usługa tokenu zabezpieczającego (STS) organizacji (dla domen federacyjnych)
- `https://autologon.microsoftazuread-sso.com` (Jeśli używasz lub planujesz korzystanie z bezproblemowego logowania jednokrotnego)

> [!WARNING]
> Jeśli Twoja organizacja korzysta z serwerów proxy, które przechwytuje ruch SSL w scenariuszach takich jak ochrona przed utratą danych lub ograniczenia dzierżawy usługi Azure AD, upewnij się, że ruch do elementu " https://device.login.microsoftonline.com " jest wykluczony z funkcji Break-and-Inspekcja TLS. Niepowodzenie wykluczenia " https://device.login.microsoftonline.com " może spowodować zakłócenia przy użyciu uwierzytelniania certyfikatu klienta, powodując problemy dotyczące rejestracji urządzeń i dostępu warunkowego opartego na urządzeniach.

Począwszy od systemu Windows 10 1803, jeśli natychmiastowe dołączenie hybrydowej usługi Azure AD dla środowiska federacyjnego przy użyciu AD FS nie powiedzie się, korzystamy Azure AD Connect do synchronizowania obiektu komputera w usłudze Azure AD, który jest następnie używany do ukończenia rejestracji urządzenia dla hybrydowego sprzężenia usługi Azure AD. Sprawdź, czy Azure AD Connect synchronizuje obiekty komputerów urządzeń, które mają być przyłączone do usługi Azure AD jako hybrydowe. Jeśli obiekty komputera należą do określonych jednostek organizacyjnych (OU), należy również skonfigurować jednostki organizacyjne do synchronizacji w Azure AD Connect. Aby dowiedzieć się więcej o synchronizowaniu obiektów komputerów za pomocą Azure AD Connect, zobacz [Konfigurowanie filtrowania przy użyciu Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy wychodzącego, firma Microsoft zaleca [zaimplementowanie funkcji autowykrywania serwera proxy sieci Web (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) w celu umożliwienia komputerom z systemem Windows 10 rejestracji urządzeń w usłudze Azure AD. Jeśli wystąpią problemy podczas konfigurowania usługi WPAD i zarządzania nią, zobacz [Rozwiązywanie problemów z automatycznym wykryciem](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Jeśli nie korzystasz z usługi WPAD i chcesz skonfigurować ustawienia serwera proxy na komputerze, możesz to zrobić, rozpoczynając od systemu Windows 10 1709. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień WinHTTP przy użyciu obiektu zasad grupy (GPO)](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> W przypadku skonfigurowania ustawień serwera proxy na komputerze przy użyciu ustawień usługi WinHTTP wszystkie komputery, które nie mogą nawiązać połączenia ze skonfigurowanym serwerem proxy, nie będą mogły połączyć się z Internetem.

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego wychodzącego serwera proxy, musisz upewnić się, że komputery z systemem Windows 10 mogą pomyślnie uwierzytelniać się na wychodzącym serwerze proxy. Ponieważ komputery z systemem Windows 10 uruchamiają rejestrację urządzeń przy użyciu kontekstu komputera, należy skonfigurować uwierzytelnianie serwera proxy wychodzące przy użyciu kontekstu komputera. Skontaktuj się z dostawcą serwera proxy ruchu wychodzącego, aby uzyskać informacje na temat wymagań dotyczących konfiguracji.

Aby sprawdzić, czy urządzenie jest w stanie uzyskać dostęp do powyższych zasobów firmy Microsoft w ramach konta systemowego, można użyć skryptu [łączności rejestracji urządzeń testowych](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurowanie dołączenia hybrydowego do usługi Azure AD

Aby skonfigurować hybrydowe dołączanie do usługi Azure AD przy użyciu Azure AD Connect, potrzebne są:

- Poświadczenia administratora globalnego dla dzierżawy usługi Azure AD  
- Poświadczenia administratora przedsiębiorstwa dla każdego z lasów
- Poświadczenia administratora AD FS

**Aby skonfigurować sprzężenie hybrydowe usługi Azure AD przy użyciu Azure AD Connect**:

1. Uruchom Azure AD Connect, a następnie wybierz pozycję **Konfiguruj**.

   ![Powitanie](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na stronie **dodatkowe zadania** wybierz pozycję **Konfiguruj opcje urządzenia**, a następnie wybierz przycisk **dalej**.

   ![Dodatkowe zadania](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na stronie **Przegląd** wybierz pozycję **dalej**.

   ![Omówienie](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na stronie **Połącz z usługą Azure AD** wprowadź poświadczenia administratora globalnego dla dzierżawy usługi Azure AD, a następnie wybierz przycisk **dalej**.

   ![Łączenie z usługą Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na stronie **Opcje urządzenia** wybierz pozycję **Konfiguruj sprzężenie hybrydowe usługi Azure AD**, a następnie wybierz przycisk **dalej**.

   ![Opcje urządzenia](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na stronie **SCP** wykonaj następujące czynności, a następnie wybierz przycisk **dalej**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Wybierz las.
   1. Wybierz usługę uwierzytelniania. Należy wybrać opcję **AD FS Server** , chyba że organizacja ma wyłącznie klientów z systemem Windows 10 i skonfigurowano synchronizację komputerów/urządzeń lub organizacja używa bezproblemowego logowania jednokrotnego.
   1. Wybierz pozycję **Dodaj** , aby wprowadzić poświadczenia administratora przedsiębiorstwa.

1. Na stronie **systemy operacyjne urządzeń** wybierz systemy operacyjne używane przez urządzenia w środowisku Active Directory, a następnie wybierz przycisk **dalej**.

   ![System operacyjny urządzenia](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na stronie **Konfiguracja Federacji** wprowadź poświadczenia administratora AD FS, a następnie wybierz przycisk **dalej**.

   ![Konfiguracja federacji](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na stronie **gotowy do skonfigurowania** wybierz pozycję **Konfiguruj**.

   ![Wszystko gotowe do skonfigurowania](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na stronie **Konfiguracja ukończona** wybierz pozycję **Zakończ**.

   ![Ukończono konfigurację](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Włącz urządzenia niskiego poziomu systemu Windows

Jeśli niektóre urządzenia dołączone do domeny są urządzeniami niskiego poziomu systemu Windows, musisz:

- Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń
- Zainstaluj program Microsoft Workplace Join dla komputerów niskiego poziomu systemu Windows

> [!NOTE]
> Obsługa systemu Windows 7 zakończyła się 14 stycznia 2020. Aby uzyskać więcej informacji, [Pomoc techniczna dla systemu Windows 7 została zakończona](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

Aby pomyślnie zakończyć dołączanie hybrydowej usługi Azure AD do urządzeń niskiego poziomu systemu Windows i uniknąć wyświetlania wierszy przez certyfikat podczas uwierzytelniania urządzeń w usłudze Azure AD, można wypchnąć zasady do urządzeń przyłączonych do domeny w celu dodania następujących adresów URL do strefy Lokalny intranet w programie Internet Explorer:

- `https://device.login.microsoftonline.com`
- Usługa STS w organizacji (dla domen federacyjnych)
- `https://autologon.microsoftazuread-sso.com` (W celu bezproblemowego logowania jednokrotnego)

Należy również włączyć opcję **Zezwalaj na aktualizacje na pasku stanu za pomocą skryptu** w strefie Lokalny intranet użytkownika.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Zainstaluj program Microsoft Workplace Join dla komputerów niskiego poziomu systemu Windows

Aby zarejestrować urządzenia niskiego poziomu systemu Windows, organizacje muszą zainstalować [Workplace Join firmy Microsoft dla komputerów z systemem innym niż Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Program Microsoft Workplace Join dla komputerów z systemem innym niż Windows 10 jest dostępny w centrum pobierania Microsoft.

Pakiet można wdrożyć przy użyciu systemu dystrybucji oprogramowania, takiego jak [Microsoft Endpoint Configuration Manager](/configmgr/). Pakiet obsługuje standardowe opcje instalacji dyskretnej z `quiet` parametrem. Bieżąca gałąź Configuration Manager oferuje korzyści w porównaniu z wcześniejszymi wersjami, takie jak możliwość śledzenia zakończonych rejestracji.

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
3. Jeśli **zarejestrowana** kolumna znajduje się w **stanie oczekiwania**, wówczas hybrydowe dołączenie do usługi Azure AD nie zostało ukończone. W środowiskach federacyjnych może to mieć miejsce tylko wtedy, gdy nie udało się zarejestrować, a program AAD Connect jest skonfigurowany do synchronizacji urządzeń.
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

Jeśli wystąpią problemy z ukończeniem hybrydowego przyłączania do usługi Azure AD dla przyłączonych do domeny urządzeń z systemem Windows, zobacz:

- [Rozwiązywanie problemów z urządzeniami za pomocą polecenia dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Rozwiązywanie problemów z funkcją hybrydowego przyłączania do usługi Azure AD](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z funkcją hybrydowego przyłączania do usługi Azure AD](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak zarządzać tożsamościami urządzeń za pomocą Azure Portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png