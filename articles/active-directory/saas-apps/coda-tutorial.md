---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z użyciem formatu CODA | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Coda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.openlocfilehash: 718afec8b36a27bfd36e2a018b39f480144bf822
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545356"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory z użyciem formatu CODA

W tym samouczku dowiesz się, jak zintegrować format CODA z Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD formacie Coda można:

* Kontrolka w usłudze Azure AD, która ma dostęp do formatu CODA.
* Zezwól użytkownikom na automatyczne logowanie do formatu CODA przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w formacie Coda (Enterprise) z wyłączoną integracją GDrive. Skontaktuj się z [zespołem pomocy technicznej Coda](mailto:support@coda.io) , aby wyłączyć integrację usługi GDrive dla Twojej organizacji, jeśli jest ona obecnie włączona.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Format Coda obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

* Format Coda obsługuje funkcję aprowizacji użytkowników **just in Time**

* Po skonfigurowaniu formatu CODA można wymusić kontrolę sesji chroniącą eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Dodawanie formatu CODA z galerii

Aby skonfigurować integrację formatu CODA z usługą Azure AD, musisz dodać CODA z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Format Coda** w polu wyszukiwania.
1. Wybierz pozycję **Coda** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla formatu CODA

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą formatu CODA przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w formacie Coda.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu formatu CODA, wykonaj następujące bloki konstrukcyjne:

1. **[Rozpocznij Konfigurowanie logowania jednokrotnego](#begin-configuration-of-coda-sso)** w formacie Coda — aby rozpocząć konfigurację logowania jednokrotnego w postaci Coda.
1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
   * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
   * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-coda-sso)** w formacie Coda — aby ukończyć konfigurację logowania jednokrotnego w formacie Coda.
   * **[Utwórz użytkownika testowego Coda](#create-coda-test-user)** — Aby uzyskać odpowiednik elementu B. Simon w formacie Coda, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="begin-configuration-of-coda-sso"></a>Rozpocznij konfigurację rejestracji jednokrotnej w formacie Coda

Aby rozpocząć, wykonaj następujące kroki w formacie Coda.

1. W formacie Coda Otwórz panel **Ustawienia organizacji** .

   ![Otwórz ustawienia organizacji](media/coda-tutorial/org-settings.png)

1. Upewnij się, że Twoja organizacja ma wyłączoną integrację z GDrive. Jeśli jest ona obecnie włączona, skontaktuj się z [zespołem pomocy technicznej Coda](mailto:support@coda.io) , aby ułatwić migrację z GDrive.

   ![GDrive wyłączone](media/coda-tutorial/gdrive-off.png)

1. W obszarze **uwierzytelnianie przy użyciu logowania jednokrotnego (SAML)** wybierz opcję **Konfiguruj SAML** .

   ![Ustawienia SAML](media/coda-tutorial/saml-settings-link.png)

1. Zwróć uwagę na wartości **identyfikatora jednostki** i **adresu URL odpowiedzi SAML**, które będą potrzebne w kolejnych krokach.

   ![Identyfikator jednostki i adres URL odpowiedzi SAML do użycia na platformie Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji w formacie **Coda** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

   a. W polu tekstowym **Identyfikator** wprowadź wartość "identyfikator jednostki" z powyższych. Powinien postępować zgodnie z wzorcem: `https://coda.io/samlId/<CUSTOMID>`

   b. W polu tekstowym **adres URL odpowiedzi** wprowadź wartość "adres URL odpowiedzi SAML" z powyższych. Powinien postępować zgodnie z wzorcem: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Twoje wartości różnią się od powyższych; wartości można znaleźć w obszarze "Konfigurowanie SAML" w formacie Coda. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie formatu CODA** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do formatu CODA.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Coda**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

   ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-coda-sso"></a>Konfigurowanie logowania jednokrotnego w formacie Coda

Aby ukończyć instalację, wprowadź wartości z Azure Active Directory w panelu protokołu **SAML** w formacie Coda.

1. W formacie Coda Otwórz panel **Ustawienia organizacji** .
1. W obszarze **uwierzytelnianie przy użyciu logowania jednokrotnego (SAML)** wybierz opcję **Konfiguruj SAML** .
1. Ustaw **dostawcę SAML** na **Azure Active Directory**.
1. W polu **adres URL logowania dostawcy tożsamości**wklej **adres URL logowania** z konsoli platformy Azure.
1. W obszarze **wystawcy dostawcy tożsamości**wklej **Identyfikator usługi Azure AD** z konsoli platformy Azure.
1. W obszarze **certyfikat publiczny dostawcy tożsamości**wybierz opcję **Przekaż certyfikat** i wybierz pobrany wcześniej plik certyfikatu.
1. Wybierz pozycję **Zapisz**.

Spowoduje to zakończenie pracy niezbędnej do ustawienia połączenia SSO protokołu SAML.

### <a name="create-coda-test-user"></a>Utwórz użytkownika testowego Coda

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w formacie Coda. Format Coda obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w formacie Coda, po uwierzytelnieniu zostanie utworzony nowy.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Coda w panelu dostępu należy automatycznie zalogować się do formatu CODA, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj format CODA z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Coda przy użyciu zaawansowanej widoczności i kontrolek](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
