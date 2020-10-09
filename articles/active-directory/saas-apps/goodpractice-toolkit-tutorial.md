---
title: 'Samouczek: integracja Azure Active Directory z zestawem narzędzi do obsługi myśli | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między zestawem narzędzi Azure Active Directory i myśli.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 10773a2a379291fa861dbb2adfdda2ba3f63117f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88550643"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Samouczek: integracja Azure Active Directory z zestawem narzędzi do obsługi myśli

W tym samouczku dowiesz się, jak zintegrować zestaw narzędzi z narzędziami do tworzenia i Azure Active Directory (Azure AD).

Dzięki tej integracji można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zestawu narzędzi.
* Zezwól użytkownikom na automatyczne logowanie się do zestawu narzędzi (Logowanie jednokrotne) za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z zestawem narzędzi do sprawdzania myśli, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Pakiet narzędzi do obsługi narzędzi do zalogowania z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zestaw narzędzi do tworzenia myśli obsługuje logowanie jednokrotne zainicjowane przez usługę SP.
* Zestaw narzędzi do obsługi myśli obsługuje funkcję aprowizacji użytkowników just in Time.
* Po skonfigurowaniu zestawu narzędzi do sprawdzania myśli można wymusić kontrolę sesji. Ta kontrolka chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Dodaj zestaw narzędzi do przypominania z galerii

Aby skonfigurować integrację zestawu narzędzi do sprawdzania myśli z usługą Azure AD, musisz dodać zestaw narzędzi do wyświetlania z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wprowadź **zestaw narzędzi do sprawdzania myśli** .
1. Wybierz **zestaw narzędzi myśli narzędzia** z wyników wyszukiwania, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD za pomocą zestawu narzędzi do sprawdzania myśli przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w zestawie narzędzi.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zestawu narzędzi do tworzenia i testowania, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj zestaw narzędzi do zdania Logowanie jednokrotne](#configure-mind-tools-toolkit-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego zestawu narzędzi do Rozmyślinia](#create-a-mind-tools-toolkit-test-user)** , aby uzyskać odpowiednika B. Simon w zestawie narzędzi. Ten odpowiednik jest połączony z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji skonfigurujesz Logowanie jednokrotne w usłudze Azure AD za pomocą zestawu narzędzi do przypominania, wykonując następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **zestawu narzędzi do rozmyślinia** wybierz pozycję **Logowanie jednokrotne**.

    ![Sekcja zarządzania z wyróżnionym logowaniem jednokrotnym](common/select-sso.png)

1. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Okno dialogowe Wybieranie metody logowania jednokrotnego z wyróżnioną pozycją SAML](common/select-saml-option.png)

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

    ![Strona Konfigurowanie pojedynczego Sign-On ze stroną SAML z ikoną ołówka dla podstawowej konfiguracji języka SAML została wyróżniona](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** w polu **adres URL logowania** wprowadź adres URL ze wzorcem `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>` .

    > [!NOTE]
    > Wartość **adresu URL logowania** nie jest prawdziwa. Zaktualizuj wartość przy użyciu rzeczywistego adresu URL logowania. Aby uzyskać wartość, skontaktuj się z [zespołem obsługi klienta zestawu narzędzi](mailto:support@goodpractice.com) .

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** przejdź do sekcji **certyfikat podpisywania SAML** . Z prawej strony **XML metadanych Federacji**wybierz pozycję **Pobierz** , aby pobrać tekst XML i zapisać go na komputerze. Zawartość XML zależy od wybranych opcji.

    ![Sekcja certyfikat podpisywania SAML z wyróżnioną pozycją Pobierz obok pliku XML metadanych Federacji](common/metadataxml.png)

1. W sekcji **Konfiguracja zestawu narzędzi do sprawdzania myśli** Skopiuj, w zależności od następujących adresów URL, które są potrzebne.

    * **Adres URL logowania**

    * **Identyfikator usługi Azure AD**

    * **Adres URL wylogowywania**

    ![Sekcja zestawu narzędzi do ustawiania myśli z wyróżnionymi adresami URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal:

1. Na lewej stronie Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. W górnej części ekranu wybierz pozycję **nowy użytkownik**.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wpisz **B. Simon**.  
   1. W polu **Nazwa użytkownika** wprowadź **B.Simon@**_formacie_**.** _rozszerzenie_. Na przykład B.Simon@contoso.com.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz wartość, która jest wyświetlana w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do zestawu narzędzi.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **zestaw narzędzi**.
1. Na stronie Przegląd aplikacji przejdź do sekcji **Zarządzanie** , a następnie wybierz pozycję **Użytkownicy i grupy**.

   ![Sekcja zarządzanie z wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

   ![Okno Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz jakiejkolwiek wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-mind-tools-toolkit-sso"></a>Skonfiguruj zestaw narzędzi do zdania Logowanie jednokrotne

Aby skonfigurować Logowanie jednokrotne na stronie **zestawu narzędzi** do obsługi myśli, Wyślij pobrany tekst **XML metadanych Federacji** i wcześniej skopiowane adresy URL do [zespołu pomocy technicznej zestawu narzędzi](mailto:support@goodpractice.com). Ustawi on to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-a-mind-tools-toolkit-test-user"></a>Utwórz użytkownika testowego zestawu narzędzi do sprawdzania myśli

W tej sekcji utworzysz użytkownika o nazwie B. Simon w narzędziu narzędzia do sprawdzania myśli.

Zestaw narzędzi do zamyślinia obsługuje obsługę just-in-Time, która jest domyślnie włączona. Nie ma żadnych akcji, które należy wykonać w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w zestawie narzędzi do przypominania, zostanie utworzony nowy, gdy spróbujesz uzyskać dostęp do zestawu narzędzi.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po wybraniu kafelka zestawu narzędzi myśli w portalu Moje aplikacje użytkownik zostanie automatycznie zalogowany do zestawu narzędzi do zdania, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [wprowadzenie do portalu My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Tutorials for integrating SaaS apps with Azure Active Directory (Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj zestaw narzędzi do namyślinia przy użyciu usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić zestaw narzędzi do tworzenia myśli z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
