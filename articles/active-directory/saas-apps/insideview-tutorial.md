---
title: 'Samouczek: integracja Azure Active Directory z usługą InsideView | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i InsideView.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: b8f12b6d55cb11c0cfeda5331072127ea4a696bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460108"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Samouczek: integracja Azure Active Directory z usługą InsideView

W tym samouczku dowiesz się, jak zintegrować usługę InsideView z usługą Azure Active Directory (Azure AD).
Ta integracja zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do usługi InsideView.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi InsideView (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą InsideView, należy dysponować:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi InsideView z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym.

* Usługa InsideView obsługuje logowanie jednokrotne inicjowane przez usługę dostawcy tożsamości.

## <a name="add-insideview-from-the-gallery"></a>Dodaj InsideView z galerii

Aby skonfigurować integrację programu InsideView z usługą Azure AD, musisz dodać InsideView z galerii do listy zarządzanych aplikacji SaaS.

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **InsideView**. W wynikach wyszukiwania wybierz pozycję **InsideView** , a następnie wybierz pozycję **Dodaj**.

    ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD za pomocą InsideView przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w InsideView.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą InsideView, należy wykonać następujące czynności:

1. **[Skonfiguruj logowanie](#configure-azure-ad-single-sign-on)** jednokrotne w usłudze Azure AD, aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-insideview-single-sign-on)** w usłudze InsideView po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** , aby włączyć logowanie jednokrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego InsideView](#create-an-insideview-test-user)** , który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne w usłudze Azure AD w Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą InsideView, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji InsideView wybierz pozycję **Logowanie jednokrotne**:

    ![Wybierz Logowanie jednokrotne](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** :

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące czynności.

    ![Podstawowa konfiguracja SAML — okno dialogowe](common/idp-reply.png)

    W polu **adres URL odpowiedzi** wprowadź adres URL w tym wzorcu:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Ta wartość jest symbolem zastępczym. Musisz użyć adresu URL rzeczywistej odpowiedzi. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej InsideView](mailto:support@insideview.com) . Można również odnieść się do wzorców przedstawionych w oknie dialogowym podstawowe informacje o **konfiguracji SAML** w Azure Portal.

5. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz link **pobierania** obok pozycji **certyfikat (RAW)**, zgodnie z wymaganiami, a następnie Zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Konfiguracja InsideView** skopiuj odpowiednie adresy URL zgodnie z wymaganiami:

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowywania**.

### <a name="configure-insideview-single-sign-on"></a>Konfigurowanie logowania jednokrotnego InsideView

1. W nowym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny InsideView jako administrator.

1. W górnej części okna wybierz pozycję **administrator**, **Ustawienia SingleSignon**, a następnie Dodaj element **SAML**.
   
   ![Ustawienia logowania jednokrotnego SAML](./media/insideview-tutorial/ic794135.png "Ustawienia logowania jednokrotnego SAML")

1. W sekcji **Dodaj nowy element SAML** wykonaj następujące czynności.

    ![Dodaj nową sekcję SAML](./media/insideview-tutorial/ic794136.png "Dodaj nową sekcję SAML")

    1. W polu **nazwa usługi STS** wprowadź nazwę konfiguracji.

    1. W polu **nieproszony punkt końcowy SamlP/WS** wprowadź wartość **adresu URL logowania** skopiowaną z Azure Portal.

    1. Otwórz certyfikat pierwotny pobrany z Azure Portal. Skopiuj zawartość certyfikatu do schowka, a następnie wklej zawartość do pola **certyfikat usługi STS** .

    1. W polu **Mapowanie identyfikatora użytkownika programu CRM** wprowadź wartość **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. W polu **Mapowanie poczty e-mail programu CRM** wprowadź wartość **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. W polu **Mapowanie pierwszej nazwy programu CRM** wprowadź wartość **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    1. W polu **Mapowanie programu CRM LastName** wprowadź wartość **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .  

    1. Wybierz pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**:

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części okna:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W oknie dialogowym **użytkownik** wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **BrittaSimon@ \<yourcompanydomain> . \<extension>**. (Na przykład BrittaSimon@contoso.com .)

    1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość znajdującą się w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego na platformie Azure, przyznając swój dostęp do usługi InsideView.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **InsideView**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **InsideView**.

    ![Lista aplikacji](common/all-applications.png)

3. W lewym okienku wybierz pozycję **Użytkownicy i grupy**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części okna.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Kliknij przycisk **Wybierz** w dolnej części okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-insideview-test-user"></a>Tworzenie użytkownika testowego InsideView

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze InsideView, należy dodać je do InsideView. Należy dodać je ręcznie.

Aby utworzyć użytkowników lub kontakty w InsideView, skontaktuj się z [zespołem pomocy technicznej InsideView](mailto:support@insideview.com).

> [!NOTE]
> Do udostępniania kont użytkowników usługi Azure AD można używać dowolnego narzędzia do tworzenia konta użytkownika lub interfejsu API dostarczonego przez InsideView.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka InsideView w panelu dostępu należy automatycznie zalogować się do wystąpienia InsideView, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)