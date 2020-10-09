---
title: 'Samouczek: integracja Azure Active Directory z obpłatami Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i wydatków.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2020
ms.author: jeedes
ms.openlocfilehash: ccdfddd3ea76eddea2ab672fe07a092c9b1f1b62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91817065"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Samouczek: integracja wydatków z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować wydatki z usługą Azure Active Directory (Azure AD). W przypadku integracji wydatków z usługą Azure AD możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do wydatków.
* Zezwól użytkownikom na automatyczne logowanie się, aby zalogować się do kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 
* Usługa wydatków obsługuje usługę **SP i dostawcy tożsamości** zainicjowane przez usługę SSO.
* Po skonfigurowaniu wydatków można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-expensein-from-the-gallery"></a>Dodawanie wydatków z galerii

Aby skonfigurować integrację kosztów w usłudze Azure AD, należy dodać wydatki z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **wydatki** w polu wyszukiwania.
1. Wybierz pozycję **Obpłaty** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-expensein"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla wydatków

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą wydatków przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w obrachunku.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą wydatków, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego](#configure-expensein-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi wydatków](#create-expensein-test-user)** , aby dysponować odpowiednikiem B. Simon w obstawce wydatków, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie rozliczenia **wydatków** aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** użytkownik nie musi wykonywać żadnych czynności, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://app.expensein.com/saml`

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** , a następnie kliknij pozycję **Pobierz** , aby pobrać **certyfikat (base64)** i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Na stronie **Konfigurowanie wydatków** należy skopiować odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do wydatków.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **wydatki**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .


## <a name="configure-expensein-sso"></a>Konfiguruj wydatki na logowanie jednokrotne

1. Aby zautomatyzować konfigurację w ramach wydatków, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj koszty** , aby skierować do aplikacji wydatków. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do wydatków. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować obpłaty wydatków, zaloguj się do swojej firmy w firmie.

1. Kliknij pozycję **administrator** w górnej części strony, a następnie przejdź do **logowania jednokrotnego** , a następnie kliknij pozycję **Dodaj dostawcę**.

     ![Zrzut ekranu, na którym jest wyświetlana karta "Administrator" i "pojedynczy Sign-On-dostawcy" oraz wybrana wartość "Dodaj dostawcę".](./media/expenseIn-tutorial/config01.png)

1. W oknie podręcznym **Nowy dostawca tożsamości** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno podręczne "Edytuj dostawcę tożsamości" z wprowadzonymi wartościami.](./media/expenseIn-tutorial/config02.png)

    a. W polu tekstowym **Nazwa dostawcy** wpisz nazwę; na przykład platforma Azure.

    b. Wybierz pozycję **tak** dla opcji **Zezwalaj na logowanie dostawcy Intitiated**.

    c. W polu tekstowym **docelowy adres URL** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.

    d. W polu tekstowym **wystawca** wklej wartość **identyfikatora usługi Azure AD**, który został skopiowany z Azure Portal.

    e. Otwórz certyfikat (base64) w Notatniku, skopiuj jego zawartość i wklej go w polu tekstowym **certyfikat** .

    f. Kliknij przycisk **Utwórz**.

### <a name="create-expensein-test-user"></a>Utwórz użytkownika testowego wydatków

Aby umożliwić użytkownikom usługi Azure AD logowanie się w celu zarejestrowania się w usłudze, należy zalogować się do wydatków. W przypadku wydatków, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się, aby zalogować się jako administrator.

2. Kliknij pozycję **administrator** w górnej części strony, a następnie przejdź do obszaru **Użytkownicy** i kliknij pozycję **nowy użytkownik**.

     ![Zrzut ekranu pokazujący kartę "Administrator" i stronę "Zarządzaj użytkownikami" z wybranym elementem "nowy użytkownik".](./media/expenseIn-tutorial/config03.png)

3. W oknie podręcznym **szczegóły** wykonaj następujące czynności:

    ![Konfiguracja wydatków](./media/expenseIn-tutorial/config04.png)

    a. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np. **B**.

    b. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. **Simon**.

    c. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `B.Simon@contoso.com`.

    d. Kliknij przycisk **Utwórz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka wydatki w panelu dostępu należy automatycznie zalogować się do wydatków, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić wydatki przy użyciu zaawansowanej widoczności i kontroli](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
