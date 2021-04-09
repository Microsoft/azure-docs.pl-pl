---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Spotinst | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Spotinst.
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
ms.openlocfilehash: 402b5a975b3ded8327edcea81b680e9990bac39e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686591"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Spotinst

W tym samouczku dowiesz się, jak zintegrować usługę Spotinst z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Spotinst z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Spotinst.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Spotinst przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Spotinst.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Spotinst obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-spotinst-from-the-gallery"></a>Dodawanie Spotinst z galerii

Aby skonfigurować integrację programu Spotinst z usługą Azure AD, musisz dodać Spotinst z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Spotinst** w polu wyszukiwania.
1. Wybierz pozycję **Spotinst** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Spotinst

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Spotinst przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Spotinst.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Spotinst, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-spotinst-sso)** w usłudze Spotinst, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Spotinst](#create-spotinst-test-user)** , aby dysponować odpowiednikiem B. Simon w Spotinst, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Spotinst** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowania dostawcy tożsamości, w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

   1. Upewnij się, że **adres URL odpowiedzi** jest ustawiony na: https://console.spotinst.com/auth/saml .
   1. W obszarze **Stan przekazywania** wprowadź identyfikator organizacji Spotinst, który można także potwierdzić na karcie **Logowanie jednokrotne** .
   1. **Adres URL logowania** musi być pusty.

1. Kliknij pozycję **Zapisz**.

1. Aplikacja Spotinst oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Spotinst oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | -----| --------------- |
    | E-mail | user.mail |
    | FirstName (Imię) | user.givenname |
    | LastName (Nazwisko) | user.surname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Spotinst** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Spotinst.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Spotinst**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-spotinst-sso"></a>Konfigurowanie logowania jednokrotnego Spotinst

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby Spotinst jako administrator zabezpieczeń.

2. Kliknij **ikonę użytkownika** w prawym górnym rogu ekranu, a następnie kliknij pozycję **Ustawienia**.

    ![Zrzut ekranu przedstawia ustawienia wybrane z ikony użytkownika.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Kliknij kartę **zabezpieczenia** u góry, a następnie wybierz pozycję **dostawcy tożsamości** i wykonaj następujące czynności:

    ![Zabezpieczenia Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Skopiuj wartość **stanu przekaźnika** dla swojego wystąpienia i wklej ją w polu tekstowym **Stan przekazywania** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. Kliknij przycisk **Przeglądaj** , aby przesłać plik XML metadanych pobrany z Azure Portal

    c. Kliknij przycisk **Zapisz**.

### <a name="create-spotinst-test-user"></a>Utwórz użytkownika testowego Spotinst

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w Spotinst.

1. Jeśli aplikacja została skonfigurowana w trybie zainicjowania programu **SP** , wykonaj następujące czynności:

   a. W innym oknie przeglądarki sieci Web Zaloguj się, aby Spotinst jako administrator zabezpieczeń.

   b. Kliknij **ikonę użytkownika** w prawym górnym rogu ekranu, a następnie kliknij pozycję **Ustawienia**.

    ![Zrzut ekranu przedstawia ustawienia wybrane z ikony użytkownika.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Kliknij pozycję **Użytkownicy** i wybierz pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawia Dodawanie użytkownika wybranego od użytkowników.](./media/spotinst-tutorial/adduser1.png)

    d. W sekcji Dodawanie użytkownika wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Dodawanie użytkownika, w której można wprowadzić podane wartości.](./media/spotinst-tutorial/adduser2.png)

    * W polu tekstowym **pełna nazwa** wprowadź pełną nazwę użytkownika `BrittaSimon` .

    * W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np `brittasimon@contoso.com` .

    * Wybierz szczegóły dotyczące organizacji **, roli konta i kont**.

2. Jeśli aplikacja została skonfigurowana w trybie inicjowania **dostawcy tożsamości** , w tej sekcji nie ma elementu Action. Spotinst obsługuje obsługę just-in-Time, która jest domyślnie włączona. Nowy użytkownik zostanie utworzony podczas próby uzyskania dostępu do Spotinst, jeśli jeszcze nie istnieje.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Spotinst w panelu dostępu należy automatycznie zalogować się do Spotinst, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Spotinst z usługą Azure AD](https://aad.portal.azure.com/)