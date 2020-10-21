---
title: 'Samouczek: integracja Azure Active Directory z programem Adobe Experience Manager | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i programem Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: d82fa5b44d4797d4254a91b4caa72b7c6b5e02ab
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309245"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Samouczek: integracja Azure Active Directory z programem Adobe Experience Manager

Z tego samouczka dowiesz się, jak zintegrować program Adobe Experience Manager z usługą Azure Active Directory (Azure AD).
Integrowanie programu Adobe Experience Manager z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do programu Adobe Experience Manager.
* Twoi użytkownicy mogą być automatycznie logowani do programu Adobe Experience Manager (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z programem Adobe Experience Manager potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja programu Adobe Experience Manager z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Adobe Experience Manager obsługuje jednokrotne logowanie inicjowane przez **dostawcę usług i dostawcę tożsamości**

* Adobe Experience Manager obsługuje aprowizację użytkowników typu **Just In Time**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Dodawanie programu Adobe Experience Manager z galerii

Aby skonfigurować integrację programu Adobe Experience Manager z usługą Azure AD, musisz dodać program Adobe Experience Manager z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać program Adobe Experience Manager z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Adobe Experience Manager**, z panelu wyników wybierz pozycję **Adobe Experience Manager**, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Program Adobe Experience Manager na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją [Application name] w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji [Application name].

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją [Application name], należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w programie Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego programu Adobe Experience Manager](#create-adobe-experience-manager-test-user)** — aby udostępnić w programie Adobe Experience Manager odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją [Application name], wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Adobe Experience Manager** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu pokazujący podstawową sekcję konfiguracyjną języka SAML oraz wyróżnianie pól tekstowych identyfikator i adres URL odpowiedzi.](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz unikatową wartość, która została również zdefiniowana na Twoim serwerze AEM.

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj wartość adresu URL odpowiedzi przy użyciu rzeczywistego adresu URL odpowiedzi. Aby uzyskać tę wartość, skontaktuj się z [zespołem pomocy technicznej dla klienta programu Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Informacje o domenie i adresach URL logowania jednokrotnego programu Adobe Experience Manager](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL swojego serwera programu Adobe Experience Manager.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfiguracja programu Adobe Experience Manager** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w programie Adobe Experience Manager

1. W innym oknie przeglądarki otwórz portal administracyjny programu **Adobe Experience Manager**.

2. Wybierz pozycję **Ustawienia**  >  **Security**  >  **Użytkownicy**zabezpieczeń.

    ![Zrzut ekranu przedstawiający kafelek użytkownicy w programie Adobe Experience Manager.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Wybierz **administratora** lub dowolnego innego odpowiedniego użytkownika.

    ![Zrzut ekranu, który wyróżnia użytkownika Adminisrator.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Wybierz pozycję **Ustawienia konta**  >  **Zarządzaj TrustStore**.

    ![Zrzut ekranu przedstawiający zarządzanie TrustStore w obszarze Ustawienia konta.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. W obszarze **Dodaj certyfikat z pliku CER** kliknij pozycję **Wybierz plik certyfikatu**. Wyszukaj i wybierz plik certyfikatu, który został już pobrany z witryny Azure Portal.

    ![Zrzut ekranu, który podświetla przycisk Wybierz plik certyfikatu.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Certyfikat jest dodawany do magazynu TrustStore. Zapisz alias certyfikatu.

    ![Zrzut ekranu pokazujący, że certyfikat został dodany do TrustStore.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Na stronie **Użytkownicy** wybierz pozycję **authentication-service**.

    ![Sreenshot, który wyróżnia usługi uwierzytelniania na ekranie.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Wybierz pozycję **Ustawienia konta**  >  **Utwórz magazyn kluczy i Zarządzaj nim**. Utwórz magazyn kluczy, podając hasło.

    ![Zrzut ekranu przedstawiający Zarządzanie magazynem kluczy.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Wróć do ekranu administratora. Następnie wybierz kolejno pozycje **Ustawienia**  >  **Operations**  >  **Konsola sieci Web**.

    ![Zrzut ekranu, który wyróżnia konsolę sieci Web w obszarze operacje w sekcji Ustawienia.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Spowoduje to otwarcie strony konfiguracji.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Znajdź pozycję **Program obsługi uwierzytelniania Adobe Granite SAML 2.0**. Następnie wybierz ikonę **Dodaj**.

    ![Zrzut ekranu przedstawiający procedurę obsługi uwierzytelniania w programie Adobe Granite SAML 2,0.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Na tej stronie wykonaj następujące czynności.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. W polu **Ścieżka** wprowadź **/**.

    b. W polu **Adres URL dostawcy tożsamości** wprowadź wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. W polu **Alias certyfikatu dostawcy tożsamości** wprowadź wartość **aliasu certyfikatu**, która została dodana w magazynie TrustStore.

    d. W polu **Identyfikator jednostki podanej w zabezpieczeniach** wprowadź wartość unikatowego **identyfikatora usługi Azure Ad**, która została skonfigurowana w witrynie Azure Portal.

    e. W polu **Adres URL usługi Assertion Consumer Service** wprowadź wartość **adresu URL odpowiedzi**, która została skonfigurowana w witrynie Azure Portal.

    f. W polu **Hasło magazynu kluczy** wprowadź **hasło** ustawione w magazynie kluczy.

    przykład W polu **Identyfikator atrybutu użytkownika** wprowadź **identyfikator nazwy** lub inny identyfikator użytkownika, który jest odpowiedni w Twoim przypadku.

    h. Wybierz pozycję **Automatycznie utwórz użytkowników CRX**.

    i. W polu **Adres URL wylogowywania** wprowadź wartość unikatowego **adresu URL wylogowywania**, która została uzyskana z witryny Azure Portal.

    j. Wybierz pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon \@ yourcompanydomain. Extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do programu Adobe Experience Manager.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Adobe Experience Manager**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Adobe Experience Manager**.

    ![Link programu Adobe Experience Manager na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-adobe-experience-manager-test-user"></a>Tworzenie użytkownika testowego programu Adobe Experience Manager

W tej sekcji utworzysz w programie Adobe Experience Manager użytkownika o nazwie Britta Simon. W przypadku wybrania opcji **Automatycznie utwórz użytkowników CRX** użytkownicy są tworzeni automatycznie po pomyślnym uwierzytelnieniu.

Aby ręcznie utworzyć użytkowników, należy skontaktować się z [zespołem pomocy technicznej programu Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html)w   celu dodania użytkowników na platformie programu Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Adobe Experience Manager w panelu dostępu powinno nastąpić automatyczne zalogowanie do programu Adobe Experience Manager, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)