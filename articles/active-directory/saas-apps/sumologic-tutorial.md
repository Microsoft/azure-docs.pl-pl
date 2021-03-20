---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą SumoLogic | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SumoLogic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 2dcc52688cabebaa6eb813e3240150ea8774e716
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521900"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą SumoLogic

W tym samouczku dowiesz się, jak zintegrować usługę SumoLogic z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SumoLogic z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SumoLogic.
* Zezwól użytkownikom na automatyczne logowanie się do usługi SumoLogic przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) SumoLogic.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SumoLogic obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

## <a name="adding-sumologic-from-the-gallery"></a>Dodawanie SumoLogic z galerii

Aby skonfigurować integrację programu SumoLogic z usługą Azure AD, musisz dodać SumoLogic z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SumoLogic** w polu wyszukiwania.
1. Wybierz pozycję **SumoLogic** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla SumoLogic

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SumoLogic przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SumoLogic.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SumoLogic, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-sumologic-sso)** w usłudze SumoLogic, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego SumoLogic](#create-sumologic-test-user)** , aby dysponować odpowiednikiem B. Simon w SumoLogic, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SumoLogic** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta SumoLogic](https://www.sumologic.com/contact-us/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja SumoLogic oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja SumoLogic oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    |  Nazwa | Atrybut źródłowy |
    | ---------------| --------------- |
    | FirstName (Imię) | user.givenname |
    | LastName (Nazwisko) | user.surname |
    | Role | user.assignedroles |

    > [!NOTE]
    > Kliknij [tutaj](../develop/active-directory-enterprise-app-role-management.md) , aby dowiedzieć się, jak skonfigurować **rolę** w usłudze Azure AD.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie SumoLogic** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi SumoLogic.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SumoLogic**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sumologic-sso"></a>Konfigurowanie logowania jednokrotnego SumoLogic

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny SumoLogic jako administrator.

1. Przejdź do pozycji **Zarządzaj \> zabezpieczeniami**.

    ![Zarządzanie](./media/sumologic-tutorial/ic778556.png "Zarządzanie")

1. Kliknij pozycję **SAML**.

    ![Globalne ustawienia zabezpieczeń](./media/sumologic-tutorial/ic778557.png "Globalne ustawienia zabezpieczeń")

1. Z listy **wybierz konfigurację lub Utwórz nową** wybierz pozycję **Azure AD**, a następnie kliknij pozycję **Konfiguruj**.

    ![Zrzut ekranu przedstawia konfigurację SAML 2,0, w której można wybrać platformę Azure A D.](./media/sumologic-tutorial/ic778558.png "Konfigurowanie protokołu SAML 2,0")

1. W oknie dialogowym **Konfigurowanie protokołu SAML 2,0** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia okno dialogowe Konfigurowanie protokołu SAML 2,0, w którym można wprowadzić podane wartości.](./media/sumologic-tutorial/ic778559.png "Konfigurowanie protokołu SAML 2,0")

    a. W polu tekstowym **nazwa konfiguracji** wpisz **Azure AD**.

    b. Wybierz **tryb debugowania**.

    c. W polu tekstowym **wystawca** wklej wartość identyfikatora usługi **Azure AD**, która została skopiowana z Azure Portal.

    d. W polu tekstowym **adres URL żądania Authn** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    e. Otwórz certyfikat zakodowany w formacie Base-64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej cały certyfikat do pola tekstowego **certyfikatu X. 509** .

    f. Jako **atrybut poczty e-mail** wybierz opcję **Użyj podmiotu SAML**.  

    przykład Wybierz pozycję **Konfiguracja logowania zainicjowana przez program SP**.

    h. W polu tekstowym **ścieżka logowania** wpisz **platformę Azure** , a następnie kliknij przycisk **Zapisz**.

### <a name="create-sumologic-test-user"></a>Utwórz użytkownika testowego SumoLogic

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze SumoLogic, muszą one być obsługiwane do SumoLogic. W przypadku SumoLogic, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **SumoLogic** .

1. Przejdź do pozycji **Manage \> Users** (Zarządzaj > Użytkownicy).

    ![Zrzut ekranu przedstawia użytkowników wybranych z menu Zarządzaj.](./media/sumologic-tutorial/ic778561.png "Użytkownicy")

1. Kliknij pozycję **Dodaj**.

    ![Zrzut ekranu przedstawia przycisk Dodaj dla użytkowników.](./media/sumologic-tutorial/ic778562.png "Użytkownicy")

1. W oknie dialogowym **nowy użytkownik** wykonaj następujące czynności:

    ![Nowy użytkownik](./media/sumologic-tutorial/ic778563.png "Nowy użytkownik")

    a. Wpisz powiązane informacje o koncie usługi Azure **AD, które** chcesz udostępnić w polach tekstowych **imię**, nazwisko i **adres e-mail** .
  
    b. Wybierz rolę.
  
    c. Jako **stan** wybierz pozycję **aktywny**.
  
    d. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników SumoLogic i interfejsów API udostępnionych przez usługę SumoLogic.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SumoLogic w panelu dostępu należy automatycznie zalogować się do SumoLogic, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj SumoLogic z usługą Azure AD](https://aad.portal.azure.com/)