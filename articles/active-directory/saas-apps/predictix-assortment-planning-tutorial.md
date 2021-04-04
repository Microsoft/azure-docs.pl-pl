---
title: 'Samouczek: integracja Azure Active Directory z planowaniem asortymentu Predictix | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a planowaniem asortymentu Predictix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: adf00d24c05deab149edb95b8087b8522dbda99a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515393"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Samouczek: integracja Azure Active Directory z planowaniem asortymentu Predictix

W tym samouczku dowiesz się, jak zintegrować Predictix planowanie asortymentu z Azure Active Directory (Azure AD).
Ta integracja zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do planowania wieloasortymentowego Predictix.
* Możesz włączyć automatyczne logowanie użytkowników, aby Predictix planowanie asortymentowe (Logowanie jednokrotne) przy użyciu swoich kont usługi Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu planowania asortymentu Predictix, należy dysponować:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja planowania asortymentu Predictix z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym.

* Planowanie asortymentu Predictix obsługuje logowanie jednokrotne zainicjowane przez usługę SP.

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>Dodawanie planowania asortymentu Predictix z galerii

Aby skonfigurować integrację Predictix z planowaniem asortymentu w usłudze Azure AD, musisz dodać planowanie asortymentu Predictix z galerii do listy zarządzanych aplikacji SaaS.

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Predictix planowanie asortymentu**. Wybierz pozycję **Planowanie asortymentu Predictix** w wynikach wyszukiwania, a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD za pomocą planowania wielowymiarowego Predictix przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w Predictix planowania asortymentów.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu planowania asortymentu Predictix, należy wykonać następujące czynności:

1. **[Skonfiguruj logowanie](#configure-azure-ad-single-sign-on)** jednokrotne w usłudze Azure AD, aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Skonfiguruj jednokrotne logowanie w ramach **[planowania asortymentu Predictix](#configure-predictix-assortment-planning-single-sign-on)** po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** , aby włączyć logowanie jednokrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego planowania asortymentu Predictix](#create-a-predictix-assortment-planning-test-user)** , który jest połączony z reprezentacją usługi Azure AD.
6. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne w usłudze Azure AD w Azure Portal.

Aby skonfigurować Logowanie jednokrotne w usłudze Azure AD przy użyciu planowania asortymentu Predictix, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **Planowanie asortymentu Predictix** wybierz pozycję **Logowanie jednokrotne**:

    ![Wybierz Logowanie jednokrotne](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** :

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące czynności.

    ![Podstawowa konfiguracja SAML — okno dialogowe](common/sp-identifier.png)

    1. W polu **adres URL logowania** wprowadź adres URL w tym wzorcu:

        ```https
        https://<sub-domain>.ap.predictix.com/sso/request
        https://<sub-domain>.dev.ap.predictix.com/
        ```

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL w tym wzorcu:

        ```https
        https://<sub-domain>.ap.predictix.com
        https://<sub-domain>.dev.ap.predictix.com
        ```

    > [!NOTE]
    > Te wartości są symbolami zastępczymi. Musisz użyć rzeczywistego adresu URL logowania i identyfikatora. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej w zakresie planowania asortymentów Predictix](https://www.infor.com/support) . Można również odnieść się do wzorców przedstawionych w oknie dialogowym podstawowe informacje o **konfiguracji SAML** w Azure Portal.

5. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz link **pobierania** obok pozycji **certyfikat (base64)**, zgodnie z wymaganiami, a następnie Zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie planowania asortymentu Predictix** skopiuj odpowiednie adresy URL na podstawie wymagań:

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowywania**.

### <a name="configure-predictix-assortment-planning-single-sign-on"></a>Konfigurowanie rejestracji jednokrotnej w planowaniu asortymentu Predictix

Aby skonfigurować Logowanie jednokrotne na stronie planowania asortymentu Predictix, należy wysłać pobrany certyfikat i adresy URL skopiowane z Azure Portal do [zespołu pomocy technicznej planowania asortymentu Predictix](https://www.infor.com/support). Ten zespół gwarantuje, że połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**:

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W oknie dialogowym **użytkownik** wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **BrittaSimon@ \<yourcompanydomain> . \<extension>**. (Na przykład BrittaSimon@contoso.com .)

    1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość znajdującą się w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego w usłudze Azure AD, dając swój dostęp do planowania wieloasortymentowego Predictix.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Planowanie asortymentu Predictix**.

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Planowanie asortymentu Predictix**.

    ![Lista aplikacji](common/all-applications.png)

3. W lewym okienku wybierz pozycję **Użytkownicy i grupy**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-predictix-assortment-planning-test-user"></a>Utwórz użytkownika testowego planowania asortymentu Predictix

Następnie należy utworzyć użytkownika o nazwie Britta Simon w programie Predictix Planning (planowanie asortymentu). Aby dodać użytkowników, Pracuj z [zespołem pomocy technicznej planowania asortymentu Predictix](https://www.infor.com/support) . Przed użyciem logowania jednokrotnego należy utworzyć i aktywować użytkowników.

> [!NOTE]
> Posiadacz konta usługi Azure AD otrzymuje wiadomość e-mail i wybierze link, aby potwierdzić konto, zanim staną się aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka planowania asortymentu Predictix w panelu dostępu należy automatycznie zalogować się do wystąpienia planowania asortymentu Predictix, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)