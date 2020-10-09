---
title: 'Samouczek: integracja Azure Active Directory z menedżerem certyfikatów Sectigo | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i menedżerem certyfikatów Sectigo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: d68e5335fff0341d8808e581061519977e1bb517
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543282"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Samouczek: integracja Azure Active Directory z menedżerem certyfikatów Sectigo

W tym samouczku dowiesz się, jak zintegrować Menedżera certyfikatów Sectigo z usługą Azure Active Directory (Azure AD).

Integracja Menedżera certyfikatów Sectigo z usługą Azure AD zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do Menedżera certyfikatów Sectigo.
* Użytkownicy mogą być automatycznie zalogowani, aby Sectigo Menedżera certyfikatów przy użyciu kont usługi Azure AD (Logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS (Software as a Service) z usługą Azure AD, zobacz [Logowanie jednokrotne do aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z menedżerem certyfikatów Sectigo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji usługi Azure AD, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Sectigo subskrypcję Menedżera certyfikatów z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku należy skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD w środowisku testowym i zintegrować Menedżera certyfikatów Sectigo z usługą Azure AD.

Menedżer certyfikatów Sectigo obsługuje następujące funkcje:

* **Logowanie jednokrotne zainicjowane przez program SP**
* **Logowanie jednokrotne inicjowane przez dostawcę tożsamości**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Dodaj Menedżera certyfikatów Sectigo w Azure Portal

Aby zintegrować Menedżera certyfikatów Sectigo z usługą Azure AD, musisz dodać Menedżera certyfikatów Sectigo do listy zarządzanych aplikacji SaaS.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Opcja Azure Active Directory](common/select-azuread.png)

1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.

    ![Opcja nowej aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **Sectigo Certificate Manager**. W wynikach wyszukiwania wybierz pozycję **Sectigo Certificate Manager**, a następnie wybierz pozycję **Dodaj**.

    ![Sectigo Menedżera certyfikatów na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Menedżera certyfikatów Sectigo w oparciu o użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Menedżerze certyfikatów Sectigo.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Menedżera certyfikatów Sectigo, należy wykonać następujące bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystanie z tej funkcji. |
| **[Konfigurowanie logowania jednokrotnego w Menedżerze certyfikatów Sectigo](#configure-sectigo-certificate-manager-single-sign-on)** | Konfiguruje ustawienia logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testuje Logowanie jednokrotne w usłudze Azure AD dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD. |
| **[Tworzenie użytkownika testowego Menedżera certyfikatów Sectigo](#create-a-sectigo-certificate-manager-test-user)** | Tworzy odpowiednik Britta Simon w Menedżerze certyfikatów Sectigo, który jest połączony z reprezentacją usługi Azure AD użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz Logowanie jednokrotne w usłudze Azure AD za pomocą Menedżera certyfikatów Sectigo w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)w okienku integracja aplikacji **Menedżera certyfikatów Sectigo** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML** lub **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć okienko **podstawowe ustawienia protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W okienku **podstawowe konfiguracje języka SAML** , aby skonfigurować *tryb zainicjowany dostawcy tożsamości*, wykonaj następujące czynności:

    1. W polu **Identyfikator** wprowadź jeden z następujących adresów URL:
       * https: \/ /CERT-Manager.com/Shibboleth
       * https: \/ /Hard.CERT-Manager.com/Shibboleth

    1. W polu **adres URL odpowiedzi** wprowadź jeden z następujących adresów URL:
        * https: \/ /CERT-Manager.com/Shibboleth.SSO/SAML2/post
        * https: \/ /Hard.CERT-Manager.com/Shibboleth.SSO/SAML2/post

    1. Wybierz pozycję **Ustaw dodatkowe adresy URL**.

    1. W polu **Stan przekazywania** wprowadź jeden z następujących adresów URL:
       * https: \/ /CERT-Manager.com/Customer/SSLSupport/IDP
       * https: \/ /Hard.CERT-Manager.com/Customer/SSLSupport/IDP

    ![Sectigo domeny Menedżera certyfikatów i adresów URL Logowanie jednokrotne](common/idp-relay.png)

1.  Aby skonfigurować aplikację w *trybie zainicjowanym przez program SP*, wykonaj następujące czynności:

    * W polu **adres URL logowania** wprowadź jeden z następujących adresów URL:
      * https: \/ /CERT-Manager.com/Shibboleth.SSO/login
      * https: \/ /Hard.CERT-Manager.com/Shibboleth.SSO/login

      ![Sectigo domeny Menedżera certyfikatów i adresów URL Logowanie jednokrotne](common/both-signonurl.png)

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** obok pozycji **certyfikat (base64)**. Wybierz opcję pobierania w zależności od wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikatu (base64)](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Menedżera certyfikatów Sectigo** Skopiuj następujące adresy URL zgodnie z wymaganiami:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w Menedżerze certyfikatów Sectigo

Aby skonfigurować Logowanie jednokrotne na stronie Menedżera certyfikatów Sectigo, Wyślij pobrany plik certyfikatu (base64) i odpowiednie adresy URL skopiowane z Azure Portal do [zespołu pomocy technicznej Menedżera certyfikatów Sectigo](https://sectigo.com/support). Zespół pomocy technicznej Menedżera certyfikatów Sectigo korzysta z informacji przesyłanych przez Ciebie, aby upewnić się, że połączenie protokołu SAML logowania jednokrotnego jest ustawione prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i wszyscy użytkownicy](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Opcja nowy użytkownik](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **brittasimon \@ \<your-company-domain> . \<extension\> **. Na przykład **brittasimon \@ contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** . Zapisz wartość wyświetlaną w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

    ![Okienko użytkownika](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji przyznano Britta Simon dostęp do Menedżera certyfikatów Sectigo, dzięki czemu może on korzystać z logowania jednokrotnego na platformie Azure.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **Sectigo Menedżera certyfikatów**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **Menedżer certyfikatów Sectigo**.

    ![Sectigo Menedżera certyfikatów na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników. Wybierz pozycję **Wybierz**.

1. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W okienku **Dodaj przypisanie** wybierz pozycję **Przypisz**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Tworzenie użytkownika testowego Menedżera certyfikatów Sectigo

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Menedżerze certyfikatów Sectigo. Powspółpracuje z [zespołem pomocy technicznej Menedżera certyfikatów Sectigo](https://sectigo.com/support) , aby dodać użytkownika na platformie Menedżera certyfikatów Sectigo. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po skonfigurowaniu logowania jednokrotnego po wybraniu opcji **Sectigo Certificate Manager** w portalu My Apps zostanie automatycznie zalogowany do Menedżera certyfikatów Sectigo. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zapoznaj się z następującymi artykułami:

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


