---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu łącznika usługi Google Cloud (G Suite) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i Google Cloud (G Suite).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: jeedes
ms.openlocfilehash: a846899ba8f9b9e7c0d2e54744f5e5044ca7a2d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732099"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu łącznika usługi Google Cloud (G Suite)

W tym samouczku dowiesz się, jak zintegrować łącznik usługi Google Cloud (G Suite) z usługą Azure Active Directory (Azure AD). Po zintegrowaniu łącznika usługi Google Cloud (G Suite) z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do łącznika usługi Google Cloud (G Suite).
* Zezwól użytkownikom na automatyczne logowanie do łącznika Google Cloud (G Suite) przy użyciu swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

- Subskrypcja usługi Azure AD.
- Subskrypcja z obsługą logowania jednokrotnego (Single Suite) w chmurze firmy Google.
- Subskrypcja usługi Google Apps lub usługi Google Cloud Platform

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku. Ten dokument został utworzony przy użyciu nowego środowiska użytkownika służącego do logowania jednokrotnego. Jeśli nadal używasz starego środowiska, konfiguracja będzie przebiegać inaczej. Nowe środowisko możesz włączyć w ustawieniach logowania jednokrotnego aplikacji G Suite. Przejdź do **usługi Azure AD, aplikacji dla przedsiębiorstw**, wybierz pozycję **Łącznik Google Cloud (G Suite)**, wybierz pozycję **Logowanie jednokrotne** , a następnie kliknij pozycję **Wypróbuj nasze nowe środowisko**.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. **P: czy ta integracja obsługuje Google Cloud Platform z integracją SSO z usługą Azure AD?**

    Odp. Tak. Usługi Google Cloud Platform i Google Apps współdzielą tę samą platformę uwierzytelniania. W związku z tym w celu przeprowadzenia integracji usługi GCP należy skonfigurować logowanie jednokrotne w ramach usługi Google Apps.

2. **P: czy Chromebooks i inne urządzenia Chrome są zgodne z logowaniem jednokrotnym w usłudze Azure AD?**
  
    Odp.: tak, użytkownicy mogą logować się do swoich urządzeń Chromebook przy użyciu poświadczeń usługi Azure AD. Zobacz ten [artykuł pomocy technicznej łącznika usługi Google Cloud (G Suite)](https://support.google.com/chrome/a/answer/6060880) , aby uzyskać informacje na temat tego, dlaczego użytkownicy mogą uzyskać monit o podanie poświadczeń dwa razy.

3. **P: Jeśli włączam Logowanie jednokrotne, użytkownicy będą mogli korzystać z poświadczeń usługi Azure AD w celu zalogowania się do dowolnego produktu Google, takiego jak Google Classroom, GMail, dysk Google, YouTube itd.?**

    Odp.: tak, w zależności od tego, [który łącznik usługi Google Cloud (G Suite)](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) został wybrany do włączenia lub wyłączenia dla organizacji.

4. **P: Czy można włączyć logowanie jednokrotne tylko dla podzbioru użytkowników łączników usługi Google Cloud (G Suite)?**

    Odp.: nie, włączenie logowania jednokrotnego bezpośrednio wymaga od wszystkich użytkowników łącznika usługi Google Cloud (G Suite) do uwierzytelnienia przy użyciu poświadczeń w usłudze Azure AD. Ponieważ łącznik usługi Google Cloud (G Suite) nie obsługuje wielu dostawców tożsamości, dostawca tożsamości dla środowiska łącznika Google Cloud (G Suite) może być usługą Azure AD lub Google--, ale nie jednocześnie.

5. **P: Jeśli użytkownik jest zalogowany za pomocą systemu Windows, czy jest automatycznie uwierzytelniany do łącznika Google Cloud (G Suite) bez monitowania o hasło?**

    Odp.: dostępne są dwie opcje umożliwiające włączenie tego scenariusza. Pierwsza opcja: użytkownicy mogą logować się do urządzeń z systemem Windows 10 za pośrednictwem [dołączania do usługi Azure Active Directory](../devices/overview.md). Druga opcja: użytkownicy mogą logować się do urządzeń z systemem Windows przyłączonych do domeny lokalnej usługi Active Directory, dla której włączono obsługę logowania jednokrotnego do usługi Azure AD za pośrednictwem wdrożenia usług [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md). Obie opcje wymagają wykonania czynności opisanych w poniższym samouczku, aby włączyć logowanie jednokrotne między usługą Azure AD i łącznikiem usługi Google Cloud (G Suite).

6. **P: co należy zrobić, gdy otrzymuję komunikat o błędzie "nieprawidłowy adres e-mail"?**

    Odp.: w przypadku tej konfiguracji atrybut poczty e-mail jest wymagany, aby użytkownicy mogli się zalogować. Tego atrybutu nie można ustawić ręcznie.

    Atrybut adresu e-mail jest wypełniany automatycznie dla każdego użytkownika z ważną licencją programu Exchange. Jeśli dla użytkownika nie włączono poczty e-mail, ten błąd będzie występował, ponieważ aplikacja musi pobrać ten atrybut w celu udzielenia dostępu.

    Możesz przejść do portal.office.com przy użyciu konta administratora, a następnie kliknąć w centrum administracyjnym, rozliczeń, subskrypcji, wybrać subskrypcję usługi Microsoft 365, a następnie kliknąć pozycję Przypisz do użytkowników, wybrać użytkowników, którzy chcą sprawdzić swoją subskrypcję, a następnie w okienku po prawej stronie kliknąć pozycję Edytuj licencje.

    Po przypisaniu licencji Microsoft 365 może upłynąć kilka minut. Po tym czasie atrybut user.mail będzie automatycznie wypełniany, co powinno spowodować rozwiązanie problemu.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Łącznik Google Cloud (G Suite) obsługuje zainicjowanie rejestracji jednokrotnej w programie **SP**

* Łącznik Google Cloud (G Suite) obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](./g-suite-provisioning-tutorial.md)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Dodawanie łącznika usługi Google Cloud (G Suite) z galerii

