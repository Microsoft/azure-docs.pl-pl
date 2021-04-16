---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z aplikacji 4me | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją 4me.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: f2a171460defb3a8befba5a674a2f1aadde636af
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517831"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-4me"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z aplikacji 4me

Z tego samouczka dowiesz się, jak zintegrować program 4me z Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji 4me z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji 4me.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji 4me przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji 4me z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program 4me obsługuje logowanie **jednokrotne inicjowane** przez sp.
* Program 4me obsługuje **aprowizowanie** użytkowników just in time.

## <a name="add-4me-from-the-gallery"></a>Dodawanie aplikacji 4me z galerii

Aby skonfigurować integrację aplikacji 4me z usługą Azure AD, należy dodać tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **4me** w polu wyszukiwania.
1. Wybierz **pozycję 4me** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-4me"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji 4me

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z aplikacji 4me przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji 4me.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą aplikacji 4me, wykonaj czynności z poniższych bloków konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji 4me](#configure-4me-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji 4me](#create-4me-test-user)** — aby mieć w aplikacji 4me odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal aplikacji **4me** znajdź sekcję  Zarządzanie i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja saml** wprowadź wartości następujących pól:

    a. W polu **tekstowym Adres URL** logowania wpisz adres URL, korzystając z jednego z następujących wzorców:

    | Środowisko| Adres URL|
    |---|---|
    | PRODUKCJA | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    b. W polu **tekstowym Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z jednego z następujących wzorców:

    | Środowisko| Adres URL|
    |---|---|
    | PRODUKCJA | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji 4me](mailto:support@4me.com), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja 4me oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych aplikacja 4me oczekuje jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML, co pokazano poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. W **sekcji Konfigurowanie aplikacji 4me** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w aplikacji o nazwie Azure Portal B.Simon.

1. W okienku po lewej stronie w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy,** a następnie wybierz **pozycję Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownik wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji 4me.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **4me**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie **wybierz pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zobaczysz wybraną rolę "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-4me-sso"></a>Konfigurowanie logowania jednokrotnego aplikacji 4me

1. W innym oknie przeglądarki internetowej zaloguj się do aplikacji 4me jako administrator.

1. W lewym górnym rogu kliknij logo **Settings** (Ustawienia), a następnie na pasku po lewej stronie kliknij pozycję **Single Sign-On** (Logowanie jednokrotne).

    ![Ustawienia aplikacji 4me](./media/4me-tutorial/settings.png)

1. Na stronie **Single Sign-On**(Logowanie jednokrotne) wykonaj następujące czynności:

    ![Logowanie jednokrotne aplikacji 4me](./media/4me-tutorial/single-sign-on.png)

    a. Wybierz opcję **Enabled** (Włączone).

    b. W polu tekstowym **Remote logout URL** (Adres URL wylogowywania zdalnego) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    c. W sekcji **SAML** w polu tekstowym **SAML SSO URL** (Adres URL logowania jednokrotnego protokołu SAML) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Certificate fingerprint** (Odcisk palca certyfikatu) wklej wartość **THUMBPRINT** (ODCISK PALCA) oddzieloną dwukropkiem w kolejności znaków podwójnych (AA:BB:CC:DD:EE:FF:GG:HH:II), która została skopiowana z witryny Azure Portal.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-4me-test-user"></a>Tworzenie użytkownika testowego aplikacji 4me

W tej sekcji w aplikacji 4me jest tworzony użytkownik o nazwie Britta Simon. Aplikacja 4me obsługuje aprowizację użytkowników typu just-in-time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji 4me, zostanie utworzony po uwierzytelnieniu.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z zespołem pomocy technicznej [aplikacji 4me.](mailto:support@4me.com)

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji 4me, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania aplikacji 4me i zainicjuj przepływ logowania z tego adresu.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka 4me w Moje aplikacje nastąpi przekierowanie do adresu URL logowania 4me. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji 4me można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
