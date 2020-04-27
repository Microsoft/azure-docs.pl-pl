---
title: Ręczne konfigurowanie urządzeń dołączonych hybrydowo do usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak ręcznie skonfigurować urządzenia dołączone hybrydowo do usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddead0b5ff094efc4abacb6bfbaf8bc4f47902b
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159288"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Samouczek: ręczne konfigurowanie urządzeń dołączonych hybrydowo do usługi Azure Active Directory

Zarządzanie urządzeniami w usłudze Azure Active Directory (Azure AD) pozwala zapewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają Twoje standardy dotyczące zabezpieczeń i zgodności. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md).

> [!TIP]
> Jeśli masz możliwość użycia usługi Azure AD Connect, zapoznaj się z odpowiednimi samouczkami dotyczącymi domen [zarządzanych](hybrid-azuread-join-managed-domains.md) lub [federacyjnych](hybrid-azuread-join-federated-domains.md). Użycie programu Azure AD Connect pozwala znacznie uprościć proces konfiguracji dołączenia hybrydowego do usługi Azure AD.

Jeśli masz lokalne środowisko usługi Active Directory i chcesz dołączyć do usługi Azure AD urządzenia dołączone do domeny, możesz to zrobić przez skonfigurowanie urządzeń dołączonych hybrydowo do usługi Azure AD. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Ręczne konfigurowanie dołączania do hybrydowej usługi Azure AD
> * Konfigurowanie punktu połączenia usługi
> * Konfigurowanie wystawiania oświadczeń
> * Włączanie urządzeń z systemem Windows niższego poziomu
> * Weryfikowanie dołączonych urządzeń
> * Rozwiązywanie problemów z implementacją

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że znasz następujące zagadnienia:

* [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)
* [Planowanie implementacji z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)
* [Kontrolowanie dołączania Twoich urządzeń do hybrydowej usługi Azure AD](hybrid-azuread-join-control.md)

Przed rozpoczęciem włączania urządzeń dołączonych hybrydowo do usługi Azure AD w swojej organizacji upewnij się, że zostały spełnione następujące wymagania:

* Używasz aktualnej wersji programu Azure AD Connect.
* Program Azure AD Connect został zsynchronizowany z obiektami komputera urządzeń, które chcesz dołączyć hybrydowo do usługi Azure AD. Jeśli obiekty komputera należą do określonych jednostek organizacyjnych, te jednostki muszą również być skonfigurowane na potrzeby synchronizacji w programie Azure AD Connect.

Program Azure AD Connect:

* Utrzymuje skojarzenie między kontem komputera w lokalnym wystąpieniu usługi Active Directory i obiektem urządzenia w usłudze Azure AD.
* Włącza inne funkcje powiązane z urządzeniem, takie jak Windows Hello dla firm.

Upewnij się, że następujące adresy URL są dostępne z komputerów wewnątrz sieci organizacji na potrzeby rejestracji komputerów w usłudze Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* Usługa STS organizacji (dla domen federacyjnych), którą należy uwzględnić w ustawieniach lokalnego intranetu użytkownika

Jeśli Twoja organizacja planuje używać bezproblemowego logowania jednokrotnego, następujący adres URL musi być dostępny z komputerów w organizacji. Należy go również dodać do lokalnej strefy intranetu użytkownika.

* `https://autologon.microsoftazuread-sso.com`

Ponadto należy włączyć następujące ustawienie w strefie intranetu użytkownika: „Zezwalaj na aktualizacje paska stanu przy użyciu skryptu”.

Jeśli Twoja organizacja używa zarządzanej instalacji (innej niż federacyjna) z lokalną usługą Azure Directory i nie używa usług Active Directory Federation Services (AD FS) do utworzenia federacji z usługą Azure AD, dołączenie hybrydowe do usługi Azure AD w systemie Windows 10 polega na obiektach komputerów w usłudze Active Directory jako synchronizowanych z usługą Azure AD. Upewnij się, że wszystkie jednostki organizacyjne zawierające obiekty komputerów, które muszą być dołączone hybrydowo do usługi Azure AD, są włączone do synchronizacji w konfiguracji synchronizacji programu Azure AD Connect.

