---
title: 'Samouczek: integracja Azure Active Directory z usługą TigerText Secure Messenger | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i TigerText Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9dff60767e923bad1322b689acd98e69eb9c2ac6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516994"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Samouczek: integracja Azure Active Directory z usługą TigerText Secure Messenger

W tym samouczku dowiesz się, jak zintegrować usługę TigerText Secure Messenger z usługą Azure Active Directory (Azure AD).

Integracja usługi TigerText Secure Messenger z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do TigerText Secure Messenger.
* Możesz pozwolić użytkownikom na automatyczne logowanie się w celu TigerText bezpiecznego programu Messenger (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby uzyskać szczegółowe informacje na temat integracji aplikacji typu "oprogramowanie jako usługa" (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą TigerText Secure Messenger, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* TigerText bezpieczna subskrypcja programu Messenger z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w środowisku testowym i zintegrować usługę TigerText Secure Messenger z usługą Azure AD.

TigerText Secure Messenger obsługuje logowanie jednokrotne inicjowane przez usługę SP (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Dodawanie TigerText bezpiecznego programu Messenger z witryny Azure Marketplace

Aby skonfigurować integrację programu TigerText Secure Messenger z usługą Azure AD, musisz dodać TigerText Secure Messenger z witryny Azure Marketplace do listy zarządzanych aplikacji SaaS:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com?azure-portal=true).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Opcja Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz pozycję **+ Nowa aplikacja** w górnej części okienka.

    ![Opcja nowej aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **TigerText Secure Messenger**. W wynikach wyszukiwania wybierz pozycję **TigerText Secure Messenger**, a następnie wybierz pozycję **Dodaj** , aby dodać aplikację.

    ![TigerText bezpiecznego programu Messenger na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą programu TigerText Secure Messenger na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze TigerText Secure Messenger.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi TigerText Secure Messenger, należy wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-single-sign-on)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj bezpieczne logowanie](#configure-tigertext-secure-messenger-single-sign-on)** jednokrotne w usłudze TigerText, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Britta Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Utwórz użytkownika testowego bezpiecznego programu TigerText](#create-a-tigertext-secure-messenger-test-user)** , tak aby użytkownik miał nazwę Britta Simon w TigerText Secure Messenger, który jest połączony z użytkownikiem usługi Azure AD o nazwie Britta Simon.
1. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi TigerText Secure Messenger, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **TigerText Secure Messenger** Application Integration wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć okienko **podstawowe ustawienia protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W okienku **podstawowe konfiguracje języka SAML** wykonaj następujące czynności:

    ![TigerText Zabezpieczanie domen i adresów URL logowania jednokrotnego](common/sp-identifier.png)

    1. W polu **adres URL logowania** wprowadź adres URL:

       `https://home.tigertext.com`

    1. W polu **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Wartość **identyfikatora (identyfikatora jednostki)** nie jest rzeczywista. Zaktualizuj tę wartość przy użyciu rzeczywistego identyfikatora. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej TigerText Secure Messenger](mailto:prosupport@tigertext.com). Można również odwołać się do wzorców przedstawionych w okienku podstawowe informacje o **konfiguracji SAML** w Azure Portal.

1. W okienku **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji** z określonych opcji i zapisać go na komputerze.

    ![Opcja pobierania XML metadanych Federacji](common/metadataxml.png)

1. W sekcji **Konfigurowanie programu TigerText Secure Messenger** Skopiuj potrzebne adresy URL lub URL:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowywania**

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurowanie logowania jednokrotnego za TigerText Secure Messenger

Aby skonfigurować Logowanie jednokrotne na stronie TigerText Secure Messenger, należy wysłać pobrany XML metadanych Federacji oraz odpowiednie skopiowane adresy URL z Azure Portal do [zespołu usługi TigerText Secure Messenger support](mailto:prosupport@tigertext.com). Zespół usługi TigerText Secure Messenger sprawdzi, czy połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**    >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i "Wszyscy użytkownicy"](common/users.png)

1. W górnej części ekranu wybierz pozycję **+ nowy użytkownik**.

    ![Opcja nowego użytkownika](common/new-user.png)

1. W okienku **użytkownika** wykonaj następujące czynności:

    ![Okienko użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **BrittaSimon \@ \<yourcompanydomain> . \<extension> **. Na przykład **BrittaSimon \@ contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure, przyznając im dostęp do TigerText Secure Messenger.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **TigerText Secure Messenger**.

    ![Okienko aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **TigerText Secure Messenger**.

    ![TigerText bezpiecznego programu Messenger na liście aplikacji](common/all-applications.png)

1. W lewym okienku w obszarze **Zarządzaj**wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **+ Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy** , a następnie wybierz **pozycję Wybierz** w dolnej części okienka.

1. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. W dolnej części okienka wybierz **pozycję Wybierz**.

1. W okienku **Dodaj przypisanie** wybierz pozycję **Przypisz**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Tworzenie użytkownika testowego bezpiecznego programu TigerText

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w TigerText Secure Messenger. Współpracuj z [zespołem pomocy technicznej TigerText Secure Messenger](mailto:prosupport@tigertext.com) , aby dodać Britta Simon jako użytkownika w TigerText Secure Messenger. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po wybraniu opcji **TigerText Secure Messenger** w portalu My Apps należy automatycznie zalogować się do subskrypcji usługi TigerText Secure Messenger, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
