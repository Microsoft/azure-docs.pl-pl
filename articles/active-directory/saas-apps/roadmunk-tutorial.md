---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Roadmunk'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Roadmunk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 26424106098a8385faff5ab6d3de33d98576ef4e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381307"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Roadmunk

W tym samouczku dowiesz się, jak zintegrować usługę Roadmunk z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Roadmunk z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Roadmunk.
* Umożliwienie użytkownikom automatycznego logowania się do usługi Roadmunk przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja Roadmunk, która obsługuje logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

Roadmunk obsługuje logowanie jednokrotne uruchomione przez *dostawcę usług* (SP) i *dostawcę tożsamości* (dostawcy tożsamości).

## <a name="add-roadmunk-from-the-gallery"></a>Dodaj Roadmunk z galerii

Aby zintegrować usługę Roadmunk z usługą Azure AD, z galerii Dodaj Roadmunk do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz **Roadmunk**.
1. Wybierz pozycję **Roadmunk** z wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Roadmunk

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Roadmunk przy użyciu użytkownika testowego o nazwie *B. Simon*. Aby przeprowadzić Logowanie jednokrotne, musisz ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Roadmunk.

Poniżej przedstawiono omówienie sposobu konfigurowania i testowania logowania jednokrotnego w usłudze Azure AD za pomocą Roadmunk:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) w celu przetestowania rejestracji jednokrotnej usługi Azure AD za pomocą polecenia B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. [Skonfiguruj Logowanie jednokrotne](#configure-roadmunk-sso) w usłudze Roadmunk, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego Roadmunk](#create-roadmunk-test-user) , aby można było połączyć odpowiedniki B. Simon w Roadmunk z reprezentacją usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby upewnić się, że konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. W Azure Portal na stronie integracja aplikacji **Roadmunk** Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę pióra dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający ikonę edycji dla podstawowej konfiguracji protokołu SAML.](common/edit-urls.png)

1. Jeśli masz plik metadanych SP i chcesz skonfigurować w trybie zainicjowanym przez dostawcy tożsamości, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. Wybierz pozycję **Przekaż plik metadanych**.

    ![Zrzut ekranu przedstawiający link do pliku metadanych przekazywania.](common/upload-metadata.png)

    b. Wybierz ikonę folderu, aby wybrać plik metadanych, który został pobrany w kroku 4 procedury "Konfigurowanie logowania jednokrotnego Roadmunk". Następnie wybierz przycisk **Upload** (Przekaż).

    ![Zrzut ekranu przedstawiający sposób wybierania pliku metadanych.](common/browse-upload-metadata.png)

    Po przekazaniu pliku metadanych w sekcji **Podstawowa konfiguracja języka SAML** wartości **identyfikatorów** i **adresów URL odpowiedzi** są wypełniane automatycznie.

    ![Zrzut ekranu przedstawiający sekcję Podstawowa konfiguracja protokołu SAML. Pola Identyfikator i adres URL odpowiedzi są wyróżnione.](common/idp-intiated.png)

    > [!Note]
    > Jeśli wartości **identyfikatorów** i **adresów URL odpowiedzi** nie zostaną wypełnione automatycznie, wprowadź wartości ręcznie.

1. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przez program SP, wybierz opcję **Ustaw dodatkowe adresy URL**. W polu **adres URL logowania** wpisz `https://login.roadmunk.com`

    ![Zrzut ekranu przedstawiający miejsce ustawienia adresu URL logowania dla trybu zainicjowanego przez program SP.](common/metadata-upload-additional-signon.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji**. Następnie wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający link do pobierania certyfikatu podpisywania SAML.](common/metadataxml.png)

1. W sekcji **Konfigurowanie Roadmunk** Skopiuj adres URL lub adresy URL, które są potrzebne.

    ![Zrzut ekranu przedstawiający miejsce kopiowania adresów URL konfiguracji.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal. Nazwa użytkownika to User *B. Simon*.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. W górnej części okna wybierz pozycję **nowy użytkownik**.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład wprowadź `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz funkcję B. Simon do korzystania z logowania jednokrotnego platformy Azure przez przyznanie dostępu do usługi Roadmunk.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Roadmunk**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** , na liście **Użytkownicy** wybierz pozycję **B. Simon**. Następnie w dolnej części okna dialogowego wybierz **pozycję Wybierz**.
1. Jeśli oczekujesz, że rola zostanie przypisana do użytkowników, wybierz ją z menu rozwijanego **Wybierz rolę** . Jeśli żadna rola nie została skonfigurowana dla tej aplikacji, zostanie wybrana **Domyślna rola dostępu** .
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-roadmunk-sso"></a>Konfigurowanie logowania jednokrotnego Roadmunk

1. Zaloguj się do witryny sieci Web Roadmunk jako administrator.

1. W dolnej części strony wybierz ikonę użytkownika, a następnie wybierz pozycję **Ustawienia konta**.

    ![Zrzut ekranu przedstawiający lokalizację wybierania ustawień konta użytkownika.](./media/roadmunk-tutorial/account.png)

1. Przejdź do **Company** pozycji  >  **Ustawienia uwierzytelniania** firmy.

1. Na stronie **Ustawienia uwierzytelniania** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający stronę Ustawienia uwierzytelniania.](./media/roadmunk-tutorial/saml-sso.png)

    a. Włącz logowanie jednokrotne **SAML (SSO)**.

    b. W sekcji **krok 1** Przekaż plik XML metadanych lub podaj adres URL metadanych.

    c. W sekcji **krok 2** Pobierz plik metadanych Roadmunk, a następnie zapisz go na komputerze.

    d. Jeśli chcesz zalogować się przy użyciu logowania jednokrotnego, w sekcji **krok 3** wybierz pozycję **Wymuszaj tylko Sign-In SAML**.

    e. Wybierz pozycję **Zapisz**.


### <a name="create-roadmunk-test-user"></a>Utwórz użytkownika testowego Roadmunk

1. Zaloguj się do witryny sieci Web Roadmunk jako administrator.

1. Wybierz ikonę użytkownika w dolnej części strony, a następnie wybierz pozycję **Ustawienia konta**.

    ![Zrzut ekranu przedstawiający sposób otwierania ustawień konta dla użytkownika testowego.](./media/roadmunk-tutorial/account.png)

1. Otwórz kartę **Użytkownicy** , a następnie wybierz pozycję **Zaproś użytkownika**.

    ![Zrzut ekranu przedstawiający kartę Użytkownicy. Przycisk Zaproś użytkownika jest wyróżniony. W otwartym oknie są wyróżnione pola wiadomości E-mail i ról.](./media/roadmunk-tutorial/create-user.png)

1. W wyświetlonym formularzu wprowadź wymagane informacje, a następnie wybierz pozycję **Zaproś**.


## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację rejestracji jednokrotnej usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka **Roadmunk** w portalu Moje aplikacje należy automatycznie zalogować się do konta Roadmunk, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Roadmunk można wymusić kontrolę sesji. Kontrola sesji chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. 

Dowiedz się [, jak wymuszać kontrolę sesji przy użyciu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


