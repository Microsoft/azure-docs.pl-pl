---
title: 'Samouczek: integracja Azure Active Directory z usługą Uberflip | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Uberflip.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 6bf3eccec70619bf73397e315c6d851f753ec52f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521836"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Samouczek: integracja Azure Active Directory z usługą Uberflip

W tym samouczku dowiesz się, jak zintegrować usługę Uberflip z usługą Azure Active Directory (Azure AD).

Integracja Uberflip z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi Uberflip.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi Uberflip (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby uzyskać szczegółowe informacje na temat integracji aplikacji typu "oprogramowanie jako usługa" (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Uberflip, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi Uberflip z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

Program Uberflip obsługuje następujące funkcje:

* Zainicjowane przez usługę SP i dostawcy tożsamości Logowanie jednokrotne (SSO).
* Inicjowanie obsługi użytkowników just in Time.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Dodawanie Uberflip z witryny Azure Marketplace

Aby skonfigurować integrację programu Uberflip z usługą Azure AD, musisz dodać Uberflip z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

   ![Opcja Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

   ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz pozycję **+ Nowa aplikacja** w górnej części okienka.

   ![Opcja nowej aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **Uberflip**. W wynikach wyszukiwania wybierz pozycję **Uberflip**, a następnie wybierz pozycję **Dodaj** , aby dodać aplikację.

   ![Uberflip na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Uberflip na podstawie użytkownika testowego o nazwie **B Simon**. Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Uberflip.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Uberflip, należy wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-single-sign-on)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj logowanie](#configure-uberflip-single-sign-on)** jednokrotne w usłudze Uberflip, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Utwórz użytkownika testowego Uberflip](#create-an-uberflip-test-user)** , aby uzyskać użytkownika o nazwie b. Simon w Uberflip, który jest połączony z użytkownikiem usługi Azure AD o nazwie b. Simon.
1. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Uberflip, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Uberflip** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** wybierz opcję **Edytuj** (ikona ołówka), aby otworzyć okienko **podstawowe ustawienia protokołu SAML** .

   ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić odpowiedź U R L.](common/edit-urls.png)

1. W okienku **podstawowe konfiguracje języka SAML** wykonaj jedną z następujących czynności, w zależności od trybu logowania jednokrotnego, który chcesz skonfigurować:

   * Aby skonfigurować aplikację w trybie logowania jednokrotnego dostawcy tożsamości, w polu **adres URL odpowiedzi (adres URL usługi konsumenckej potwierdzenia)** wprowadź adres URL przy użyciu następującego wzorca:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip domenę i adresy URL Logowanie jednokrotne](common/both-replyurl.png)

     > [!NOTE]
     > Ta wartość nie jest rzeczywista. Zaktualizuj tę wartość przy użyciu adresu URL rzeczywistej odpowiedzi. Aby uzyskać wartość rzeczywistą, skontaktuj się z [zespołem pomocy technicznej Uberflip](mailto:support@uberflip.com). Można również odwołać się do wzorców przedstawionych w okienku podstawowe informacje o **konfiguracji SAML** w Azure Portal.

   * Aby skonfigurować aplikację w trybie rejestracji jednokrotnej zainicjowanej przez usługę SP, wybierz opcję **Ustaw dodatkowe adresy URL**, a następnie w polu **adres URL logowania** wprowadź następujący adres URL:

     `https://app.uberflip.com/users/login`

     ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/both-signonurl.png)

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji** z określonych opcji i zapisać go na komputerze.

   ![Opcja pobierania XML metadanych Federacji](common/metadataxml.png)

1. W okienku **Konfiguracja Uberflip** Skopiuj adres URL lub adresy URL, które są potrzebne:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowywania**

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Uberflip

Aby skonfigurować Logowanie jednokrotne na stronie Uberflip, musisz wysłać pobrany XML metadanych Federacji i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej Uberflip](mailto:support@uberflip.com). Zespół Uberflip sprawdzi, czy połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając im dostęp do Uberflip.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **Uberflip**.

    ![Okienko aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **Uberflip**.

    ![Uberflip na liście aplikacji](common/all-applications.png)

1. W lewym okienku w obszarze **Zarządzaj**wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **+ Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **B Simon** na liście **Użytkownicy** , a następnie wybierz **pozycję Wybierz** w dolnej części okienka.

1. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. W dolnej części okienka wybierz **pozycję Wybierz**.

1. W okienku **Dodaj przypisanie** wybierz pozycję **Przypisz**.

### <a name="create-an-uberflip-test-user"></a>Tworzenie użytkownika testowego Uberflip

Użytkownik o nazwie B. Simon jest teraz tworzony w Uberflip. Nie trzeba wykonywać żadnych czynności w celu utworzenia tego użytkownika. Uberflip obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. Jeśli użytkownik o nazwie B. Simon nie istnieje już w Uberflip, zostanie utworzony nowy po uwierzytelnieniu.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po wybraniu opcji **Uberflip** w portalu Moje aplikacje należy automatycznie zalogować się do subskrypcji usługi Uberflip, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](./tutorial-list.md)

* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)