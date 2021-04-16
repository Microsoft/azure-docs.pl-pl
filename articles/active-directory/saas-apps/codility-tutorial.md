---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą Codility | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedynczy między Azure Active Directory i Codility.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: a27d06774b1795f736497057b129945b617a6258
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495132"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-codility"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą Codility

Z tego samouczka dowiesz się, jak zintegrować platformę Codility z Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji Codility z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Codility.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Codility przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Codility z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Codility obsługuje logowanie **jednokrotne inicjowane przez** sp i IDP
* Codility obsługuje **aprowizowanie** użytkowników just in time

## <a name="adding-codility-from-the-gallery"></a>Dodawanie aplikacji Codility z galerii

Aby skonfigurować integrację aplikacji Codility z usługą Azure AD, musisz dodać aplikację Codility z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **Codility** w polu wyszukiwania.
1. Wybierz **pozycję Codility** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-codility"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Codility

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z użyciem aplikacji Codility przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Codility.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z platformą Codility, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji Codility —](#configure-codility-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Codility](#create-codility-test-user)** — aby mieć w aplikacji Codility odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal **aplikacji Codility** znajdź sekcję Zarządzanie  i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym  przez dostawcy tożsamości, w sekcji Podstawowa konfiguracja protokołu **SAML** wprowadź wartości następujących pól:

    a. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.codility.net/social/complete/saml/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.codility.net`


1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez** sp. z o.o.:

    a. W **polu tekstowym Adres URL** logowania wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.codility.net`

    b. W polu **tekstowym Stan** przekaźnika wpisz wartość, korzystając z następującego wzorca: `<UNIQUE_IDENTIFIER>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL odpowiedzi, identyfikatora, adresu URL logowania i stanu przekaźnika. Skontaktuj się z zespołem pomocy technicznej klienta aplikacji [Codility,](mailto:support@codility.com) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą języka SAML w sekcji Certyfikat podpisywania  **SAML** znajdź plik **XML** metadanych **federacji** i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W **sekcji Konfigurowanie aplikacji Codility** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Codility.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Codility**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-codility-sso"></a>Konfigurowanie logowania jednokrotnego aplikacji Codility

Aby skonfigurować logowanie pojedyncze po stronie aplikacji **Codility,** musisz wysłać pobrany plik XML metadanych federacji i odpowiednie adresy **URL** skopiowane z aplikacji Azure Portal do zespołu pomocy technicznej aplikacji [Codility.](mailto:support@codility.com) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-codility-test-user"></a>Tworzenie użytkownika testowego aplikacji Codility

W tej sekcji w aplikacji Codility jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Codility obsługuje aprowizowanie użytkowników just in time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Codility, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania codility, pod którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania aplikacji Codility i zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal (Przetestuj tę aplikację w aplikacji) i powinno nas na przykład automatycznie zalogować się do aplikacji Codility, dla której została ustawiona logowanie jednokrotne 

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Codility w aplikacji Moje aplikacje, jeśli zostanie skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie skonfigurowane w trybie dostawcy tożsamości, powinno na zostać automatycznie zalogowane do aplikacji Codility, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu codility można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


