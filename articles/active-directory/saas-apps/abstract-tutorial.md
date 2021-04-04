---
title: 'Samouczek: integracja Azure Active Directory z abstrakcyjną | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i abstrakcyjne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: e1c3236c4c1957b4d0daee8d30c71f03fb8674dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587829"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Samouczek: Integrowanie abstrakcyjne z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować streszczenie z Azure Active Directory (Azure AD). Po zintegrowaniu integracji z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do abstrakcyjnej.
* Zezwól użytkownikom na automatyczne logowanie do programu abstract przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Abstrakcyjne obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-abstract-from-the-gallery"></a>Dodawanie abstrakcyjnej z galerii

Aby skonfigurować integrację abstrakcyjną z usługą Azure AD, należy dodać abstrakcyjną z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **abstract** w polu wyszukiwania.
1. Wybierz opcję **abstrakcyjny** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą narzędzia abstract przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w postaci abstrakcyjnej.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą abstrakcyjnej, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj abstrakcyjne logowanie JEDNOkrotne](#configure-abstract-sso)** — w celu skonfigurowania pojedynczych ustawień Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika test abstrakcyjny](#create-abstract-test-user)** , aby uzyskać odpowiednik Britta Simon w postaci abstrakcyjnej, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **abstrakcyjną** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana w trybie inicjalizacji **dostawcy tożsamości** , a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://app.abstract.com/signin`

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Skonfiguruj abstrakcyjne Logowanie jednokrotne

Upewnij się, że pobierasz `App Federation Metadata Url` i `Azure AD Identifier` z Azure Portal, ponieważ będą potrzebne do skonfigurowania logowania jednokrotnego na abstrakcyjnym.

Te informacje znajdują się na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** :

* Znajduje się `App Federation Metadata Url` w sekcji **certyfikat podpisywania SAML** .
* Znajduje się `Azure AD Identifier` w sekcji **Konfigurowanie abstrakcyjne** .


Teraz można przystąpić do konfigurowania logowania jednokrotnego w postaci abstrakcyjnej:

>[!Note]
>Musisz uwierzytelnić się przy użyciu konta administratora organizacji, aby uzyskać dostęp do ustawień logowania jednokrotnego na serwerze abstrakcyjnym.

1. Otwórz [abstrakcyjną aplikację sieci Web](https://app.abstract.com/).
2. Przejdź do strony **uprawnienia** na pasku po lewej stronie.
3. W sekcji **Konfigurowanie logowania jednokrotnego** wprowadź **adres URL metadanych** i **Identyfikator jednostki**.
4. Wprowadź wszelkie ręczne wyjątki, które mogą być dostępne. Wiadomości e-mail wymienione w sekcji wyjątki ręczne pomijają Logowanie jednokrotne i mogą logować się przy użyciu poczty e-mail i hasła. 
5. Kliknij przycisk **Zapisz zmiany**.

>[!Note] 
>Musisz użyć podstawowych adresów e-mail na liście wyjątków ręcznych. Aktywacja SSO zakończy się niepowodzeniem, jeśli lista wiadomości e-mail jest pomocniczą wiadomością e-mail użytkownika. Jeśli tak się stanie, zobaczysz komunikat o błędzie z podstawowym adresem e-mail dla nieprawidłowego konta. Dodaj tę podstawową wiadomość e-mail do listy wyjątków ręcznych po zweryfikowaniu, że znasz użytkownika.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do abstrakcyjnej.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **abstrakcyjne**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-abstract-test-user"></a>Utwórz użytkownika test abstrakcyjny

Aby przetestować Logowanie jednokrotne na streszczeniu:

1. Otwórz [abstrakcyjną aplikację sieci Web](https://app.abstract.com/).
2. Przejdź do strony **uprawnienia** na pasku po lewej stronie.
3. Kliknij przycisk **Testuj przy użyciu mojego konta**. Jeśli test zakończy się niepowodzeniem, [skontaktuj się z naszym zespołem pomocy technicznej](https://help.abstract.com/hc/).

>[!Note]
>Musisz uwierzytelnić się przy użyciu konta administratora organizacji, aby uzyskać dostęp do ustawień logowania jednokrotnego na serwerze abstrakcyjnym.
Konto administratora organizacji musi być przypisane do programu abstract na Azure Portal.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka abstract w panelu dostępu należy automatycznie zalogować się do streszczenia, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)