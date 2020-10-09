---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą iProva | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją iProva.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 187ec5c42c733837689de95c8111b557519f8b7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552844"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą iProva

W tym samouczku dowiesz się, jak zintegrować usługę iProva z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi iProva z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do iProva.
* Zezwól użytkownikom na automatyczne logowanie się do usługi iProva przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* subskrypcja z włączonym logowaniem jednokrotnym (SSO) iProva.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja iProva obsługuje logowanie jednokrotne inicjowane przez **SP**

* Po skonfigurowaniu iProva można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>Dodawanie aplikacji iProva z galerii

Aby skonfigurować integrację aplikacji iProva z usługą Azure AD, musisz dodać tę aplikację z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **iProva** w polu wyszukiwania.
1. Wybierz pozycję **iProva** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla iProva

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą iProva przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w iProva.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą iProva, wykonaj następujące bloki konstrukcyjne:

1. **[Pobieranie informacji o konfiguracji z aplikacji iProva](#retrieve-configuration-information-from-iprova)** — jako przygotowanie do następnych kroków.
1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Utwórz użytkownika testowego iProva](#create-iprova-test-user)** , aby dysponować odpowiednikiem B. Simon w iProva, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-iprova-sso)** w usłudze iProva, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="retrieve-configuration-information-from-iprova"></a>Pobieranie informacji o konfiguracji z aplikacji iProva

W tej sekcji pobierzesz informacje z aplikacji iProva w celu skonfigurowania logowania jednokrotnego usługi Azure AD.

1. Otwórz przeglądarkę internetową i przejdź do **strony z informacjami o formacie SAML2** w aplikacji iProva przy użyciu następującego wzorca adresu URL:

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![Wyświetlanie strony z informacjami o formacie SAML2 w aplikacji iProva](media/iprova-tutorial/iprova-saml2-info.png)

1. Pozostaw otwartą kartę przeglądarki, gdy wykonujesz następne kroki w innej karcie przeglądarki.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **IProva** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. Wypełnij pole **Adres URL logowania** przy użyciu wartości wyświetlonej obok etykiety **Adres URL logowania** na stronie **informacji o formacie SAML2 aplikacji iProva**. Ta strona jest wciąż otwarta w innej karcie przeglądarki.

    b. Wypełnij pole **Identyfikator** przy użyciu wartości wyświetlonej obok etykiety **EntityID** na stronie **informacji o formacie SAML2 aplikacji iProva**. Ta strona jest wciąż otwarta w innej karcie przeglądarki.

    c. Wypełnij pole **Adres URL odpowiedzi** przy użyciu wartości wyświetlonej obok etykiety **Adres URL odpowiedzi** na stronie**informacji o formacie SAML2 aplikacji iProva**. Ta strona jest wciąż otwarta w innej karcie przeglądarki.

1. Aplikacja iProva oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja iProva oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy| Przestrzeń nazw  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

## <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi iProva.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Z listy aplikacji wybierz pozycję **iProva**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="create-iprova-test-user"></a>Tworzenie użytkownika testowego aplikacji iProva

1. Zaloguj się do aplikacji iProva za pomocą konta **administratora**.

2. Otwórz menu **Przejdź do**.

3. Wybierz pozycję **Application management** (Zarządzanie aplikacją).

4. Wybierz pozycję **Users** (Użytkownicy) na panelu **Users and user groups** (Użytkownicy i grupy użytkowników).

5. Wybierz pozycję **Dodaj**.

6. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika, na przykład `B.Simon@contoso.com` .

7. W polu **pełna nazwa** wprowadź pełną nazwę użytkownika, na przykład **B. Simon**.

8. Wybierz opcję **Bez hasła (użyj logowania jednokrotnego)**.

9. W polu **adres e-mail** wprowadź adres e-mail użytkownika, np `B.Simon@contoso.com` .

10. Przewiń w dół do końca strony, a następnie wybierz pozycję **Finish** (Zakończ).

## <a name="configure-iprova-sso"></a>Konfigurowanie logowania jednokrotnego iProva

1. Zaloguj się do aplikacji iProva za pomocą konta **administratora**.

2. Otwórz menu **Przejdź do**.

3. Wybierz pozycję **Application management** (Zarządzanie aplikacją).

4. Wybierz pozycję **General** (Ogólne) na panelu **System settings** (Ustawienia systemowe).

5. Kliknij pozycję **Edytuj**.

6. Przewiń w dół do pozycji **Kontrola dostępu**.

    ![Ustawienia kontroli dostępu aplikacji iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Znajdź ustawienie **Użytkownicy są automatycznie logowani za pomocą kont sieci** i zmień je na **Tak, uwierzytelniaj za pomocą protokołu SAML**. Pojawią się dodatkowe opcje.

8. Wybierz pozycję **Skonfiguruj**.

9. Wybierz opcję **Dalej**.

10. Teraz w aplikacji iProva zostanie wyświetlone pytanie o to, czy pobrać dane federacji z adresu URL, czy przekazać je z pliku. Wybierz opcję **From URL** (Z adresu URL).

    ![Pobieranie metadanych usługi Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Wklej adres URL metadanych zapisany w ostatnim kroku w sekcji „Konfigurowanie logowania jednokrotnego usługi Azure AD”.

12. Wybierz przycisk strzałki, aby pobrać metadane z usługi Azure AD.

13. Po zakończeniu pobierania zostanie wyświetlony komunikat potwierdzający **Valid Federation Data file downloaded** (Pobrano prawidłowy plik danych federacji).

14. Wybierz opcję **Dalej**.

15. Pomiń teraz opcję **Test login** (Testuj logowanie) i wybierz pozycję **Next** (Dalej).

16. Z listy rozwijanej **Claim to use** (Oświadczenie do użycia) wybierz pozycję **windowsaccountname**.

17. Wybierz pozycję **Zakończ**.

18. Teraz wróć do ekranu **Edytowanie ustawień ogólnych**. Przewiń w dół strony, a następnie wybierz przycisk **OK**, aby zapisać konfigurację.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka iProva w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji iProva, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj iProva z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić iProva z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)