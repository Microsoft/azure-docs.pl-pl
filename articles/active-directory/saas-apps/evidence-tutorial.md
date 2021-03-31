---
title: 'Samouczek: integracja Azure Active Directory z usługą Evidence.com | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Evidence.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: jeedes
ms.openlocfilehash: ff77ace78dd3cdd4ee942308432795421f6da997
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453802"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evidencecom"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Evidence.com

W tym samouczku dowiesz się, jak zintegrować usługę Evidence.com z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Evidence.com z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Evidence.com.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Evidence.com przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Evidence.com.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Evidence.com obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Po skonfigurowaniu Evidence.com można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-evidencecom-from-the-gallery"></a>Dodawanie aplikacji Evidence.com z galerii

Aby skonfigurować integrację aplikacji Evidence.com w usłudze Azure AD, należy dodać aplikację Evidence.com z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Evidence.com** w polu wyszukiwania.
1. Wybierz pozycję **Evidence.com** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evidencecom"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Evidence.com

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Evidence.com przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Evidence.com.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Evidence.com, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-evidencecom-sso)** w usłudze Evidence.com, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Evidence.com](#create-evidencecom-test-user)** , aby dysponować odpowiednikiem B. Simon w Evidence.com, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Evidence.com** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<yourtenant>.evidence.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<yourtenant>.evidence.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<your tenant>.evidence.com/?class=UIX&proc=Login`
    
    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta aplikacji Evidence.com](https://communities.taser.com/support/SupportContactUs?typ=LE) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **konfigurowanie Evidence.com** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Evidence.com.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Evidence.com**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-evidencecom-sso"></a>Konfigurowanie logowania jednokrotnego Evidence.com

1. W osobnym oknie przeglądarki sieci Web Zaloguj się do dzierżawy Evidence.com jako administrator i przejdź do karty **administratora**

2. Kliknij pozycję **Agency Single Sign On** (Logowanie jednokrotne agencji)

3. Wybierz pozycję **SAML Based Single Sign On** (Logowanie jednokrotne oparte na SAML)

4. Skopiuj wartości **Identyfikator usługi Azure AD**, **Adres URL logowania** i **Adres URL wylogowania** wyświetlane w witrynie Azure Portal do odpowiednich pól w aplikacji Evidence.com.

5. Otwórz pobrany plik certyfikatu (Base64) w Notatniku, skopiuj zawartość do schowka, a następnie wklej ją w polu **Security Certificate** (Certyfikat zabezpieczeń). 

6. Zapisz konfigurację w aplikacji Evidence.com.

### <a name="create-evidencecom-test-user"></a>Tworzenie użytkownika testowego aplikacji Evidence.com

Aby użytkownicy usługi Azure AD mogli się logować, należy ich zaaprowizować w celu uzyskiwania dostępu w aplikacji Evidence.com. W tej sekcji opisano sposób tworzenia kont użytkowników usługi Azure AD w aplikacji Evidence.com

**Aby aprowizować konto użytkownika w aplikacji Evidence.com:**

1. W oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Evidence.com jako administrator.

2. Przejdź do karty **Administrator**.

3. Kliknij pozycję **Dodaj użytkownika**.

4. Kliknij przycisk **Dodaj** .

5. **Adres e-mail** dodanego użytkownika musi odpowiadać nazwie użytkownika w usłudze Azure AD, któremu chcesz udzielić dostępu. Jeśli nazwa użytkownika i adres e-mail nie są tą samą wartością w Twojej organizacji, możesz skorzystać z sekcji **Evidence.com > Atrybuty > Logowanie jednokrotne** w witrynie Azure Portal, aby zmienić identyfikator nazwy wysyłany do aplikacji Evidence.com na adres e-mail.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Evidence.com w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Evidence.com, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Evidence.com z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić Evidence.com z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)