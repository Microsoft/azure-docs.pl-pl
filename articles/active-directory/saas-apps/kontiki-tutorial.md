---
title: 'Samouczek: integracja Azure Active Directory z usługą Kontiki | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Kontiki.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 9e0b438e0cfa729300099463086248780c69484c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95019844"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Samouczek: integracja Azure Active Directory z usługą Kontiki

W tym samouczku dowiesz się, jak zintegrować usługę Kontiki z usługą Azure Active Directory (Azure AD).

Integracja Kontiki z usługą Azure AD zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do usługi Kontiki.
* Użytkownicy mogą być automatycznie zalogowani w usłudze Kontiki przy użyciu kont usługi Azure AD (Logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS (Software as a Service) z usługą Azure AD, zobacz [Logowanie jednokrotne do aplikacji w Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Kontiki, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji usługi Azure AD, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Subskrypcja Kontiki z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku należy skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD w środowisku testowym i zintegrować usługę Kontiki z usługą Azure AD.

Program Kontiki obsługuje następujące funkcje:

* **Logowanie jednokrotne zainicjowane przez program SP**
* **Aprowizowanie użytkowników typu just in time**

## <a name="add-kontiki-in-the-azure-portal"></a>Dodaj Kontiki w Azure Portal

Aby zintegrować usługę Kontiki z usługą Azure AD, musisz dodać Kontiki do listy zarządzanych aplikacji SaaS.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Opcja Azure Active Directory](common/select-azuread.png)

1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.

    ![Opcja nowej aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **Kontiki**. W wynikach wyszukiwania wybierz pozycję **Kontiki**, a następnie wybierz pozycję **Dodaj**.

    ![Kontiki na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Kontiki na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Kontiki.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Kontiki, należy wykonać następujące bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystanie z tej funkcji. |
| **[Konfigurowanie logowania jednokrotnego Kontiki](#configure-kontiki-single-sign-on)** | Konfiguruje ustawienia logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testuje Logowanie jednokrotne w usłudze Azure AD dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD. |
| **[Tworzenie użytkownika testowego Kontiki](#create-a-kontiki-test-user)** | Tworzy odpowiednik Britta Simon w Kontiki, który jest połączony z reprezentacją usługi Azure AD użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz Logowanie jednokrotne w usłudze Azure AD za pomocą Kontiki w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)w okienku integracja aplikacji **Kontiki** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML** lub **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć okienko **podstawowe ustawienia protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W okienku **podstawowe konfiguracje języka SAML** w polu tekstowym **adres URL logowania** wprowadź adres URL, który ma następujący wzorzec: `https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki domenę i adresy URL Logowanie jednokrotne](common/sp-signonurl.png)

    > [!NOTE]
    > Skontaktuj się z [zespołem obsługi klienta Kontiki](https://kollective.com/support/) , aby uzyskać poprawną wartość do użycia. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** obok pozycji **XML metadanych Federacji**. Wybierz opcję pobierania w zależności od wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikatu XML metadanych Federacji](common/metadataxml.png)

1. W sekcji **Konfiguracja Kontiki** Skopiuj następujące adresy URL zgodnie z wymaganiami:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Kontiki

Aby skonfigurować Logowanie jednokrotne po stronie Kontiki, Wyślij pobrany plik XML metadanych Federacji i odpowiednie adresy URL skopiowane z Azure Portal do [zespołu pomocy technicznej Kontiki](https://kollective.com/support/). Zespół pomocy technicznej Kontiki używa tych informacji, aby upewnić się, że połączenie protokołu SAML logowania jednokrotnego jest ustawione prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i wszyscy użytkownicy](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Opcja nowy użytkownik](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **brittasimon \@ \<your-company-domain> . \<extension>**. Na przykład **brittasimon \@ contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** . Zapisz wartość wyświetlaną w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

    ![Okienko użytkownika](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji przyznano Britta Simon dostęp do usługi Kontiki, dzięki czemu może on korzystać z logowania jednokrotnego na platformie Azure.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **Kontiki**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **Kontiki**.

    ![Kontiki na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników. Wybierz pozycję **Wybierz**.

1. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W okienku **Dodaj przypisanie** wybierz pozycję **Przypisz**.

### <a name="create-a-kontiki-test-user"></a>Tworzenie użytkownika testowego Kontiki

Nie ma elementu akcji, aby skonfigurować Inicjowanie obsługi użytkowników w Kontiki. Gdy przypisany użytkownik próbuje zalogować się do usługi Kontiki za pomocą portalu My Apps, Kontiki sprawdza, czy użytkownik istnieje. Jeśli nie zostanie znalezione żadne konto użytkownika, program Kontiki automatycznie utworzy konto użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po skonfigurowaniu logowania jednokrotnego, gdy wybierzesz pozycję **Kontiki** w portalu My Apps, nastąpi automatyczne zalogowanie do Kontiki. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zapoznaj się z następującymi artykułami:

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)