---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z portalem użytkowników B2B rozwiązania Zscaler | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Portal użytkowników B2B rozwiązania Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.openlocfilehash: f6b7fb79b5aab1ee3ea6b5b710c766c1a34b099c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519813"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu portalu użytkowników B2B rozwiązania Zscaler

W tym samouczku dowiesz się, jak zintegrować Portal użytkowników B2B rozwiązania Zscaler z usługą Azure Active Directory (Azure AD). Po zintegrowaniu portalu użytkowników B2B rozwiązania Zscaler z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do portalu użytkownika B2B rozwiązania Zscaler.
* Zezwól użytkownikom na automatyczne logowanie do portalu użytkowników B2B rozwiązania Zscaler przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) w portalu użytkownika B2B rozwiązania Zscaler.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Portal użytkownika B2B rozwiązania Zscaler obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

* Portal użytkowników B2B rozwiązania Zscaler obsługuje Inicjowanie obsługi użytkowników **just in Time**

* Po skonfigurowaniu portalu użytkownika B2B rozwiązania Zscaler można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Dodawanie portalu użytkownika B2B rozwiązania Zscaler z galerii

Aby skonfigurować integrację portalu użytkowników B2B rozwiązania Zscaler z usługą Azure AD, musisz dodać portal użytkowników B2B rozwiązania Zscaler z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **rozwiązania ZSCALER Portal użytkowników B2B** w polu wyszukiwania.
1. Wybierz pozycję **rozwiązania Zscaler Portal użytkowników B2B** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla portalu użytkowników B2B rozwiązania Zscaler

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą portalu użytkownika B2B rozwiązania Zscaler przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w portalu użytkowników B2B rozwiązania Zscaler.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą portalu użytkowników B2B rozwiązania Zscaler, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj rozwiązania ZSCALER logowanie](#configure-zscaler-b2b-user-portal-sso)** jednokrotne w portalu użytkownika B2B — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego portalu użytkownika B2B](#create-zscaler-b2b-user-portal-test-user)** w systemie rozwiązania Zscaler, aby dysponować odpowiednikiem B. Simon w portalu użytkownika B2B rozwiązania Zscaler, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **portalu użytkownika B2B rozwiązania Zscaler** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej w portalu użytkowników B2B rozwiązania Zscaler](https://help.zscaler.com/) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie portalu użytkowników B2B rozwiązania Zscaler** , skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do portalu użytkownika B2B rozwiązania Zscaler.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler Portal użytkowników B2B**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Konfigurowanie rejestracji jednokrotnej w portalu użytkownika B2B rozwiązania Zscaler

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmowej portalu użytkownika B2B rozwiązania Zscaler jako administrator i wykonaj następujące czynności:

1. Z lewej strony menu kliknij pozycję **Administracja** i przejdź do sekcji **uwierzytelnianie** , a następnie kliknij pozycję **Konfiguracja dostawcy tożsamości**.

    ![Administracja administratora dostępu prywatnego rozwiązania Zscaler](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. W prawym górnym rogu kliknij pozycję **Dodaj konfigurację dostawcy tożsamości**. 

    ![Administrator dostępu prywatnego rozwiązania Zscaler dostawcy tożsamości](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Na stronie **Dodawanie konfiguracji dostawcy tożsamości** wykonaj następujące czynności:
 
    ![Rozwiązania Zscaler administrator dostępu prywatnego](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Kliknij pozycję **Wybierz plik** , aby przekazać pobrany plik metadanych z usługi Azure AD w polu **przekazywania pliku metadanych dostawcy tożsamości** .

    b. Odczytuje **metadane dostawcy tożsamości** z usługi Azure AD i wypełnia wszystkie informacje o polach, jak pokazano poniżej.

    ![Rozwiązania Zscaler konfiguracja administratora dostępu prywatnego](./media/zscaler-b2b-user-tutorial/config.png)

    c. Wybierz **domenę z pola domeny.**
    
    d. Kliknij pozycję **Zapisz**.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Utwórz użytkownika testowego portalu użytkownika B2B rozwiązania Zscaler

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w portalu użytkownika B2B rozwiązania Zscaler. Portal użytkowników B2B rozwiązania Zscaler obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w portalu użytkownika B2B rozwiązania Zscaler, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Portal użytkowników B2B rozwiązania Zscaler w panelu dostępu należy automatycznie zalogować się do portalu użytkownika B2B programu rozwiązania Zscaler, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj rozwiązania Zscaler Portal użytkowników B2B z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)