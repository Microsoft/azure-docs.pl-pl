---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Bizagi na potrzeby automatyzacji procesów cyfrowych | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Bizagi na potrzeby automatyzacji procesów cyfrowych.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: 5f8126f497bfd66544576b218a903c50e58106b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544506"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Bizagi na potrzeby automatyzacji procesów cyfrowych

W tym samouczku dowiesz się, jak zintegrować usługę Bizagi for Digital Process Automation Services lub serwer z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Bizagi na potrzeby automatyzacji procesów cyfrowych za pomocą usługi Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do projektu Bizagi dla usług lub serwera automatyzacji procesów cyfrowych.
* Umożliwia użytkownikom automatyczne logowanie do projektu Bizagi for Digital Process AutomationServices lub serwer z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Projekt Bizagi przy użyciu usług lub serwera automatyzacji. 
* Dysponować własnymi certyfikatami dla podpisów potwierdzenia SAML. Te certyfikaty muszą być generowane w formacie P12 lub PFX.
* Plik metadanych w formacie XML wygenerowany w projekcie Bizagi. 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w projekcie Bizagi za pomocą usług Automation Services lub Server.

* Bizagi na potrzeby automatyzacji procesów cyfrowych obsługuje zainicjowanie rejestracji jednokrotnej **SP**
* Po skonfigurowaniu Bizagi na potrzeby automatyzacji procesów cyfrowych można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>Dodawanie Bizagi do automatyzacji procesów cyfrowych z galerii

Aby skonfigurować integrację Bizagi na potrzeby automatyzacji procesów cyfrowych w usłudze Azure AD, musisz dodać Bizagi do automatyzacji procesów cyfrowych z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Bizagi for Digital Process Automation** w polu wyszukiwania.
1. Wybierz pozycję **Bizagi dla automatyzacji procesów cyfrowych** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Bizagi na potrzeby automatyzacji procesów cyfrowych

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Bizagi na potrzeby automatyzacji procesów cyfrowych przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w projekcie Bizagi.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Bizagi na potrzeby automatyzacji procesów cyfrowych, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Bizagi dla logowania jednokrotnego w usłudze proces cyfrowy](#configure-bizagi-for-digital-process-automation-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Bizagi dla użytkownika testowego automatyzacji procesów cyfrowych](#create-bizagi-for-digital-process-automation-test-user)** , aby dysponować odpowiednikiem B. Simon w Bizagi dla automatyzacji procesów cyfrowych, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **Bizagi for Digital Process Automation** Application Integration Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Przekaż plik metadanych Bizagi w opcji **Przekaż plik metadanych** .
1. Przejrzyj konfigurację. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL projektu Bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL projektu Bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Aby uzyskać te wartości, skontaktuj się z [Bizagi dla zespołu pomocy technicznej dotyczącej automatyzacji procesów cyfrowych](mailto:jarvein.rivera@bizagi.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)
    
    Ten adres URL metadanych musi być zarejestrowany w opcjach uwierzytelniania projektu Bizagi.
    
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML**kliknij ikonę Edytuj/pióra, aby uzyskać **atrybuty użytkownika & oświadczeniami** , aby edytować unikatowy identyfikator użytkownika.
    
    Ustaw unikatowy identyfikator użytkownika jako User. mail.

### <a name="create-an-azure-ad-test"></a>Tworzenie testu usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Bizagi do automatyzacji procesów cyfrowych.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Bizagi dla automatyzacji procesów cyfrowych**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Konfigurowanie Bizagi na potrzeby logowania jednokrotnego do automatyzacji procesów cyfrowych

Aby skonfigurować Logowanie jednokrotne na stronie **Bizagi for Digital Process Automation** , musisz wysłać **adres URL metadanych federacji aplikacji** do [Bizagi dla zespołu pomocy technicznej automatyzacji procesów cyfrowych](mailto:jarvein.rivera@bizagi.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Utwórz Bizagi dla użytkownika testowego automatyzacji procesów cyfrowych

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Bizagi na potrzeby automatyzacji procesów cyfrowych. Współpracuj z [Bizagi dla zespołu pomocy technicznej automatyzacji procesów cyfrowych](mailto:jarvein.rivera@bizagi.com) , aby dodać użytkowników w usłudze Bizagi for Digital Process Automation platform. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Bizagi for Digital Process Automation w panelu dostępu należy automatycznie zalogować się do portalu Bizagi for Digital Process Automation, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Bizagi na potrzeby automatyzacji procesów cyfrowych przy użyciu usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
