---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z usługą EAB — opieka strategiczna | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i EAB.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.openlocfilehash: e345fc89e3aa0f63ece1dd52afe0cc6fe4585533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88555574"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-strategic-care"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z usługą EAB — opieka strategiczna

W tym samouczku dowiesz się, jak zintegrować usługę EAB, korzystając z Azure Active Directory (Azure AD). Po zintegrowaniu usługi EAB Nawiguj pod opieką strategiczną dzięki usłudze Azure AD możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do EABego nawigowania po strategicznym znaczeniu.
* Zezwól użytkownikom na automatyczne logowanie do usługi EAB, aby przejść do strategii strategicznej przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* EAB przejdź do subskrypcji z włączoną obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* EAB — opieka strategiczna obsługuje zainicjowanie rejestracji jednokrotnej **SP**

## <a name="adding-eab-navigate-strategic-care-from-the-gallery"></a>Dodawanie EABu do opieki strategicznej z galerii

Aby skonfigurować integrację programu EAB, przejdź do usługi Azure AD, musisz dodać EABe z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **EAB** w polu wyszukiwania.
1. Wybierz pozycję EAB, aby **przechodź strategiczne** z poziomu panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-strategic-care"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD na potrzeby EAB Nawigacja strategiczna

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą usługi EAB, aby korzystać z opieki strategicznej przy użyciu użytkownika testowego o nazwie **B** Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze EAB.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi EAB, należy dokończyć następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Skonfiguruj usługę EAB, aby przejść do ochrony przed **[logowaniem jednokrotnym](#configure-eab-navigate-strategic-care-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz EABe, aby przejść do usługi strategicznych test opieki zdrowotnej](#create-eab-navigate-strategic-care-test-user)** — Aby uzyskać odpowiedni odpowiednik B. Simon w EAB, przejdź do strategii strategicznej, która jest połączona z usługą Azure AD reprezentacją użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **EAB przejdź do integracji strategicznej** aplikacji, Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<CUSTOMERURL>.eab.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej EAB](mailto:tech@gradesfirst.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do usługi EAB w celu przechodzenia do strategii strategicznej.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję EAB, aby **przejść do strategii strategicznej**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-eab-navigate-strategic-care-sso"></a>Konfigurowanie EAB nawigowanie po usłudze logowania jednokrotnego

Aby skonfigurować Logowanie jednokrotne w witrynie EAB, przejdź do strony z **opieką strategiczną** , musisz wysłać **adres URL metadanych federacji aplikacji** do EAB, aby [przejść do zespołu usługi wsparcia strategicznego](mailto:tech@gradesfirst.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-eab-navigate-strategic-care-test-user"></a>Utwórz EABe Nawigacja strategiczna — Użytkownik testowy

W tej sekcji utworzysz użytkownika o nazwie B. Simon w EAB nawigowanie po strategicznej ochronie. Współpracuj z [zespołem pomocy technicznej EAB](mailto:tech@gradesfirst.com) , aby dodać użytkowników do EABowej platformy opieki strategicznej. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka opieka strategiczna EAB przejdź do panelu dostępu należy automatycznie zalogować się do usługi EAB, aby przejść do strategii strategicznej, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę EAB, aby przejść strategicznie do usługi Azure AD](https://aad.portal.azure.com/)

