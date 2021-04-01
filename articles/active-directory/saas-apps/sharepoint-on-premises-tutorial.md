---
title: 'Samouczek: integracja Azure Active Directory z lokalnym programem SharePoint | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i lokalnym programem SharePoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: a693b22c609829f3bf6e76637eac5793d73703e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96862313"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego z lokalnym programem SharePoint

Z tego samouczka dowiesz się, jak zintegrować lokalny program SharePoint z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu SharePoint w środowisku lokalnym z usługą Azure AD można:

* Kontroluj, kto ma dostęp do lokalnego programu SharePoint w usłudze Azure AD.
* Umożliwia użytkownikom automatyczne logowanie do lokalnego programu SharePoint przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z lokalnym programem SharePoint, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Farma programu SharePoint 2013 lub nowsza.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym. Użytkownicy z usługi Azure AD mogą uzyskiwać dostęp do lokalnego programu SharePoint.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Tworzenie aplikacji dla przedsiębiorstw w Azure Portal

Aby skonfigurować integrację lokalnego programu SharePoint z usługą Azure AD, musisz dodać lokalny program SharePoint z galerii do swojej listy zarządzanych aplikacji SaaS.

Aby dodać lokalne SharePoint z galerii:

1. W Azure Portal, w okienku po lewej stronie wybierz pozycję **Azure Active Directory**.

   > [!NOTE]
   > Jeśli element nie jest dostępny, można go również otworzyć za pomocą linku **wszystkie usługi** w górnej części okienka po lewej stronie. W poniższym przeglądzie łącze **Azure Active Directory** znajduje się w sekcji **tożsamość** . Możesz również wyszukać ją przy użyciu pola filtr.

1. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

