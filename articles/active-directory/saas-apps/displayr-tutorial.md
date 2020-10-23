---
title: 'Samouczek: integracja Azure Active Directory z programem unplay | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Play.
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
ms.openlocfilehash: 13edc0280f1a6f7e962e8e4593d8a17990dd9e6f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454749"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Samouczek: integracja z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować program z usługą Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do odsłuchania.
* Zezwól użytkownikom na automatyczne logowanie się w celu odtworzenia przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Program unplay obsługuje **usługę** zainicjowaną przez usługę SSO.

## <a name="adding-displayr-from-the-gallery"></a>Dodawanie odsłuchania z galerii

Aby skonfigurować integrację z odtwarzaniem w usłudze Azure AD, musisz dodać do niej program z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** **w polu wyszukiwania wpisz ciąg** .
1. Wybierz opcję **Odsłuchaj** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą elementu unplay przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie unplay.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu programu do odtwarzania, należy wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj odtwarzanie](#configure-displayr)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego](#create-displayr-test-user)** , aby uzyskać odpowiednika Britta Simon w oddzieleniu, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji dla **odtwarzania** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujący krok:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<YOURDOMAIN>.displayr.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta](mailto:support@displayr.com) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji Podstawowa konfiguracja protokołu SAML w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **certyfikat (base64)** , a następnie wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Aplikacja do odtwarzania oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![Zrzut ekranu pokazujący sekcję "atrybuty użytkownika" z wyróżnioną ikoną "Edytuj".](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja do odtwarzania oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji **atrybuty użytkownika & oświadczenia** w oknie dialogowym **oświadczenia grupy (wersja zapoznawcza)** wykonaj następujące czynności:

    a. Kliknij **pióro** obok **grup zwróconych w ramach żądania**.

    ![Zrzut ekranu przedstawiający sekcję "atrybuty użytkownika & oświadczenia" z ikoną "pióro" obok pozycji "grupy zwrócone w ramach oświadczenia".](./media/displayr-tutorial/config04.png)

    ![Zrzut ekranu pokazujący okno "oświadczenia grupy (wersja zapoznawcza) z wybranymi ustawieniami.](./media/displayr-tutorial/config05.png)

    b. Wybierz pozycję **wszystkie grupy** z listy radiowej.

    c. Wybierz **atrybut Source** **identyfikatora grupy**.

    d. Zaznacz pole wyboru **Dostosuj nazwę tego żądania**.

    e. Sprawdź **grupy emisji jako oświadczenia ról**.

    f. Kliknij pozycję **Zapisz**.

1. W sekcji **Konfiguracja odporności** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Skonfiguruj odtwarzanie

1. Aby zautomatyzować konfigurację w ramach odtwarzania, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij opcję **odsłuchanie** spowoduje przekierowanie do aplikacji dotyczącej odtwarzania. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do osoby podpisującej. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować odtwarzanie, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do swojej firmy jako administrator i wykonaj następujące czynności:

4. Kliknij pozycję **Ustawienia** , a następnie przejdź do **konta**.

    ![Zrzut ekranu pokazujący ikonę "Ustawienia" i wybraną wartość "konto".](./media/displayr-tutorial/config01.png)

5. Przejdź do pozycji **Ustawienia** w górnym menu i przewiń w dół stronę, aby kliknąć pozycję **Konfiguruj logowanie jednokrotne (SAML)**.

    ![Zrzut ekranu pokazujący wybraną kartę "Ustawienia" i wybraną akcję "Konfiguruj logowanie jednokrotne (S A M").](./media/displayr-tutorial/config02.png)

6. Na stronie Logowanie jednokrotne **(SAML)** wykonaj następujące czynności:

    ![Konfigurowanie](./media/displayr-tutorial/config03.png)

    a. Zaznacz pole Włącz logowanie jednokrotne **(SAML)** .

    b. Skopiuj rzeczywistą wartość **identyfikatora** z sekcji **Podstawowa konfiguracja języka SAML** w usłudze Azure AD i wklej ją do pola tekstowego **wystawcy** .

    c. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.

    d. W polu tekstowym **adres URL wylogowywania** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    e. Otwórz certyfikat (base64) w Notatniku, skopiuj jego zawartość i wklej go do pola tekstowego **certyfikat** .

    f. **Mapowania grup** są opcjonalne.

    przykład Kliknij pozycję **Zapisz**.  

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

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do osoby pobierającej.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **oddisplay**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-displayr-test-user"></a>Utwórz użytkownika testowego odtwarzania

Aby włączyć użytkowników usługi Azure AD, zaloguj się do programu Play, muszą one być obsługiwane w oddziałach. W przypadku odtwarzania Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do programu Play jako administrator.

2. Kliknij pozycję **Ustawienia** , a następnie przejdź do **konta**.

    ![Zrzut ekranu pokazujący ikonę "Ustawienia (koło zębate)" z wybraną pozycją "konto".](./media/displayr-tutorial/config01.png)

3. Przejdź do **opcji Ustawienia** w górnym menu i przewiń w dół strony, do momentu, gdy **użytkownik** kliknie pozycję **nowy użytkownik**.

    ![Zrzut ekranu pokazujący kartę "Ustawienia" z wyróżnioną opcją "Użytkownicy" i wybraną przycisk "nowy użytkownik".](./media/displayr-tutorial/config07.png)

4. Na stronie **nowy użytkownik** wykonaj następujące czynności:

    ![Konfiguracja odtwarzania](./media/displayr-tutorial/config06.png)

    a. W polu tekstowym **Nazwa** wprowadź nazwę użytkownika, na przykład **Brittasimon**.

    b. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `Brittasimon@contoso.com`.

    c. Wybierz odpowiednie **członkostwo w grupie**.

    d. Kliknij pozycję **Zapisz**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka odtwarzania w panelu dostępu należy automatycznie zalogować się do oddziału, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)