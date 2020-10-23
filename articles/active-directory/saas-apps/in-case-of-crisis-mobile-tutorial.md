---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) w przypadku wystąpienia kryzysu — urządzenia przenośne | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i w przypadku kryzysu-Mobile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: cee037b7f92dd55bb8fba264e164087a1cf8983b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460278"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-in-case-of-crisis---mobile"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) w przypadku wystąpienia kryzysu — urządzenia przenośne

W tym samouczku dowiesz się, jak zintegrować usługę w przypadku wystąpienia kryzysu — Mobile with Azure Active Directory (Azure AD). W przypadku integracji z usługą Azure AD w przypadku wystąpienia kryzysowego można:

* Kontrolka w usłudze Azure AD, która ma dostęp do aplikacji w przypadku kryzysu-Mobile.
* Zezwól użytkownikom na automatyczne logowanie się na wypadek kryzysu — mobilnych z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* W przypadku subskrypcji z włączonym logowaniem jednokrotnym (SSO) dla urządzeń przenośnych.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* W przypadku kryzysu — Mobile obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-in-case-of-crisis---mobile-from-the-gallery"></a>Dodawanie w przypadku kryzysu — urządzenia przenośne z galerii

Aby skonfigurować integrację programu w przypadku kryzysu — urządzenia przenośne w usłudze Azure AD, należy dodać w przypadku kryzysu — urządzenia przenośne z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania **w przypadku kryzysu-Mobile** .
1. Wybierz pozycję **w przypadku kryzysu — urządzenia przenośne** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-in-case-of-crisis---mobile"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na wypadek kryzysu — urządzenia przenośne

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD w przypadku wystąpienia kryzysu-Mobile przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w przypadku kryzysu-Mobile.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu programu w przypadku kryzysu — urządzenia przenośne, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj w przypadku kryzysu — logowanie JEDNOkrotne](#configure-in-case-of-crisis---mobile-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz w przypadku kryzysu testowego użytkownika](#create-in-case-of-crisis---mobile-test-user)** , aby dysponować odpowiednikiem B. Simon w przypadku kryzysu-Mobile, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **w przypadku kryzysu —** integracja aplikacji mobilnych Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana w trybie inicjalizacji **dostawcy tożsamości** , a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (RAW)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. Przejdź do sekcji **Zarządzanie** w lewej części strony, kliknij **kartę właściwości** , a następnie skopiuj **adres URL dostępu użytkownika** i Zapisz go na komputerze.

    ![Właściwości logowania jednokrotnego](./media/in-case-of-crisis-mobile-tutorial/properties.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu na wypadek kryzysu — mobilnego.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **w przypadku kryzysu — Mobile**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-in-case-of-crisis---mobile-sso"></a>Konfigurowanie w przypadku kryzysu — Logowanie jednokrotne dla urządzeń przenośnych

Aby skonfigurować Logowanie jednokrotne **w przypadku systemu kryzysowego** , należy wysłać pobranego **certyfikatu (RAW)** i skopiować **adres URL dostępu użytkownika** z Azure Portal do [programu w przypadku kryzysu-Mobile Support Team](https://www.rockdovesolutions.com/features/enterprise-ready). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-in-case-of-crisis---mobile-test-user"></a>Tworzenie w przypadku kryzysu — Użytkownik testowy dla urządzeń przenośnych

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w przypadku kryzysu-Mobile. Praca z usługą [w przypadku wystąpienia kryzysowego — zespół pomocy technicznej dla urządzeń przenośnych](https://www.rockdovesolutions.com/features/enterprise-ready) do dodawania użytkowników w przypadku platformy kryzysowej. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka na platformie kryzysowej w panelu dostępu należy automatycznie zalogować się do programu na wypadek kryzysu — urządzenia przenośne, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj w przypadku kryzysu — urządzenia przenośne z usługą Azure AD](https://aad.portal.azure.com/)