---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą OpenAthens | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą OpenAthens.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 7754da168465c58c1481f355f3597a621a99609e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522312"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą OpenAthens

W tym samouczku dowiesz się, jak zintegrować usługę OpenAthens z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi OpenAthens z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do OpenAthens.
* Zezwól użytkownikom na automatyczne logowanie się do usługi OpenAthens przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) OpenAthens.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa OpenAthens obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**
* Usługa OpenAthens obsługuje aprowizację użytkowników typu **Just In Time**

## <a name="adding-openathens-from-the-gallery"></a>Dodawanie usługi OpenAthens z galerii

Aby skonfigurować integrację usługi OpenAthens z usługą Azure AD, musisz dodać usługę OpenAthens z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **OpenAthens** w polu wyszukiwania.
1. Wybierz pozycję **OpenAthens** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla OpenAthens

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą OpenAthens przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w OpenAthens.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą OpenAthens, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-openathens-sso)** w usłudze OpenAthens, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego OpenAthens](#create-openathens-test-user)** , aby dysponować odpowiednikiem B. Simon w OpenAthens, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **OpenAthens** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** przekaż **plik metadanych dostawcy usług**, dla którego kroki zostały wymienione w dalszej części tego samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie metadanych usługi Openathens](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![Przeglądanie i przekazywanie metadanych usługi Openathens](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartość **Identyfikator** zostanie automatycznie wypełniona w polu tekstowym sekcji **Podstawowa konfiguracja protokołu SAML**:

    ![Domena i adresy URL usługi OpenAthens — informacje o logowaniu jednokrotnym](common/idp-identifier.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie OpenAthens** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi OpenAthens.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **OpenAthens**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-openathens-sso"></a>Konfigurowanie logowania jednokrotnego OpenAthens

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny OpenAthens jako administrator.

1. Wybierz pozycję **Connections** (Połączenia) z listy na karcie **Management** (Zarządzanie).

    ![Zrzut ekranu przedstawiający stronę firmy "OpenAthens" z opcją "Connections" wybraną z karty "Zarządzanie".](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Wybierz pozycję **SAML 1.1/2.0**, a następnie wybierz przycisk **Configure** (Konfiguruj).

    ![Zrzut ekranu pokazujący "Wybieranie lokalnego uwierzytelniania systemu". Zostanie wyświetlone okno dialogowe z opcją "S A M L 1.1/2.0" i wybranym przyciskiem "Konfiguruj".](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. W celu dodania konfiguracji wybierz przycisk **Browse** (Przeglądaj), aby przekazać plik xml metadanych pobrany z witryny Azure Portal, a następnie wybierz przycisk **Add** (Dodaj).

    ![Zrzut ekranu pokazujący system uwierzytelniania "Dodaj S A M L". zostanie wyświetlone okno dialogowe z akcją "Przeglądaj" i "Dodaj".](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Wykonaj poniższe kroki na karcie **Details** (Szczegóły).

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. W obszarze **Display name mapping** (Mapowanie nazwy wyświetlanej) wybierz opcję **Use attribute** (Użyj atrybutu).

    b. W polu tekstowym **Display name attribute** (Atrybut nazwy wyświetlanej) wprowadź wartość `http://schema.microsoft.com/identity/claims/displayname`.

    c. W obszarze **Unique user mapping** (Mapowanie unikatowego użytkownika) wybierz opcję **Use attribute** (Użyj atrybutu).

    d. W polu tekstowym **Unique user attribute** (Atrybut unikatowego użytkownika) wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. W obszarze **Status** (Stan) zaznacz wszystkie trzy pola wyboru.

    f. W obszarze **Create local accounts** (Tworzenie kont lokalnych) wybierz opcję **automatically** (automatycznie).

    przykład Wybierz pozycję **Save changes** (Zapisz zmiany).

    h. Z karty **</> Relying Party** (</> Jednostka uzależniona) skopiuj adres **Metadata URL** (Adres URL metadanych) i otwórz go w przeglądarce, aby pobrać plik **XML metadanych dostawcy usług**. Przekaż ten plik metadanych dostawcy usług w sekcji **Podstawowa konfiguracja protokołu SAML** w usłudze Azure AD.

    ![Zrzut ekranu pokazujący wybraną kartę "Jednostka uzależniona" i wyróżnioną pozycję "metadane U R L".](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Tworzenie użytkownika testowego usługi OpenAthens

W tej sekcji w usłudze OpenAthens jest tworzony użytkownik o nazwie Britta Simon. Usługa OpenAthens obsługuje **aprowizację użytkowników just in time**, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w usłudze OpenAthens, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka OpenAthens w panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi OpenAthens, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj OpenAthens z usługą Azure AD](https://aad.portal.azure.com/)