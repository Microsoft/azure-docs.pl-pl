---
title: 'Samouczek: integracja Azure Active Directory z lokalnym programem SharePoint | Microsoft Docs'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81867105"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z lokalnym programem SharePoint

W tym samouczku dowiesz się, jak zintegrować program SharePoint lokalnie z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu SharePoint w środowisku lokalnym z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do lokalnego programu SharePoint.
* Umożliwia użytkownikom automatyczne logowanie do lokalnego programu SharePoint przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację Azure Active Directory z lokalnym programem SharePoint, potrzebne są następujące elementy:

* Subskrypcja usługi Azure Active Directory. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Farma programu SharePoint 2013 lub nowsza.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Azure Active Directory Logowanie jednokrotne w środowisku testowym. Użytkownicy z Azure Active Directory będą mogli uzyskiwać dostęp do lokalnego programu SharePoint.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Tworzenie aplikacji dla przedsiębiorstw w Azure Portal

Aby skonfigurować integrację lokalnego programu SharePoint z usługą Azure AD, musisz dodać lokalny program SharePoint z galerii do swojej listy zarządzanych aplikacji SaaS.

Aby dodać lokalny program SharePoint z galerii, wykonaj następujące czynności:

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

 > [!NOTE]
 > Jeśli element nie powinien być dostępny, można go również otworzyć za pomocą linku naprawione **wszystkie usługi** w górnej części lewego panelu nawigacji. W poniższym omówieniu link **Azure Active Directory** znajduje się w sekcji **tożsamość** lub można go wyszukać przy użyciu pola tekstowego filtr.

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

