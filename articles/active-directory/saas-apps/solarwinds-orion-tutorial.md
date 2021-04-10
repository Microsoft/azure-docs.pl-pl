---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą SolarWinds Orion | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SolarWinds Orion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: jeedes
ms.openlocfilehash: 4ac5bf2756b82361388ab9f2866b80c63395f90d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591388"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą SolarWinds Orion

W tym samouczku dowiesz się, jak zintegrować usługę SolarWinds Orion z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SolarWinds Orion z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SolarWinds Orion.
* Zezwól użytkownikom na automatyczne logowanie do SolarWinds Orion przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO, SolarWinds Orion).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SolarWinds Orion obsługuje logowanie jednokrotne **z użyciem SP i dostawcy tożsamości** .

## <a name="add-solarwinds-orion-from-the-gallery"></a>Dodaj SolarWinds Orion z galerii

Aby skonfigurować integrację usługi SolarWinds Orion w usłudze Azure AD, musisz dodać Orion SolarWinds z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SolarWinds Orion** w polu wyszukiwania.
1. Wybierz pozycję **SolarWinds Orion** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla SolarWinds Orion

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SolarWinds Orion przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SolarWinds Orion.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SolarWinds Orion, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie JEDNOkrotne w usłudze SolarWinds Orion](#configure-solarwinds-orion-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego programu SolarWinds Orion](#create-solarwinds-orion-test-user)** , aby uzyskać odpowiednika B. Simon w SolarWinds Orion, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SolarWinds Orion** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta SolarWinds Orion](mailto:technicalsupport@solarwinds.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja SolarWinds Orion oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja SolarWinds Orion oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | ----------- | --------- |
    | FirstName (Imię) | user.givenname |
    | LastName (Nazwisko) | user.surname |
    | E-mail |user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie SolarWinds Orion** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Evergreen.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Evergreen**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-solarwinds-orion-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze SolarWinds Orion

1. Zaloguj się do SolarWinds Orion i przejdź do **ustawień**  ->  **wszystkie ustawienia**.

    ![Zrzut ekranu przedstawia wszystkie ustawienia wybrane z ustawień.](./media/solarwinds-orion-tutorial/settings.png)

1. W sekcji **konta użytkowników** wybierz pozycję **Konfiguracja SAML**.

    ![Zrzut ekranu przedstawia konfigurację SAML wybraną z kont użytkowników.](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Kliknij pozycję **Dodaj dostawcę tożsamości**.

    ![Zrzut ekranu przedstawia konfigurację protokołu SAML, w której można wybrać opcję Dodaj dostawcę tożsamości.](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Na stronie **Dodawanie dostawcy tożsamości** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę Dodawanie dostawcy tożsamości, na której można wprowadzić podane wartości.](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Przejdź do karty **Konfiguracja** .

    b. W polu tekstowym **Nazwa dostawcy tożsamości** nadaj poprawną nazwę, taką jak `My SSO service` .

    c. W polu tekstowym **adres URL elementu docelowego logowania jednokrotnego** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d.  W polu tekstowym **adres URL wystawcy** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    e. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikat podpisywania X. 509** .

    f. Kliknij pozycję **Zapisz**.

### <a name="create-solarwinds-orion-test-user"></a>Utwórz użytkownika testowego SolarWinds Orion

1. Zaloguj się do witryny sieci Web SolarWinds Orion i przejdź do obszaru **Ustawienia**  ->  **wszystkie ustawienia**.

    ![Zrzut ekranu przedstawia wszystkie ustawienia wybrane z ustawień.](./media/solarwinds-orion-tutorial/settings.png)

1. W sekcji **konta użytkowników** wybierz pozycję **Zarządzaj kontami**.

    ![Zrzut ekranu przedstawia wybraną konfigurację protokołu SAML.](./media/solarwinds-orion-tutorial/user-accounts.png)

1. Na karcie **pojedyncze konta** kliknij pozycję **Dodaj nowe konto**.

    ![Zrzut ekranu przedstawia Dodaj nowe konto wybrane w obszarze Zarządzaj kontami.](./media/solarwinds-orion-tutorial/create-user.png)

1. Wybierz typ konta, które ma być konieczne do utworzenia pojedynczych użytkowników lub grup SAML.

    ![Zrzut ekranu przedstawia Dodawanie nowego konta, w którym można wybrać typ konta.](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  W polu tekstowym **Identyfikator nazwy** wprowadź nazwę, która musi być zgodna z nazwą użytkownika lub grupy, tak jak w usłudze Azure AD.

1.  Kliknij przycisk **dalej** , a następnie Prześlij stronę.

    ![Zrzut ekranu przedstawia Dodawanie nowego konta, w którym można wprowadzić nazwę I D z platformy Azure A D.](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL SolarWinds Orion logowania, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania SolarWinds Orion i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do SolarWinds Orion, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka SolarWinds Orion w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do SolarWinds Orion, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu SolarWinds Orion można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
