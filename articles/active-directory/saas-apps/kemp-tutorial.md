---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Kemp LoadMaster Azure AD Integration | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i integracją usługi Azure AD Kemp LoadMaster.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 7ac50acbf9b1b4371ac17f997828f9b8818e53b0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459122"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym przy użyciu integracji usługi Azure AD Kemp LoadMaster

W tym samouczku dowiesz się, jak zintegrować integrację usługi Kemp LoadMaster Azure AD z usługą Azure Active Directory (Azure AD). Po zintegrowaniu integracji usługi Kemp LoadMaster Azure AD z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do integracji usługi Azure AD Kemp LoadMaster.
* Zezwól użytkownikom na automatyczne logowanie do usługi Kemp LoadMaster Azure AD Integration przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) usługi Azure AD (Kemp LoadMaster).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Kemp LoadMaster, integracja z usługą Azure AD obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne
* Po skonfigurowaniu integracji usługi Kemp LoadMaster w usłudze Azure AD można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>Dodawanie integracji usługi Azure AD Kemp LoadMaster z galerii

Aby skonfigurować integrację usługi Kemp LoadMaster Azure AD Integration z usługą Azure AD, musisz dodać integrację usługi Azure AD z galerii Kemp LoadMaster do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Kemp LoadMaster Azure AD Integration** w polu wyszukiwania.
1. Wybierz pozycję **Kemp LoadMaster integracja z usługą Azure AD** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Kemp LoadMaster Azure AD Integration

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Kemp LoadMaster Azure AD Integration przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Kemp LoadMaster Azure AD Integration.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi Kemp LoadMaster Azure AD Integration, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Skonfiguruj usługę rejestracji jednokrotnej w usłudze **[Kemp LoadMaster Azure AD](#configure-kemp-loadmaster-azure-ad-integration-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.

1. **[Publikowanie serwera sieci Web](#publishing-web-server)**
    1. **[Tworzenie usługi wirtualnej](#create-a-virtual-service)**
    1. **[Certyfikaty i zabezpieczenia](#certificates-and-security)**
    1. **[Profil SAML integracji usługi Azure AD Kemp LoadMaster](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[Weryfikowanie zmian](#verify-the-changes)**
1. **[Konfigurowanie uwierzytelniania opartego na protokole Kerberos](#configuring-kerberos-based-authentication)**
    1. **[Utwórz konto delegowania protokołu Kerberos dla integracji usługi Kemp LoadMaster Azure AD](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[Kemp LoadMaster Azure AD Integration KCD (konta delegowania protokołu Kerberos)](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[Kemp LoadMaster Azure AD Integration ESP](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Utwórz użytkownika testowego integracji usługi Kemp LoadMaster w usłudze Azure AD](#create-kemp-loadmaster-azure-ad-integration-test-user)** , aby uzyskać odpowiednik usługi B. Simon w Kemp LOADMASTER Azure AD, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Kemp LoadMaster Azure AD Integration** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta integracji usługi Azure AD Kemp LoadMaster](mailto:support@kemp.ax) . Przydatne mogą się również okazać wzorce przedstawione w sekcji Podstawowa konfiguracja protokołu SAML w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i **XML metadanych Federacji**, wybierz pozycję **Pobierz** , aby pobrać plik XML certyfikatu i metadanych Federacji, i Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](./media/kemp-tutorial/certificate-base-64.png)

1. W sekcji **Konfigurowanie usługi Kemp LoadMaster Azure AD Integration** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do integracji Kemp LoadMaster usługi Azure AD.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Kemp LoadMaster integracja z usługą Azure AD**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Konfigurowanie usługi Kemp LoadMaster Azure AD Integration SSO

## <a name="publishing-web-server"></a>Publikowanie serwera sieci Web 

### <a name="create-a-virtual-service"></a>Tworzenie usługi wirtualnej 

1. Przejdź do Kemp LoadMaster Azure AD Integration LoadMaster Web UI > usług wirtualnych > Dodaj nowy.

1. Kliknij przycisk Dodaj nowy.

1. Określ parametry usługi wirtualnej.

    ![Zrzut ekranu przedstawiający stronę "Określ parametry dla usługi wirtualnej" z przykładowymi wartościami w polach.](./media/kemp-tutorial/kemp-1.png)

    a. Adres wirtualny
    
    b. Port
    
    c. Nazwa usługi (opcjonalnie)
    
    d. Protokół 

1. Przejdź do sekcji prawdziwe serwery.

1. Kliknij pozycję Dodaj nowe.

1. Określ parametry rzeczywistego serwera.
    
    ![Zrzut ekranu przedstawiający stronę "Określ parametry dla rzeczywistego serwera" z przykładowymi wartościami w polach.](./media/kemp-tutorial/kemp-2.png)

    a. Wybierz opcję Zezwalaj na adresy zdalne
    
    b. Wpisz prawdziwy adres serwera
    
    c. Port
    
    d. Metoda przekazująca
    
    e. Waga
    
    f. Limit połączeń
    
    przykład Kliknij przycisk Dodaj ten rzeczywisty serwer

## <a name="certificates-and-security"></a>Certyfikaty i zabezpieczenia
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Importowanie certyfikatu w usłudze Kemp LoadMaster Azure AD Integration 
 
1. Przejdź do portalu sieci Web Kemp LoadMaster Azure AD Integration > Certificates & Security > certyfikaty SSL.

1. W obszarze Zarządzanie certyfikatami > konfiguracja certyfikatu.

1. Kliknij pozycję Importuj certyfikat.

1. Określ nazwę pliku, który zawiera certyfikat. Plik może również zawierać klucz prywatny. Jeśli plik nie zawiera klucza prywatnego, należy również określić plik zawierający klucz prywatny. Certyfikat może być w obu. PEM lub. Format PFX (IIS).

1. Kliknij pozycję Wybierz plik w pliku certyfikatu.

1. Kliknij plik klucza (opcjonalnie).

1. Kliknij pozycję Zapisz.

### <a name="ssl-acceleration"></a>Przyspieszenie SSL
 
1. Przejdź do usługi Kemp LoadMaster Web UI > usług wirtualnych > widoków/modyfikacji usług.

1. Kliknij przycisk Modyfikuj w obszarze Operacja.

1. Kliknij pozycję Właściwości protokołu SSL (która działa w warstwie 7).
    
    ![Zrzut ekranu pokazujący sekcję "S S L właściwości" z wybraną opcją "S S-Enabled — włączonym przyspieszeniem" i wybranym przykładem certyfikatu.](./media/kemp-tutorial/kemp-3.png)
    
    a. Kliknij włączony przyspieszenie SSL.
    
    b. W obszarze dostępne certyfikaty wybierz zaimportowany certyfikat i kliknij `>` symbol.
    
    c. Po pojawieniu się żądanego certyfikatu SSL w polu przypisane certyfikaty kliknij pozycję **Ustaw certyfikaty**.

    > [!NOTE]
    > Upewnij się, że klikniesz pozycję **Ustaw certyfikaty**.

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Profil SAML integracji usługi Azure AD Kemp LoadMaster
 
### <a name="import-idp-certificate"></a>Importuj certyfikat dostawcy tożsamości

Przejdź do Kemp LoadMaster usługi Azure AD Integration Web Console 

1. Kliknij pozycję Certyfikaty pośrednie w obszarze Certyfikaty i urząd.

    ![Zrzut ekranu przedstawiający sekcję "aktualnie zainstalowane certyfikaty pośrednie" z wybranym przykładem certyfikatu.](./media/kemp-tutorial/kemp-6.png)

    a. Kliknij pozycję Wybierz plik w obszarze Dodaj nowy certyfikat pośredni.
    
    b. Przejdź do pliku certyfikatu, który został wcześniej pobrany z aplikacji Azure AD Enterprise.
    
    c. Kliknij pozycję Otwórz.
    
    d. Podaj nazwę w polu Nazwa certyfikatu.
    
    e. Kliknij pozycję Dodaj certyfikat.

### <a name="create-authentication-policy"></a>Utwórz zasady uwierzytelniania 
 
Przejdź do pozycji Zarządzanie logowaniem jednokrotnym w obszarze usługi wirtualne.

   ![Zrzut ekranu przedstawiający stronę "Zarządzanie S".](./media/kemp-tutorial/kemp-7.png)
   
   a. Kliknij przycisk Dodaj w obszarze Dodaj nową konfigurację po stronie klienta po podania nazwy.

   b. Wybierz pozycję SAML w obszarze Protokół uwierzytelniania.

   c. Wybierz pozycję plik metadanych w obszarze dostawcy tożsamościing (obsługa administracyjna). 

   d. Kliknij pozycję Wybierz plik.

   e. Przejdź do wcześniej pobranego kodu XML z Azure Portal.

   f. Kliknij pozycję Otwórz i kliknij pozycję Importuj plik metadanych dostawcy tożsamości.

   przykład Wybierz certyfikat pośredni z certyfikatu dostawcy tożsamości.

   h. Ustaw identyfikator jednostki SP, który powinien być zgodny z tożsamością utworzoną w Azure Portal 

   i. Kliknij pozycję Ustaw identyfikator jednostki SP.

### <a name="set-authentication"></a>Ustaw uwierzytelnianie  
 
W konsoli sieci Web integracji usługi Kemp LoadMaster Azure AD

1. Kliknij pozycję usługi wirtualne.

1. Kliknij pozycję Wyświetl/Modyfikuj usługi.

1. Kliknij przycisk Modyfikuj i przejdź do opcji ESP.
    
    ![Zrzut ekranu przedstawiający stronę "Wyświetl/Modyfikuj usługi" z rozwiniętymi sekcjami "Opcje ESP" i "prawdziwe serwery".](./media/kemp-tutorial/kemp-8.png)

    a. Kliknij pozycję Włącz ESP.
    
    b. Wybierz pozycję SAML w trybie uwierzytelniania klienta.
    
    c. Wybierz wcześniej utworzone uwierzytelnianie po stronie klienta w domenie logowania jednokrotnego.
    
    d. Wpisz nazwę hosta w polu dozwolone hosty wirtualne i kliknij opcję Ustaw dozwolone hosty wirtualne.
    
    e. Wpisz/* w dozwolonych katalogach wirtualnych (na podstawie wymagań dostępu) i kliknij pozycję Ustaw katalogi dozwolone.

### <a name="verify-the-changes"></a>Weryfikowanie zmian 
 
Przejdź do adresu URL aplikacji 

Powinna zostać wyświetlona strona logowania dzierżawców zamiast wcześniej nieuwierzytelnionego dostępu. 

![Zrzut ekranu przedstawiający stronę "Logowanie" w dzierżawie.](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Konfigurowanie uwierzytelniania opartego na protokole Kerberos 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Utwórz konto delegowania protokołu Kerberos dla integracji usługi Kemp LoadMaster Azure AD 

1. Utwórz konto użytkownika (w tym przykładzie AppDelegation).
    
    ![Zrzut ekranu przedstawiający okno "KCD User Properties" z wybraną kartą "Account" (konto).](./media/kemp-tutorial/kemp-10.png)


    a. Wybierz kartę Edytor atrybutów.

    b. Przejdź do właściwości ServicePrincipalName. 

    c. Wybierz pozycję servicePrincipalName, a następnie kliknij pozycję Edytuj.

    d. Wpisz http/kcduser w polu wartość do dodania, a następnie kliknij przycisk Dodaj. 

    e. Kliknij przycisk Zastosuj i OK. Okno musi zostać zamknięte przed jego otwarciem (aby wyświetlić kartę nowe delegowanie). 

1. Otwórz ponownie okno właściwości użytkownika, a karta delegowanie zostanie udostępniona. 

1. Wybierz kartę Delegowanie.

    ![Zrzut ekranu przedstawiający okno "właściwości użytkownika KCD" z wybraną kartą "delegowanie".](./media/kemp-tutorial/kemp-11.png)

    a. Wybierz pozycję Ufaj temu użytkownikowi w przypadku delegowania tylko do określonych usług.

    b. Wybierz pozycję Użyj dowolnego protokołu uwierzytelniania.

    c. Dodaj prawdziwe serwery i Dodaj http jako usługę. 
    
    d. Zaznacz pole wyboru rozwinięte. 

    e. Można zobaczyć wszystkie serwery z nazwą hosta i FQDN.
    
    f. Kliknij przycisk OK.

> [!NOTE]
> Ustaw nazwę SPN w aplikacji/witrynie sieci Web, jeśli ma to zastosowanie. Aby uzyskać dostęp do aplikacji, gdy została ustawiona tożsamość puli aplikacji. Aby uzyskać dostęp do aplikacji IIS przy użyciu nazwy FQDN, przejdź do rzeczywistego wiersza polecenia serwera i wpisz SetSpn z wymaganymi parametrami. Na przykład: Setspn – S HTTP/sescoindc. sunehes. co. in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>Kemp LoadMaster Azure AD Integration KCD (konta delegowania protokołu Kerberos) 

Przejdź do Kemp LoadMaster usługi Azure AD Integration Konsola sieci Web > usług wirtualnych > zarządzanie logowaniem jednokrotnym.

![Zrzut ekranu przedstawiający stronę "Zarządzanie domeną zarządzania i domeny".](./media/kemp-tutorial/kemp-12.png)

a. Przejdź do konfiguracji logowania jednokrotnego po stronie serwera.

b. Wpisz nazwę w polu Dodaj nową konfigurację Server-Side i kliknij przycisk Dodaj.

c. Wybierz opcję ograniczone delegowanie protokołu Kerberos w protokole uwierzytelniania.

d. Wpisz nazwę domeny w obszarze Kerberos.

e. Kliknij pozycję Ustaw obszar Kerberos.

f. Wpisz adres IP kontrolera domeny w centrum dystrybucji kluczy Kerberos.

przykład Kliknij pozycję Ustaw protokół KDC Kerberos.

h. W polu Nazwa użytkownika usługi Kerberos wpisz KCD nazwę użytkownika.

i. Kliknij pozycję Ustaw zaufaną nazwę użytkownika centrum dystrybucji kluczy.

j. Wpisz hasło w polu Zaufane hasło użytkownika protokołu Kerberos.

k. Kliknij pozycję Ustaw KCD zaufanego hasła użytkownika.

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Kemp LoadMaster Azure AD Integration ESP 

Przejdź do usługi Virtual Services, > przeglądać/modyfikować usługi.

![Serwer webKemp LoadMaster Azure AD Integration](./media/kemp-tutorial/kemp-13.png)

a. Kliknij przycisk Modyfikuj na nazwie nick usługi wirtualnej.
    
b. Kliknij pozycję Opcje ESP.
    
c. W obszarze tryb uwierzytelniania serwera wybierz pozycję KCD.
        
d. W obszarze Konfiguracja Server-Side wybierz utworzony wcześniej profil po stronie serwera.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Tworzenie użytkownika testowego integracji usługi Kemp LoadMaster w usłudze Azure AD

W tej sekcji utworzysz użytkownika o nazwie B. Simon w usłudze Kemp LoadMaster Azure AD Integration. Współpraca z [zespołem pomocy technicznej usługi Kemp LoadMaster Azure AD Integration](mailto:support@kemp.ax) , aby dodać użytkowników na platformie integracji usługi Azure AD Kemp LoadMaster. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka integracja z usługą Azure AD Kemp LoadMaster w panelu dostępu należy automatycznie zalogować się do integracji z usługą Azure AD Kemp LoadMaster, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj integrację usługi Kemp LoadMaster Azure AD z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić integrację usługi Kemp LoadMaster Azure AD z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)