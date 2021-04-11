---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z programem Oracle PeopleSoft — chroniona przez usługę F5 BIG-IP APM | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Oracle PeopleSoft-Protected przy użyciu programu F5 BIG-IP APM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 3af149f0c1db7f354be6bd968bbd0cf858493d4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219301"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z programem Oracle PeopleSoft — chroniona przez F5

W tym samouczku dowiesz się, jak zintegrować usługę Oracle PeopleSoft — chronioną za pomocą programu F5 BIG-IP APM z Azure Active Directory (Azure AD). W przypadku integrowania usługi Oracle PeopleSoft — chronionej za pomocą protokołu F5 BIG-IP APM z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do firmy Oracle PeopleSoft — chronionej za pomocą usługi F5 BIG-IP APM.
* Zezwól użytkownikom na automatyczne logowanie do programu Oracle PeopleSoft — chronionego za pomocą usługi APM BIG-IP.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.
* Ten samouczek zawiera instrukcje dotyczące programu Oracle PeopleSoft ELM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

1. Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
1. Oracle PeopleSoft — ochrona przy użyciu protokołu F5 BIG-IP APM (Logowanie jednokrotne).

1. Wdrożenie wspólnego rozwiązania wymaga następującej licencji:

    1. F5 BIG-IP® najlepszym pakietem (lub) 
    2. F5 BIG-IP — licencja autonomiczna Menedżera zasad (APM)™ 
    3. F5 BIG-IP Access Manager — licencja dodatku™ (APM) na istniejącym® lokalnym™ Traffic Managerm Big-IP F5 (LTM).
    4. Oprócz powyższej licencji system F5 może również mieć licencję na: 
        * Subskrypcja filtrowania adresów URL do korzystania z bazy danych kategorii adresów URL. 
        * Subskrypcja analizy protokołu IP F5 w celu wykrywania i blokowania znanych ataków i złośliwego ruchu. 
        * Sprzętowy moduł zabezpieczeń (HSM) służący do zabezpieczania kluczy cyfrowych i zarządzania nimi w celu silnego uwierzytelniania.
1. System F5 BIG-IP jest inicjowany z modułami APM (LTM jest opcjonalny).
1. Chociaż jest to opcjonalne, zdecydowanie zaleca się wdrożenie systemów F5 w [grupie urządzeń synchronizacji/pracy w trybie failover](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), która obejmuje aktywną parę wstrzymania z ruchomym adresem IP w celu zapewnienia wysokiej dostępności. Więcej nadmiarowości interfejsu można uzyskać przy użyciu protokołu kontroli agregacji (LACP). LACP zarządza podłączonymi interfejsami fizycznymi jako pojedynczym interfejsem wirtualnym (Grupa agregowania) i wykrywa wszystkie błędy interfejsów w grupie.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oracle PeopleSoft — ochrona za pomocą F5 BIG-IP APM obsługuje logowanie jednokrotne z użyciem protokołu **SP i dostawcy tożsamości** .

## <a name="add-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Dodawanie programu Oracle PeopleSoft — chronionego za pomocą F5 BIG-IP APM z galerii

