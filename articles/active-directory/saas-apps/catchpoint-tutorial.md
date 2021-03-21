---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą punkt przechwytywania'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i punkt przechwytywania.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: a8515920985c569df74b1e328d6bfe1c4ec97195
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735317"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym za pomocą punkt przechwytywania

W tym samouczku dowiesz się, jak zintegrować usługę punkt przechwytywania z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi punkt przechwytywania z usługą Azure AD można:

* Kontroluj dostęp użytkowników do punkt przechwytywania z usługi Azure AD.
* Włącz automatyczne logowanie w usłudze punkt przechwytywania dla użytkowników z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi punkt przechwytywania z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa punkt przechwytywania obsługuje logowanie jednokrotne z użyciem zainicjowanych przez usługę SP i dostawcy tożsamości.
* Punkt przechwytywania obsługuje Inicjowanie obsługi użytkowników just-in-Time (JIT).

## <a name="add-catchpoint-from-the-gallery"></a>Dodaj punkt przechwytywania z galerii

Aby skonfigurować integrację programu punkt przechwytywania z usługą Azure AD, Dodaj punkt przechwytywania do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal za pomocą konto Microsoftów służbowych.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **punkt przechwytywania** w polu wyszukiwania.
1. Wybierz pozycję **punkt przechwytywania** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-catchpoint"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla punkt przechwytywania

Aby logowanie jednokrotne działało, należy połączyć użytkownika usługi Azure AD z użytkownikiem w punkt przechwytywania. Na potrzeby tego samouczka skonfigurujemy użytkownika testowego o nazwie **B. Simon**. 

Wykonaj następujące sekcje:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso), aby włączyć tę funkcję dla użytkowników.
    * [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user), aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user), aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj Logowanie jednokrotne](#configure-catchpoint-sso)w usłudze punkt przechwytywania, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * [Utwórz użytkownika testowego punkt przechwytywania](#create-a-catchpoint-test-user), aby umożliwić Łączenie konta testowego B. Simon usługi Azure AD z podobnym kontem użytkownika w usłudze punkt przechwytywania.
1. [Przetestuj Logowanie jednokrotne](#test-sso), aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki w Azure Portal, aby włączyć logowanie jednokrotne w usłudze Azure AD:

1. Zaloguj się w witrynie Azure Portal.
1. Na stronie integracja aplikacji **punkt przechwytywania** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka, aby edytować **podstawowe ustawienia konfiguracji protokołu SAML** .

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Skonfiguruj tryb zainicjowany dla punkt przechwytywania:
   - W przypadku trybu zainicjowane przez **dostawcy tożsamości** wprowadź wartości dla następujących pól:
     - Dla **identyfikatora**: `https://portal.catchpoint.com/SAML2`
     - **Adres URL odpowiedzi**:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - W przypadku trybu inicjowania programu **SP** wybierz opcję **Ustaw dodatkowe adresy URL** i wprowadź następującą wartość:
     - Dla **adresu URL logowania**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Aplikacja punkt przechwytywania oczekuje potwierdzeń SAML w określonym formacie. Dodawanie mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów SAML. Poniższa tabela zawiera listę atrybutów domyślnych:

    | Nazwa | Atrybut źródłowy|
    | ------------ | --------- |
    | GivenName | User. givenneame |
    | Nazwisko | user.surname |
    | EmailAddress | user.mail |
    | Nazwa | user.userprincipalname |
    | Unikatowy identyfikator użytkownika | user.userprincipalname |

    ![Zrzut ekranu listy atrybutów użytkownika &](common/default-attributes.png)

1. Ponadto aplikacja punkt przechwytywania oczekuje, że inny atrybut zostanie przesłany w odpowiedzi SAML. Zobacz poniższą tabelę. Ten atrybut jest również wstępnie wypełniony, ale można go przejrzeć i zaktualizować w celu dopasowania do własnych wymagań.

    | Nazwa | Atrybut źródłowy|
    | ------------ | --------- |
    | namespace | user.assignedrole |

    > [!NOTE]
    > To `namespace` zastrzeżenie musi być zamapowane przy użyciu nazwy konta. Ta nazwa konta powinna być skonfigurowana z rolą w usłudze Azure AD, aby mogła zostać przeniesiona z powrotem w odpowiedzi SAML. Aby uzyskać więcej informacji na temat ról w usłudze Azure AD, zobacz [Konfigurowanie roszczeń ról wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Przejdź do strony **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** . W sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)**. Wybierz pozycję **Pobierz** , aby zapisać certyfikat na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfiguracja punkt przechwytywania** Skopiuj adresy URL, które są potrzebne w późniejszym kroku.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji użyjesz Azure Portal, aby utworzyć użytkownika testowego usługi Azure AD o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład wprowadź `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** . Zanotuj wartość wyświetlaną hasło.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi punkt przechwytywania.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **punkt przechwytywania**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli skonfigurowano role zgodnie z opisem w powyższej tabeli, można wybrać ją z listy rozwijanej **Wybierz rolę** .
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-catchpoint-sso"></a>Konfigurowanie logowania jednokrotnego punkt przechwytywania

1. W innym oknie przeglądarki sieci Web Zaloguj się do aplikacji punkt przechwytywania jako administrator.

1. Wybierz ikonę **Ustawienia** , a następnie pozycję **dostawca tożsamości logowania jednokrotnego**.

    ![Zrzut ekranu ustawień punkt przechwytywania z wybranym dostawcą tożsamości logowania jednokrotnego](./media/catchpoint-tutorial/configuration1.png)

1. Na stronie **Logowanie** jednokrotne wprowadź następujące pola:

   ![Zrzut ekranu strony punkt przechwytywania logowania jednokrotnego](./media/catchpoint-tutorial/configuration2.png)

   Pole | Wartość
   ----- | ----- 
   **Przestrzeń nazw** | Prawidłowa wartość przestrzeni nazw.
   **Wystawca dostawcy tożsamości** | `Azure AD Identifier`Wartość z Azure Portal.
   **Adres URL logowania jednokrotnego** | `Login URL`Wartość z Azure Portal.
   **Certyfikat** | Zawartość pobranego `Certificate (Base64)` pliku z Azure Portal. Użyj Notatnika, aby wyświetlić i skopiować.

   Możesz również przekazać **plik XML metadanych Federacji** , wybierając opcję **Przekaż metadane** .

1. Wybierz pozycję **Zapisz**.

### <a name="create-a-catchpoint-test-user"></a>Tworzenie użytkownika testowego punkt przechwytywania

Punkt przechwytywania obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. Brak elementów akcji w tej sekcji. Jeśli B. Simon nie istnieje jako użytkownik w punkt przechwytywania, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania punkt przechwytywania, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania punkt przechwytywania i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do punkt przechwytywania, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka punkt przechwytywania w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do punkt przechwytywania, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


> [!NOTE]
> Gdy logujesz się do aplikacji punkt przechwytywania za pomocą strony logowania, po podaniu **poświadczeń punkt przechwytywania** Wprowadź prawidłową wartość **przestrzeni nazw** w polu **poświadczenia firmy (SSO)** i wybierz pozycję **Zaloguj**.
> 
> ![Konfiguracja punkt przechwytywania](./media/catchpoint-tutorial/loginimage.png)

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu punkt przechwytywania można wymusić kontrolę sesji. To zabezpieczenie chroni przed eksfiltracji i niefiltrowaniem poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji jest rozszerzeniem dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
