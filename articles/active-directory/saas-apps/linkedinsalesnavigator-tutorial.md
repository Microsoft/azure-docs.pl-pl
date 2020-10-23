---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia do nawigatora sprzedaży w serwisie LinkedIn | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i nawigatorem sprzedaży w serwisie LinkedIn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: 3f4d4d1e0fc44b9fb031ce5e3e45219fa8169562
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458510"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą nawigatora sprzedaży w serwisie LinkedIn

W tym samouczku dowiesz się, jak zintegrować nawigatora sprzedaży serwisu LinkedIn z Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD nawigatora sprzedaży w serwisie LinkedIn można:

* Kontrolka w usłudze Azure AD, która ma dostęp do nawigatora sprzedaży w serwisie LinkedIn.
* Zezwól użytkownikom na automatyczne logowanie do nawigatora sprzedaży w serwisie LinkedIn przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi LinkedIn Sales Navigator — Rejestracja jednokrotna (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Nawigator sprzedaży serwisu LinkedIn obsługuje usługę **SP i dostawcy tożsamości** zainicjowane przez usługę SSO
* Nawigator sprzedaży w serwisie LinkedIn obsługuje funkcję aprowizacji użytkowników **just in Time**
* Nawigator sprzedaży w serwisie LinkedIn obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Dodawanie nawigatora sprzedaży serwisu LinkedIn z galerii

Aby skonfigurować integrację usługi LinkedIn Sales Navigator z usługą Azure AD, musisz dodać nawigatora sprzedaży serwisu LinkedIn z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Nawigator sprzedaży w serwisie LinkedIn** w polu wyszukiwania.
1. Wybierz kolejno pozycje **LinkedIn Sales Navigator** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla nawigatora sprzedaży w serwisie LinkedIn

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą nawigatora sprzedaży LinkedIn przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze LinkedIn Sales Navigator.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą nawigatora sprzedaży w serwisie LinkedIn, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę LinkedIn Sales Navigator — logowanie JEDNOkrotne](#configure-linkedin-sales-navigator-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego nawigatora sprzedaży w serwisie LinkedIn](#create-linkedin-sales-navigator-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze LinkedIn Sales Navigator, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji w usłudze **LinkedIn Sales Navigator** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wprowadź wartość **identyfikatora jednostki** . wartość identyfikatora jednostki zostanie skopiowana z portalu LinkedIn w dalszej części tego samouczka.

    b. W polu tekstowym **adres URL odpowiedzi** wprowadź wartość **adresu URL dostępu konsumenta potwierdzenia (ACS** ), a w dalszej części tego samouczka skopiuj wartość adresu URL dostępu do odbiorcy usługi ACS z portalu LinkedIn.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. Aplikacja nawigatora sprzedaży w serwisie LinkedIn oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja nawigatora sprzedaży w serwisie LinkedIn oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | --- | --- |
    | poczta e-mail| user.mail |
    | działu,| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    | Unikatowy identyfikator użytkownika | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie nawigatora sprzedaży w serwisie LinkedIn** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do nawigatora sprzedaży w serwisie LinkedIn.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz opcję **Nawigator sprzedaży w serwisie LinkedIn**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-linkedin-sales-navigator-sso"></a>Konfigurowanie rejestracji jednokrotnej w serwisie LinkedIn Sales Navigator

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny sieci Web **nawigatora sprzedaży** w serwisie LinkedIn jako administrator.

1. W **Centrum kont** kliknij przycisk **Ustawienia globalne** w obszarze **Ustawienia**. Ponadto wybierz pozycję **Nawigator sprzedaży** z listy rozwijanej.

    ![Zrzut ekranu przedstawia ustawienia aplikacji, w których można wybrać opcję Nawigator sprzedaży.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknij **lub kliknij tutaj, aby załadować i skopiować pojedyncze pola z formularza** , i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia pojedyncze Sign-On, w których można wprowadzić podane wartości.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Skopiuj **Identyfikator jednostki** i wklej go do pola tekstowego **Identyfikator** w **podstawowej konfiguracji SAML** w Azure Portal.

    b. Skopiuj **adres URL dostępu konsumenta (ACS) potwierdzenia** i wklej go w polu tekstowym **adres URL odpowiedzi** w **podstawowej konfiguracji SAML** w Azure Portal.

1. Przejdź do sekcji **LinkedIn Admin Settings** (Ustawienia administratora LinkedIn). Przekaż pobrany plik XML z Azure Portal, klikając opcję **Przekaż plik XML** .

    ![Zrzut ekranu przedstawia Konfigurowanie ustawień dostawcy usług LinkedIn, w których można przekazać plik X M L.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknij przycisk **On** (Włącz), aby włączyć funkcję logowania jednokrotnego. Stan funkcji logowania jednokrotnego zmieni się z **Not Connected** (Niepołączona) na **Connected** (Połączona)

    ![Zrzut ekranu przedstawia pojedyncze Sign-On, na których można włączyć uwierzytelnianie użytkowników za pomocą S S O.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Tworzenie użytkownika testowego nawigatora sprzedaży w serwisie LinkedIn

Aplikacja połączonego nawigatora sprzedaży obsługuje Inicjowanie obsługi użytkowników just in Time (JIT) i po automatycznym utworzeniu użytkowników uwierzytelniania w aplikacji. Aktywuj **automatyczne przypisywanie licencji** , aby przypisać licencję do użytkownika.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Nawigator sprzedaży w serwisie LinkedIn w panelu dostępu należy automatycznie zalogować się do nawigatora sprzedaży w serwisie LinkedIn, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj nawigatora sprzedaży serwisu LinkedIn z usługą Azure AD](https://aad.portal.azure.com/)