1. W polu wyszukiwania wprowadź wartość **lokalna programu SharePoint**. W okienku wyników wybierz pozycję **lokalna programu SharePoint** .

    <kbd>![Lokalny program SharePoint na liście wyników](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Określ nazwę wystąpienia lokalnego programu SharePoint, a następnie wybierz pozycję **Dodaj** , aby dodać aplikację.

1. W nowej aplikacji dla przedsiębiorstw wybierz pozycję **Właściwości**, a następnie sprawdź wartość pola **wymagane przypisanie użytkownika?**.

   <kbd>![Czy przypisanie użytkownika jest wymagane? /](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   W tym scenariuszu wartość jest równa **no**.

## <a name="configure-and-test-azure-ad"></a>Konfigurowanie i testowanie usługi Azure AD

W tej sekcji skonfigurujesz Logowanie jednokrotne usługi Azure AD za pomocą programu SharePoint lokalnie. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w lokalnym programie SharePoint.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu lokalnego programu SharePoint, należy wykonać następujące bloki konstrukcyjne:

- [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
- [Skonfiguruj lokalnie program SharePoint](#configure-sharepoint-on-premises) , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
- [Utwórz użytkownika testowego usługi Azure AD w Azure Portal,](#create-an-azure-ad-test-user-in-the-azure-portal) aby utworzyć nowego użytkownika w usłudze Azure AD na potrzeby logowania jednokrotnego.
- [Utwórz grupę zabezpieczeń usługi Azure AD w Azure Portal,](#create-an-azure-ad-security-group-in-the-azure-portal) aby utworzyć nową grupę zabezpieczeń w usłudze Azure AD na potrzeby logowania jednokrotnego.
- [Udziel uprawnień do konta usługi Azure AD w lokalnym programie SharePoint](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) , aby przyznać uprawnienia użytkownikowi usługi Azure AD.
- [Udziel uprawnień do grupy usługi Azure AD w lokalnym programie SharePoint](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) w celu nadania uprawnień grupie usługi Azure AD.
- [Przyznaj dostęp do konta gościa lokalnemu programu SharePoint w Azure Portal,](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) aby przyznać uprawnienia do konta gościa w usłudze Azure AD dla lokalnego programu SharePoint.
- [Skonfiguruj zaufanego dostawcę tożsamości dla wielu aplikacji sieci Web](#configure-the-trusted-identity-provider-for-multiple-web-applications) , aby używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji sieci Web.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączasz Logowanie jednokrotne usługi Azure AD w Azure Portal.

Aby skonfigurować Logowanie jednokrotne w usłudze Azure AD przy użyciu lokalnego programu SharePoint:

1. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**. Wybierz wcześniej utworzoną nazwę aplikacji korporacyjnej i wybierz pozycję **Logowanie jednokrotne**.

1. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML** , aby włączyć logowanie jednokrotne.
 
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** .

1. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące kroki:

    ![Lokalna domena i adres URL programu SharePoint informacje logowania jednokrotnego](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. W polu **Identyfikator** wprowadź adres URL przy użyciu tego wzorca: `urn:<sharepointFarmName>:<federationName>` .

    1. W polu **adres URL odpowiedzi** wprowadź adres URL przy użyciu tego wzorca: `https://<YourSharePointSiteURL>/_trust/` .

    1. W polu **adres URL logowania** wprowadź adres URL przy użyciu tego wzorca: `https://<YourSharePointSiteURL>/` .
    1. Wybierz pozycję **Zapisz**.

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, identyfikatora i adresu URL odpowiedzi.

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **certyfikat (base64)** z danej opcji na podstawie wymagań i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. W sekcji **Konfigurowanie lokalnego programu SharePoint** skopiuj odpowiednie adresy URL na podstawie wymagań:
    
    - **Adres URL logowania**
    
        Skopiuj adres URL logowania i Zastąp **/SAML2** na końcu **/wsfed** , tak aby wyglądał tak, jak wygląda https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed . (Ten adres URL nie jest prawidłowy).

    - **Identyfikator usługi Azure AD**
    - **Adres URL wylogowywania**

    > [!NOTE]
    > Ten adres URL nie może być używany w programie SharePoint. Należy zastąpić **/SAML2** z **/wsfed**. Aplikacja lokalna programu SharePoint używa tokenu SAML 1,1, więc usługa Azure AD oczekuje żądania protokołu WS pokarmowego z serwera programu SharePoint. Po uwierzytelnieniu wystawia token SAML 1,1.

### <a name="configure-sharepoint-on-premises"></a>Konfigurowanie lokalnego programu SharePoint

1. Utwórz nowego zaufanego dostawcę tożsamości w programie SharePoint Server 2016.

    Zaloguj się do programu SharePoint Server i Otwórz powłokę zarządzania programu SharePoint. Wypełnij wartości:
    - **$Realm** to wartość identyfikatora z sekcji domena lokalna i adresy URL programu SharePoint w Azure Portal.
    - **$wsfedurl** jest adresem URL usługi SSO.
    - **$FilePath** jest ścieżką pliku, do której pobrano plik certyfikatu z Azure Portal.

    Uruchom następujące polecenia, aby skonfigurować nowego zaufanego dostawcę tożsamości.

    > [!TIP]
    > Jeśli dopiero zaczynasz pracę z programem PowerShell lub chcesz dowiedzieć się więcej na temat tego, jak działa program PowerShell, zobacz [SharePoint PowerShell](/powershell/sharepoint/overview).


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
1. Włącz zaufanego dostawcę tożsamości dla swojej aplikacji.

    1. W obszarze **Administracja centralna** przejdź do pozycji **Zarządzaj aplikacją sieci Web** i wybierz aplikację sieci Web, która ma być zabezpieczona za pomocą usługi Azure AD.

    1. Na wstążce wybierz pozycję **dostawcy uwierzytelniania** i wybierz strefę, której chcesz użyć.

    1. Wybierz pozycję **zaufany dostawca tożsamości** i wybierz pozycję Zidentyfikuj zarejestrowanego dostawcę o nazwie *AzureAD*.

    1. Wybierz przycisk **OK**.

    ![Konfigurowanie dostawcy uwierzytelniania](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Tworzenie użytkownika testowego usługi Azure AD w Azure Portal

Celem tej sekcji jest utworzenie użytkownika testowego w Azure Portal.

1. W Azure Portal, w okienku po lewej stronie wybierz pozycję **Azure Active Directory**. W okienku **Zarządzanie** wybierz pozycję **Użytkownicy**.

1. Wybierz pozycję **Wszyscy użytkownicy**  >  **nowy użytkownik** w górnej części ekranu.

1. Wybierz pozycję **Utwórz użytkownika** i we właściwościach użytkownika wykonaj następujące czynności. Możesz być w stanie tworzyć użytkowników w usłudze Azure AD przy użyciu sufiksu dzierżawy lub dowolnej zweryfikowanej domeny. 

    1. W polu **Nazwa** wprowadź nazwę użytkownika. Użyto **Użytkownik testowy**.
  
    1. W polu **Nazwa użytkownika** podaj wartość `TestUser@yourcompanydomain.extension`. Ten przykład pokazuje `TestUser@contoso.com` .

       ![Okno dialogowe Użytkownik](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz wartość, która pojawia się w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

    1. Możesz teraz udostępnić witrynę TestUser@contoso.com i zezwolić temu użytkownikowi na dostęp do niego.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Utwórz grupę zabezpieczeń usługi Azure AD w Azure Portal

1. Wybierz   >  **grupy** Azure Active Directory.

1. Wybierz pozycję **Nowa grupa**.

1. Wypełnij pola **Typ grupy**, **Nazwa grupy**, **Opis grupy** i **Typ członkostwa** . Wybierz strzałki, aby wybrać elementy członkowskie, a następnie wyszukaj lub wybierz członków, których chcesz dodać do grupy. Wybierz **pozycję Wybierz** , aby dodać wybrane elementy członkowskie, a następnie wybierz pozycję **Utwórz**.

![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Przyznawanie uprawnień do konta usługi Azure AD w środowisku lokalnym programu SharePoint

Aby udzielić dostępu do użytkownika usługi Azure AD w lokalnym programie SharePoint, Udostępnij zbiór witryn lub Dodaj użytkownika usługi Azure AD do jednej z grup kolekcji witryn. Użytkownicy mogą teraz logować się do programu SharePoint 201x przy użyciu tożsamości z usługi Azure AD, ale nadal istnieją okazje do ulepszenia środowiska użytkownika. Na przykład wyszukiwanie użytkownika przedstawia wiele wyników wyszukiwania w selektorze osób. Istnieje wynik wyszukiwania dla każdego z typów oświadczeń, które są tworzone w mapowaniu oświadczenia. Aby wybrać użytkownika przy użyciu selektora osób, należy dokładnie wprowadzić swoją nazwę użytkownika i wybrać wynik żądania **nazwy** .

![Wyniki wyszukiwania oświadczeń](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Nie ma sprawdzania poprawności szukanych wartości, co może prowadzić do błędów pisowni lub Przypadkowe wybranie błędnego typu roszczeń przez użytkowników. Ta sytuacja może uniemożliwić użytkownikom pomyślne uzyskiwanie dostępu do zasobów.

Aby naprawić ten scenariusz za pomocą selektora osób, rozwiązanie typu open source o nazwie [AzureCP](https://yvand.github.io/AzureCP/) udostępnia niestandardowego dostawcę oświadczeń dla programu SharePoint 2013, 2016 i 2019. Za pomocą interfejsu API Microsoft Graph można rozpoznać, co użytkownicy wprowadzają i sprawdzają poprawność. Aby uzyskać więcej informacji, zobacz [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Bez AzureCP można dodawać grupy przez dodanie identyfikatora grupy usługi Azure AD, ale ta metoda nie jest przyjazna i niezawodna dla użytkownika. Oto jak wygląda:
  > 
  >![Dodawanie grupy usługi Azure AD do grupy programu SharePoint według identyfikatora](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Przyznawanie uprawnień do grupy usługi Azure AD w środowisku lokalnym programu SharePoint

Aby przypisać grupy zabezpieczeń usługi Azure AD do lokalnego programu SharePoint, konieczne jest użycie niestandardowego dostawcy oświadczeń dla programu SharePoint Server. W tym przykładzie używa AzureCP.

 > [!NOTE]
 > AzureCP nie jest produktem firmy Microsoft i nie jest obsługiwany przez pomoc techniczna firmy Microsoft. Aby pobrać, zainstalować i skonfigurować AzureCP w lokalnej farmie programu SharePoint, zobacz witrynę sieci Web [AzureCP](https://yvand.github.io/AzureCP/) . 

1. Skonfiguruj AzureCP w farmie lokalnej programu SharePoint lub alternatywne rozwiązanie niestandardowego dostawcy oświadczeń. Aby skonfigurować AzureCP, zobacz witrynę sieci Web [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html) .

1. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**. Wybierz wcześniej utworzoną nazwę aplikacji korporacyjnej i wybierz pozycję **Logowanie jednokrotne**.

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** Edytuj **atrybuty użytkownika & sekcji oświadczenia** .

1. Wybierz pozycję **Dodaj zgłoszenie do grupy**.

1. Wybierz, które grupy skojarzone z użytkownikiem mają być zwracane w ramach żądania. W takim przypadku wybierz pozycję **wszystkie grupy**. W sekcji **atrybut źródłowy** wybierz pozycję **Identyfikator grupy** i wybierz pozycję **Zapisz**.

Aby udzielić dostępu do grupy zabezpieczeń usługi Azure AD w lokalnym programie SharePoint, należy udostępnić zbiór witryn lub dodać grupę zabezpieczeń usługi Azure AD do jednej z grup kolekcji witryn.

1. Przejdź do **kolekcji witryn programu SharePoint**. W obszarze **Ustawienia witryny** dla zbioru witryn wybierz pozycję **osoby i grupy**. 

1. Wybierz grupę programu SharePoint, a następnie wybierz pozycję **Nowy**  >  **Dodaj użytkowników do tej grupy**. Podczas wpisywania nazwy grupy Selektor osób wyświetla grupę zabezpieczeń usługi Azure AD.

    ![Dodawanie grupy usługi Azure AD do grupy programu SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Udzielanie dostępu do konta gościa w środowisku lokalnym programu SharePoint w Azure Portal

Można udzielić dostępu do witryny programu SharePoint do konta gościa w spójny sposób, ponieważ nazwa UPN jest teraz modyfikowana. Na przykład użytkownik `jdoe@outlook.com` jest reprezentowany jako `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` . Aby udostępnić witrynę użytkownikom zewnętrznym, należy dodać pewne modyfikacje w sekcji **atrybuty użytkownika & oświadczenia** w Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**. Wybierz wcześniej utworzoną nazwę aplikacji korporacyjnej i wybierz pozycję **Logowanie jednokrotne**.

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** Edytuj **atrybuty użytkownika & sekcji oświadczenia** .

1. W **wymaganej strefie żądania** wybierz pozycję **unikatowy identyfikator użytkownika (identyfikator nazwy)**.

1. Zmień właściwość **atrybutu źródłowego** na wartość **User. localuserprincipalname**, a następnie wybierz pozycję **Zapisz**.

    ![Atrybuty użytkownika & początkowy atrybut źródłowy oświadczenia](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Korzystając ze wstążki, Wróć do **logowania opartego na protokole SAML**. Teraz sekcja " **atrybuty użytkownika & oświadczenia** " wygląda następująco: 

    ![Atrybuty użytkownika, & oświadczenia ostateczne](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > W tej konfiguracji nie są wymagane nazwisko i imię.

1. W Azure Portal, w okienku po lewej stronie wybierz pozycję **Azure Active Directory** , a następnie wybierz pozycję **Użytkownicy**.

1. Wybierz pozycję **nowy użytkownik-Gość**.

1. Wybierz opcję **Zaproś użytkownika** . Wypełnij właściwości użytkownika i wybierz pozycję **Zaproś**.

1. Możesz teraz udostępnić witrynę MyGuestAccount@outlook.com i zezwolić temu użytkownikowi na dostęp do niego.

    ![Udostępnianie witryny przy użyciu konta gościa](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurowanie zaufanego dostawcy tożsamości dla wielu aplikacji sieci Web

Konfiguracja działa dla jednej aplikacji sieci Web, ale jest wymagana dodatkowa konfiguracja, jeśli zamierzasz używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji sieci Web. Załóżmy na przykład, że aplikacja sieci Web zostanie rozszerzona o adres URL `https://sales.contoso.com` , a teraz chcesz uwierzytelniać użytkowników `https://marketing.contoso.com` . W tym celu należy zaktualizować dostawcę tożsamości, aby honorować parametr WReply i zaktualizować rejestrację aplikacji w usłudze Azure AD, aby dodać adres URL odpowiedzi.

1. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**. Wybierz wcześniej utworzoną nazwę aplikacji korporacyjnej i wybierz pozycję **Logowanie jednokrotne**.

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** Edytuj **podstawową konfigurację SAML**.

    ![Podstawowa konfiguracja SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. W polu **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** Dodaj adres URL dodatkowych aplikacji sieci Web i wybierz pozycję **Zapisz**.

    ![Edytuj podstawową konfigurację języka SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. Na serwerze programu SharePoint Otwórz powłokę zarządzania programu SharePoint 201x i uruchom następujące polecenia. Użyj nazwy wystawcy zaufanego tokenu tożsamości, która została wcześniej użyta.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. W obszarze **Administracja centralna** przejdź do aplikacji sieci Web i Włącz istniejącego zaufanego dostawcę tożsamości.

Mogą istnieć inne scenariusze, w których chcesz zapewnić dostęp do lokalnego wystąpienia programu SharePoint dla użytkowników wewnętrznych. W tym scenariuszu należy wdrożyć Microsoft Azure Active Directory Connect, aby zezwolić na synchronizowanie lokalnych użytkowników z usługą Azure AD. Ta konfiguracja została omówiona w innym artykule.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu lokalnego programu SharePoint można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
