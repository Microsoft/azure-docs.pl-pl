---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia EAB nawigacji IMPL | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i EAB nawigowanie po witrynie IMPL.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 1ef4cd8830cec4eb8d72855f8308035e47bf3d4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88555621"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-impl"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) za pomocą EAB nawigacji IMPL

W ramach tego samouczka dowiesz się, jak zintegrować usługę EAB nawigacji IMPL z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi EAB nawigowanie po usłudze IMPL z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi EAB, przejdź do IMPL.
* Zezwól użytkownikom na automatyczne logowanie do EAB nawigowanie po usłudze IMPL przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* EAB przejdź do subskrypcji z włączoną obsługą logowania jednokrotnego (SSO) IMPL.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* EAB IMPL **SP**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-eab-navigate-impl-from-the-gallery"></a>Dodawanie EAB nawigacji IMPL z galerii

Aby skonfigurować integrację usługi EAB, przejdź do obszaru IMPL w usłudze Azure AD, musisz dodać EAB Nawiguj IMPL z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **EAB** w polu wyszukiwania.
1. Wybierz pozycję **EAB, przejdź do Impl** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-impl"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD na potrzeby EAB nawigowanie po witrynie IMPL

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą EAB nawigacji IMPL przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w EAB nawigacji po IMPL.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą EAB nawigacji IMPL, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Konfigurowanie ustawień logowania jednokrotnego za pomocą **[EAB nawigacji Impl](#configure-eab-navigate-impl-sso)** — aby skonfigurować ustawienia rejestracji jednokrotnej na stronie aplikacji.
    * **[Utwórz EAB przejdź do Impl użytkownika testowego](#create-eab-navigate-impl-test-user)** — Aby uzyskać odpowiedni odpowiednik B. Simon w EAB, przejdź do Impl, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **EAB przejdź** do integracji aplikacji Impl Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól: w polu tekstowym **Identyfikator (identyfikator jednostki)** wprowadź dokładnie następującą wartość:  `https://impl.bouncer.eab.com`
    
    W polu tekstowym **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** wprowadź obie następujące wartości jako oddzielne wiersze: `https://impl.bouncer.eab.com/sso/saml2/acs`
    `https://impl.bouncer.eab.com/sso/saml2/acs/`
    
    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.navigate.impl.eab.com/`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej EAB](mailto:EABTechSupport@eab.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

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
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do EAB nawigacji IMPL.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **EAB przejdź do Impl**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-eab-navigate-impl-sso"></a>Konfigurowanie EAB nawigowania po usłudze IMPL

Aby skonfigurować Logowanie jednokrotne w witrynie **EAB, przejdź** do strony Impl, musisz wysłać **adres URL metadanych federacji aplikacji** do EAB, przejdź do [zespołu pomocy technicznej Impl](mailto:EABTechSupport@eab.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-eab-navigate-impl-test-user"></a>Utwórz EAB nawigację IMPL użytkownika testowego

W tej sekcji utworzysz użytkownika o nazwie B. Simon w EAB nawigacji IMPL. Współpracuj z [zespołem pomocy technicznej EAB nawigowanie po Impl](mailto:EABTechSupport@eab.com) , aby dodać użytkowników na platformie EAB nawigację Impl. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka EAB przejdź do IMPL w panelu dostępu należy automatycznie zalogować się do IMPL EAB, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę EAB, przejdź do IMPL z usługą Azure AD](https://aad.portal.azure.com/)
