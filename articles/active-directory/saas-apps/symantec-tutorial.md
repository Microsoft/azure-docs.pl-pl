---
title: 'Samouczek: Azure Active Directory integracji z usługą Symantec Web Security Service (WSS) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Symantec Web Security Service (WSS).
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
ms.openlocfilehash: af7d126bfdc9ff8edf6b498747fab9c7f497a0f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484850"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Samouczek: Azure Active Directory integracji z usługą Symantec Web Security Service (WSS)

Z tego samouczka dowiesz się, jak zintegrować konto usługi Symantec Web Security Service (WSS) z kontem usługi Azure Active Directory (Azure AD), tak aby usługa WSS mogła uwierzytelnić użytkownika końcowego aprowizowanego w usłudze Azure AD przy użyciu uwierzytelniania SAML i wymuszać reguły zasad na poziomie użytkownika lub grupy.

Integracja usługi Symantec Web Security Service (WSS) z usługą Azure AD zapewnia następujące korzyści:

- Zarządzanie wszystkimi użytkownikami końcowymi i grupami używanymi przez konto usługi WSS z poziomu portalu usługi Azure AD.

- Zezwolenie użytkownikom końcowym na uwierzytelnianie w usłudze WSS przy użyciu ich poświadczeń usługi Azure AD.

- Włączenie wymuszania reguł zasad na poziomie użytkownika i grupy na koncie usługi WSS.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi Symantec Web Security Service (WSS) z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Symantec Web Security Service (WSS) obsługuje logowanie **jednokrotne inicjowane przez dostawcy** tożsamości.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Dodawanie usługi Symantec Web Security Service (WSS) z galerii

Aby skonfigurować integrację usługi Symantec Web Security Service (WSS) z usługą Azure AD, musisz dodać usługę Symantec Web Security Service (WSS) z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **Symantec Web Security Service (WSS)** w polu wyszukiwania.
1. Wybierz **pozycję Symantec Web Security Service (WSS) z** panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi Symantec Web Security Service (WSS)

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą Symantec Web Security Service (WSS) przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi Symantec Web Security Service (WSS).

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą Symantec Web Security Service (WSS), wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. Konfigurowanie logowania jednokrotnego w usłudze **[Symantec Web Security Service (WSS)](#configure-symantec-web-security-service-wss-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. Tworzenie użytkownika testowego usługi **[Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)** — aby mieć w usłudze Symantec Web Security Service (WSS) odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W witrynie Azure Portal stronie integracji aplikacji **Symantec Web Security Service (WSS)** znajdź sekcję Zarządzanie i wybierz pozycję Logowanie **pojedyncze.** 
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu **tekstowym** Identyfikator wpisz adres URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Skontaktuj się z [zespołem pomocy technicznej klienta usługi Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us), jeśli wartości **identyfikatora** i **adresu URL odpowiedzi** z jakiegoś powodu nie działają. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w aplikacji Azure Portal B.Simon.

1. W okienku po lewej stronie w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy,** a następnie wybierz pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownika wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do usługi Symantec Web Security Service (WSS).

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Symantec Web Security Service (WSS).**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zobaczysz wybraną rolę "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-symantec-web-security-service-wss-sso"></a>Konfigurowanie logowania jednokrotnego usługi Symantec Web Security Service (WSS)

Aby skonfigurować logowanie jednokrotne po stronie usługi Symantec Web Security Service (WSS), zapoznaj się z dokumentacją online usługi WSS. Pobrany **plik XML metadanych federacji** musi zostać zaimportowany do portalu usługi WSS. Jeśli potrzebujesz pomocy z konfiguracją w portalu usługi WSS, skontaktuj się z [zespołem pomocy technicznej usługi Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us).

### <a name="create-symantec-web-security-service-wss-test-user"></a>Tworzenie użytkownika testowego usługi Symantec Web Security Service (WSS)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze Symantec Web Security Service (WSS). Odpowiednią nazwę użytkownika końcowego można utworzyć ręcznie w portalu usługi WSS lub poczekać, aż użytkownicy/grupy aprowizowani w usłudze Azure AD zostaną zsynchronizowani z portalem usługi WSS (około 15 minut). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. Publiczny adres IP komputera użytkownika końcowego, który będzie używany do przeglądania witryn internetowych, musi być także aprowizowany w portalu usługi Symantec Web Security Service (WSS).

> [!NOTE]
> Kliknij [tutaj](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1), aby uzyskać publiczny adres IP swojego komputera.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Test this application in Azure Portal and you should be automatically signed in to the Symantec Web Security Service (WSS) (Przetestuj tę aplikację w usłudze Azure Portal i powinno na celu automatyczne zalogowanie się do usługi Symantec Web Security Service (WSS), dla której została skonfigurowania logowania jednokrotnego.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Symantec Web Security Service (WSS) na platformie Moje aplikacje powinno na celu automatyczne zalogowanie się do usługi Symantec Web Security Service (WSS), dla której została skonfigurowania logowania jednokrotnego. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi Symantec Web Security Service (WSS) można wymusić kontrolę sesji, która chroni przed eksfiltracją i przefiltracją poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
