---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z logowaniem | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedynczy między Azure Active Directory i wychodzącym.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 392697978041cdab50f45e22a73acbadb9122546
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519451"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egress"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z ruchem wychodzącym

Z tego samouczka dowiesz się, jak zintegrować ruch wychodzący z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Egress z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do danych wychodzących.
* Umożliwianie użytkownikom automatycznego zalogowania się do ruchu wychodzącego przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja z obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Ruch wychodzący obsługuje **logowanie jednokrotne inicjowane przez** sp oraz IDP.
* Ruch wychodzący **obsługuje aprowizowanie** użytkowników just in time.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="add-egress-from-the-gallery"></a>Dodawanie danych wychodzących z galerii

Aby skonfigurować integrację danych wychodzących z usługą Azure AD, musisz dodać ruch wychodzący z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **ruch wychodzący** w polu wyszukiwania.
1. Wybierz **pozycję Ruch wychodzący** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-egress"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla danych wychodzących

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z wyjściem przy użyciu użytkownika testowego **o nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach połączenia wychodzącego.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z wyjściem, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego —](#configure-egress-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego dla danych wychodzących](#create-egress-test-user)** — aby mieć w widoku wychodzącym odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **integracji** aplikacji wychodzącej znajdź sekcję **Zarządzanie** i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez** sp. z o.o.:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://switch.egress.com/ui/`

1. Kliknij pozycję **Zapisz**.

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** w sekcji Certyfikat **podpisywania** **SAML** kliknij przycisk kopiowania, aby skopiować adres URL metadanych federacji aplikacji i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do danych wychodzących.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Ruch wychodzący.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-egress-sso"></a>Konfigurowanie logowania jednokrotnego dla danych wychodzących

1. W innym oknie przeglądarki zaloguj się do firmowej witryny wychodzącej jako administrator.

1. Wykonaj poniższe kroki na poniższej stronie.

    ![Konfiguracja wychodzącego](./media/egress-tutorial/configure-1.PNG)

    a. W menu po lewej stronie kliknij pozycję **Konfiguracja logowania jednokrotnego.**

    b. Wybierz **przycisk radiowy Use single sign on** (Użyj logowania pojedynczego), aby użyć funkcji logowania pojedynczego.

    c. Podaj prawidłowy opis w **polu tekstowym Opis** dostawcy.

    d. W polu **tekstowym Metadata URL** (Adres URL metadanych) wklej skopiowaną wartość adresu **URL** metadanych federacji aplikacji.

    e. Kliknij pozycję **Załaduj metadane.**

    f. Kliknij przycisk **Zapisz,** aby zaktualizować konfigurację logowania jednokrotnego.

### <a name="create-egress-test-user"></a>Tworzenie użytkownika testowego dla danych wychodzących

1. Zaloguj się do **firmowej witryny** wychodzącej.

1. Kliknij pozycję **Invite Users (Zaproś** użytkowników) w menu po lewej stronie, a następnie kliknij pozycję Invite Single User (Zaproś pojedynczego użytkownika), aby dodać użytkownika. 

    ![Zrzut ekranu przedstawiający stronę "Zaproś użytkowników" z wybranym przyciskiem "Zaproś pojedynczego użytkownika".](./media/egress-tutorial/create-user-1.PNG)

1. Wypełnij wymagane pola i kliknij pozycję **Zaproś**.

    ![Tworzenie użytkownika testowego dla danych wychodzących](./media/egress-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania ruchu wychodzącego, pod którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania ruchu wychodzącego i zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal (Przetestuj tę aplikację) i powinno nas na przykład automatycznie zalogować się do usługi Egress, dla której została ustawiona logowanie jednokrotne. 

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Ruchu wychodzącego w trybie Moje aplikacje, jeśli zostanie skonfigurowany w trybie SP nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie skonfigurowane w trybie dostawcy tożsamości, powinno na celu automatyczne zalogowanie się do ruchu wychodzącego, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ruchu wychodzącego można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
