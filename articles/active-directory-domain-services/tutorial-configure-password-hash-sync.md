---
title: Włącz synchronizację skrótów haseł dla Azure AD Domain Services | Microsoft Docs
description: W tym samouczku dowiesz się, jak włączyć synchronizację skrótów haseł przy użyciu Azure AD Connect do Azure Active Directory Domain Services domenie zarządzanej.
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 4daad0d0739bb3407be13f717572c8aa99947d1a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967294"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Samouczek: Włączanie synchronizacji haseł w Azure Active Directory Domain Services dla środowisk hybrydowych

W przypadku środowisk hybrydowych dzierżawa Azure Active Directory (Azure AD) można skonfigurować do synchronizacji ze środowiskiem lokalnym Active Directory Domain Services (AD DS) za pomocą Azure AD Connect. Domyślnie Azure AD Connect nie synchronizuje starszych skrótów NT LAN Manager (NTLM) i haseł protokołu Kerberos, które są zbędne dla Azure Active Directory Domain Services (Azure AD DS).

Aby korzystać z usługi Azure AD DS z kontami synchronizowanymi ze środowiska lokalnego AD DS, należy skonfigurować Azure AD Connect do synchronizowania tych skrótów haseł wymaganych do uwierzytelniania NTLM i Kerberos. Po skonfigurowaniu Azure AD Connect zdarzenie tworzenia konta lokalnego lub zmiany hasła synchronizuje także starsze skróty haseł do usługi Azure AD.

Nie musisz wykonywać tych czynności, jeśli używasz kont tylko w chmurze bez lokalnego środowiska AD DS lub jeśli używasz *lasu zasobów*. W przypadku domen zarządzanych korzystających z lasu zasobów lokalne skróty haseł nigdy nie są synchronizowane. Uwierzytelnianie dla kont lokalnych umożliwia korzystanie z relacji zaufania lasów z własnymi AD DS kontrolerami domeny.

W ramach tego samouczka nauczysz się:

> [!div class="checklist"]
> * Dlaczego są wymagana starsze skróty NTLM i hasła protokołu Kerberos
> * Jak skonfigurować starszą synchronizację skrótów haseł dla Azure AD Connect

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa Azure Active Directory skojarzona z subskrypcją, która jest synchronizowana z katalogiem lokalnym przy użyciu Azure AD Connect.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
    * W razie potrzeby [włącz Azure AD Connect na potrzeby synchronizacji skrótów haseł][enable-azure-ad-connect].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności [Utwórz i skonfiguruj Azure Active Directory Domain Services domenę zarządzaną][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Synchronizacja skrótów haseł przy użyciu Azure AD Connect

Azure AD Connect służy do synchronizowania obiektów, takich jak konta użytkowników i grupy, z lokalnego środowiska AD DS do dzierżawy usługi Azure AD. W ramach procesu synchronizacja skrótów haseł umożliwia kontom używanie tego samego hasła w środowisku Premium AD DS i usłudze Azure AD.

Aby można było uwierzytelniać użytkowników w domenie zarządzanej, usługa Azure AD DS wymaga skrótów haseł w formacie odpowiednim do uwierzytelniania NTLM i Kerberos. Usługa Azure AD nie przechowuje skrótów haseł w formacie wymaganym do uwierzytelniania NTLM lub Kerberos, dopóki nie zostanie włączona usługa Azure AD DS dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD nie przechowuje również żadnych poświadczeń hasła w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie może automatycznie generować tych skrótów uwierzytelniania NTLM lub Kerberos w oparciu o istniejące poświadczenia użytkownika.

Azure AD Connect można skonfigurować do synchronizowania wymaganych skrótów uwierzytelniania NTLM lub Kerberos dla AD DS platformy Azure. Upewnij się, że wykonano kroki umożliwiające [włączenie Azure AD Connect synchronizacji skrótów haseł][enable-azure-ad-connect]. Jeśli masz istniejące wystąpienie Azure AD Connect, [Pobierz i zaktualizuj do najnowszej wersji][azure-ad-connect-download] , aby upewnić się, że można synchronizować starsze skróty haseł dla protokołów NTLM i Kerberos. Ta funkcja jest niedostępna w wczesnych wersjach Azure AD Connect lub za pomocą starszego narzędzia DirSync. Wymagany jest Azure AD Connect w wersji *1.1.614.0* lub nowszej.

> [!IMPORTANT]
> Azure AD Connect należy instalować i konfigurować tylko na potrzeby synchronizacji z lokalnymi środowiskami AD DS. Instalowanie Azure AD Connect w domenie zarządzanej AD DS platformy Azure nie jest obsługiwane do synchronizowania obiektów z powrotem do usługi Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Włącz synchronizację skrótów haseł

Za pomocą Azure AD Connect zainstalowanych i skonfigurowanych do synchronizacji z usługą Azure AD Skonfiguruj teraz starszą synchronizację skrótów haseł dla protokołów NTLM i Kerberos. Skrypt programu PowerShell służy do konfigurowania wymaganych ustawień, a następnie do uruchamiania pełnej synchronizacji haseł z usługą Azure AD. Azure AD Connect po ukończeniu procesu synchronizacji skrótów haseł, użytkownicy mogą logować się do aplikacji za pośrednictwem usługi Azure AD DS, które używają starszych skrótów haseł NTLM lub Kerberos.

1. Na komputerze z zainstalowanym Azure AD Connect z menu Start Otwórz **usługę synchronizacji > Azure AD Connect**.
1. Wybierz kartę **Łączniki** . Wymienione są informacje o połączeniu służące do ustanowienia synchronizacji między środowiskiem lokalnym AD DS i usługą Azure AD.

    **Typ** wskazuje, *Azure Active Directory systemu Windows (Microsoft)* dla łącznika usługi Azure AD lub *Active Directory Domain Services* dla lokalnego łącznika AD DS. Zanotuj nazwy łączników, które mają być używane w skrypcie programu PowerShell w następnym kroku.

    ![Wyświetl listę nazw łączników w Service Manager synchronizacji](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    W tym przykładowym zrzucie ekranu są używane następujące łączniki:

    * Łącznik usługi Azure AD ma nazwę *contoso.onmicrosoft.com-AAD*
    * Łącznik AD DS lokalnego ma nazwę *OnPrem.contoso.com*

1. Skopiuj i wklej następujący skrypt programu PowerShell na komputerze z zainstalowanym Azure AD Connect. Skrypt wyzwala pełną synchronizację haseł, która zawiera starsze skróty haseł. Zaktualizuj `$azureadConnector` zmienne i `$adConnector` przy użyciu nazw łączników z poprzedniego kroku.

    Uruchom ten skrypt w każdym lesie usługi AD, aby zsynchronizować skróty haseł dla lokalnego konta NTLM i Kerberos do usługi Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    W zależności od rozmiaru katalogu pod względem liczby kont i grup synchronizacja starszych skrótów haseł do usługi Azure AD może zająć trochę czasu. Hasła są następnie synchronizowane z domeną zarządzaną po przeprowadzeniu synchronizacji z usługą Azure AD.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Dlaczego są wymagana starsze skróty NTLM i hasła protokołu Kerberos
> * Jak skonfigurować starszą synchronizację skrótów haseł dla Azure AD Connect

> [!div class="nextstepaction"]
> [Dowiedz się, jak synchronizacja działa w Azure AD Domain Servicesej domenie zarządzanej](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
