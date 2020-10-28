---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą zrzut ekranu przedstawiający-O-Matic | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zrzut ekranu przedstawiający-O-Matic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.openlocfilehash: eae60ba9aabe417e986c7637ce700302bfe6c7bf
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676564"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą zrzut ekranu przedstawiający-O-Matic

W tym samouczku dowiesz się, jak zintegrować zrzut ekranu przedstawiający-O-Matic z usługą Azure Active Directory (Azure AD). W przypadku integracji zrzut ekranu przedstawiający-O-Matic z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zrzut ekranu przedstawiający-O-Matic.
* Zezwól użytkownikom na automatyczne logowanie do zrzut ekranu przedstawiający-O-Matic z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (zrzut ekranu przedstawiający-O-Matic).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zrzut ekranu przedstawiający-O-Matic obsługuje zainicjowane przez usługę **SP** SSO
* Zrzut ekranu przedstawiający-O — Matic obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Dodawanie zrzut ekranu przedstawiający-O-Matic z galerii

Aby skonfigurować integrację zrzut ekranu przedstawiający-O-Matic z usługą Azure AD, musisz dodać zrzut ekranu przedstawiający-O-Matic z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** .
1. W sekcji **Dodaj z galerii** wpisz **zrzut ekranu przedstawiający-O-Matic** w polu wyszukiwania.
1. Wybierz pozycję **zrzut ekranu przedstawiający-O-Matic** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla zrzut ekranu przedstawiający-O-Matic

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą zrzut ekranu przedstawiający-O-Matic przy użyciu użytkownika testowego o nazwie **B. Simon** . Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w zrzut ekranu przedstawiający-O-Matic.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zrzut ekranu przedstawiający-O-Matic, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj zrzut ekranu przedstawiający-O-Matic SSO](#configure-screencast-o-matic-sso)** -, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego zrzut ekranu przedstawiający-o-Matic](#create-screencast-o-matic-test-user)** , aby dysponować odpowiednikiem B. Simon w zrzut ekranu przedstawiający-O-Matic, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **zrzut ekranu przedstawiający-O-Matic** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne** .
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML** .
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej zrzut ekranu przedstawiający-O-Matic](mailto:support@screencast-o-matic.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **set up zrzut ekranu przedstawiający-O-Matic** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło** .
   1. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do zrzut ekranu przedstawiający-O-Matic.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Na liście Aplikacje wybierz pozycję **zrzut ekranu przedstawiający-O-Matic** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy** .

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-screencast-o-matic-sso"></a>Konfigurowanie rejestracji jednokrotnej zrzut ekranu przedstawiający-O-Matic

1. Aby zautomatyzować konfigurację w ramach zrzut ekranu przedstawiający-O-Matic, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie** .

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij przycisk **set up zrzut ekranu przedstawiający-o-Matic** przekieruje Cię do aplikacji zrzut ekranu przedstawiający-o-Matic. W tym miejscu podaj poświadczenia administratora, aby zalogować się do zrzut ekranu przedstawiający-O-Matic. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-11.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować zrzut ekranu przedstawiający-O-Matic, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej witryny zrzut ekranu przedstawiający-O-Matic jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję **subskrypcja** .

    ![Subskrypcja](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. W obszarze **Strona dostępu** , kliknij przycisk **Setup (Konfiguracja** ).

    ![Dostęp](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. Na **stronie dostęp do Instalatora** wykonaj następujące czynności.

1. W obszarze **adres URL dostępu** , wpisz InstanceName w określonym polu tekstowym.

    ![Dostęp](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Wybierz pozycję **Wymagaj użytkownika domeny** w sekcji **ograniczenie użytkownika protokołu SAML (opcjonalnie)** .

1. W obszarze **Przekaż plik XML metadanych dostawcy tożsamości** kliknij pozycję **Wybierz plik** , aby przekazać metadane pobrane z Azure Portal.

1. Kliknij pozycję **OK** .

    ![Dostęp](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Utwórz użytkownika testowego zrzut ekranu przedstawiający-O-Matic

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w zrzut ekranu przedstawiający-O-Matic. Zrzut ekranu przedstawiający-O — Matic obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w zrzut ekranu przedstawiający-O-Matic, zostanie utworzony nowy po uwierzytelnieniu. Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej zrzut ekranu przedstawiający-O-Matic](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka zrzut ekranu przedstawiający-O-Matic w panelu dostępu należy automatycznie zalogować się do zrzut ekranu przedstawiający-O-Matic, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj zrzut ekranu przedstawiający-O-Matic z usługą Azure AD](https://aad.portal.azure.com/)