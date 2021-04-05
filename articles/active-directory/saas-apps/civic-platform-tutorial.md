---
title: 'Samouczek: integracja Azure Active Directory z platformą projektowi Civic Innovation | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i platformą projektowi Civic Innovation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.openlocfilehash: 26109d4b8875dd0b442521513dbd219dc0de06e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455973"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Samouczek: integracja platformy projektowi Civic Innovation z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować platformę projektowi Civic Innovation z usługą Azure Active Directory (Azure AD). Gdy integrujesz platformę projektowi Civic Innovation z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do platformy projektowi Civic Innovation.
* Zezwól użytkownikom na automatyczne logowanie do platformy projektowi Civic Innovation przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) platformy projektowi Civic Innovation.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma projektowi Civic Innovation obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**





## <a name="adding-civic-platform-from-the-gallery"></a>Dodawanie platformy projektowi Civic Innovation z galerii

Aby skonfigurować integrację platformy projektowi Civic Innovation z usługą Azure AD, musisz dodać platformę projektowi Civic Innovation z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **projektowi Civic Innovation platformę** w polu wyszukiwania.
1. Wybierz pozycję **platforma projektowi Civic Innovation** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą platformy projektowi Civic Innovation przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na platformie projektowi Civic Innovation.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą platformy projektowi Civic Innovation, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-civic-platform-sso)** w usłudze projektowi Civic Innovation, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika testowego platformy projektowi Civic Innovation](#create-civic-platform-test-user)** , aby dysponować odpowiednikiem B. Simon na platformie projektowi Civic Innovation, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **platformy projektowi Civic Innovation** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.accela.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `civicplatform.accela.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta platformy projektowi Civic Innovation](mailto:skale@accela.com) , aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Zrzut ekranu przedstawia stronę certyfikatu podpisywania SAML, w której można skopiować metadane federacji aplikacji, które są l.](common/copy-metadataurl.png)

1. Przejdź do **Azure Active Directory**  >  **rejestracje aplikacji** w usłudze Azure AD, wybierz aplikację.

1. Skopiuj **Identyfikator katalogu (dzierżawy)** i Zapisz go w Notatniku.

    ![Skopiuj katalog (identyfikator dzierżawy) i Zapisz go w kodzie aplikacji](media/civic-platform-tutorial/directoryid.png)

1. Skopiuj **Identyfikator aplikacji** i Zapisz go w Notatniku.

   ![Kopiowanie identyfikatora aplikacji (klienta)](media/civic-platform-tutorial/applicationid.png)

1. Przejdź do **Azure Active Directory**  >  **rejestracje aplikacji** w usłudze Azure AD, wybierz aplikację. Wybierz pozycję **certyfikaty & wpisy tajne**.

1. Wybierz pozycję wpisy **tajne klienta — > nowego klucza tajnego klienta**.

1. Podaj opis klucza tajnego i czas trwania. Po zakończeniu wybierz pozycję **Dodaj**.

   > [!NOTE]
   > Po zapisaniu klucza tajnego klienta zostanie wyświetlona wartość wpisu tajnego klienta. Skopiuj tę wartość, ponieważ nie można pobrać klucza później.

   ![Skopiuj wartość klucza tajnego, ponieważ nie można pobrać jej później](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Konfigurowanie logowania jednokrotnego dla platformy projektowi Civic Innovation

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej witryny w chmurze Atlassian jako administrator.

1. Kliknij pozycję **standardowe opcje**.

    ![Zrzut ekranu przedstawia witrynę Atlassian w chmurze z opcjami standardowymi określonymi w obszarze Narzędzia administratora.](media/civic-platform-tutorial/standard-choices.png)

1. Utwórz standardową **ssoconfig** wyboru.

1. Wyszukaj **ssoconfig**  i Prześlij.

    ![Zrzut ekranu przedstawia standardowe opcje wyszukiwania z podaną konfiguracją nazwa s-s.](media/civic-platform-tutorial/sso-config.png)

1. Rozwiń SSOCONFIG, klikając czerwoną kropkę.

    ![Zrzut ekranu przedstawia opcje standardowe Przeglądaj z usługą S S O dostępnej konfiguracji.](media/civic-platform-tutorial/sso-config01.png)

1. Podaj informacje o konfiguracji dotyczące logowania jednokrotnego w następującym kroku:

    ![Zrzut ekranu przedstawia standardowe elementy opcji Edycja dla konfiguracji S S O.](media/civic-platform-tutorial/sso-config02.png)

    1. W polu  **Identyfikator aplikacji** wprowadź wartość identyfikatora, która została skopiowana z Azure Portal.

    1. W polu **clientSecret** wprowadź wartość **klucza tajnego** , która została skopiowana z Azure Portal.

    1. W polu **directoryId** wprowadź wartość **identyfikatora katalogu (dzierżawy)** , która została skopiowana z Azure Portal.

    1. Wprowadź idpName. Np.: `Azure`

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do platformy projektowi Civic Innovation.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **platforma projektowi Civic Innovation**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-civic-platform-test-user"></a>Utwórz użytkownika testowego platformy projektowi Civic Innovation

W tej sekcji utworzysz użytkownika o nazwie B. Simon na platformie projektowi Civic Innovation. Współpracuj z zespołem pomocy technicznej platformy projektowi Civic Innovation, aby dodać użytkowników w [zespole obsługi klienta platformy projektowi Civic Innovation](mailto:skale@accela.com). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka platforma projektowi Civic Innovation w panelu dostępu należy automatycznie zalogować się do platformy projektowi Civic Innovation, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)