---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z logowaniem jednokrotnym (SSO) w usłudze NegometrixPortal | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i NegometrixPortal Logowanie jednokrotne (SSO).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.openlocfilehash: d972868cf9c5d67824eab781bc99a7cac5f7b313
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92507143"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-negometrixportal-single-sign-on-sso"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu logowania jednokrotnego (SSO) NegometrixPortal

W tym samouczku dowiesz się, jak zintegrować NegometrixPortal Logowanie jednokrotne (SSO) z usługą Azure Active Directory (Azure AD). Gdy integrujesz Logowanie jednokrotne w usłudze NegometrixPortal z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do logowania jednokrotnego (SSO) NegometrixPortal.
* Zezwól użytkownikom na automatyczne logowanie się, aby NegometrixPortal Logowanie jednokrotne (SSO) przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) NegometrixPortal.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* NegometrixPortal Logowanie jednokrotne (SSO) obsługuje zainicjowanie rejestracji jednokrotnej w usłudze **SP**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-negometrixportal-single-sign-on-sso-from-the-gallery"></a>Dodawanie logowania jednokrotnego (SSO) NegometrixPortal z galerii

Aby skonfigurować integrację logowania jednokrotnego (SSO) NegometrixPortal w usłudze Azure AD, musisz dodać Logowanie jednokrotne (SSO) NegometrixPortal z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz NegometrixPortal Logowanie jednokrotne **(SSO)** w polu wyszukiwania.
1. Wybierz pozycję NegometrixPortal Logowanie jednokrotne **(SSO)** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-negometrixportal-single-sign-on-sso"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby logowania jednokrotnego w usłudze NegometrixPortal (SSO)

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego (SSO) w usłudze NegometrixPortal przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze NegometrixPortal Logowanie jednokrotne (SSO).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu logowania jednokrotnego (SSO) w usłudze NegometrixPortal, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Skonfiguruj Logowanie jednokrotne **[(SSO) NegometrixPortal](#configure-negometrixportal-single-sign-on-sso-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego logowania jednokrotnego (SSO) NegometrixPortal](#create-negometrixportal-single-sign-on-sso-test-user)** , aby mieć odpowiedni odpowiednik B. Simon w NegometrixPortal logowania jednokrotnego (SSO), który jest połączony z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji logowania jednokrotnego **(SSO) NegometrixPortal** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://portal.negometrix.com/sso/<CUSTOMURL>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta rejestracji jednokrotnej (SSO) NegometrixPortal](mailto:sander.hoek@negometrix.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja logowania jednokrotnego (SSO) NegometrixPortal oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych NegometrixPortal aplikacja logowania jednokrotnego (SSO) oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------|  --------- |
    | głównej | user.userprincipalname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji zostanie włączone Logowanie jednokrotne w usłudze B. Simon, przyznając dostęp do logowania jednokrotnego (SSO) NegometrixPortal.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję NegometrixPortal Logowanie jednokrotne **(SSO)**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-negometrixportal-single-sign-on-sso-sso"></a>Konfigurowanie logowania jednokrotnego (SSO) NegometrixPortal

Aby skonfigurować Logowanie jednokrotne na stronie logowania jednokrotnego **(SSO) NegometrixPortal** , musisz wysłać **adres URL metadanych federacji aplikacji** do [zespołu pomocy technicznej logowania jednokrotnego (SSO) usługi NegometrixPortal](mailto:sander.hoek@negometrix.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-negometrixportal-single-sign-on-sso-test-user"></a>Utwórz użytkownika testowego logowania jednokrotnego (SSO) NegometrixPortal

W tej sekcji utworzysz użytkownika o nazwie B. Simon w NegometrixPortal logowania jednokrotnego (SSO). Pracuj z [zespołem pomocy technicznej Logowanie jednokrotne (SSO) NegometrixPortal](mailto:sander.hoek@negometrix.com) , aby dodać użytkowników z platformy logowania jednokrotnego (SSO) NegometrixPortal. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka logowania jednokrotnego (SSO) NegometrixPortal w panelu dostępu należy automatycznie zalogować się do logowania jednokrotnego (SSO) usługi NegometrixPortal, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj NegometrixPortal Logowanie jednokrotne (SSO) z usługą Azure AD](https://aad.portal.azure.com/)