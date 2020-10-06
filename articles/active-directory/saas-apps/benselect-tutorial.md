---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą BenSelect | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i BenSelect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: 12c0647e0244464966c52bafe8c976eb4a35ddda
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91741214"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-benselect"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą BenSelect

W tym samouczku dowiesz się, jak zintegrować usługę BenSelect z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi BenSelect z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do BenSelect.
* Zezwól użytkownikom na automatyczne logowanie się do usługi BenSelect przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) BenSelect.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.


* Usługa BenSelect obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-benselect-from-the-gallery"></a>Dodawanie BenSelect z galerii

Aby skonfigurować integrację programu BenSelect z usługą Azure AD, musisz dodać BenSelect z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **BenSelect** w polu wyszukiwania.
1. Wybierz pozycję **BenSelect** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-benselect"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla BenSelect

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą BenSelect przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w BenSelect.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą BenSelect, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-benselect-sso)** w usłudze BenSelect, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego BenSelect](#create-benselect-test-user)** , aby dysponować odpowiednikiem B. Simon w BenSelect, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **BenSelect** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://www.benselect.com/enroll/login.aspx?Path=<tenant name>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta BenSelect](mailto:support@selerix.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja BenSelect oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu przedstawia atrybuty użytkownika z domyślnymi atrybutami, takimi jak użytkownik. imięname i EmailAddress User. mail.](common/edit-attribute.png)

1. Kliknij ikonę **Edytuj** , aby edytować **wartość identyfikatora nazwy**.

    ![Zrzut ekranu pokazuje okienko atrybuty użytkownika & oświadczenia z ikoną Edytuj o nazwie.](media/benselect-tutorial/mail-prefix1.png)

1. W sekcji **Zarządzanie oświadczeniami użytkowników** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia Zarządzanie oświadczeniami użytkowników, w których można wprowadzać wartości opisane w tym kroku.](media/benselect-tutorial/mail-prefix2.png)

    a. Wybierz **transformację** jako **Źródło**.

    b. Na liście rozwijanej **transformacja** wybierz pozycję **ExtractMailPrefix ()**.

    c. Na liście rozwijanej **parametr 1** wybierz pozycję **User. userPrincipalName**.

    d. Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (RAW)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie BenSelect** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi BenSelect.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **BenSelect**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-benselect-sso"></a>Konfigurowanie logowania jednokrotnego BenSelect

Aby skonfigurować Logowanie jednokrotne na stronie **BenSelect** , musisz wysłać pobrany **certyfikat (RAW)** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej BenSelect](mailto:support@selerix.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!NOTE]
> Należy zauważyć, że ta integracja wymaga algorytmu SHA256 (SHA1 nie jest obsługiwana), aby ustawić Logowanie jednokrotne na odpowiednim serwerze, takim jak app2101 itp.

### <a name="create-benselect-test-user"></a>Utwórz użytkownika testowego BenSelect

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w BenSelect. Aby dodać użytkowników na platformie BenSelect, Pracuj z [zespołem pomocy technicznej BenSelect](mailto:support@selerix.com) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka BenSelect w panelu dostępu należy automatycznie zalogować się do BenSelect, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj BenSelect z usługą Azure AD](https://aad.portal.azure.com/)

