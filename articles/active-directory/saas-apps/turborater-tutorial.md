---
title: 'Samouczek: integracja Azure Active Directory z usługą TurboRater | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i TurboRater.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 0c22993baa6a9095bddba67bdc9d18a40021db6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546393"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Samouczek: integracja Azure Active Directory z usługą TurboRater

W tym samouczku dowiesz się, jak zintegrować usługę TurboRater z usługą Azure Active Directory (Azure AD).

Integracja TurboRater z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi TurboRater.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi TurboRater (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby uzyskać szczegółowe informacje na temat integracji aplikacji typu "oprogramowanie jako usługa" (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą TurboRater, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja TurboRater z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

Usługa TurboRater obsługuje logowanie jednokrotne inicjowane przez dostawcy tożsamości (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Dodawanie TurboRater z witryny Azure Marketplace

Aby skonfigurować integrację programu TurboRater z usługą Azure AD, musisz dodać TurboRater z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com?azure-portal=true).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Opcja Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Opcja aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz pozycję **+ Nowa aplikacja** w górnej części okienka.

    ![Opcja nowej aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **TurboRater**. W wynikach wyszukiwania wybierz pozycję **TurboRater**, a następnie wybierz pozycję **Dodaj** , aby dodać aplikację.

    ![TurboRater na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą TurboRater na podstawie użytkownika testowego o nazwie  **B Simon**. Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w TurboRater.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi TurboRater, należy wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-single-sign-on)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj logowanie](#configure-turborater-single-sign-on)** jednokrotne w usłudze TurboRater, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Utwórz użytkownika testowego TurboRater](#create-a-turborater-test-user)** , aby uzyskać użytkownika o nazwie b. Simon w TurboRater, który jest połączony z użytkownikiem usługi Azure AD o nazwie b. Simon.
1. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą TurboRater, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **TurboRater** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz opcję **Edytuj** (ikona ołówka), aby otworzyć okienko **podstawowe ustawienia protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W okienku **podstawowe konfiguracje języka SAML** wykonaj następujące czynności:

    ![TurboRater domenę i adresy URL Logowanie jednokrotne](common/idp-intiated.png)

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL:

       `https://www.itcdataservices.com`

    1. W polu **adres URL odpowiedzi (adres URL usługi konsumenckej potwierdzenia)** wprowadź adres URL przy użyciu następującego wzorca:

       | Środowisko | Adres URL |
       | ---------------| --------------- |
       | Testowanie  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Na żywo  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy je zastąpić rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej TurboRater](https://www.getitc.com/support). Można również odwołać się do wzorców przedstawionych w okienku podstawowe informacje o **konfiguracji SAML** w Azure Portal.

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji** z określonych opcji i zapisać go na komputerze.

    ![Opcja pobierania XML metadanych Federacji](common/metadataxml.png)

1. W sekcji **Konfigurowanie TurboRater** Skopiuj adres URL lub adresy URL, które są potrzebne:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowywania**

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Konfigurowanie logowania jednokrotnego TurboRater

Aby skonfigurować Logowanie jednokrotne na stronie TurboRater, musisz wysłać pobrany XML metadanych Federacji i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej TurboRater](https://www.getitc.com/support). Zespół TurboRater sprawdzi, czy połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**    >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i "Wszyscy użytkownicy"](common/users.png)

1. W górnej części ekranu wybierz pozycję **+ nowy użytkownik**.

    ![Opcja nowego użytkownika](common/new-user.png)

1. W okienku **użytkownika** wykonaj następujące czynności:

    ![Okienko użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wprowadź **BSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **BSimon \@ \<yourcompanydomain> . \<extension> **. Na przykład **BSimon \@ contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając im dostęp do TurboRater.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **TurboRater**.

    ![Okienko aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **TurboRater**.

    ![TurboRater na liście aplikacji](common/all-applications.png)

1. W lewym okienku w obszarze **Zarządzaj**wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **+ Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **B. Simon** na liście **Użytkownicy** , a następnie wybierz **pozycję Wybierz** w dolnej części okienka.

1. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. W dolnej części okienka wybierz **pozycję Wybierz**.

1. W okienku **Dodaj przypisanie** wybierz pozycję **Przypisz**.

### <a name="create-a-turborater-test-user"></a>Tworzenie użytkownika testowego TurboRater

W tej sekcji utworzysz użytkownika o nazwie B. Simon w TurboRater. Współpracuj z [zespołem pomocy technicznej TurboRater](https://www.getitc.com/support) , aby dodać B. Simon jako użytkownika w TurboRater. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po wybraniu opcji **TurboRater** w portalu Moje aplikacje należy automatycznie zalogować się do subskrypcji usługi TurboRater, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