Aby skonfigurować integrację łącznika Google Cloud (G Suite) z usługą Azure AD, musisz dodać Łącznik Google Cloud (G Suite) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Łącznik Google Cloud (G Suite)** w polu wyszukiwania.
1. Wybierz pozycję **Łącznik Google Cloud (G Suite)** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla łącznika Google Cloud (G Suite)

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą łącznika Google Cloud (G Suite) przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w łączniku Google Cloud (G Suite).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą łącznika Google Cloud (G Suite), wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę Logowanie jednokrotne w usłudze Google Cloud (G Suite)](#configure-google-cloud-g-suite-connector-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego łącznika usługi Google Cloud (g Suite)](#create-google-cloud-g-suite-connector-test-user)** , aby dysponować odpowiednikiem B. Simon w łączniku Google Cloud (g Suite), który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **łącznika Google Cloud (G Suite)** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz utworzyć konfigurację dla usługi **Gmail**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:  

    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

1. Jeśli chcesz utworzyć konfigurację dla usług **Google Cloud Platform**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:  
    
    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Łącznik usługi Google Cloud (G Suite) nie udostępnia wartości identyfikatora/identyfikatora jednostki na potrzeby konfiguracji logowania jednokrotnego, więc po odpisaniu opcji **wystawcy specyficznego dla domeny** wartość identyfikatora będzie równa `google.com` . W przypadku zaznaczenia opcji **wystawcy specyficzny dla domeny** będzie to możliwe `google.com/a/<yourdomainname.com>` . Aby zaznaczyć/wyczyścić opcję **wystawcy specyficzną dla domeny** , należy przejść do sekcji **Konfigurowanie usługi SSO łącznika usługi Google Cloud (G Suite)** , która została omówiona w dalszej części tego samouczka. Aby uzyskać więcej informacji, skontaktuj się z [zespołem pomocy technicznej usługi Google Cloud (G Suite)](https://www.google.com/contact/).

1. Aplikacja łącznika usługi Google Cloud (G Suite) oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia przykład tego działania. Domyślną wartością **unikatowego identyfikatora użytkownika** jest **User. userPrincipalName** , ale Łącznik Google Cloud (G Suite) oczekuje, że jest on mapowany przy użyciu adresu e-mail użytkownika. Do tego celu można użyć atrybutu **user.mail** z listy lub odpowiedniej wartości atrybutu zgodnie z konfiguracją organizacji.

    ![image (obraz)](common/default-attributes.png)


1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie łącznika usługi Google Cloud (G Suite)** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do łącznika usługi Google Cloud (G Suite).

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Łącznik Google Cloud (G Suite)**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Konfigurowanie logowania jednokrotnego łącznika usługi Google Cloud (G Suite)

1. Otwórz nową kartę w przeglądarce i zaloguj się do [konsoli administracyjnej łącznika Google Cloud (G Suite)](https://admin.google.com/) przy użyciu konta administratora.

2. Kliknij pozycję **Security** (Zabezpieczenia). Jeśli nie widzisz linku, może on znajdować się w menu **More Controls** (Więcej kontrolek) u dołu ekranu.

    ![Kliknij pozycję Security (Zabezpieczenia).][10]

3. Na stronie **Security** (Zabezpieczenia) kliknij pozycję **Set up single sign-on (SSO)** (Konfigurowanie logowania jednokrotnego).

    ![Kliknij pozycję SSO (Logowanie jednokrotne).][11]

4. Wprowadź następujące zmiany w konfiguracji:

    ![Konfigurowanie logowania jednokrotnego][12]

    a. Zaznacz pole wyboru **Setup SSO with third-party identity provider** (Skonfiguruj logowanie jednokrotne za pomocą dostawcy tożsamości innej firmy).

    b. W polu **adres URL strony logowania** w łączniku usługi Google Cloud (G Suite) Wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    c. W polu **adres URL strony logowania** w łączniku usługi Google Cloud (G Suite) Wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    > [!NOTE]
    > Usługa Google Cloud (G Suite) jest oparta na protokole wylogowania protokołu SAML. Dlatego w polu **adres URL strony logowania** musi być używany adres URL WYLOGOWYWAnia SAML, czyli adres URL logowania jako wartość tego samego.

    d. W przypadku łącznika usługi Google Cloud (G Suite) dla **certyfikatu weryfikacji** Przekaż certyfikat pobrany z Azure Portal.   

    e. Zaznacz/Usuń zaznaczenie opcji **Użyj określonego wystawcy dla domeny** zgodnie z uwagą wymienionym w powyższej sekcji podstawowe informacje o **konfiguracji języka SAML** w usłudze Azure AD.

    f. W polu **Zmień hasło URL** w usłudze Google Cloud (G Suite) Wklej wartość **adresu URL zmiany hasła** skopiowanego z Azure Portal.

    przykład Kliknij pozycję **Zapisz**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Utwórz użytkownika testowego łącznika usługi Google Cloud (G Suite)

Celem tej sekcji jest [utworzenie użytkownika w łączniku Google Cloud (G Suite)](https://support.google.com/a/answer/33310?hl=en) o nazwie B. Simon. Po ręcznym utworzeniu użytkownika w łączniku usługi Google Cloud (G Suite) użytkownik będzie mógł zalogować się przy użyciu poświadczeń logowania Microsoft 365.

Łącznik Google Cloud (G Suite) obsługuje również automatyczne Inicjowanie obsługi użytkowników. Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników, należy najpierw [skonfigurować łącznik usługi Google Cloud (G Suite) do automatycznego aprowizacji użytkowników](./g-suite-provisioning-tutorial.md).

> [!NOTE]
> Upewnij się, że użytkownik już istnieje w łączniku Google Cloud (G Suite), jeśli nie włączono obsługi administracyjnej w usłudze Azure AD przed przetestowaniem rejestracji jednokrotnej.

> [!NOTE]
> Jeśli konieczne jest ręczne utworzenie użytkownika, skontaktuj się z [zespołem pomocy technicznej firmy Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania łącznika usługi Google Cloud (G Suite), w którym można zainicjować przepływ logowania. 

* Przejdź do adresu URL logowania łącznika usługi Google Cloud (G Suite) bezpośrednio i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka łącznika usługi Google Cloud (G Suite) w obszarze Moje aplikacje zostanie przekierowany do adresu URL logowania łącznika usługi Google Cloud (G Suite). Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu łącznika usługi Google Cloud (G Suite) można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
