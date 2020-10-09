---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu podniesienia uprawnień w serwisie LinkedIn | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i podniesienia uprawnień do serwisu LinkedIn.
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
ms.openlocfilehash: 5dcd705719510cce3a113c5b0fdc46d016313b6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856416"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu podniesienia uprawnień serwisu LinkedIn

W tym samouczku dowiesz się, jak zintegrować usługę LinkedIn z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi LinkedIn w usłudze Azure AD możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do podniesienia uprawnień do serwisu LinkedIn.
* Umożliwienie użytkownikom automatycznego logowania się do usługi LinkedIn podnoszenie uprawnień przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Obsługa usługi logowania jednokrotnego (SSO) w serwisie LinkedIn.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.



* Podniesienie poziomu serwisu LinkedIn obsługuje usługę **SP i dostawcy tożsamości** zainicjowane przez usługę SSO
* Podnoszenie uprawnień serwisu LinkedIn obsługuje funkcję aprowizacji użytkowników **just in Time**
* Podnoszenie uprawnień serwisu LinkedIn obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Dodawanie podniesienia uprawnień do serwisu LinkedIn z galerii

Aby skonfigurować integrację serwisu LinkedIn z usługą Azure AD, należy dodać podnoszenie uprawnień serwisu LinkedIn z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **LinkedIn Podnieś poziom** w polu wyszukiwania.
1. Wybierz opcję **Podnieś poziom uprawnień** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby podniesienia uprawnień do serwisu LinkedIn

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą funkcji podniesienia uprawnień za pomocą użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD a pokrewnym użytkownikiem w serwisie LinkedIn Podnieś poziom.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą podniesienia uprawnień serwisu LinkedIn, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj serwis LinkedIn Podnieś poziom logowania jednokrotnego](#configure-linkedin-elevate-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie podniesienia uprawnień użytkownika testowego w serwisie LinkedIn](#create-linkedin-elevate-test-user)** , aby dysponować odpowiednikiem B. Simon w serwisie LinkedIn Podnieś poziom, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **serwis LinkedIn podnoszenie poziomu** integracji aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wprowadź wartość **identyfikatora jednostki** . wartość identyfikatora jednostki zostanie skopiowana z portalu LinkedIn w dalszej części tego samouczka.

    b. W polu tekstowym **adres URL odpowiedzi** wprowadź wartość **adresu URL dostępu konsumenta potwierdzenia (ACS** ), a w dalszej części tego samouczka skopiuj wartość adresu URL dostępu do odbiorcy usługi ACS z portalu LinkedIn.

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. Podnoszenie poziomu aplikacji w serwisie LinkedIn oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Podnoszenie poziomu aplikacji na serwis LinkedIn oczekuje na zamapowanie NameIdentifier z **użytkownikiem. mail**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę Edytuj i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, usługa LinkedIn Podnieś poziom aplikacji oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | -------| -------------|
    | działu, | user.department |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie podniesienia uprawnień serwisu LinkedIn** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do podniesienia uprawnień do serwisu LinkedIn.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Podnieś poziom serwisu LinkedIn**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-linkedin-elevate-sso"></a>Konfigurowanie podniesienia poziomu logowania za pomocą serwisu LinkedIn

1. W innym oknie przeglądarki sieci Web logowanie do konta w serwisie LinkedIn Podnieś poziom dzierżawy jako administrator.

1. W **Centrum kont** kliknij przycisk **Ustawienia globalne** w obszarze **Ustawienia**. Ponadto wybierz z listy rozwijanej pozycję **Podnieś poziom na podniesienie** poziomu usługi AAD.

    ![Zrzut ekranu przedstawia ustawienia globalne, w których można wybrać podnoszenie poziomu testu D.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknij **lub kliknij tutaj, aby załadować i skopiować pojedyncze pola z formularza** , i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia pojedyncze Sign-On, w których można wprowadzić podane wartości.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Skopiuj **Identyfikator jednostki** i wklej go do pola tekstowego **Identyfikator** w **podstawowej konfiguracji SAML** w Azure Portal.

    b. Skopiuj **adres URL dostępu konsumenta (ACS) potwierdzenia** i wklej go w polu tekstowym **adres URL odpowiedzi** w **podstawowej konfiguracji SAML** w Azure Portal.

1. Przejdź do sekcji **LinkedIn Admin Settings** (Ustawienia administratora LinkedIn). Przekaż pobrany plik XML z Azure Portal, klikając opcję Przekaż plik XML.

    ![Zrzut ekranu przedstawia Konfigurowanie ustawień dostawcy usług LinkedIn, w których można przekazać plik X M L.](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknij przycisk **On** (Włącz), aby włączyć funkcję logowania jednokrotnego. Stan rejestracji jednokrotnej zmieni się z **niepołączone** na **połączone**

    ![Zrzut ekranu przedstawia pojedyncze Sign-On, w których można wybrać opcję automatycznego przypisywania licencji.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>Utwórz użytkownika testowego podniesienia uprawnień w serwisie LinkedIn

Podwyższanie poziomu aplikacji przez serwis LinkedIn obsługuje funkcję aprowizacji użytkowników just in Time, a po automatycznym utworzeniu użytkowników uwierzytelniania w aplikacji. Na stronie Ustawienia administratora w portalu usługi LinkedIn Podnieś poziom uprawnień **automatycznie Przypisz licencje** do aktywnej obsługi przed chwilą i spowoduje to również przypisanie licencji do użytkownika. Podnoszenie uprawnień serwisu LinkedIn obsługuje również automatyczne Inicjowanie obsługi użytkowników. więcej informacji można znaleźć w [tym miejscu](linkedinelevate-provisioning-tutorial.md) w artykule Jak skonfigurować automatyczne Inicjowanie obsługi użytkowników.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka podnoszenie uprawnień w serwisie LinkedIn w panelu dostępu należy automatycznie zalogować się do usługi LinkedIn, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj podniesienie poziomu usługi LinkedIn w usłudze Azure AD](https://aad.portal.azure.com/)

