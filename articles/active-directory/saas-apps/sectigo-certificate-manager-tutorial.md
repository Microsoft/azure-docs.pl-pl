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
ms.openlocfilehash: c589db84b6221aa23868b3b49aea84f33623619f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92673869"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Samouczek: integracja Azure Active Directory z menedżerem certyfikatów Sectigo

W tym samouczku dowiesz się, jak zintegrować Menedżera certyfikatów Sectigo (zwany również menedżerem SCM) z usługą Azure Active Directory (Azure AD).

Integracja Menedżera certyfikatów Sectigo z usługą Azure AD zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do Menedżera certyfikatów Sectigo.
* Użytkownicy mogą być automatycznie zalogowani, aby Sectigo Menedżera certyfikatów przy użyciu kont usługi Azure AD (Logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS (Software as a Service) z usługą Azure AD, zobacz [Logowanie jednokrotne do aplikacji w Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z menedżerem certyfikatów Sectigo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji usługi Azure AD, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Sectigo konto menedżera certyfikatów.

> [!NOTE]
> Sectigo uruchamia wiele wystąpień Menedżera certyfikatów Sectigo. Głównym wystąpieniem Menedżera certyfikatów Sectigo jest  **https: \/ /CERT-Manager.com**, a ten adres URL jest używany w tym samouczku.  Jeśli Twoje konto jest w innym wystąpieniu, należy odpowiednio dostosować adresy URL.

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

1. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące czynności:

    1. W polu **Identyfikator (identyfikator jednostki)** dla głównego wystąpienia Menedżera certyfikatów Sectigo wprowadź **https: \/ /CERT-Manager.com/Shibboleth**.

    1. W polu **adres URL odpowiedzi** dla głównego wystąpienia Menedżera certyfikatów Sectigo wprowadź **https: \/ /CERT-Manager.com/Shibboleth.SSO/SAML2/post**.
        
    > [!NOTE]
    > Chociaż ogólnie rzecz biorąc, **adres URL logowania** jest obowiązkowy w *trybie zainicjowanym* przez program SP, nie jest on konieczny do logowania się z Menedżera certyfikatów Sectigo.        

1. Opcjonalnie w sekcji **Podstawowa konfiguracja protokołu SAML** , aby skonfigurować *tryb zainicjowany przez dostawcy tożsamości* i zezwolić na działanie **testu** , wykonaj następujące czynności:

    1. Wybierz pozycję **Ustaw dodatkowe adresy URL**.

    1. W polu **Stan przekazywania** wprowadź adres URL specyficzny dla klienta Sectigo Certificate Manager. W przypadku głównego wystąpienia Menedżera certyfikatów Sectigo wprowadź **https: \/ /CERT-Manager.com/Customer/ \<customerURI\> /IDP**.

    ![Sectigo domeny Menedżera certyfikatów i adresów URL Logowanie jednokrotne](common/idp-relay.png)

1. W sekcji **atrybuty użytkownika & oświadczenia** wykonaj następujące czynności:

    1. Usuń wszystkie **dodatkowe oświadczenia**.
    
    1. Wybierz pozycję **Dodaj nowe oświadczenie** i Dodaj następujące cztery oświadczenia:
    
        | Nazwa | Przestrzeń nazw | Element źródłowy | Atrybut źródłowy | Opis |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | puste | Atrybut | user.userprincipalname | Musi pasować do pola **Identyfikator osoby dostawcy tożsamości** w Menedżerze certyfikatów Sectigo dla administratorów. |
        | mail (poczta) | puste | Atrybut | user.mail | Wymagane |
        | givenName | puste | Atrybut | user.givenname | Opcjonalne |
        | sn | puste | Atrybut | user.surname | Opcjonalne |

       ![Menedżer certyfikatów Sectigo — Dodaj cztery nowe oświadczenia](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. W sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** obok pozycji **XML metadanych Federacji**. Zapisz plik XML na komputerze.

    ![Opcja pobierania XML metadanych Federacji](common/metadataxml.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w Menedżerze certyfikatów Sectigo

Aby skonfigurować Logowanie jednokrotne na stronie Menedżera certyfikatów Sectigo, Wyślij pobrany plik XML metadanych Federacji do [zespołu pomocy technicznej Menedżera certyfikatów Sectigo](https://sectigo.com/support). Zespół pomocy technicznej Menedżera certyfikatów Sectigo korzysta z informacji przesyłanych przez Ciebie, aby upewnić się, że połączenie protokołu SAML logowania jednokrotnego jest ustawione prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i wszyscy użytkownicy](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Opcja nowy użytkownik](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **brittasimon \@ \<your-company-domain> . \<extension\>**. Na przykład **brittasimon \@ contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** . Zapisz wartość wyświetlaną w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

    ![Okienko użytkownika](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji przyznano Britta Simon dostęp do Menedżera certyfikatów Sectigo, dzięki czemu użytkownik może korzystać z logowania jednokrotnego na platformie Azure.

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

Ta sekcja umożliwia testowanie konfiguracji logowania jednokrotnego usługi Azure AD.

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Test z Sectigo Certificate Manager (Logowanie jednokrotne zainicjowane przez SP)

Przejdź do adresu URL specyficznego dla klienta (dla głównego wystąpienia Menedżera certyfikatów Sectigo, https: \/ /CERT-Manager.com/Customer/ \<customerURI\> /, a następnie wybierz poniższy przycisk **lub Zaloguj się przy użyciu** usługi.  W przypadku poprawnego skonfigurowania nastąpi automatyczne zalogowanie do Menedżera certyfikatów Sectigo.

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Testowanie konfiguracji logowania jednokrotnego na platformie Azure (Logowanie jednokrotne zainicjowane przez dostawcy tożsamości)

W okienku integracja aplikacji **Menedżera certyfikatów Sectigo** wybierz pozycję **Logowanie jednokrotne** i wybierz przycisk **Testuj** .  W przypadku poprawnego skonfigurowania nastąpi automatyczne zalogowanie do Menedżera certyfikatów Sectigo.

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>Testowanie przy użyciu portalu My Apps (Logowanie jednokrotne zainicjowane przez dostawcy tożsamości)

Wybierz pozycję **Menedżer certyfikatów Sectigo** w portalu Moje aplikacje.  Po poprawnym skonfigurowaniu nastąpi automatyczne zalogowanie do Menedżera certyfikatów Sectigo. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zapoznaj się z następującymi artykułami:

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)