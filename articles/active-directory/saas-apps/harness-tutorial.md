---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą programu obsługi | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i programu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.openlocfilehash: d6a6c8b49582b34c2603e0ddf78b76736f97c183
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92445583"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z programem obsługi

W tym samouczku dowiesz się, jak zintegrować program z usługą Azure Active Directory (Azure AD). W przypadku integrowania programu z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu.
* Zezwól użytkownikom na automatyczne logowanie się, aby korzystać z kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Obsługa rejestracji jednokrotnej (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program obsługi obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-harness-from-the-gallery"></a>Dodawanie zespołu z galerii

Aby skonfigurować integrację programu z usługą Azure AD, musisz dodać zespół z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** **w polu wyszukiwania wpisz ciąg** .
1. Wybierz pozycję **zespół** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu dla zespołu

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu z użyciem użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu, należy wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę Logowanie jednokrotne](#configure-harness-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego zespołu](#create-harness-test-user)** usługi, aby dysponować odpowiednikiem B. Simon w programie dla zespołu, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **integracja aplikacji z** obsługą Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://app.harness.io/`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zostanie wyświetlony rzeczywisty adres URL odpowiedzi z sekcji **Konfigurowanie usługi logowania jednokrotnego** , która została omówiona w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie zestawu** , skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **zespół**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-harness-sso"></a>Konfigurowanie logowania jednokrotnego

1. Aby zautomatyzować konfigurację w ramach programu, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Instalator zespół** przeprowadzi Cię do aplikacji programu. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do programu. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program instalacyjny, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej lokacji firmy jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij pozycję **stałe**  >  Ustawienia uwierzytelniania **Zarządzanie dostępem do** zabezpieczeń  >  .

    ![Zrzut ekranu pokazujący menu "zabezpieczenia ciągłe" z wybraną opcją "Zarządzanie dostępem" i "ustawienia uwierzytelniania".](./media/harness-tutorial/configure01.png)

5. W sekcji **dostawcy logowania jednokrotnego** kliknij pozycję **+ Dodaj dostawcę rejestracji jednokrotnej** protokołu  >  **SAML**.

    ![Zrzut ekranu pokazujący dostawców "S S" z opcją "+ Add S O Providers-S A M".](./media/harness-tutorial/configure03.png)

6. W wyskakującym okienku **dostawcy SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno podręczne "S A M L" z wyróżnionymi polami "U R L" i "Display Name" oraz wybranymi przyciskami "Wybierz plik" i "Prześlij".](./media/harness-tutorial/configure02.png)

    a. Skopiuj **dostawcę rejestracji jednokrotnej, Włącz logowanie przy użyciu protokołu SAML, a następnie wprowadź następujące wystąpienie adresu URL** i wklej je w polu tekstowym adres URL odpowiedzi w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. W polu tekstowym **Nazwa wyświetlana** wpisz nazwę wyświetlaną.

    c. Kliknij pozycję **Wybierz plik** , aby przekazać plik XML metadanych Federacji pobrany z usługi Azure AD.

    d. Kliknij przycisk **Prześlij**.

### <a name="create-harness-test-user"></a>Utwórz użytkownika testowego zespołu

Aby umożliwić użytkownikom usługi Azure AD logowanie się do programu, muszą one być obsługiwane w programie. W programie dla zespołu program aprowizacji jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się, aby skorzystać z programu jako administrator.

1. W prawym górnym rogu strony kliknij pozycję **ciągły**  >  **dostęp do**  >  **zasobów zarządzanie użytkownikami**.

    ![Zrzut ekranu pokazujący menu "zabezpieczenia ciągłe" z wybraną opcją "Zarządzanie dostępem" i "Użytkownicy".](./media/harness-tutorial/configure04.png)

1. Po prawej stronie kliknij pozycję **+ Dodaj użytkownika**.

    ![Zrzut ekranu przedstawiający stronę "Użytkownicy" z wybraną akcją "+ Dodaj użytkownika".](./media/harness-tutorial/configure05.png)

1. W oknie podręcznym **Dodaj użytkownika** wykonaj następujące czynności:

    ![Konfiguracja zespołu](./media/harness-tutorial/configure06.png)

    a. W polu tekstowym **adresy e-mail wprowadź adres** e-mail użytkownika `B.simon@contoso.com` .

    b. Wybierz **grupy użytkowników**.

    c. Kliknij przycisk **Prześlij**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka zestaw w panelu dostępu należy automatycznie zalogować się do zestawu, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę Azure AD](https://aad.portal.azure.com/)