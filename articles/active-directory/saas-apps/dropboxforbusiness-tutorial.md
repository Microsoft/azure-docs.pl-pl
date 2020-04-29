---
title: 'Samouczek: integracja Azure Active Directory z usługą Dropbox dla firm | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Dropbox dla Firm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df7fc366c5087e66c3022c212870397d77e6e34d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77046760"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Samouczek: Integrowanie usługi Dropbox dla firm z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Dropbox dla firm z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Dropbox dla firm z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi Dropbox dla firm.
* Zezwól użytkownikom na automatyczne logowanie do usługi Dropbox dla firm przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja obsługująca Logowanie jednokrotne w usłudze Dropbox dla firm.

## <a name="scenario-description"></a>Opis scenariusza

* W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Aplikacja Dropbox dla Firm obsługuje logowanie jednokrotne inicjowane przez **SP**

* Usługa Dropbox dla firm obsługuje [Automatyczne Inicjowanie obsługi i cofanie aprowizacji użytkowników](dropboxforbusiness-tutorial.md)
* Po skonfigurowaniu usługi Dropbox można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dodawanie aplikacji Dropbox dla Firm z galerii

Aby skonfigurować integrację aplikacji Dropbox dla Firm z usługą Azure AD, musisz dodać aplikację Dropbox dla Firm z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Dropbox dla firm** w polu wyszukiwania.
1. Wybierz pozycję **Dropbox dla firm** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Dropbox dla firm przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Dropbox dla firm.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Dropbox dla firm, należy wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.    
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj usługę Dropbox dla logowania jednokrotnego](#configure-dropbox-for-business-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Dropbox dla Firm](#create-dropbox-for-business-test-user)** — aby mieć w aplikacji Dropbox dla Firm odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji usługi **Dropbox dla firm** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://www.dropbox.com/sso/<id>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz wartość: `Dropbox`

    > [!NOTE]
    > Podana wyżej wartość adresu URL logowania nie jest prawdziwa. Ta wartość zostanie zaktualizowana przy użyciu rzeczywistego adresu URL logowania, co objaśniono w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj aplikację Dropbox dla Firm** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji zostanie włączone Logowanie jednokrotne w usłudze Britta Simon, przyznając dostęp do usługi Dropbox dla firm.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Dropbox dla firm**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-dropbox-for-business-sso"></a>Konfigurowanie usługi Dropbox dla firmowego logowania jednokrotnego

1. Aby zautomatyzować konfigurację w usłudze Dropbox dla firm, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Konfiguracja usługi Dropbox dla firm** spowoduje przekierowanie do aplikacji usługi Dropbox dla firm. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Dropbox dla firm. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-8.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Aby ręcznie skonfigurować usługi Dropbox dla firm, Otwórz nowe okno przeglądarki sieci Web i przejdź do dzierżawy usługi Dropbox dla firm i zaloguj się do dzierżawy usługi Dropbox dla firm. i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/ic769509.png "Konfigurowanie logowania jednokrotnego")

4. Kliknij **ikonę użytkownika** i wybierz kartę **Settings** (Ustawienia).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure1.png "Konfigurowanie logowania jednokrotnego")

5. W okienku nawigacji po lewej stronie kliknij pozycję **Admin console** (Konsola administracyjna).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure2.png "Konfigurowanie logowania jednokrotnego")

6. W obszarze **Admin console** (Konsola administracyjna) kliknij pozycję **Settings** (Ustawienia) w okienku nawigacji po lewej stronie.

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure3.png "Konfigurowanie logowania jednokrotnego")

7. Wybierz opcję **Single sign-on** (Logowanie jednokrotne) w sekcji **Authentication** (Uwierzytelnianie).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure4.png "Konfigurowanie logowania jednokrotnego")

8. W sekcji **Logowanie** jednokrotne wykonaj następujące czynności:  

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure5.png "Konfigurowanie logowania jednokrotnego")

    a. Wybierz pozycję **wymagane** jako opcję z listy rozwijanej dla **logowania jednokrotnego**.

    b. Kliknij pozycję **Add sign-in URL** (Dodaj adres URL logowania), a następnie w polu tekstowym **Identity provider sign-in URL** (Adres URL logowania dostawcy tożsamości) wklej wartość **adresu URL logowania**, która została skopiowana z witryny Azure Portal, i wybierz pozycję ** Done** (Gotowe).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurowanie logowania jednokrotnego")

    c. Kliknij pozycję **Upload certificate** (Przekaż certyfikat), a następnie przejdź do **pliku certyfikatu zakodowanego w formacie Base64**, który został pobrany z witryny Azure Portal.

    d. Kliknij pozycję **Copy link** (Kopiuj link) i wklej skopiowaną wartość w polu tekstowym **Adres URL logowania** w sekcji **Domena i adresy URL aplikacji Dropbox dla Firm** w witrynie Azure Portal.

    e. Kliknij przycisk **Zapisz**.

### <a name="create-dropbox-for-business-test-user"></a>Tworzenie użytkownika testowego aplikacji Dropbox dla Firm

W tej sekcji w aplikacji Dropbox dla Firm jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Dropbox dla Firm obsługuje aprowizację użytkowników Just In Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Dropbox dla Firm, zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Dropbox dla Firm](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka usługi Dropbox dla firm w panelu dostępu należy automatycznie zalogować się do usługi Dropbox dla firm, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Ochrona usługi Dropbox przy użyciu zaawansowanej widoczności i kontrolek](https://docs.microsoft.com/cloud-app-security/protect-dropbox)