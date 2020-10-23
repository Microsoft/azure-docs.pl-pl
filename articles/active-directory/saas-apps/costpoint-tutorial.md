---
title: 'Samouczek: integracja Azure Active Directory z usługą Costpoint | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 16c9d64cc4dd49898245d74108cd6a4f2f0e4660
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455174"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Samouczek: integracja Costpoint z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Costpoint z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Costpoint z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Costpoint.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Costpoint przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Costpoint.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Costpoint obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .

## <a name="generate-costpoint-metadata"></a>Generuj metadane Costpoint

Costpoint konfiguracja logowania jednokrotnego SAML została omówiona w przewodniku **DeltekCostpoint711Security.pdf** . Pobierz ten przewodnik z witryny obsługi Costpoint deltek, a następnie zapoznaj się z sekcją **Konfiguracja logowania**jednokrotnego protokołu SAML  >  **między Costpoint i Microsoft Azure** . Postępuj zgodnie z instrukcjami i wygeneruj plik **XML metadanych Federacji COSTPOINT Sp** . 

![Zrzut ekranu przedstawiający "Narzędzie konfiguracji produktu" z wybraną kartą "WebLogic-Security".](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Dodaj Costpoint z galerii

Aby zintegrować usługę Costpoint z usługą Azure AD, najpierw Dodaj Costpoint do listy zarządzanych aplikacji SaaS z galerii w Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.

1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .

   ![Przycisk Azure Active Directory](common/select-azuread.png)

1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

   ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.

   ![Przycisk Nowa aplikacja](common/add-new-app.png)

1. W sekcji **Dodaj z galerii** wprowadź **Costpoint** w polu wyszukiwania.

   ![Costpoint na liście wyników](common/search-new-app.png)

1. Na liście wyników wybierz pozycję **Costpoint**, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Konfigurowanie i testowanie pojedynczej SGN usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Costpoint przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Costpoint.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Costpoint, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj Costpoint](#configure-costpoint)** , aby skonfigurować ustawienia logowania jednokrotnego dla aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Utwórz użytkownika testowego Costpoint](#create-a-costpoint-test-user)** , aby miał odpowiednik B. Simon w Costpoint, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. Na stronie integracja aplikacji **Costpoint** wybierz pozycję **Logowanie jednokrotne**.

   ![Konfigurowanie linku logowania jednokrotnego](common/select-sso.png)

1. Jeśli masz *plik metadanych dostawcy usług*, w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

   > [!NOTE]
   > Plik metadanych dostawcy usług jest pobierany w temacie [generowanie metadanych Costpoint](#generate-costpoint-metadata). Jak używać pliku, wyjaśniono w dalszej części tego samouczka.
 
   1. Wybierz przycisk **Przekaż plik metadanych** , a następnie wybierz plik **XML metadanych Federacji Costpoint Sp** wcześniej wygenerowany przez Costpoint, a następnie wybierz przycisk **Dodaj** , aby przekazać plik.

      ![Przekaż plik metadanych](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są wypełniane automatycznie w sekcji Costpoint.

      > [!NOTE]
      > Jeśli wartości **identyfikatorów** i **adresów URL odpowiedzi** nie są autopolulated, wprowadź wartości ręcznie zgodnie z wymaganiami. Sprawdź, czy **Identyfikator (identyfikator jednostki)** i **adres URL odpowiedzi (adres URL usługi konsumenckej potwierdzenia)** są poprawnie ustawione, a ten **adres URL usług ACS** jest prawidłowym adresem URL Costpoint kończącym się **/LoginServlet.CPS**.

   1. Wybierz pozycję **Ustaw dodatkowe adresy URL**. W polu **Stan przekazywania**wprowadź wartość przy użyciu następującego wzorca: `system=[your system]` (na przykład **system = DELTEKCP**).

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz ikonę **kopiowania** , aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go w Notatniku.

   ![Certyfikat podpisywania SAML](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Konfigurowanie Costpoint

1. Wróć do narzędzia konfiguracji Costpoint. W polu tekstowym **XML metadanych Federacji dostawcy tożsamości** wklej zawartość pliku *adresów URL metadanych federacji aplikacji* . 

   ![Narzędzie konfiguracji Costpoint](./media/costpoint-tutorial/config-utility-idp.png)

1. Kontynuuj instrukcje z przewodnika **DeltekCostpoint711Security.pdf** , aby zakończyć konfigurację SAML Costpoint.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

   ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

   ![Przycisk Nowy użytkownik](common/new-user.png)

1. We właściwościach **użytkownika** wykonaj następujące czynności:

   ![Okno dialogowe Użytkownik](common/user-properties.png)

   1. W polu **Nazwa** wpisz **B. Simon**.
   
   1. W polu **Nazwa użytkownika** wprowadź `b.simon\@yourcompanydomain.extension` (na przykład B.Simon@contoso.com ).
   
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz wartość, która jest wyświetlana w polu **hasło** .
   
   1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie usłudze B. Simon dostępu do usługi Costpoint.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

1. Na liście Aplikacje wybierz pozycję **Costpoint**.

1. W sekcji **Zarządzanie** na stronie Przegląd aplikacji wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

   ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** , na liście **Użytkownicy** wybierz pozycję **B. Simon**. Następnie wybierz **pozycję Wybierz**.

1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika, a następnie wybierz **pozycję Wybierz**.

1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-costpoint-test-user"></a>Tworzenie użytkownika testowego Costpoint

W tej sekcji utworzysz użytkownika w Costpoint. Załóżmy, że identyfikator użytkownika to **b. Simon** , a nazwa użytkownika to **b. Simon**. Współpracuj z [zespołem obsługi klienta Costpoint](https://www.deltek.com/about/contact-us) , aby dodać użytkownika na platformie Costpoint. Aby można było korzystać z logowania jednokrotnego, użytkownik musi zostać utworzony i aktywowany.

Po utworzeniu użytkownika należy zaznaczyć pole wyboru **Metoda uwierzytelniania** dla użytkownika, które musi być **Active Directory**, musi być zaznaczona opcja Logowanie jednokrotne **protokołu SAML** , a nazwa użytkownika Azure Active Directory musi być **Active Directory lub identyfikatorem certyfikatu** (pokazanym na poniższym zrzucie ekranu).

![Costpoint użytkownika](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Costpoint w panelu dostępu należy automatycznie zalogować się do aplikacji Costpoint, ponieważ konfigurujesz Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków umożliwiających integrację aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)