W przypadku urządzeń z systemem Windows 10 w wersji 1703 lub starszej, jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, musisz zaimplementować usługę autowykrywania internetowego serwera proxy, aby umożliwić komputerom z systemem Windows 10 przeprowadzenie rejestracji w usłudze Azure AD.

Począwszy od systemu Windows 10 1803, nawet jeśli próba dołączenia hybrydowego do usługi Azure AD przez urządzenie w domenie federacyjnej przy użyciu usług AD FS nie powiedzie się, a program Azure AD Connect jest skonfigurowany tak, aby synchronizować obiekty komputerów/urządzeń z usługą Azure AD, urządzenie podejmie próbę hybrydowego dołączenia do usługi Azure AD za pomocą zsynchronizowanego komputera/urządzenia.

Aby sprawdzić, czy urządzenie jest w stanie uzyskać dostęp do powyższych zasobów firmy Microsoft w ramach konta systemowego, można użyć skryptu [łączności rejestracji urządzeń testowych](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="verify-configuration-steps"></a>Weryfikowanie kroków konfiguracji

Możesz skonfigurować urządzenia dołączone hybrydowo do usługi Azure AD dla różnych typów platform urządzeń Windows. W tym temacie opisano wymagane kroki dla wszystkich typowych scenariuszy konfiguracji.  

Skorzystaj z poniższej tabeli, aby uzyskać przegląd kroków wymaganych dla danego scenariusza:  

| Kroki | Bieżący system Windows i synchronizacja skrótów haseł | Bieżący system Windows i federacja | System Windows niższego poziomu |
| :--- | :---: | :---: | :---: |
| Konfigurowanie punktu połączenia usługi | ![Zaznacz][1] | ![Zaznacz][1] | ![Zaznacz][1] |
| Konfigurowanie wystawiania oświadczeń |     | ![Zaznacz][1] | ![Zaznacz][1] |
| Włączanie urządzeń z systemem innym niż Windows 10 |       |        | ![Zaznacz][1] |
| Weryfikowanie dołączonych urządzeń | ![Zaznacz][1] | ![Zaznacz][1] | ![Zaznacz][1] |

## <a name="configure-a-service-connection-point"></a>Konfigurowanie punktu połączenia usługi

Urządzenia używają obiektu punktu połączenia usługi podczas rejestracji do odnajdywania informacji o dzierżawie usługi Azure AD. W lokalnym wystąpieniu usługi Active Directory obiekt punktu połączenia usługi dla urządzeń dołączonych hybrydowo do usługi Azure AD musi istnieć w partycji kontekstu nazewnictwa konfiguracji lasu komputera. Każdy las ma tylko jeden kontekst nazewnictwa konfiguracji. W konfiguracji usługi Active Directory z wieloma lasami punkt połączenia usługi musi istnieć we wszystkich lasach, które zawierają komputery dołączone do domeny.

Aby pobrać kontekst nazewnictwa konfiguracji danego lasu, możesz użyć polecenia cmdlet [**Get ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx).  

W przypadku lasu z nazwą domeny usługi Active Directory *fabrikam.com* kontekst nazewnictwa konfiguracji jest następujący:

`CN=Configuration,DC=fabrikam,DC=com`

W Twoim lesie obiekt punktu połączenia usługi dla automatycznej rejestracji urządzeń dołączonych do domeny znajduje się w:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

W zależności od sposobu wdrożenia programu Azure AD Connect obiekt punktu połączenia usługi może być już skonfigurowany.
Aby zweryfikować istnienie obiektu i pobrać wartości z odnajdywania, możesz użyć następującego skryptu programu Windows PowerShell:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

Dane wyjściowe **$scp.Keywords** pokazują informacje o dzierżawie usługi Azure AD. Oto przykład:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Jeśli punkt połączenia usługi nie istnieje, możesz go utworzyć, uruchamiając polecenie cmdlet `Initialize-ADSyncDomainJoinedComputerSync` na serwerze programu Azure AD Connect. Do uruchomienia tego polecenia cmdlet są wymagane poświadczenia administratora przedsiębiorstwa.  

Polecenie cmdlet:

* Tworzy punkt połączenia usługi w lesie usługi Active Directory, z którym jest połączony program Azure AD Connect.
* Wymaga określenia parametru `AdConnectorAccount`. To konto jest konfigurowane jako konto łącznika usługi Active Directory w programie Azure AD Connect.


Poniższy skrypt pokazuje przykład użycia polecenia cmdlet. W tym skrypcie element `$aadAdminCred = Get-Credential` wymaga wpisania nazwy użytkownika. Musisz podać nazwę użytkownika w formacie głównej nazwy użytkownika (nazwy UPN) (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

Polecenie cmdlet `Initialize-ADSyncDomainJoinedComputerSync`:

* Korzysta z modułu Active Directory PowerShell i narzędzi usług Azure Active Directory Domain Services (Azure AD DS). Te narzędzia są zależne od usług internetowych usługi Active Directory uruchomionych na kontrolerze domeny. Usługi internetowe usługi Active Directory są obsługiwane na kontrolerach domeny z systemem Windows Server 2008 R2 lub nowszym.
* Jest obsługiwane tylko przez moduł MSOnline programu PowerShell w wersji 1.1.166.0. Aby pobrać ten moduł, użyj [tego linku](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0).
* Jeśli narzędzia AD DS nie są zainstalowane, `Initialize-ADSyncDomainJoinedComputerSync` program zakończy się niepowodzeniem. Narzędzia AD DS można zainstalować za pomocą Menedżer serwera w obszarze **funkcje** > **Narzędzia administracji zdalnej serwera** > **Narzędzia do administrowania rolami**.

W przypadku kontrolerów domeny z systemem Windows Server 2008 i jego wcześniejszymi wersjami użyj następującego skryptu, aby utworzyć punkt połączenia usługi. W konfiguracji z wieloma lasami użyj następującego skryptu, aby utworzyć punkt połączenia usługi w każdym lesie, w którym istnieją komputery.

   ```PowerShell
   $verifiedDomain = "contoso.com" # Replace this with any of your verified domain names in Azure AD
   $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47" # Replace this with you tenant ID
   $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com" # Replace this with your Active Directory configuration naming context

   $de = New-Object System.DirectoryServices.DirectoryEntry
   $de.Path = "LDAP://CN=Services," + $configNC
   $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
   $deDRC.CommitChanges()

   $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
   $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
   $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

   $deSCP.CommitChanges()
   ```

W poprzednim skrypcie symbol zastępczy to `$verifiedDomain = "contoso.com"`. Zastąp go jedną ze zweryfikowanych nazw domen w usłudze Azure AD. Musisz być właścicielem domeny, aby móc jej używać.

Aby uzyskać więcej informacji na temat zweryfikowanych nazw domen, zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../active-directory-domains-add-azure-portal.md).

Aby uzyskać listę swoich zweryfikowanych domen firmowych, możesz użyć polecenia cmdlet [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0).

![Lista domen firmy](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Konfigurowanie wystawiania oświadczeń

W konfiguracji federacyjnej usługi Azure AD urządzenia korzystają z usług AD FS lub lokalnej usługi federacyjnej partnera firmy Microsoft w celu uwierzytelniania w usłudze Azure AD. Urządzenia uwierzytelniają się w celu uzyskania tokenu dostępu, aby zarejestrować się w usłudze Azure Active Directory Device Registration (Azure DRS).

Urządzenia z bieżącym systemem Windows uwierzytelniają się przy użyciu zintegrowanego uwierzytelniania systemu Windows w aktywnym punkcie końcowym protokołu WS-Trust (w wersji 1.3 lub 2005) hostowanym przez lokalną usługę federacyjną.

W przypadku korzystania z AD FS należy włączyć następujące punkty końcowe protokołu WS-Trust
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> **Usługi ADFS/Services/Trust/2005/windowstransport** lub **ADFS/Services/Trust/13/windowstransport** powinny być włączone tylko jako punkty końcowe dostępne dla intranetu i nie mogą być udostępniane jako punkty końcowe dla ekstranetu za pośrednictwem serwera proxy aplikacji sieci Web. Aby dowiedzieć się więcej na temat wyłączania punktów końcowych usługi WS-Trust systemu Windows, zobacz temat [wyłączanie punktów końcowych systemu Windows WS-Trust na serwerze proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Punkty końcowe można sprawdzić za pomocą konsoli zarządzania AD FS w obszarze**punkty końcowe** **usługi** > .

> [!NOTE]
>Jeśli nie masz usługi AD FS jako lokalnej usługi federacyjnej, postępuj zgodnie z instrukcjami od dostawcy, aby upewnić się, że obsługuje on punkty końcowe protokołu WS-Trust 1.3 lub 2005, i że są one publikowane za pomocą pliku wymiany metadanych (MEX).

Aby rejestracja urządzenia została ukończona, poniższe oświadczenia muszą istnieć w tokenie odebranym przez usługę Azure DRS. Usługa Azure DRS utworzy obiekt urządzenia w usłudze Azure AD za pomocą niektórych z tych informacji. Program Azure AD Connect następnie używa tych informacji do skojarzenia nowo utworzonego obiektu urządzenia za pomocą lokalnego konta komputera.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Jeśli masz więcej niż jedną zweryfikowaną nazwę domeny, musisz udostępnić następujące oświadczenie dla komputerów:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Jeśli już wystawiasz oświadczenie ImmutableID (na przykład alternatywny identyfikator logowania), musisz podać jedno odpowiednie oświadczenie dla komputerów:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

W poniższych sekcjach znajdziesz następujące informacje:

* Wartości, które powinno zawierać każde oświadczenie.
* Jak definicja będzie wyglądać w usługach AD FS.

Definicja ułatwia sprawdzenie, czy wartości są obecne, czy też trzeba je utworzyć.

> [!NOTE]
> Jeśli nie używasz usług AD FS dla lokalnego serwera federacyjnego, postępuj zgodnie z instrukcjami dostawcy, aby utworzyć odpowiednią konfigurację do wystawiania tych oświadczeń.

### <a name="issue-account-type-claim"></a>Wystawianie oświadczenia typu konta

Oświadczenie `http://schemas.microsoft.com/ws/2012/01/accounttype` musi zawierać wartość **DJ**, która identyfikuje urządzenie jako komputer dołączony do domeny. W usługach AD FS możesz dodać regułę przekształcania wystawiania, która wygląda następująco:

   ```
   @RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );
   ```

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Wystawianie oświadczenia objectGUID lokalnego konta komputera

Oświadczenie `http://schemas.microsoft.com/identity/claims/onpremobjectguid` musi zawierać wartość **objectGUID** lokalnego konta komputera. W usługach AD FS możesz dodać regułę przekształcania wystawiania, która wygląda następująco:

   ```
   @RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$", 
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Wystawianie oświadczenia objectSID lokalnego konta komputera

Oświadczenie `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` musi zawierać wartość **objectSid** lokalnego konta komputera. W usługach AD FS możesz dodać regułę przekształcania wystawiania, która wygląda następująco:

   ```
   @RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);
   ```

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Wystawianie oświadczenia issuerID dla komputera, gdy wiele zweryfikowanych nazw domen znajduje się w usłudze Azure AD

Oświadczenie`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` musi zawierać identyfikator URI (Uniform Resource Identifier) dowolnej zweryfikowanej nazwy domeny, która łączy się z lokalną usługą federacyjną (AD FS lub partnera) wystawiającą token. W usługach AD FS można dodać reguły przekształcania wystawiania wyglądające podobnie do następujących, w tej konkretnej kolejności po tych wymienionych wcześniej. Należy pamiętać, że jest niezbędna jedna reguła służąca do jawnego wystawiania reguły dla użytkowników. W następujących regułach jest dodawana pierwsza reguła, która identyfikuje użytkowników w stosunku do uwierzytelniania komputerów.

   ```
   @RuleName = "Issue account type with the value User when its not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://<verified-domain-name>/adfs/services/trust/"
   );
   ```

W poprzednim oświadczeniu symbol zastępczy to `<verified-domain-name>`. Zastąp go jedną ze zweryfikowanych nazw domen w usłudze Azure AD. Użyj na przykład nazwy `Value = "http://contoso.com/adfs/services/trust/"`.

Aby uzyskać więcej informacji na temat zweryfikowanych nazw domen, zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Aby uzyskać listę zweryfikowanych domen firmowych, możesz użyć polecenia cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0).

![Lista domen firmy](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Wystaw oświadczenie ImmutableID dla komputera, gdy istnieje ono dla użytkowników (na przykład został ustawiony alternatywny identyfikator logowania)

Oświadczenie `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` musi zawierać prawidłową wartość dla komputerów. W usługach AD FS można utworzyć reguły przekształcania wystawiania w następujący sposób:

   ```
   @RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Skrypt pomocnika służący do utworzenia reguł przekształcania wystawiania usług AD FS

Poniższy skrypt pomaga w tworzeniu reguł przekształcania wystawiania opisanych wcześniej.

   ```
   $multipleVerifiedDomainNames = $false
   $immutableIDAlreadyIssuedforUsers = $false
   $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains

   $rule1 = '@RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );'

   $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'

   $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);'

   $rule4 = ''
   if ($multipleVerifiedDomainNames -eq $true) {
   $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
   );'
   }

   $rule5 = ''
   if ($immutableIDAlreadyIssuedforUsers -eq $true) {
   $rule5 = '@RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'
   }

   $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

   $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

   $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

   Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString
   ```

#### <a name="remarks"></a>Uwagi

* Ten skrypt dołącza reguły do istniejących reguł. Nie należy uruchamiać skryptu dwa razy, ponieważ zestawy reguł zostałyby dodane dwa razy. Upewnij się, że nie istnieją żadne odpowiadające reguły dla tych oświadczeń (zgodnie z odpowiadającymi warunkami) przed ponownym uruchomieniem skryptu.
* Jeśli masz wiele zweryfikowanych nazw domen (jak pokazano w portalu usługi Azure AD lub przy użyciu polecenia cmdlet **Get-MsolDomain**), ustaw wartość elementu **$multipleVerifiedDomainNames** w skrypcie na **$true**. Upewnij się również, że usuniesz wszystkie istniejące **issuerid** , które mogły zostać utworzone przez Azure AD Connect lub za pośrednictwem innych metod. Oto przykład dla tej reguły:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Jeśli masz już wystawiony **ImmutableID** oświadczeń dla kont użytkowników, ustaw wartość elementu **$immutableIDAlreadyIssuedforUsers** w skrypcie na **$true**.

## <a name="enable-windows-down-level-devices"></a>Włączanie urządzeń z systemem Windows niższego poziomu

Jeśli część urządzeń dołączonych do domeny to urządzenia z systemem Windows niższego poziomu, musisz spełnić następujące warunki:

* Ustawić zasady w usłudze Azure AD, aby umożliwić użytkownikom rejestrowanie urządzeń.
* Skonfigurować lokalną usługę federacyjną w celu wystawiania oświadczeń do obsługi zintegrowanego uwierzytelniania systemu Windows na potrzeby rejestracji urządzeń.
* Dodać punkt końcowy uwierzytelniania urządzeń usługi Azure AD do strefy lokalnego intranetu, aby uniknąć monitów o certyfikat podczas uwierzytelniania urządzenia.
* Kontrolować urządzenia z systemem Windows niższego poziomu.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Ustawianie zasad w usłudze Azure AD w celu umożliwienia użytkownikom rejestrowania urządzeń

Aby zarejestrować urządzenia z systemem Windows niższego poziomu, upewnij się, że włączono ustawienie umożliwiające użytkownikom rejestrowanie urządzeń w usłudze Azure AD. W Azure Portal można znaleźć to ustawienie w obszarze **Azure Active Directory** > **Użytkownicy i grupy** > **Ustawienia urządzenia**.

Następujące zasady muszą mieć ustawioną wartość **wszystkie**: **Użytkownicy mogą zarejestrować swoje urządzenia w usłudze Azure AD**.

![Przycisk Wszystkie, który umożliwia użytkownikom rejestrowanie urządzeń](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>Konfigurowanie lokalnej usługi federacyjnej

Lokalna usługa federacyjna musi obsługiwać wystawianie oświadczeń **authenticationmethod** i **wiaormultiauthn**, gdy odbiera żądanie uwierzytelnienia do jednostki uzależnionej usługi Azure AD przechowującej parametr resouce_params z następującą zakodowaną wartością:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Jeśli takie żądanie przychodzi, lokalna usługa federacyjna musi uwierzytelnić użytkownika przy użyciu zintegrowanego uwierzytelniania systemu Windows. W razie powodzenia usługa ta musi wystawić dwa następujące oświadczenia:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

W usługach AD FS musisz dodać regułę przekształcania wystawiania, która przechodzi przez metodę uwierzytelniania. Aby dodać tę regułę:

1. W konsoli zarządzania AD FS przejdź do pozycji **AD FS** > **zaufania relacje** > zaufania**jednostek uzależnionych**.
1. Kliknij prawym przyciskiem myszy obiekt relacji zaufania jednostki uzależnionej Platforma tożsamości usługi Microsoft Office 365, a następnie wybierz pozycję **Edytuj reguły oświadczeń**.
1. Na karcie **Reguły przekształcania wystawiania** wybierz pozycję **Dodaj regułę**.
1. Na liście szablonów **Reguła oświadczenia** wybierz pozycję **Wysyłanie oświadczeń przy użyciu reguły niestandardowej**.
1. Wybierz opcję **Dalej**.
1. W polu **Nazwa reguły dotyczącej oświadczeń** wprowadź nazwę **Auth Method Claim Rule**.
1. W polu **Reguła oświadczenia** wprowadź następującą regułę:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Na serwerze federacyjnym wprowadź następujące polecenie programu PowerShell. Zastąp ** \<RPObjectName\> ** nazwą obiektu jednostki uzależnionej dla obiektu zaufania jednostki uzależnionej usługi Azure AD. Ten obiekt zazwyczaj nosi nazwę **Platforma tożsamości usługi Microsoft Office 365**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Dodawanie punktu końcowego uwierzytelniania urządzeń usługi Azure AD do stref lokalnego intranetu

Aby uniknąć monitów o certyfikat podczas uwierzytelniania użytkowników zarejestrowanych urządzeń w usłudze Azure AD, możesz wypchnąć zasady do urządzeń dołączonych do domeny w celu dodania następujących adresów URL do strefy Lokalny intranet w programie Internet Explorer:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Kontrolowanie urządzeń z systemem Windows niższego poziomu

Aby zarejestrować urządzenia z systemem Windows niższego poziomu, musisz pobrać i zainstalować pakiet Instalatora Windows (msi) z Centrum pobierania. Aby uzyskać więcej informacji, zapoznaj się z sekcją kontrola [poprawności hybrydowego dołączania do usługi Azure AD na urządzeniach niskiego poziomu systemu Windows](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Weryfikowanie dołączonych urządzeń

Możesz sprawdzić pomyślnie dołączone urządzenia w Twojej organizacji za pomocą polecenia cmdlet [Get-MsolDevice](/powershell/msonline/v1/get-msoldevice) w [module programu PowerShell usługi Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Dane wyjściowe tego polecenia cmdlet pokazują urządzenia, które są zarejestrowane i dołączone w usłudze Azure AD. Aby uzyskać wszystkie urządzenia, użyj parametru **-All**, a następnie filtruj je przy użyciu właściwości **deviceTrustType**. Urządzenia dołączone do domeny mają wartość **Dołączone do domeny**.

## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacją

Jeśli występują problemy z ukończeniem dołączania hybrydowego do usługi Azure AD dla urządzeń z systemem Windows dołączonych do domeny, zobacz:

* [Rozwiązywanie problemów z dołączeniem hybrydowym do usługi Azure AD urządzeń z bieżącym systemem Windows](troubleshoot-hybrid-join-windows-current.md)
* [Rozwiązywanie problemów z dołączeniem hybrydowym do usługi Azure AD urządzeń z systemem Windows niższego poziomu](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
