---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą TimeOffManager | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i TimeOffManager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 0adb8d62d02325f92c77d2171eaafd6c2de16f0c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516350"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą TimeOffManager

W tym samouczku dowiesz się, jak zintegrować usługę TimeOffManager z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi TimeOffManager z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do TimeOffManager.
* Zezwól użytkownikom na automatyczne logowanie się do usługi TimeOffManager przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) TimeOffManager.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.


* Usługa TimeOffManager obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

* TimeOffManager obsługuje Inicjowanie obsługi użytkowników **just in Time**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.


## <a name="adding-timeoffmanager-from-the-gallery"></a>Dodawanie TimeOffManager z galerii

Aby skonfigurować integrację programu TimeOffManager z usługą Azure AD, musisz dodać TimeOffManager z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **TimeOffManager** w polu wyszukiwania.
1. Wybierz pozycję **TimeOffManager** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla TimeOffManager

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą TimeOffManager przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w TimeOffManager.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą TimeOffManager, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-timeoffmanager-sso)** w usłudze TimeOffManager, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego TimeOffManager](#create-timeoffmanager-test-user)** , aby dysponować odpowiednikiem B. Simon w TimeOffManager, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **TimeOffManager** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Tę wartość można uzyskać ze **strony ustawień logowania** jednokrotnego, która została omówiona w dalszej części samouczka lub skontaktuj się z [zespołem pomocy technicznej TimeOffManager](https://www.purelyhr.com/contact-us). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja TimeOffManager oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja TimeOffManager oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | --- | --- |
    | Firstname |Użytkownik. podanąname |
    | Lastname |User. nazwisko |
    | E-mail |User.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie TimeOffManager** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi TimeOffManager.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **TimeOffManager**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-timeoffmanager-sso"></a>Konfigurowanie logowania jednokrotnego TimeOffManager

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny TimeOffManager jako administrator.

2. Przejdź do ** \> opcji konta konta \> Ustawienia pojedyncze Sign-On**.
   
    ![Zrzut ekranu przedstawia ustawienia pojedynczych Sign-On wybranych z opcji konta.](./media/timeoffmanager-tutorial/ic795917.png "Ustawienia pojedynczego Sign-On")

3. W sekcji **Ustawienia pojedynczej Sign-On** wykonaj następujące czynności:
   
    ![Zrzut ekranu przedstawia sekcję ustawień pojedynczej Sign-On, w której można wprowadzić podane wartości.](./media/timeoffmanager-tutorial/ic795918.png "Ustawienia pojedynczego Sign-On")
   
    a. Otwórz certyfikat zakodowany w formacie Base-64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej cały certyfikat do pola tekstowego **certyfikatu X. 509** .
   
    b. W polu tekstowym **wystawcy dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** , który został skopiowany z Azure Portal.
   
    c. W polu tekstowym **adres URL punktu końcowego dostawcy tożsamości** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.
   
    d. Jako **Wymuszanie protokołu SAML**, wybierz pozycję **nie**.
   
    e. Jako **Autotwórz użytkowników**wybierz pozycję **tak**.
   
    f. W polu tekstowym **adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowanego z Azure Portal.
   
    przykład Kliknij przycisk **Zapisz zmiany**.

4. Na stronie **Ustawienia logowania** jednokrotnego skopiuj wartość **adres URL usługi** konsumenckej potwierdzenia i wklej ją w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal. 

      ![Zrzut ekranu przedstawia łącze usługi konsument potwierdzenia U R L.](./media/timeoffmanager-tutorial/ic795915.png "Ustawienia pojedynczego Sign-On")

### <a name="create-timeoffmanager-test-user"></a>Utwórz użytkownika testowego TimeOffManager

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w TimeOffManager. TimeOffManager obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze TimeOffManager, zostanie utworzony nowy po uwierzytelnieniu.

>[!NOTE]
>Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników TimeOffManager i interfejsów API udostępnionych przez usługę TimeOffManager.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka TimeOffManager w panelu dostępu należy automatycznie zalogować się do TimeOffManager, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj TimeOffManager z usługą Azure AD](https://aad.portal.azure.com/)