Aby skonfigurować integrację z programem Oracle PeopleSoft — chronioną za pomocą programu F5 BIG-IP APM do usługi Azure AD, należy dodać do listy zarządzanych aplikacji SaaS aplikację Oracle PeopleSoft — chronioną przez F5.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg **Oracle PeopleSoft — chroniony przez F5** .
1. Wybierz pozycję **Oracle PeopleSoft — chroniona przez F5 Big-IP APM** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu Oracle PeopleSoft — chronionego przez F5 BIG-IP APM

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Oracle PeopleSoft — chronionego za pomocą protokołu F5 BIG-IP APM przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Oracle PeopleSoft-Protected za pomocą usługi F5 BIG-IP APM.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą programu Oracle PeopleSoft — chronionego przez F5, należy wykonać następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj oprogramowanie Oracle PeopleSoft-Protected przez F5 Big-IP APM logowanie](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** jednokrotne — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz program oracle PeopleSoft-Protected przez F5, który jest użytkownikiem testowym usługi APM Big-IP](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** , aby mieć odpowiedni odpowiednik B. Simon w programie Oracle PeopleSoft — chroniony przez F5, usługa APM, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja z aplikacją **PeopleSoft firmy Oracle, która jest chroniona za pomocą F5 Big-IP** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<FQDN>.peoplesoft.f5.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. W polu tekstowym **adres URL wylogowywania** wpisz adres URL, używając następującego wzorca: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Te wartości nie są prawdziwe. Zaktualizuj te wartości z rzeczywistym adresem URL Sign-On, identyfikatorem, adresem URL odpowiedzi i adresem URL wylogowania. Skontaktuj się z [zespołem obsługi klienta programu Oracle PeopleSoft — chronionym przez F5 Big-IP APM](https://support.f5.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Oracle PeopleSoft — ochrona za pomocą F5 BIG-IP APM oczekuje potwierdzenia SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Oracle PeopleSoft — chroniona przez F5 BIG-IP APM oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | ------------------ | --------- |
    | POLA | user.employeeid |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Pobierz **plik XML metadanych Federacji** i **certyfikat (base64)** i Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do bazy danych Oracle PeopleSoft chronionej za pomocą usługi F5 BIG-IP APM.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Oracle PeopleSoft — chroniona przez F5 Big-IP APM**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Konfigurowanie programu Oracle PeopleSoft-Protected przez F5 BIG-IP APM Logowanie jednokrotne

### <a name="f5-saml-sp-configuration"></a>F5 — Konfiguracja protokołu SAML SP

Zaimportuj certyfikat metadanych do F5, który będzie używany później w procesie instalacji. Przejdź do **systemu > zarządzanie certyfikatami > zarządzania certyfikatami, > listy certyfikatów SSL**. Wybierz pozycję **Importuj** z prawej strony.

![F5 — Konfiguracja protokołu SAML SP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>Skonfiguruj łącznik SAML dostawcy tożsamości 

1. Przejdź do **dostępu > federacyjnej > SAML: Service Provider > zewnętrznych łączników dostawcy tożsamości** i kliknij pozycję **Utwórz > z metadanych**.

    ![Łącznik SAML dostawcy tożsamości F5](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. Na poniższej stronie kliknij przycisk **Przeglądaj** , aby przekazać plik XML.

1. Podaj prawidłową nazwę w polu tekstowym **Nazwa dostawcy tożsamości** , a następnie kliknij przycisk **OK**.

    ![Nowy łącznik SAML dostawcy tożsamości](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. Wykonaj wymagane kroki na karcie **Ustawienia zabezpieczeń** , a następnie kliknij przycisk **OK**.

    ![Edytuj łącznik SAML dostawcy tożsamości](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>Konfigurowanie protokołu SAML SP

1. Przejdź do **lokalizacji dostęp > federacyjnej > dostawcy usług SAML > lokalnych usług Sp** , a następnie kliknij przycisk **Utwórz**. Uzupełnij poniższe informacje i kliknij przycisk **OK**.

    * Nazwa: `<Name>`
    * Identyfikator jednostki: `https://<FQDN>.peoplesoft.f5.com`
    * Ustawienia nazw SP
        * Równaniu `https`
        * Host: `<FQDN>.peoplesoft.f5.com`
        * Zharmonizowan `<Description>`

    ![nowe usługi SAML SP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. Wybierz pozycję Konfiguracja SP, PeopleSoftAppSSO, a **następnie kliknij pozycję Powiązywanie/Usuwanie powiązania dostawcy tożsamości łączników**.
Kliknij przycisk **Dodaj nowy wiersz** i wybierz **zewnętrzny łącznik dostawcy tożsamości** utworzony w poprzednim kroku, kliknij przycisk **Aktualizuj**, a następnie kliknij przycisk **OK**.

    ![Tworzenie usługi SAML SP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Konfigurowanie aplikacji

### <a name="create-a-new-pool"></a>Utwórz nową pulę
1. Przejdź do **Przystawka ruch lokalny > pule > puli**, kliknij przycisk **Utwórz**, uzupełnij poniższe informacje, a następnie kliknij przycisk **Zakończono**.

    * Nazwa: `<Name>`
    * Zharmonizowan `<Description>`
    * Monitory kondycji: `http`
    * Ulica `<Address>`
    * Port usługi: `<Service Port>`

    ![Tworzenie nowej puli](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Utwórz nowy profil SSL klienta

Przejdź do **ruchu lokalnego > profile > SSL > klienta > +**, uzupełnij poniższe informacje i kliknij przycisk **Zakończ**.

* Nazwa: `<Name>`
* Certyfikatu `<Certificate>`
* Klucz: `<Key>`

![Nowy profil SSL klienta](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Utwórz nowy serwer wirtualny

1. Przejdź do **ruchu lokalnego > serwery wirtualne > listy serwerów wirtualnych > +**, uzupełnij poniższe informacje i kliknij przycisk **Zakończ**.
    * Nazwa: `<Name>`
    * Adres/maska miejsca docelowego: `<Address>`
    * Port usługi: port 443 HTTPS
    * Profil HTTP (klient): http

    ![Utwórz nowy serwer wirtualny](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Wypełnij następujące wartości na poniższej stronie:

    * Profil SSL (klient): `<SSL Profile>`
    * Translacja adresów źródłowych: Automap
    * Profil dostępu: `<Access Profile>`
    * Domyślna pula: `<Pool>`


    ![Utwórz nową pulę PeopleSoft serwera wirtualnego](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>Konfigurowanie aplikacji PeopleSoft do obsługi protokołu F5 Big-IP APM jako rozwiązania do logowania jednokrotnego

>[!Note]
> Odwoła https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. Zaloguj się do konsoli usługi PeopleSoft `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` przy użyciu poświadczeń administratora (przykład: PS/PS).

    ![Samoobsługa Menedżera](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. W aplikacji PeopleSoft Utwórz **OAMPSFT** jako nowy profil użytkownika i skojarz ją z niską rolą zabezpieczeń, taką jak **PeopleSoft User**.
Przejdź do **Peopletools > Security > profile użytkowników > profile użytkowników** , aby utworzyć nowy profil użytkownika, na przykład: **OAMPSFT** i Dodaj **użytkownika PeopleSoft**.

    ![PeopleSoft użytkownika](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Uzyskaj dostęp do profilu sieci Web i wprowadź **OAMPSFT** jako **Identyfikator użytkownika** dostępu publicznego.

    ![Profile użytkowników](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. W **Projektant aplikacji PeopleTools** otwórz rekord **FUNCLIB_LDAP** .

    ![Konfiguracja profilu sieci Web](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. Zaktualizuj nagłówek użytkownika za pomocą **PS_SSO_UID** dla funkcji **OAMSSO_AUTHENTICATION** .
W funkcji **getWWWAuthConfig ()** Zastąp wartość przypisaną do **&DefaultUserId** przy użyciu **OAMPSFT** zdefiniowanego w profilu sieci Web. Zapisz definicję rekordu.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Uzyskaj dostęp do strony **jednokrotnego PeopleCode** (PeopleTools, Security, Security Objects, jednokrotnego PeopleCode) i włącz funkcję **OAMSSO_AUTHENTICATION** — jednokrotnego PeopleCode for Oracle Access Manager Single jednokrotnego.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>Konfigurowanie programu F5 Big-IP APM do wypełniania nagłówka HTTP "PS_SSO_UID" przy użyciu identyfikatora użytkownika PeopleSoft

### <a name="configuring-per-request-policy"></a>Konfigurowanie zasad Per-Request
1. Przejdź do **dostępu > profilu/zasad > Per-Request zasad**, kliknij przycisk **Utwórz**, uzupełnij poniższe informacje, a następnie kliknij przycisk **Zakończ**.

    * Nazwa: `<Name>`
    * Typ profilu: wszystkie
    * Języki: `<Language>`

    ![Konfigurowanie zasad Per-Request ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Kliknij kolejno pozycje **edytuj** Per-Request zasady `<Name>` ![ Edytowanie zasad Per-Request PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Przypisywanie zasad Per-Request do serwera wirtualnego

Przejdź do **ruchu lokalnego > serwery wirtualne > listy serwerów wirtualnych > PeopleSoftApp** Określ `<Name>` jako zasady Per-Request

![PeopleSoftSSO jako zasady Per-Request ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>Konfigurowanie usługi F5 Big-IP APM do obsługi logowania jednokrotnego z aplikacji PeopleSoft

Aby dodać obsługę logowania jednokrotnego dla wszystkich użytkowników PeopleSoft, wykonaj następujące kroki:

1. Określ prawidłowy adres URL wylogowywania dla portalu PeopleSoft
    * Aby określić adres używany przez aplikację PeopleSoft do kończenia sesji użytkownika, należy otworzyć Portal przy użyciu dowolnej przeglądarki sieci Web i włączyć narzędzia debugowania przeglądarki, jak pokazano w poniższym przykładzie:

        ![adres URL wylogowywania dla portalu PeopleSoft ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Znajdź element z `PT_LOGOUT_MENU` identyfikatorem i Zapisz ścieżkę URL z parametrami zapytania. W naszym przykładzie mamy następującą wartość: `/psp/ps/?cmd=logout`

1. Utwórz LTM iRule, który przekieruje użytkownika na adres URL wylogowania APM: `/my.logout.php3`

    * Przejdź do **ruchu lokalnego > iRule**, kliknij przycisk **Utwórz**, uzupełnij poniższe informacje, a następnie kliknij przycisk **Zakończ**.

        Nazwa: `<Name>`  
        Definicja:  
                    _gdy HTTP_REQUEST {Switch-globalizowania--[HTTP:: URI] { `/psp/ps/?cmd=logout` {http:: redirect `/my.logout.php3` }}}_

1. Przypisywanie utworzonego iRule do serwera wirtualnego

    * Przejdź do **ruchu lokalnego > serwery wirtualne > listy serwerów wirtualnych > PeopleSoftApp > zasoby**. Kliknij przycisk **Zarządzaj...** przycisk   

    * Określ `<Name>` jako włączone iRule i kliknij przycisk **Zakończono**.

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * Nadaj **nazwę** wartości TextBox jako `<Name>` 

        ![Zakończono _iRule_PeopleSoftApp](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Tworzenie bazy danych Oracle PeopleSoft-Protected przez użytkownika testowego APM BIG-IP

W tej sekcji utworzysz użytkownika o nazwie B. Simon w bazie danych Oracle PeopleSoft-Protected przez F5. Współpracuj z [programem oracle PeopleSoft-Protected przez zespół pomocy technicznej Big-IP APM](https://support.f5.com) , aby dodać użytkowników w usłudze Oracle PeopleSoft-Protected za pomocą platformy F5 Big-IP APM. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do programu Oracle PeopleSoft-Protected przez F5 BIG-IP APM logowania na adres URL, w którym można zainicjować przepływ logowania.  

* Przejdź do strony Oracle PeopleSoft-Protected przez F5 BIG-IP APM logowanie bezpośrednio i zainicjuj w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do PeopleSoft-Protected Oracle za pomocą usługi APM Big-IP, dla której skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka programu Oracle PeopleSoft-Protected przez F5 BIG-IP APM w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do PeopleSoft-Protected Oracle przy użyciu usługi F5 BIG-IP, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Oracle PeopleSoft-Protected przy użyciu protokołu F5, aby można było wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
