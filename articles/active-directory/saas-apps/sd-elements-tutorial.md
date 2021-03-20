---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z elementami SD | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i elementami SD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: a9bcda4affa19cf8793cd078fdc5b96d842eb42b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893618"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory z elementami SD

W tym samouczku dowiesz się, jak zintegrować elementy SD z Azure Active Directory (Azure AD). Po zintegrowaniu elementów SD z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do elementów SD.
* Zezwól użytkownikom na automatyczne logowanie do elementów SD przy użyciu ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) elementów SD.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Elementy SD obsługują zainicjowane Logowanie jednokrotne w usłudze **dostawcy tożsamości**

## <a name="adding-sd-elements-from-the-gallery"></a>Dodawanie elementów SD z galerii

Aby skonfigurować integrację elementów SD z usługą Azure AD, należy dodać elementy SD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **elementy SD** w polu wyszukiwania.
1. Wybierz pozycję **elementy SD** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla elementów SD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą elementów SD przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w elementach SD.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą elementów SD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj elementy SD Logowanie jednokrotne](#configure-sd-elements-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    * **[Tworzenie elementów SD test użytkownika](#create-sd-elements-test-user)** — Aby uzyskać odpowiedniki B. Simon w elementach SD, które są połączone z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracji aplikacji **elementów SD** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta elementów SD](mailto:support@sdelements.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja elementów SD oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja elementów SD oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | --- | --- |
    | poczta e-mail |user.mail |
    | firstname |user.givenname |
    | lastname |user.surname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie elementów SD** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do elementów SD.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **elementy SD**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sd-elements-sso"></a>Konfigurowanie elementów SD z logowaniem jednokrotnym

1. Aby włączyć logowanie jednokrotne, skontaktuj się z [zespołem pomocy technicznej dla elementów SD](mailto:support@sdelements.com) i podaj plik certyfikatu pobrane.

1. W innym oknie przeglądarki Zaloguj się do dzierżawy elementów SD jako administrator.

1. W menu u góry kliknij pozycję **system**, a następnie opcję **Logowanie jednokrotne**.

    ![Zrzut ekranu pokazujący wybraną wartość "system" i "Logowanie jednokrotne" wybraną z listy rozwijanej.](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. W oknie dialogowym **pojedyncze ustawienia Sign-On** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Jako **Typ logowania jednokrotnego** wybierz pozycję **SAML**.

    b. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD**, który został skopiowany z Azure Portal.

    c. W polu tekstowym **Sign-On usługi dostawca tożsamości** , wklej wartość **adresu URL logowania**, którą skopiowano z Azure Portal.

    d. Kliknij pozycję **Zapisz**.

### <a name="create-sd-elements-test-user"></a>Tworzenie użytkownika testowego elementów SD

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w elementach SD. W przypadku elementów SD tworzenie użytkowników elementów SD jest zadaniem ręcznym.

**Aby utworzyć B. Simon w elementach SD, wykonaj następujące czynności:**

1. W oknie przeglądarki sieci Web Zaloguj się do witryny firmy elementów SD jako administrator.

1. W menu u góry kliknij pozycje **Zarządzanie** użytkownikami, a następnie **Użytkownicy**.

    ![Zrzut ekranu pokazujący, że wybrano "Użytkownicy" z listy rozwijanej "Zarządzanie użytkownikami".](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Kliknij pozycję **Dodaj nowego użytkownika**.

    ![Zrzut ekranu pokazujący wybrany przycisk "Dodaj nowego użytkownika".](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. W oknie dialogowym **Dodawanie nowego użytkownika** wykonaj następujące czynności:

    ![Tworzenie użytkownika testowego elementów SD](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np **b.simon@contoso.com** .

    b. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np **. B.**

    c. W polu **tekstowym nazwisko wpisz nazwisko użytkownika** , np. **Simon**.

    d. Jako **rola** wybierz pozycję **użytkownik**.

    e. Kliknij pozycję **Create User** (Utwórz użytkownika).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka elementy SD w panelu dostępu należy automatycznie zalogować się do elementów SD, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj elementy SD z usługą Azure AD](https://aad.portal.azure.com/)