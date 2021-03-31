---
title: 'Samouczek: integracja Azure Active Directory z usługą Freedcamp | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Freedcamp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 19378251408d55868ed844a5505ae48ece55dc3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92451499"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Samouczek: integracja Freedcamp z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Freedcamp z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Freedcamp z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Freedcamp.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Freedcamp przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Freedcamp.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Freedcamp obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .

## <a name="adding-freedcamp-from-the-gallery"></a>Dodawanie Freedcamp z galerii

Aby skonfigurować integrację programu Freedcamp z usługą Azure AD, musisz dodać Freedcamp z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Freedcamp** w polu wyszukiwania.
1. Wybierz pozycję **Freedcamp** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Freedcamp przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Freedcamp.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Freedcamp, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Freedcamp](#configure-freedcamp)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Freedcamp](#create-freedcamp-test-user)** , aby uzyskać odpowiednik Britta Simon w Freedcamp, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Freedcamp** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    1. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Użytkownicy mogą również wprowadzić wartości adresu URL w odniesieniu do ich własnej domeny klienta i mogą nie być koniecznie wzorcem `freedcamp.com` , mogą wprowadzić dowolną wartość określoną przez domenę klienta, konkretną dla swojego wystąpienia aplikacji. Ponadto można skontaktować się z [zespołem obsługi klienta Freedcamp](mailto:devops@freedcamp.com) , aby uzyskać więcej informacji na temat wzorców adresów URL.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Freedcamp** skopiuj odpowiednie adresy URL na podstawie wymagania.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Konfigurowanie Freedcamp

1. Aby zautomatyzować konfigurację w programie Freedcamp, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup Freedcamp** , aby skierować do aplikacji Freedcamp. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Freedcamp. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Freedcamp, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Freedcamp jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij pozycję **profil** , a następnie przejdź do **obszaru Moje konto**.

    ![Zrzut ekranu pokazujący, że wybrano "profil" i "Moje konto".](./media/freedcamp-tutorial/config01.png)

5. Z lewej strony paska menu kliknij pozycję Logowanie **jednokrotne** i na stronie **połączenia logowania jednokrotnego** wykonaj następujące czynności:

    ![Zrzut ekranu pokazujący "S S" wybrany na pasku menu po lewej stronie i na stronie "połączenia S S" z wprowadzonymi wartościami i wybranym przyciskiem "Prześlij".](./media/freedcamp-tutorial/config02.png)

    a. W polu tekstowym **tytuł** wpisz tytuł.

    b. W polu tekstowym **Identyfikator jednostki** wklej wartość identyfikatora usługi **Azure AD** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. Otwórz w Notatniku certyfikat szyfrowany algorytmem Base64, skopiuj jego zawartość i wklej go do pola tekstowego **certyfikat** .

    e. Kliknij przycisk **Prześlij**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi Freedcamp.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Freedcamp**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-freedcamp-test-user"></a>Utwórz użytkownika testowego Freedcamp

Aby włączyć użytkowników usługi Azure AD, zaloguj się do usługi Freedcamp, muszą one być obsługiwane w usłudze Freedcamp. W Freedcamp, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby Freedcamp jako administrator zabezpieczeń.

2. W prawym górnym rogu strony kliknij pozycję **profil** , a następnie przejdź do obszaru **Zarządzanie systemem**.

    ![Konfiguracja Freedcamp](./media/freedcamp-tutorial/config03.png)

3. Po prawej stronie strony Zarządzanie systemem wykonaj następujące czynności:

    ![Zrzut ekranu pokazujący wybrany przycisk "Dodaj lub Zaproś użytkowników", wyróżniono pole "E-mail" oraz wybrany przycisk "Dodaj użytkownika".](./media/freedcamp-tutorial/config04.png)

    a. Kliknij pozycję **Dodaj lub Zaproś użytkowników**.

    b. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np `Brittasimon@contoso.com` .

    c. Kliknij pozycję **Dodaj użytkownika**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Freedcamp w panelu dostępu należy automatycznie zalogować się do Freedcamp, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)