---
title: 'Samouczek: integracja Azure Active Directory z usługą LoginRadius | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i LoginRadius.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606423"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Samouczek: integracja Azure Active Directory z usługą LoginRadius

W tym samouczku dowiesz się, jak zintegrować usługę LoginRadius z usługą Azure Active Directory (Azure AD).

Integracja LoginRadius z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi LoginRadius.
* Możesz pozwolić użytkownikom na automatyczne logowanie do LoginRadius (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą LoginRadius, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze LoginRadius

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* LoginRadius obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-loginradius-from-the-gallery"></a>Dodawanie LoginRadius z galerii

Aby skonfigurować integrację programu LoginRadius z usługą Azure AD, musisz dodać LoginRadius z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać LoginRadius z galerii:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz ikonę **Azure Active Directory** .

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz opcję **wszystkie aplikacje** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** :

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **LoginRadius**, wybierz pozycję **LoginRadius** w panelu wyników, a następnie wybierz przycisk **Dodaj** , aby dodać aplikację.

    ![LoginRadius na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą LoginRadius na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w LoginRadius.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi LoginRadius, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-loginradius-single-sign-on)** jednokrotne w usłudze LoginRadius, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego LoginRadius](#create-loginradius-test-user)** , aby uzyskać odpowiednik Britta Simon w LoginRadius, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą LoginRadius, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **LoginRadius** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W okienku **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **edycji** , aby otworzyć okienko **podstawowe ustawienia protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** :

   ![LoginRadius domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

   1. W polu tekstowym **adres URL logowania** wprowadź adres URL `https://secure.loginradius.com/login`

   1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wprowadź adres URL `https://lr.hub.loginradius.com/`

   1. W polu tekstowym **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** wprowadź adres URL LoginRadius ACS `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

5. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji** z określonych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfiguracja LoginRadius** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

   - Adres URL logowania

   - Identyfikator usługi Azure AD

   - Adres URL wylogowywania

## <a name="configure-loginradius-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On LoginRadius

W tej sekcji włączasz Logowanie jednokrotne w usłudze Azure AD w konsoli administracyjnej LoginRadius.

1. Zaloguj się do konta [konsoli administratora](https://adminconsole.loginradius.com/login) LoginRadius.

2. Przejdź do sekcji **Zarządzanie zespołem** w [konsoli administracyjnej LoginRadius](https://secure.loginradius.com/account/team).

3. Wybierz kartę **Logowanie jednokrotne** , a następnie wybierz pozycję **Azure AD**:

   ![Zrzut ekranu pokazujący menu logowania jednokrotnego w konsoli zarządzania zespołem LoginRadius](./media/loginradius-tutorial/azure-ad.png)
4. Na stronie Konfiguracja usługi Azure AD wykonaj następujące czynności:

   ![Zrzut ekranu przedstawiający konfigurację Azure Active Directory w konsoli zarządzania zespołem LoginRadius](./media/loginradius-tutorial/single-sign-on.png)

    1. W polu **Lokalizacja dostawcy identyfikatora** wprowadź punkt końcowy logowania uzyskany na podstawie konta usługi Azure AD.

    1. W **adres URL wylogowania dostawcy identyfikatora** wprowadź punkt końcowy wylogowywania uzyskany na podstawie konta usługi Azure AD.
 
    1. W obszarze **Identyfikator certyfikatu dostawcy** wprowadź certyfikat usługi Azure AD, który uzyskasz z konta usługi Azure AD. Wprowadź wartość certyfikatu z nagłówkiem i stopką. Przykład: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. W polu **certyfikat dostawcy usług** i **klucz certyfikatu dostawcy serwera** wprowadź certyfikat i klucz. 

       Certyfikat z podpisem własnym można utworzyć, uruchamiając następujące polecenia w wierszu polecenia (Linux/Mac):

       - Polecenie pobrania klucza certyfikatu dla SP: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Polecenie pobrania certyfikatu dla programu SP: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Pamiętaj, aby wprowadzić certyfikat i wartości klucza certyfikatu z nagłówkiem i stopką:
       > - Przykładowy format wartości certyfikatu: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Przykładowy format wartości klucza certyfikatu: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. W sekcji **Mapowanie danych** wybierz pola (pola SP) i wprowadź odpowiednie pola usługi Azure AD (pola dostawcy tożsamości).

    Poniżej wymieniono niektóre z wymienionych poniżej nazw pól dla usługi Azure AD.

    | Pola    | Klucz profilu                                                          |
    | --------- | -------------------------------------------------------------------- |
    | E-mail     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName (Imię) | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName (Nazwisko)  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > Wymagane jest mapowanie pola **adresu e-mail** . Mapowania pól **FirstName** i **LastName** są opcjonalne.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Celem tej sekcji jest utworzenie użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach **użytkownika** wykonaj następujące czynności.

   ![Okno dialogowe Użytkownik](common/user-properties.png)

   1. W polu **Nazwa** wprowadź **BrittaSimon**.
  
   1. W polu **Nazwa użytkownika** wprowadź wartość `brittasimon@yourcompanydomain.extension` . Na przykład BrittaSimon@contoso.com.

   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi LoginRadius.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **LoginRadius**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **LoginRadius**.

    ![Link LoginRadius na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika** , a następnie wybierz pozycję **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy** , a następnie wybierz przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.

7. W okienku **Dodaj przypisanie** wybierz przycisk **Przypisz** .

### <a name="create-loginradius-test-user"></a>Utwórz użytkownika testowego LoginRadius

1. Zaloguj się do konta [konsoli administratora](https://adminconsole.loginradius.com/login) LoginRadius.

2. Przejdź do sekcji Zarządzanie zespołem w konsoli administracyjnej LoginRadius.

   ![Zrzut ekranu pokazujący konsolę administracyjną LoginRadius](./media/loginradius-tutorial/team-management.png)
3. Wybierz pozycję **Dodaj członka zespołu** w menu po stronie, aby otworzyć formularz. 

4. W formularzu **Dodaj członka zespołu** utworzysz użytkownika o nazwie Britta Simon w witrynie LoginRadius, podając szczegóły użytkownika i przypisując uprawnienia, które ma mieć użytkownik. Aby dowiedzieć się więcej na temat uprawnień opartych na rolach, zobacz sekcję [uprawnienia dostępu do roli](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) w dokumencie LoginRadius [Zarządzaj członkami zespołu](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

1. W przeglądarce przejdź do https://accounts.loginradius.com/auth.aspx i wybierz pozycję **Logowanie za pomocą logowania jednokrotnego w** usłudze.
2. Wprowadź nazwę aplikacji LoginRadius, a następnie wybierz pozycję **Zaloguj**.
3. Powinno to spowodować otwarcie okna podręcznego z prośbą o zalogowanie się do konta usługi Azure AD.
4. Po uwierzytelnieniu wyskakujące okienko zostanie zamknięte i użytkownik zostanie zalogowany do konsoli administracyjnej LoginRadius.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)
