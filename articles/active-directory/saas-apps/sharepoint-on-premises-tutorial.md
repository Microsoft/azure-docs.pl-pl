---
title: 'Samouczek: Integracja usługi Azure Active Directory z lokalnym programem SharePoint | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i lokalnym programem SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867105"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z lokalnym programem SharePoint

W tym samouczku dowiesz się, jak zintegrować program SharePoint lokalnie z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu SharePoint lokalnie z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do programu SharePoint lokalnie.
* Włącz użytkownikom automatyczne logowanie do lokalnego programu SharePoint za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure Active Directory z lokalnym programem SharePoint, potrzebne są następujące elementy:

* Subskrypcja usługi Azure Active Directory. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Farma programu SharePoint 2013 lub nowsza.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować azure active service logowania jednokrotnego w środowisku testowym. Użytkownicy usługi Azure Active Directory będą mogli uzyskać dostęp do lokalnego programu Sharepoint.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Tworzenie aplikacji dla przedsiębiorstw w witrynie Azure portal

Aby skonfigurować integrację lokalnego programu SharePoint z usługą Azure AD, musisz dodać lokalny program SharePoint z galerii do swojej listy zarządzanych aplikacji SaaS.

Aby dodać lokalny program SharePoint z galerii, wykonaj następujące czynności:

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

 > [!NOTE]
 > Jeśli element nie powinien być dostępny, można go również otworzyć za pośrednictwem łącza Stałe **wszystkie usługi** w górnej części lewego panelu nawigacyjnego. W poniższym omówieniu **łącze usługi Azure Active Directory** znajduje się w sekcji **Tożsamość** lub można je przeszukiwać za pomocą pola tekstowego filtru.

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

