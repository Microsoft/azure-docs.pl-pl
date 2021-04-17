---
title: 'Samouczek: Azure Active Directory integracji z programem M-Files | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją M-Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 680380722746522a0eb3fe6518452472be952075
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482845"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Samouczek: Azure Active Directory integracji z programem M-Files

Z tego samouczka dowiesz się, jak zintegrować narzędzie M-Files z usługą Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji M-Files z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji M-Files.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji M-Files przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji M-Files z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* M-Files obsługuje logowanie **jednokrotne inicjowane** przez sp.

## <a name="add-m-files-from-the-gallery"></a>Dodawanie aplikacji M-Files z galerii

Aby skonfigurować integrację aplikacji M-Files w usłudze Azure AD, należy dodać aplikację M-Files z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **M-Files** w polu wyszukiwania.
1. Wybierz **pozycję M-Files** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji M-Files

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z użyciem aplikacji M-Files, korzystając z użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji M-Files.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą aplikacji M-Files, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji M-Files](#configure-m-files-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji M-Files](#create-m-files-test-user)** — aby mieć w aplikacji M-Files odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na **stronie integracji aplikacji M-Files** znajdź sekcję **Zarządzanie** i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji M-Files](mailto:support@m-files.com). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie aplikacji M-Files** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w aplikacji o nazwie Azure Portal B.Simon.

1. W okienku po lewej stronie Azure Portal pozycję Azure Active Directory **pozycję** **Użytkownicy,** a następnie pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownik wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji M-Files.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **M-Files**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-m-files-sso"></a>Konfigurowanie logowania jednokrotnego w aplikacji M-Files

1. Aby zapewnić skonfigurowanie logowania jednokrotnego dla swojej aplikacji, skontaktuj się z [zespołem pomocy technicznej aplikacji M-Files](mailto:support@m-files.com) i przekaż mu pobrane metadane.
   
    >[!NOTE]
    >Wykonaj poniższe czynności, aby zapewnić skonfigurowanie logowania jednokrotnego w swojej aplikacji klasycznej M-File. Jeśli chcesz skonfigurować logowanie jednokrotne tylko dla wersji internetowej aplikacji M-Files, nie są wymagane żadne dodatkowe czynności.  

1. Wykonaj poniższe czynności, aby skonfigurować w aplikacji klasycznej M-Files logowanie jednokrotne za pomocą usługi Azure AD. Aby pobrać aplikację M-Files, przejdź do strony [pobierania aplikacji M-Files](https://www.m-files.com/customers/product-downloads/download-update-links/).

1. Otwórz okno **M-Files Desktop Settings** (Ustawienia pulpitu M-Files). Następnie kliknij przycisk **Add** (Dodaj).
   
    ![Zrzut ekranu przedstawiający pozycję Ustawienia pulpitu aplikacji M-Files, w której można wybrać pozycję Dodaj.](./media/m-files-tutorial/settings.png)

1. W oknie **Document Vault Connection Properties** (Właściwości połączenia magazynu dokumentów) wykonaj następujące kroki:
   
    ![Zrzut ekranu przedstawia właściwości połączenia usługi Document Vault, w których można wprowadzić opisane wartości.](./media/m-files-tutorial/general.png)  

    W obszarze typu sekcji serwera użyj następujących wartości:  

    a. W polu **Name** (Nazwa) wpisz `<tenant-name>.cloudvault.m-files.com`. 
 
    b. W polu **Port Number** (Numer portu) wpisz **4466**. 

    c. W polu **Protocol** (Protokół) wybierz pozycję **HTTPS**. 

    d. W polu **Authentication** (Uwierzytelnianie) wybierz pozycję **Specific Windows user** (Konkretny użytkownik systemu Windows). Zostanie wyświetlona strona logowania. Wstaw swoje poświadczenia usługi Azure AD. 

    e. W obszarze **Vault on Server** (Magazyn na serwerze) wybierz odpowiedni magazyn na serwerze.
 
    f. Kliknij przycisk **OK**.

### <a name="create-m-files-test-user"></a>Tworzenie użytkownika testowego aplikacji M-Files

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji M-Files. Współpracuj z [zespołem pomocy technicznej aplikacji M-Files](mailto:support@m-files.com), aby dodać użytkowników w aplikacji M-Files.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji M-Files, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania aplikacji M-Files i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Kliknięcie kafelka M-Files w Moje aplikacje spowoduje przekierowanie do adresu URL logowania do aplikacji M-Files. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji M-Files można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
