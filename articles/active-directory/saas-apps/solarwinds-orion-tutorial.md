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
ms.date: 07/24/2020
ms.author: jeedes
ms.openlocfilehash: 723546d6a997d895c1a1df39b558aabb797d0a11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545101"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą SolarWinds Orion

W tym samouczku dowiesz się, jak zintegrować usługę SolarWinds Orion z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SolarWinds Orion z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SolarWinds Orion.
* Zezwól użytkownikom na automatyczne logowanie do SolarWinds Orion przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO, SolarWinds Orion).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SolarWinds Orion obsługuje logowanie jednokrotne **z użyciem SP i dostawcy tożsamości**
* Po skonfigurowaniu SolarWinds Orion można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-solarwinds-orion-from-the-gallery"></a>Dodawanie SolarWinds Orion z galerii

Aby skonfigurować integrację usługi SolarWinds Orion w usłudze Azure AD, musisz dodać Orion SolarWinds z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SolarWinds Orion** w polu wyszukiwania.
1. Wybierz pozycję **SolarWinds Orion** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla SolarWinds Orion

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SolarWinds Orion przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SolarWinds Orion.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SolarWinds Orion, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie JEDNOkrotne w usłudze SolarWinds Orion](#configure-solarwinds-orion-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego programu SolarWinds Orion](#create-solarwinds-orion-test-user)** , aby uzyskać odpowiednika B. Simon w SolarWinds Orion, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SolarWinds Orion** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

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
    | Poczta e-mail |user.mail |

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
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do SolarWinds Orion.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SolarWinds Orion**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-solarwinds-orion-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze SolarWinds Orion

1. Zaloguj się do SolarWinds Orion i przejdź do **ustawień**  ->  **wszystkie ustawienia**.

    ![ Konfiguracja SolarWinds Orion ](./media/solarwinds-orion-tutorial/settings.png)

1. W sekcji **konta użytkowników** wybierz pozycję **Konfiguracja SAML**.

    ![ Konfiguracja SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Kliknij pozycję **Dodaj dostawcę tożsamości**.

    ![ Konfiguracja SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Na stronie **Dodawanie dostawcy tożsamości** wykonaj następujące czynności:

    ![ Konfiguracja SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Przejdź do karty **Konfiguracja** .

    b. W polu tekstowym **Nazwa dostawcy tożsamości** nadaj poprawną nazwę, taką jak `My SSO service` .

    c. W polu tekstowym **adres URL elementu docelowego logowania jednokrotnego** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d.  W polu tekstowym **adres URL wystawcy** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    e. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikat podpisywania X. 509** .

    f. Kliknij pozycję **Zapisz**.

### <a name="create-solarwinds-orion-test-user"></a>Utwórz użytkownika testowego SolarWinds Orion

1. Zaloguj się do witryny sieci Web SolarWinds Orion i przejdź do obszaru **Ustawienia**  ->  **wszystkie ustawienia**.

    ![ SolarWinds Orion Utwórz użytkownika testowego ](./media/solarwinds-orion-tutorial/settings.png)

1. W sekcji **konta użytkowników** wybierz pozycję **Zarządzaj kontami**.

    ![ SolarWinds Orion Utwórz użytkownika testowego ](./media/solarwinds-orion-tutorial/user-accounts.png)

1. Na karcie **pojedyncze konta** kliknij pozycję **Dodaj nowe konto**.

    ![ SolarWinds Orion Utwórz użytkownika testowego ](./media/solarwinds-orion-tutorial/create-user.png)

1. Wybierz typ konta, które ma być konieczne do utworzenia pojedynczych użytkowników lub grup SAML.

    ![ SolarWinds Orion Utwórz użytkownika testowego ](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  W polu tekstowym **Identyfikator nazwy** wprowadź nazwę, która musi być zgodna z nazwą użytkownika lub grupy, tak jak w usłudze Azure AD.

1.  Kliknij przycisk **dalej** , a następnie Prześlij stronę.

    ![ SolarWinds Orion Utwórz użytkownika testowego ](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SolarWinds Orion w panelu dostępu należy automatycznie zalogować się do Orion SolarWinds, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę SolarWinds Orion w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić SolarWinds Orion z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

