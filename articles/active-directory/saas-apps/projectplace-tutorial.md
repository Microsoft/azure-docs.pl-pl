---
title: 'Samouczek: integracja Azure Active Directory z usługą Projectplace | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Projectplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: 508a3ca02ec6c0475e78d16691f6597e1a389f97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511856"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Samouczek: integracja Projectplace z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Projectplace z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Projectplace z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Projectplace.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Projectplace przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.
* Użytkownicy mogą być obsługiwani w Projectplace automatycznie.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Projectplace.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Projectplace obsługuje usługę **SP i dostawcy tożsamości** zainicjowana za pomocą logowania jednokrotnego i obsługuje Inicjowanie obsługi użytkowników **just in Time** .

## <a name="adding-projectplace-from-the-gallery"></a>Dodawanie Projectplace z galerii

Aby skonfigurować integrację programu Projectplace z usługą Azure AD, musisz dodać Projectplace z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Projectplace** w polu wyszukiwania.
1. Wybierz pozycję **Projectplace** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Projectplace przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Projectplace.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Projectplace, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Projectplace](#configure-projectplace)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Projectplace](#create-projectplace-test-user)** , aby miał odpowiednik B. Simon w Projectplace, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Projectplace** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przy użyciu usługi **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja języka SAML** aplikacja zostanie wstępnie skonfigurowana i wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://service.projectplace.com`

1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj **, aby skopiować** **adres URL metadanych federacji aplikacji** zgodnie z wymaganiami i zapisać go w Notatniku.

   ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

1. W sekcji **Konfigurowanie Projectplace** skopiuj odpowiednie adresy URL na podstawie wymagania.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Konfigurowanie Projectplace

Aby skonfigurować Logowanie jednokrotne na stronie **Projectplace** , musisz wysłać skopiowany **adres URL metadanych federacji aplikacji** z Azure Portal do [zespołu pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support). Ten zespół gwarantuje, że połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

>[!NOTE]
>Konfiguracja logowania jednokrotnego musi być wykonywana przez [zespół pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support). Otrzymasz powiadomienie zaraz po zakończeniu konfiguracji. 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Projectplace.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Projectplace**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-projectplace-test-user"></a>Utwórz użytkownika testowego Projectplace

>[!NOTE]
>Możesz pominąć ten krok, jeśli włączono obsługę administracyjną w Projectplace. Możesz polecić [zespół pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support) , aby włączyć funkcję aprowizacji, a po pierwszym zalogowaniu użytkownicy zostaną utworzeni w Projectplace.

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Projectplace, należy dodać je do Projectplace. Należy dodać je ręcznie.

**Aby utworzyć konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej witryny firmowej **Projectplace** jako administrator.

2. Przejdź do pozycji **osoby**, a następnie wybierz pozycję **Członkowie**:
   
    ![Przejdź do pozycji osoby, a następnie wybierz pozycję Elementy członkowskie](./media/projectplace-tutorial/ic790228.png "People")

3. Wybierz pozycję **Dodaj członka**:
   
    ![Wybierz pozycję Dodaj członka](./media/projectplace-tutorial/ic790232.png "Dodaj członków")

4. W sekcji **Dodawanie elementu członkowskiego** wykonaj następujące czynności.
   
    ![Dodaj sekcję członkowską](./media/projectplace-tutorial/ic790233.png "Nowi członkowie")
   
    1. W polu **nowe elementy członkowskie** wprowadź adres e-mail prawidłowego konta usługi Azure AD, które chcesz dodać.
   
    1. Wybierz pozycję **Wyślij**.

   Wiadomość e-mail zawierająca link do potwierdzenia konta, zanim staną się aktywne, jest wysyłana do posiadacza konta usługi Azure AD.

>[!NOTE]
>Aby dodać konta użytkowników usługi Azure AD, można również użyć dowolnego innego narzędzia do tworzenia konta użytkownika lub interfejsu API dostarczonego przez Projectplace.


### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Projectplace w panelu dostępu należy automatycznie zalogować się do Projectplace, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)