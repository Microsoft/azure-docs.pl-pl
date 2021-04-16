---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z aplikacją Wrru App | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między aplikacją Azure Active Directory i aplikacją W międzylądową.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 8b4cb988329f599e098b0e392938ae050b55577d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520913"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wru-app"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z aplikacją Wrru

Z tego samouczka dowiesz się, jak zintegrować aplikację Wrru z aplikacją Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji Wrru z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Wrru.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Wrru przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji ZUS z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Wrru obsługuje logowanie **jednokrotne inicjowane** przez sp.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="adding-wru-app-from-the-gallery"></a>Dodawanie aplikacji Wrru z galerii

Aby skonfigurować integrację aplikacji Wrru z usługą Azure AD, musisz dodać aplikację Wrru z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **w polu** wyszukiwania pozycję Aplikacja ze sklepu.
1. Wybierz **pozycję Aplikacja Wdam z** panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-wru-app"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Wrru

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z aplikacją Wrru przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w aplikacji Wrru.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Wrru, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego aplikacji Wuru](#configure-wuru-app-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Wuru](#create-wuru-app-test-user)** — aby mieć w aplikacji Wrru odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji **Wrru**  znajdź sekcję Zarządzanie i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja saml** wprowadź wartości następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://app.wuru.site/api/auth/azure`

    b. W polu **tekstowym Identyfikator (identyfikator jednostki)** wpisz wartość: `urn:amazon:cognito:sp:us-east-2_142Y3PTBg`

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą języka SAML w sekcji Certyfikat podpisywania  **SAML** znajdź plik **XML** metadanych **federacji** i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W **sekcji Konfigurowanie aplikacji W zestawie** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Wrru.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Aplikacja systemu Windows.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-wuru-app-sso"></a>Konfigurowanie logowania jednokrotnego aplikacji Wuru

Aby skonfigurować logowanie pojedyncze po stronie aplikacji aplikacji **Wåru,** musisz wysłać pobrany plik XML metadanych federacji i odpowiednie adresy **URL** skopiowane z aplikacji Azure Portal do zespołu pomocy technicznej aplikacji [Wrru.](mailto:contacto@wuru.site) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-wuru-app-test-user"></a>Tworzenie użytkownika testowego aplikacji Wuru

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji Wrru. We współpracy z zespołem pomocy technicznej aplikacji W [jru](mailto:contacto@wuru.site) dodaj użytkowników na platformie aplikacji Wrru. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji Wrru, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania aplikacji Wru i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Aplikacja w Moje aplikacje spowoduje to przekierowywanie do adresu URL logowania do aplikacji Wrru. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji Wrru można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


