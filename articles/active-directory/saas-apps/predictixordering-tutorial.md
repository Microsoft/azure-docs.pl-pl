---
title: 'Samouczek: integracja Azure Active Directory z kolejnością Predictix | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i porządkowaniem Predictix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 0eec8d9d88b286e386bd310c98d03737e56e05b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553737"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Samouczek: integracja Azure Active Directory z kolejnością Predictix

W tym samouczku dowiesz się, jak zintegrować Predictix porządkowanie z usługą Azure Active Directory (Azure AD).
Ta integracja zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do porządkowania Predictix.
* Możesz umożliwić użytkownikom automatyczne logowanie do Predictix porządkowanie (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Predictix porządkowaniem, musisz dysponować:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja porządkowania Predictix z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym.

* Porządkowanie Predictix obsługuje logowanie jednokrotne zainicjowane przez usługę SP.

## <a name="add-predictix-ordering-from-the-gallery"></a>Dodawanie porządkowania Predictix z galerii

Aby skonfigurować integrację Predictix porządkowanie do usługi Azure AD, musisz dodać zamówienie Predictix z galerii do listy zarządzanych aplikacji SaaS.

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Predictix porządkowanie**. Wybierz pozycję **Predictix porządkowanie** w wynikach wyszukiwania, a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD za pomocą Predictix porządkowanie przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w kolejności Predictix.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu porządkowania Predictix, należy wykonać następujące czynności:

1. **[Skonfiguruj logowanie](#configure-azure-ad-single-sign-on)** jednokrotne w usłudze Azure AD, aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Predictix porządkowanie](#configure-predictix-ordering-single-sign-on)** jednokrotne na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** , aby włączyć logowanie jednokrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego porządkowania Predictix](#create-a-predictix-ordering-test-user)** , który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne w usłudze Azure AD w Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu porządkowania Predictix, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **Predictix porządkowanie** aplikacji wybierz pozycję **Logowanie jednokrotne**:

    ![Wybierz Logowanie jednokrotne](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** :

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące czynności.

    ![Podstawowa konfiguracja SAML — okno dialogowe](common/sp-identifier.png)

    1. W polu **adres URL logowania** wprowadź adres URL w tym wzorcu:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL w tym wzorcu:

        ```https
        https://<companyname-pricing>.dev.ordering.predictix.com
        https://<companyname-pricing>.ordering.predictix.com
        ```

    > [!NOTE]
    > Te wartości są symbolami zastępczymi. Musisz użyć rzeczywistego adresu URL logowania i identyfikatora. Aby uzyskać wartości, skontaktuj się z [zespołem pomocy technicznej dotyczącej porządkowania Predictix](https://www.predix.io/support/) . Można również odnieść się do wzorców przedstawionych w oknie dialogowym podstawowe informacje o **konfiguracji SAML** w Azure Portal.

5. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz link **pobierania** obok pozycji **certyfikat (base64)**, zgodnie z wymaganiami, a następnie Zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie porządkowania Predictix** skopiuj odpowiednie adresy URL zgodnie z wymaganiami:

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    2. **Identyfikator usługi Azure AD**.

    3. **Adres URL wylogowywania**.

### <a name="configure-predictix-ordering-single-sign-on"></a>Konfigurowanie rejestracji jednokrotnej w Predictix kolejności

Aby skonfigurować Logowanie jednokrotne na stronie porządkowania Predictix, należy wysłać pobrany certyfikat i adresy URL, które zostały skopiowane z Azure Portal do [zespołu pomocy technicznej porządkowania Predictix](https://www.predix.io/support/). Ten zespół gwarantuje, że połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**:

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W oknie dialogowym **użytkownik** wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **BrittaSimon@ \<yourcompanydomain> . \<extension> **. (Na przykład BrittaSimon@contoso.com .)

    1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość znajdującą się w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego w usłudze Azure AD, dając swój dostęp do porządkowania Predictix.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Predictix porządkowanie**:

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Predictix porządkowanie**.

    ![Lista aplikacji](common/all-applications.png)

3. W lewym okienku wybierz pozycję **Użytkownicy i grupy**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-predictix-ordering-test-user"></a>Tworzenie użytkownika testowego porządkowania Predictix

Następnie musisz utworzyć użytkownika o nazwie Britta Simon w porządku Predictix. Aby dodać użytkowników, Pracuj z [zespołem pomocy technicznej dotyczącej porządkowania Predictix](https://www.predix.io/support/) . Przed użyciem logowania jednokrotnego należy utworzyć i aktywować użytkowników.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka porządkowania Predictix w panelu dostępu należy automatycznie zalogować się do wystąpienia porządkowania Predictix, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
