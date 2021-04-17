---
title: 'Samouczek: Azure Active Directory integracji z usługą Clarizen One | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między Azure Active Directory i Clarizen One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: f7e90ff4c69e03482a1608185bc947ccb8604187
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516945"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>Samouczek: Azure Active Directory integracji z usługą Clarizen One

Z tego samouczka dowiesz się, jak zintegrować usługę Clarizen One z Azure Active Directory (Azure AD). Integrowanie aplikacji Clarizen One z usługą Azure AD umożliwia:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Clarizen One.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Clarizen One przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Clarizen One z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Clarizen One obsługuje logowanie **jednokrotne inicjowane** przez dostawcy tożsamości.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="add-clarizen-one-from-the-gallery"></a>Dodawanie aplikacji Clarizen One z galerii

Aby skonfigurować integrację aplikacji Clarizen One z usługą Azure AD, musisz dodać aplikację Clarizen One z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **Clarizen One** w polu wyszukiwania.
1. Wybierz **pozycję Clarizen One** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Clarizen One

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z aplikacji Clarizen One przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Clarizen One.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacji Clarizen One, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie aplikacji Clarizen One SSO](#configure-clarizen-one-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego](#create-clarizen-one-test-user)** aplikacji Clarizen — aby mieć w aplikacji Clarizen One odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal aplikacji **Clarizen One** znajdź sekcję  Zarządzanie i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    a. W polu **tekstowym** Identyfikator wpisz wartość: `Clarizen`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W **sekcji Konfigurowanie aplikacji Clarizen One** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w aplikacji o nazwie Azure Portal B.Simon.

1. W okienku po lewej stronie Azure Portal pozycję Azure Active Directory **pozycję** **Użytkownicy,** a następnie pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownika wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Clarizen One.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Clarizen One.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-clarizen-one-sso"></a>Konfigurowanie aplikacji Clarizen One SSO

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Clarizen One jako administrator.

1. Kliknij swoją nazwę użytkownika, a następnie kliknij pozycję **Settings** (Ustawienia).

    ![Kliknięcie pozycji "Settings" (Ustawienia) pod nazwą użytkownika](./media/clarizen-tutorial/setting.png "Ustawienia")

1. Kliknij **kartę Ustawienia** globalne. Następnie obok uwierzytelniania **federowego** kliknij pozycję **edytuj**.

    ![Karta "Ustawienia globalne"](./media/clarizen-tutorial/authentication.png "Ustawienia globalne")

1. W oknie dialogowym **Federated Authentication** (Uwierzytelnianie federacyjne) wykonaj następujące kroki:

    ![Okno dialogowe "Uwierzytelnianie federowane"](./media/clarizen-tutorial/federated-authentication.png "Uwierzytelnianie federowane")

    a. Wybierz pozycję **Enable Federated Authentication** (Włącz uwierzytelnianie federacyjne).

    b. Kliknij pozycję **Upload** (Przekaż), aby przekazać pobrany certyfikat.

    c. W polu tekstowym **Sign-in URL** (Adres URL logowania) wprowadź wartość **adresu URL logowania** z okna konfiguracji aplikacji usługi Azure AD.

    d. W polu tekstowym **Sign-in URL** (Adres URL wylogowywania) wprowadź wartość **adresu URL wylogowywania** z okna konfiguracji aplikacji usługi Azure AD.

    e. Wybierz pozycję **Use POST** (Użyj metody POST).

    f. Kliknij pozycję **Zapisz**.

### <a name="create-clarizen-one-test-user"></a>Tworzenie użytkownika testowego aplikacji Clarizen One

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji Clarizen One.

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Clarizen One, należy aprowizować konta użytkowników. W przypadku usługi Clarizen One aprowizowanie jest zadaniem ręcznym.

1. Zaloguj się do firmowej witryny aplikacji Clarizen One jako administrator.

2. Kliknij kartę **People** (Osoby).

    ![Kliknięcie pozycji "People" (Osoby)](./media/clarizen-tutorial/people.png "People")

3. Kliknij przycisk **Invite User** (Zaproś użytkownika).

    ![Przycisk "Zaproś użytkownika"](./media/clarizen-tutorial/user.png "Zapraszanie użytkowników")

1. W oknie dialogowym **Invite people** (Zapraszanie osób) wykonaj następujące czynności:

    ![Okno dialogowe "Invite People" (Zapraszanie osób)](./media/clarizen-tutorial/invite-people.png "Zapraszanie osób")

    a. W polu **Email** (Adres e-mail) wpisz adres e-mail konta użytkownika Britta Simon.

    b. Kliknij pozycję **Invite** (Zaproś).

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Test this application in Azure Portal (Przetestuj tę aplikację w aplikacji). Powinno na celu to automatyczne zalogowanie się do aplikacji Clarizen One, dla której masz skonfigurować logowanie jednokrotne.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Clarizen One w aplikacji Moje aplikacje powinno na celu automatyczne zalogowanie się do aplikacji Clarizen One, dla której masz skonfigurować logowanie jednokrotne. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji Clarizen One można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).