4. W polu wyszukiwania wpisz **lokalny program SharePoint**, wybierz pozycję **SharePoint on-premises** w panelu wyników.

    <kbd>![Lokalny program SharePoint na liście wyników](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Określ nazwę dla lokalnego programu SharePoint i kliknij przycisk **Dodaj** , aby dodać aplikację.

1. W nowej aplikacji dla przedsiębiorstw kliknij pozycję Właściwości i sprawdź wartość pola **wymagane przypisanie użytkownika** .

   <kbd>![Lokalny program SharePoint na liście wyników](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

w naszym scenariuszu ta wartość jest równa **no**.

## <a name="configure-and-test-azure-ad"></a>Konfigurowanie i testowanie usługi Azure AD

W tej sekcji skonfigurujesz Logowanie jednokrotne w usłudze Azure AD przy użyciu lokalnego programu SharePoint.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem lokalnego programu SharePoint.

Aby skonfigurować i przetestować Azure Active Directory Logowanie jednokrotne przy użyciu lokalnego programu SharePoint, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj lokalnie program SharePoint](#configure-sharepoint-on-premises)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD w Azure Portal](#create-an-azure-ad-test-user-in-the-azure-portal)** — Utwórz nowego użytkownika w usłudze Azure AD w celu logowania jednokrotnego.
1. **[Utwórz grupę zabezpieczeń usługi Azure AD w Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** — Utwórz nową grupę zabezpieczeń w usłudze Azure AD w celu logowania jednokrotnego.
1. **[Przyznawanie uprawnień do Azure Active Directory konta w lokalnym programie SharePoint](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** — nadaj uprawnienia użytkownikowi usługi Azure AD.
1. **[Przyznawanie uprawnień do grupy usługi Azure AD w lokalnym programie SharePoint](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** — nadaj uprawnienia grupie usługi Azure AD.
1. **[Przyznaj dostęp do konta gościa lokalnie do lokalnego programu SharePoint w Azure Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** -Udziel uprawnień kontu gościa w usłudze Azure AD dla lokalnego programu SharePoint.
1. **[Konfigurowanie zaufanego dostawcy tożsamości dla wielu aplikacji sieci Web](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** — jak używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji sieci Web

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą lokalnego programu SharePoint, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)Otwórz katalog usługi Azure AD, kliknij pozycję aplikacje dla **przedsiębiorstw**, kliknij **wcześniej utworzoną nazwę aplikacji korporacyjnej** i kliknij pozycję Logowanie **jednokrotne**.

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego kliknij tryb **SAML** , aby włączyć logowanie jednokrotne.
 
3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** kliknij ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe Konfigurowanie protokołu SAML** .

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL lokalnego programu SharePoint na potrzeby logowania jednokrotnego](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. W polu **Identyfikator** wpisz adres URL, używając następującego wzorca:`urn:<sharepointFarmName>:<federationName>`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YourSharePointSiteURL>/_trust/`

    1. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca:`https://<YourSharePointSiteURL>/`
    1. Kliknij pozycję Zapisz.

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. W sekcji **Skonfiguruj lokalny program SharePoint ** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.
    
    1. **Adres URL logowania**
    
        Kopiuj adres URL logowania Zastąp **/SAML2** na końcu **/wsfed**, tak jak **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** (ten adres URL nie jest prawidłowy)

    1. **Identyfikator usługi Azure AD**
    1. **Adres URL wylogowywania**
    > [!NOTE]
    > Ten adres URL nie może być używany w programie SharePoint: należy zastąpić **/SAML2** z **/wsfed**. Aplikacja Lokalny program SharePoint używa tokenu SAML 1.1, dzięki czemu usługa Azure AD oczekuje żądania usługi WS-Fed z serwera programu SharePoint, a po uwierzytelnieniu wystawia token SAML 1.1 .

### <a name="configure-sharepoint-on-premises"></a>Konfigurowanie lokalnego programu SharePoint

1. **Tworzenie nowego zaufanego dostawcy tożsamości w programie SharePoint Server 2016**

    Zaloguj się do programu SharePoint Server i Otwórz powłokę zarządzania programu SharePoint. Wypełnij wartości:
    1. **$Realm** (wartość identyfikatora z sekcji domena lokalna i adresy URL programu SharePoint w Azure Portal).
    1. **$wsfedurl** (adres URL usługi logowania jednokrotnego).
   1. **$FilePath** (ścieżka pliku, do której pobrano plik certyfikatu) z Azure Portal.

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
1. **Włącz zaufanego dostawcę tożsamości dla swojej aplikacji**

    a. W witrynie Administracja centralna przejdź do sekcji **Zarządzanie aplikacją internetową** i wybierz aplikację internetową, którą chcesz zabezpieczyć za pomocą usługi Azure AD.

    b. Na wstążce kliknij pozycję **Dostawcy uwierzytelniania** i wybierz strefę, której chcesz użyć.

    c. Kliknij pozycję **Trusted Identity Provider** (Zaufany dostawca tożsamości), a następnie kliknij pozycję *Add Trusted Identity Provider* (Dodaj zaufanego dostawcę tożsamości).

    d. Kliknij przycisk **OK**.

    ![Konfigurowanie dostawcy uwierzytelniania](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Tworzenie użytkownika testowego usługi Azure AD w Azure Portal

Celem tej sekcji jest utworzenie użytkownika testowego w Azure Portal.

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**, w obszarze **Zarządzaj** kadrowaniem wybierz pozycję **Użytkownicy**.

2. Następnie wybierz opcję **Wszyscy użytkownicy** , a następnie **nowy użytkownik** w górnej części ekranu.

3. Wybierz opcję **Utwórz użytkownika** i we właściwościach użytkownika, wykonaj następujące czynności.  
   Możesz być w stanie tworzyć użytkowników w usłudze Azure AD przy użyciu sufiksu dzierżawy lub dowolnej zweryfikowanej domeny. 

    a. W polu **Nazwa** wprowadź nazwę użytkownika, która została użyta w **Użytkownik testowy**.
  
    b. W polu **Nazwa użytkownika** wpisz `TestUser@yourcompanydomain.extension`  
    Na przykład: TestUser@contoso.com

    ![Okno dialogowe Użytkownik](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

    e. Możesz teraz udostępnić witrynę TestUser@contoso.com i zezwolić temu użytkownikowi na dostęp do niego.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Utwórz grupę zabezpieczeń usługi Azure AD w Azure Portal

1. Kliknij **Azure Active Directory > grupy**.

2. Kliknij pozycję **Nowa grupa**:

3. Wypełnij pola **Typ grupy**, **Nazwa grupy**, **Opis grupy**, **Typ członkostwa**. Kliknij strzałkę, aby wybrać elementy członkowskie, a następnie wyszukaj lub kliknij element członkowski, który chcesz dodać do grupy. Kliknij pozycję **Wybierz** , aby dodać wybrane elementy członkowskie, a następnie kliknij pozycję **Utwórz**.

![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Przyznawanie uprawnień do Azure Active Directory konta w lokalnym programie SharePoint

Aby udzielić dostępu Azure Active Directory użytkownikowi w lokalnym programie SharePoint należy udostępnić zbiór witryn lub dodać użytkownika Azure Active Directory do jednej z grup kolekcji witryn. Użytkownicy mogą teraz logować się do programu SharePoint 201x przy użyciu tożsamości z usługi Azure AD, ale nadal istnieją okazje do ulepszenia środowiska użytkownika. Na przykład wyszukiwanie użytkownika przedstawia wiele wyników wyszukiwania w selektorze osób. Istnieje wynik wyszukiwania dla każdego z typów oświadczeń, które są tworzone w mapowaniu oświadczenia. Aby wybrać użytkownika za pomocą selektora osób, należy dokładnie wpisać jego nazwę użytkownika i wybrać wynik oświadczenia **name**.

![Wyniki wyszukiwania oświadczeń](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Nie ma sprawdzania poprawności szukanych wartości, co może prowadzić do błędów pisowni lub Przypadkowe wybranie błędnego typu roszczeń przez użytkowników. Może to uniemożliwić użytkownikom pomyślny dostęp do zasobów.

**Aby naprawić selektora osób** w tym scenariuszu, istnieje rozwiązanie typu "open source" o nazwie [AzureCP](https://yvand.github.io/AzureCP/) , które udostępnia niestandardowego dostawcę oświadczeń dla programu SharePoint 2013, 2016 i 2019. Użyje interfejsu API Microsoft Graph, aby rozwiązać, co użytkownicy wprowadzają i sprawdzają poprawność. Dowiedz się więcej o rozwiązaniu [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Bez AzureCP można dodawać grupy, dodając identyfikator grupy usługi Azure AD, ale nie jest to przyjazne i niezawodne przez użytkownika. Oto jak wygląda:  
  >   
  >![Dodaj grupę usługi Azure AD do grupy programu SharePoint](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Przyznawanie uprawnień grupie usługi Azure AD w środowisku lokalnym programu SharePoint

Aby można było przypisywać Azure Active Directory grupy zabezpieczeń do lokalnego programu SharePoint, konieczne będzie użycie niestandardowego dostawcy oświadczeń dla programu SharePoint Server. W naszym przykładzie użyto AzureCP.

 > [!NOTE]
 > Należy pamiętać, że AzureCP nie jest produktem firmy Microsoft ani nie jest obsługiwany przez pomoc techniczną firmy Microsoft. Pobierz, zainstaluj i skonfiguruj AzureCP w lokalnej farmie programu SharePoint na https://yvand.github.io/AzureCP/. 

1. Skonfiguruj AzureCP w farmie lokalnej programu SharePoint lub alternatywne rozwiązanie niestandardowego dostawcy oświadczeń. kroki konfiguracji AzureCP są dostępne pod adresemhttps://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. W Azure Portal Otwórz katalog usługi Azure AD. Kliknij pozycję **aplikacje dla przedsiębiorstw**, kliknij **wcześniej utworzoną nazwę aplikacji korporacyjnej** i kliknij pozycję Logowanie **jednokrotne**.

1. Na stronie **Skonfiguruj Logowanie jednokrotne za pomocą sam**, Edytuj sekcję **atrybuty użytkownika & oświadczenia** .

1. Kliknij pozycję **Dodaj zgłoszenie do grupy**.

1. Wybierz, które grupy skojarzone z użytkownikiem mają być zwracane w ramach tego żądania, w naszym przypadku wybierz pozycję **wszystkie grupy** i w sekcji atrybut źródłowy wybierz pozycję **Identyfikator grupy** i kliknij pozycję **Zapisz**.

Aby udzielić dostępu do Azure Active Directory grupy zabezpieczeń w lokalnym programie SharePoint, należy udostępnić zbiór witryn lub dodać grupę zabezpieczeń Azure Active Directory do jednej z grup kolekcji witryn.

1. Przejdź do kolekcji witryn programu SharePoint, w obszarze Ustawienia witryny dla zbioru witryn kliknij pozycję "osoby i grupy". Wybierz grupę programu SharePoint, a następnie kliknij pozycję Nowy, Dodaj użytkowników do tej grupy, a następnie wpisz nazwę grupy Selektor osób będzie wyświetlał Azure Active Directory grupę zabezpieczeń.

    ![Dodaj grupę usługi Azure AD do grupy programu SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Udzielanie dostępu do konta gościa w środowisku lokalnym programu SharePoint w Azure Portal

Teraz można udzielić dostępu do witryny programu SharePoint do konta gościa w spójny sposób. Dzieje się tak, że nazwa UPN zostanie zmodyfikowana. Użytkownik z jdoe@outlook.com będzie reprezentowany jako `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. W przypadku udostępniania lokacji użytkownikom zewnętrznym możliwe jest bezproblemowe środowisko, dlatego należy dodać modyfikacje w sekcji **atrybuty użytkownika & oświadczenia** w Azure Portal.

1. W Azure Portal Otwórz katalog usługi Azure AD. Kliknij pozycję **aplikacje dla przedsiębiorstw**, kliknij **wcześniej utworzoną nazwę aplikacji korporacyjnej** i kliknij pozycję Logowanie **jednokrotne**.

1. Na stronie **Skonfiguruj Logowanie jednokrotne za pomocą sam**, Edytuj sekcję **atrybuty użytkownika & oświadczenia** .

1. w **wymaganej strefie roszczeń** kliknij **unikatowy identyfikator użytkownika (identyfikator nazwy)**.

1. Zmień właściwość **atrybutu źródłowego** na wartość **User. localuserprincipalname** i **Zapisz**.

    ![Atrybuty użytkownika & początkowe oświadczenia](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Za pomocą wstążki Wróć do **logowania opartego na protokole SAML** teraz, **atrybuty użytkownika & oświadczenia** będzie wyglądać następująco: 

    ![Atrybuty użytkownika, & oświadczenia ostateczne](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Nazwisko i imię nie są wymagane w tej konfiguracji.

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Użytkownicy**.

1. Kliknij **nowego użytkownika-gościa**

1. Wybierz opcję **Zaproś użytkownika** i wypełnij w górę właściwości użytkownika, a następnie kliknij pozycję **Zaproś**.

1. Możesz teraz udostępnić witrynę MyGuestAccount@outlook.com i zezwolić temu użytkownikowi na dostęp do niego.

    ![Udostępnianie witryny przy użyciu konta gościa](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurowanie zaufanego dostawcy tożsamości dla wielu aplikacji sieci Web

Konfiguracja działa w przypadku jednej aplikacji internetowej, ale wymaga dodatkowej konfiguracji, jeśli zamierzasz używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji internetowych. Załóżmy na przykład, że rozszerzyliśmy aplikację internetową w celu używania adresu URL `https://sales.contoso.com` i teraz chcemy również uwierzytelniać użytkowników w `https://marketing.contoso.com`. Aby to zrobić, musimy zaktualizować dostawcę tożsamości, aby uwzględnić parametr WReply i zaktualizować rejestrację aplikacji w usłudze Azure AD w celu dodania adresu URL odpowiedzi.

1. W Azure Portal Otwórz katalog usługi Azure AD. Kliknij pozycję **aplikacje dla przedsiębiorstw**, kliknij **wcześniej utworzoną nazwę aplikacji korporacyjnej** i kliknij pozycję Logowanie **jednokrotne**.

2. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu Menedżera sam**Edytuj **podstawową konfigurację języka SAML**.

    ![Edytuj podstawową konfigurację języka SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. W polu **adres URL odpowiedzi (adres URL usługi konsumenckej potwierdzenia)** Dodaj adres URL dodatkowych aplikacji sieci Web i kliknij przycisk **Zapisz**.

    ![Edytuj podstawową konfigurację języka SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Na serwerze programu SharePoint Otwórz **powłokę zarządzania programu SharePoint 201x** i wykonaj następujące polecenia, używając nazwy wystawcy zaufanego tokenu tożsamości, który został wcześniej użyty.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. W witrynie Administracja centralna przejdź do aplikacji internetowej i włącz istniejącego zaufanego dostawcy tożsamości.

Możesz mieć inny scenariusz, w którym chcesz zapewnić dostęp do lokalnego programu SharePoint dla użytkowników wewnętrznych, w tym scenariuszu należy wdrożyć Microsoft Azure Active Directory Connect, które umożliwią synchronizowanie użytkowników lokalnych z usługą Azure Active Directory, ta konfiguracja będzie częścią innego artykułu. 

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest tożsamość hybrydowa z Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
