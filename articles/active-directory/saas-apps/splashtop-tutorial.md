---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Splashtop | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Splashtop.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 72c7a24f165d48d3ba2ea0dbcc2b41c818e3f1d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88524571"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Splashtop

W tym samouczku dowiesz się, jak zintegrować usługę Splashtop z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Splashtop z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Splashtop.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Splashtop przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Splashtop.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Splashtop obsługuje logowanie jednokrotne w usłudze **SP**

* Po skonfigurowaniu Splashtop można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Dodawanie Splashtop z galerii

Aby skonfigurować integrację programu Splashtop z usługą Azure AD, musisz dodać Splashtop z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Splashtop** w polu wyszukiwania.
1. Wybierz pozycję **Splashtop** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Splashtop

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Splashtop przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Splashtop.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Splashtop, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-splashtop-sso)** w usłudze Splashtop, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Splashtop](#create-splashtop-test-user)** , aby dysponować odpowiednikiem B. Simon w Splashtop, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Splashtop** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://my.splashtop.com/login/sso`

1. Aplikacja Splashtop oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych, natomiast **NameIdentifier** jest mapowany z **User. userPrincipalName**. Aplikacja biletówmanager oczekuje, że **NameIdentifier** mają być mapowane przy użyciu elementu **User. mail**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **set-up Splashtop** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Splashtop.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Splashtop**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-splashtop-sso"></a>Konfigurowanie logowania jednokrotnego Splashtop

W tej sekcji należy zastosować nową metodę rejestracji jednokrotnej z [portalu sieci Web Splashtop](https://my.splashtop.com/login).
1. W portalu sieci Web Splashtop przejdź do pozycji **Informacje o koncie**  /  karta**zespół** , przewiń w dół, aby znaleźć sekcję **Logowanie** jednokrotne. Następnie kliknij pozycję **Zastosuj dla nowej metody rejestracji jednokrotnej**.

    ![image (obraz)](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. W oknie stosowanie Podaj **nazwę logowania jednokrotnego**. Na przykład nowy Azure, a następnie wybierz pozycję **Azure** jako typ dostawcy tożsamości, a następnie Wstaw **adres URL logowania** i **Identyfikator usługi Azure AD** skopiowane z aplikacji Splashtop na Azure Portal.

    ![image (obraz)](media/splashtop-tutorial/azure-sso-1.png)

1. Aby uzyskać informacje o certyfikacie, kliknij prawym przyciskiem myszy plik certyfikatu pobrany z aplikacji Splashtop na Azure Portal, edytuj go za pomocą Notatnika, a następnie skopiuj zawartość, a następnie wklej ją w polu **Pobierz certyfikat (base64)** .

    ![obraz ](media/splashtop-tutorial/cert-1.png) obrazu ![ obrazu ](media/splashtop-tutorial/cert-2.png) ![](media/splashtop-tutorial/azure-sso-2.png)

1. To wszystko. Kliknij przycisk **Zapisz** i Splashtop Weryfikacja logowania jednokrotnego skontaktuje się z Tobą w celu uzyskania informacji weryfikacyjnych, a następnie aktywuj metodę rejestracji jednokrotnej.

### <a name="create-splashtop-test-user"></a>Utwórz użytkownika testowego Splashtop

1. Po aktywowaniu metody rejestracji jednokrotnej Sprawdź nowo utworzoną metodę logowania jednokrotnego, aby włączyć ją w sekcji **Logowanie** jednokrotne.

    ![image (obraz)](media/splashtop-tutorial/enable.png)

1. Zaproś użytkownika testowego, na przykład `B.Simon@contoso.com` do zespołu Splashtop z nowo utworzoną metodą rejestracji jednokrotnej.

    ![image (obraz)](media/splashtop-tutorial/invite.png)

1. Możesz również zmienić istniejące konto Splashtop na konto logowania jednokrotnego, aby zapoznać się z [instrukcjami](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. To wszystko. Możesz użyć konta logowania jednokrotnego, aby zalogować się w portalu internetowym Splashtop lub aplikacji biznesowej Splashtop.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Splashtop w panelu dostępu należy automatycznie zalogować się do Splashtop, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Splashtop z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Splashtop z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)