4. W polu wyszukiwania wpisz program **SharePoint lokalnie**, wybierz pozycję **SharePoint lokalnie** z panelu wyników.

    <kbd>![Lokalny program SharePoint na liście wyników](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Określ nazwę programu SharePoint OnPrem i kliknij przycisk **Dodaj,** aby dodać aplikację.

1. W nowej aplikacji Enterprise Kliknij właściwości i sprawdź wartość **przypisania użytkownika wymagane**

   <kbd>![Lokalny program SharePoint na liście wyników](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

w naszym scenariuszu wartość ta jest ustawiona na **Nie**.

## <a name="configure-and-test-azure-ad"></a>Konfigurowanie i testowanie usługi Azure AD

W tej sekcji można skonfigurować azure ad logowania jednokrotnego w programie SharePoint lokalnie.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem lokalnego programu SharePoint.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure Active Directory za pomocą lokalnego programu SharePoint, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Konfigurowanie programu SharePoint lokalnie](#configure-sharepoint-on-premises)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD w witrynie Azure portal](#create-an-azure-ad-test-user-in-the-azure-portal)** — utwórz nowego użytkownika w usłudze Azure AD dla logowania jednokrotnego.
1. **[Tworzenie grupy zabezpieczeń usługi Azure AD w portalu Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** — tworzenie nowej grupy zabezpieczeń w usłudze Azure AD dla logowania jednokrotnego.
1. **[Udziel uprawnień do konta usługi Azure Active Directory w programie SharePoint lokalnie](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** — przyznaj uprawnienia użytkownikowi usługi Azure AD.
1. **[Udziel uprawnień do grupy usługi Azure AD w lokalnym programie SharePoint](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** — przyznaj uprawnienia grupie usługi Azure AD.
1. **[Udziel dostępu do konta gościa do lokalnego programu SharePoint w witrynie Azure portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** — przyznaj uprawnienia do konta gościa w usłudze Azure AD dla lokalnego programu SharePoint.
1. **[Konfigurowanie zaufanego dostawcy tożsamości dla wielu aplikacji sieci Web](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** — jak używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji sieci Web

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą lokalnego programu SharePoint, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)otwórz katalog usługi Azure AD, kliknij **aplikacje enterprise**, kliknij **poprzednio utworzoną nazwę aplikacji Enterprise** i kliknij pozycję **Logowanie jednokrotne**.

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** kliknij tryb **SAML,** aby włączyć logowanie jednokrotne.
 
3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą narzędzia SAML** kliknij ikonę **Edytuj,** aby otworzyć podstawowe okno dialogowe **Konfiguracji SAML.**

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL lokalnego programu SharePoint na potrzeby logowania jednokrotnego](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. W polu **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`urn:<sharepointFarmName>:<federationName>`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YourSharePointSiteURL>/_trust/`

    1. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<YourSharePointSiteURL>/`
    1. kliknij na zapisz.

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. W sekcji **Skonfiguruj lokalny program SharePoint ** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.
    
    1. **Adres URL logowania**
    
        Kopiuj adres URL logowania zamień **/saml2** na **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** końcu za pomocą **/wsfed**, wyglądałoby to tak (ten adres URL nie jest dokładny)

    1. **Identyfikator usługi Azure AD**
    1. **Adres URL wylogowywania**
    > [!NOTE]
    > Ten adres URL nie może być używany tak, jak w programie SharePoint: musisz zastąpić **/saml2** **/wsfed**. Aplikacja Lokalny program SharePoint używa tokenu SAML 1.1, dzięki czemu usługa Azure AD oczekuje żądania usługi WS-Fed z serwera programu SharePoint, a po uwierzytelnieniu wystawia token SAML 1.1 .

### <a name="configure-sharepoint-on-premises"></a>Konfigurowanie programu SharePoint lokalnie

1. **Tworzenie nowego zaufanego dostawcy tożsamości w programie SharePoint Server 2016**

    Zaloguj się do serwera programu SharePoint i otwórz powłokę zarządzania programem SharePoint. Wypełnij wartości:
    1. **$realm** (wartość identyfikatora z lokalnej sekcji Domena i adresy URL programu SharePoint w witrynie Azure portal).
    1. **$wsfedurl** (adres URL usługi logowania jednokrotnego).
   1. **$filepath** (ścieżka pliku, do której pobrano plik certyfikatu) z witryny Azure portal.

    Uruchom następujące polecenia, aby skonfigurować nowego zaufanego dostawcę tożsamości.

    > [!TIP]
    > Jeśli dopiero zaczynasz pracę z programem PowerShell lub chcesz dowiedzieć się więcej na temat tego, jak działa program PowerShell, zobacz [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **Włączanie zaufanego dostawcy tożsamości dla aplikacji**

    a. W witrynie Administracja centralna przejdź do sekcji **Zarządzanie aplikacją internetową** i wybierz aplikację internetową, którą chcesz zabezpieczyć za pomocą usługi Azure AD.

    b. Na wstążce kliknij pozycję **Dostawcy uwierzytelniania** i wybierz strefę, której chcesz użyć.

    d. Kliknij pozycję **Trusted Identity Provider** (Zaufany dostawca tożsamości), a następnie kliknij pozycję *Add Trusted Identity Provider* (Dodaj zaufanego dostawcę tożsamości).

    d. Kliknij przycisk **OK**.

    ![Konfigurowanie dostawcy uwierzytelniania](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Tworzenie użytkownika testowego usługi Azure AD w witrynie Azure portal

Celem tej sekcji jest utworzenie użytkownika testowego w witrynie Azure portal.

1. W witrynie Azure portal w lewym okienku wybierz pozycję **Azure Active Directory**, w **obszarze Zarządzanie** wątek wybierz **pozycję Użytkownicy**.

2. Następnie wybierz **pozycję Wszyscy użytkownicy,** a następnie **nowi użytkownicy** w górnej części ekranu.

3. Wybierz opcję **Utwórz użytkownika** i we właściwościach Użytkownika wykonaj następujące czynności.  
   Możesz utworzyć użytkowników w usłudze Azure AD przy użyciu sufiksu dzierżawy lub dowolnej zweryfikowanej domeny. 

    a. W polu **Nazwa** wprowadź nazwę użytkownika , użyliśmy **TestUser**.
  
    b. W polu **Nazwa użytkownika** wpisz `TestUser@yourcompanydomain.extension`  
    Na przykład: TestUser@contoso.com

    ![Okno dialogowe Użytkownik](./media/sharepoint-on-premises-tutorial/user-properties.png)

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

    e. Teraz możesz udostępnić TestUser@contoso.com witrynę i zezwolić temu użytkownikowi na jej dostęp.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Tworzenie grupy zabezpieczeń usługi Azure AD w witrynie Azure portal

1. Kliknij **grup > usługi Azure Active Directory**.

2. Kliknij **pozycję Nowa grupa**:

3. Wypełnij **typ grupy,** **Nazwa grupy,** **Opis grupy,** **Typ członkostwa**. Kliknij strzałkę, aby wybrać członków, a następnie wyszukaj lub kliknij członka, którego chcesz dodać do grupy. Kliknij **wybierz,** aby dodać wybranych członków, a następnie kliknij **przycisk Utwórz**.

![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Udzielanie uprawnień do konta usługi Azure Active Directory w programie SharePoint lokalnie

Aby udzielić dostępu użytkownikowi usługi Azure Active Directory w lokalnym programie SharePoint, musisz udostępnić zbiór witryn lub dodać użytkownika usługi Azure Active Directory do jednej z grup zbioru witryn. Użytkownicy mogą teraz logować się do programu SharePoint 201x przy użyciu tożsamości z usługi Azure AD, ale nadal istnieją możliwości poprawy środowiska użytkownika. Na przykład wyszukiwanie użytkownika przedstawia wiele wyników wyszukiwania w selektorze osób. Istnieje wynik wyszukiwania dla każdego z typów oświadczeń, które są tworzone w mapowaniu oświadczeń. Aby wybrać użytkownika za pomocą selektora osób, należy dokładnie wpisać jego nazwę użytkownika i wybrać wynik oświadczenia **name**.

![Wyniki wyszukiwania oświadczeń](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Nie ma sprawdzania poprawności wartości, które można wyszukiwać, co może prowadzić do błędów pisowni lub użytkowników przypadkowo wybierając niewłaściwy typ oświadczenia. Może to uniemożliwić użytkownikom pomyślny dostęp do zasobów.

**Aby naprawić selektor osób w** tym scenariuszu, istnieje rozwiązanie typu open source o nazwie [AzureCP,](https://yvand.github.io/AzureCP/) który zapewnia niestandardowego dostawcy oświadczeń dla programu SharePoint 2013, 2016 i 2019. Użyje interfejsu API programu Microsoft Graph, aby rozpoznać, co użytkownicy wejdą i wykonają sprawdzanie poprawności. Dowiedz się więcej o rozwiązaniu [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Bez usługi AzureCP można dodać grupy, dodając identyfikator grupy usługi Azure AD, ale nie jest to przyjazne i niezawodne dla użytkownika. Jest jak to wygląda:  
  >   
  >![Dodawanie grupy usługi Azure AD do grupy programu SharePoint](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Udzielanie uprawnień grupie usługi Azure AD w lokalnym programie SharePoint

Aby przypisać lokalne grupy zabezpieczeń usługi Azure Active Directory do programu SharePoint, konieczne będzie użycie dostawcy oświadczeń niestandardowych dla programu SharePoint Server. W naszym przykładzie użyliśmy usługi AzureCP.

 > [!NOTE]
 > Należy pamiętać, że usługa AzureCP nie jest produktem firmy Microsoft ani obsługiwana przez dział pomocy technicznej firmy Microsoft. Pobieranie, instalowanie i konfigurowanie usługi AzureCP w https://yvand.github.io/AzureCP/lokalnej farmie programu SharePoint na program . 

1. Skonfiguruj usługę AzureCP w lokalnej farmie programu SharePoint lub w alternatywnym rozwiązaniu dostawcy oświadczeń niestandardowych. kroki konfiguracji usługi AzureCP są dostępne pod adresemhttps://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. W witrynie Azure portal otwórz katalog usługi Azure AD. Kliknij **aplikacje Enterprise**, kliknij **na poprzednio utworzoną nazwę aplikacji Enterprise** i kliknij opcję **Logowanie jednokrotne.**

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą narzędzia SAM**edytuj sekcję **Atrybuty & Oświadczenia użytkownika.**

1. Kliknij **pozycję Dodaj oświadczenie grupy**.

1. Wybierz grupy skojarzone z użytkownikiem w reklamacji, w naszym przypadku wybierz **opcję Wszystkie grupy** i w sekcji Atrybut Źródło wybierz pozycję Identyfikator **grupy** i kliknij **zapisz**.

Aby udzielić dostępu do grupy zabezpieczeń usługi Azure Active Directory w lokalnym programie SharePoint, należy udostępnić zbiór witryn lub dodać grupę zabezpieczeń usługi Azure Active Directory do jednej z grup zbioru witryn.

1. Przejdź do zbioru witryn programu SharePoint w obszarze Ustawienia witryny dla zbioru witryn kliknij pozycję "Osoby i grupy". Wybierz grupę programu SharePoint, a następnie kliknij pozycję Nowy, "Dodaj użytkowników do tej grupy" i zacznij wpisywać nazwę grupy, w jakiej selektor osób wyświetli grupę zabezpieczeń usługi Azure Active Directory.

    ![Dodawanie grupy usługi Azure AD do grupy programu SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Udzielanie dostępu do konta gościa do lokalnego programu SharePoint w witrynie Azure portal

teraz można przyznać dostęp do witryny programu SharePoint kontu gościa w spójny sposób. Zdarza się, że upn zostanie zmodyfikowany. Użytkownik jdoe@outlook.com z będzie reprezentowany jak `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Jest możliwe, aby uzyskać bezproblemowe środowisko podczas udostępniania witryny użytkownikom zewnętrznym, konieczne byłoby dodanie pewnych modyfikacji w sekcji **Atrybuty użytkownika & oświadczenia** w witrynie Azure portal.

1. W witrynie Azure portal otwórz katalog usługi Azure AD. Kliknij **aplikacje Enterprise**, kliknij **na poprzednio utworzoną nazwę aplikacji Enterprise** i kliknij opcję **Logowanie jednokrotne.**

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą narzędzia SAM**edytuj sekcję **Atrybuty & Oświadczenia użytkownika.**

1. w strefie **wymaganego oświadczenia** kliknij **unikatowy identyfikator użytkownika (nazwa id).**

1. Zmień właściwość **atrybutu źródłowego** na wartość **user.localuserprincipalname** i **zapisz**.

    ![Atrybuty użytkownika & oświadczenia początkowe](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Za pomocą wstążki wróć do **saml oparte Zaloguj się** teraz **atrybuty użytkownika & oświadczenia** sekcji będzie wyglądać następująco: 

    ![Atrybuty użytkownika & Oświadczenia ostateczne](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Nazwisko i imię nie są wymagane w tej konfiguracji.

1. W witrynie Azure portal w lewym okienku wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**.

1. kliknij na **Nowy użytkownik gościa**

1. Wybierz opcję **Zaproś użytkownika** i wypełnij właściwości Użytkownika i kliknij **zaproś**.

1. Teraz możesz udostępnić MyGuestAccount@outlook.com witrynę i zezwolić temu użytkownikowi na jej dostęp.

    ![Udostępnianie witryny z kontem gościa](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurowanie zaufanego dostawcy tożsamości dla wielu aplikacji sieci Web

Konfiguracja działa w przypadku jednej aplikacji internetowej, ale wymaga dodatkowej konfiguracji, jeśli zamierzasz używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji internetowych. Załóżmy na przykład, że rozszerzyliśmy aplikację internetową w celu używania adresu URL `https://sales.contoso.com` i teraz chcemy również uwierzytelniać użytkowników w `https://marketing.contoso.com`. Aby to zrobić, musimy zaktualizować dostawcę tożsamości, aby uwzględnić parametr WReply i zaktualizować rejestrację aplikacji w usłudze Azure AD w celu dodania adresu URL odpowiedzi.

1. W witrynie Azure portal otwórz katalog usługi Azure AD. Kliknij **aplikacje Enterprise**, kliknij **na poprzednio utworzoną nazwę aplikacji Enterprise** i kliknij opcję **Logowanie jednokrotne.**

2. Na stronie **Konfigurowanie logowania jednokrotnego z sam**, edytuj **podstawową konfigurację SAML**.

    ![edytowanie podstawowej konfiguracji SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. W **adresie URL odpowiedzi (adres URL usługi konsumenta potwierdzenia)** dodaj adres URL dodatkowych aplikacji internetowych i kliknij przycisk **Zapisz**.

    ![edytowanie podstawowej konfiguracji SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Na serwerze programu SharePoint otwórz **powłokę zarządzania programu SharePoint 201x** i wykonaj następujące polecenia, używając nazwy wystawcy zaufanego tokenu tożsamości, który był wcześniej używany.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. W witrynie Administracja centralna przejdź do aplikacji internetowej i włącz istniejącego zaufanego dostawcy tożsamości.

Może istnieć inny scenariusz, w którym chcesz udzielić dostępu do lokalnego punktu udostępnień dla użytkowników wewnętrznych, w tym scenariuszu należy wdrożyć usługę Microsoft Azure Active Directory Connect, która umożliwi synchronizację użytkowników lokalnych z usługą Azure Active Directory, ta konfiguracja będzie częścią innego artykułu. 

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest tożsamość hybrydowa w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
