---
title: 'Samouczek: Azure Active Directory integracji z usługą Powerschool Performance Matters | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedynczej aplikacji Azure Active Directory Powerschool Performance Matters.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 7b75e2cbffaaf05dc0f5ca30497c165b91adf6d1
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515349"
---
# <a name="tutorial-azure-active-directory-integration-with-powerschool-performance-matters"></a>Samouczek: Azure Active Directory integracji z powerschool performance matters

Z tego samouczka dowiesz się, jak zintegrować usługę Powerschool Performance Matters z usługą Azure Active Directory (Azure AD). Integrowanie aplikacji Powerschool Performance Matters z usługą Azure AD umożliwia:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Powerschool Performance Matters.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Powerschool Performance Matters przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Powerschool Performance Matters z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Powerschool Performance Matters obsługuje logowanie **jednokrotne inicjowane** przez sp.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="add-powerschool-performance-matters-from-the-gallery"></a>Dodawanie aplikacji Powerschool Performance Matters z galerii

Aby skonfigurować integrację aplikacji Powerschool Performance Matters z usługą Azure AD, musisz dodać aplikację Powerschool Performance Matters z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **Powerschool Performance Matters** w polu wyszukiwania.
1. Wybierz **pozycję Powerschool Performance Matters z** panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-powerschool-performance-matters"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Powerschool Performance Matters

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD Form.com przy użyciu użytkownika testowego **o nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Form.com.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD Form.com, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w](#configure-powerschool-performance-matters-sso)** aplikacji Powerschool Performance Matters — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego](#create-powerschool-performance-matters-test-user)** aplikacji Powerschool Performance Matters — aby mieć w aplikacji Powerschool Performance Matters odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal **Aplikacji Powerschool Performance Matters** znajdź sekcję  Zarządzanie i wybierz pozycję **Logowanie pojedynczej.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujący krok:

    W **polu tekstowym Adres URL** logowania wpisz adres URL, korzystając z jednego z następujących wzorców:
    
    ```https
        https://ola.performancematters.com/ola/?clientcode=<Client Code>
        https://unify.performancematters.com/?idp=<IDP>
    ```

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z zespołem pomocy technicznej klienta [Powerschool Performance Matters,](mailto:pmsupport@powerschoo.com) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W **sekcji Konfigurowanie aplikacji Powerschool Performance Matters** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w aplikacji Azure Portal B.Simon.

1. W okienku po lewej stronie Azure Portal pozycję Azure Active Directory **pozycję** **Użytkownicy,** a następnie pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownik wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Powerschool Performance Matters.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Powerschool Performance Matters**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zobaczysz wybraną rolę "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-powerschool-performance-matters-sso"></a>Konfigurowanie aplikacji Powerschool Performance Matters SSO

Aby skonfigurować logowanie pojedynczy po stronie aplikacji **Powerschool Performance Matters,** musisz wysłać pobrany plik XML metadanych federacji i odpowiednie adresy **URL** skopiowane z aplikacji Azure Portal do zespołu pomocy technicznej aplikacji [Powerschool Performance Matters.](mailto:pmsupport@powerschoo.com) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-powerschool-performance-matters-test-user"></a>Tworzenie użytkownika testowego aplikacji Powerschool Performance Matters

W tej sekcji utworzysz użytkownika Britta Simon w aplikacji Powerschool Performance Matters. We współpracy z zespołem pomocy technicznej aplikacji [Powerschool Performance Matters](mailto:pmsupport@powerschoo.com) dodaj użytkowników na platformie Powerschool Performance Matters. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Powerschool Performance Matters, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Powerschool Performance Matters w Moje aplikacje nastąpi przekierowanie do adresu URL logowania Powerschool Performance Matters. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji Powerschool Performance Matters można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).