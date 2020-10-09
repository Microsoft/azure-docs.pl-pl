---
title: 'Samouczek: integracja SSO usługi Azure AD z usługą Trend Micro Web Security (TMWS)'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Trend Micro Web Security (TMWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: f2a6598cc28c39719d73be333bd74c24fce9371b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88551907"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Trend Micro Web Security (TMWS)

W tym samouczku dowiesz się, jak zintegrować usługę Trend Micro Web Security (TMWS) z Azure Active Directory (Azure AD). Po zintegrowaniu usługi TMWS z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do TMWS.
* Zezwól użytkownikom na automatyczne logowanie się do usługi TMWS przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi TMWS, która jest włączona dla logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa TMWS obsługuje logowanie jednokrotne zainicjowane przez usługę SP.
* Po skonfigurowaniu TMWS można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. Aby dowiedzieć się, jak wymusić kontrolę sesji przy użyciu Microsoft Cloud App Security, zobacz Dołączanie [i wdrażanie kontrola dostępu warunkowego aplikacji dla dowolnej aplikacji](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-tmws-from-the-gallery"></a>Dodaj TMWS z galerii

Aby skonfigurować integrację programu TMWS z usługą Azure AD, musisz dodać TMWS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) za pomocą konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź w polu wyszukiwania pozycję **Trend Micro Web Security (TMWS)** .
1. Wybierz pozycję **Trend Micro Web Security (TMWS)** w wynikach wyszukiwania, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla TMWS

Skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD za pomocą TMWS przy użyciu użytkownika testowego o nazwie B. Simon. Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w TMWS.

Wykonaj następujące podstawowe kroki, aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą TMWS:

1. [Skonfiguruj Logowanie jednokrotne w usłudze Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
    1. [Udziel użytkownikowi testu usługi Azure AD](#grant-the-azure-ad-test-user-access-to-tmws) dostępu do TMWS.
    1. [Konfigurowanie ustawień synchronizacji użytkowników i grup w usłudze Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [Skonfiguruj logowanie JEDNOkrotne TMWS](#configure-tmws-sso) na stronie aplikacji.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby zweryfikować konfigurację.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Trend Micro Web Security (TMWS)** w sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz przycisk pióra dla **podstawowej konfiguracji SAML** , aby edytować ustawienia:

   ![Edytuj podstawowe ustawienia konfiguracji SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wpisz wartości w następujących polach:

    a. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL w następującym wzorcu:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. W polu **adres URL odpowiedzi** wprowadź następujący adres URL:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Wartość identyfikatora w poprzednim kroku nie jest wartością, którą należy wprowadzić. Musisz użyć rzeczywistego identyfikatora. Tę wartość można uzyskać w **ustawieniach dostawcy usług w sekcji Portal administracyjny platformy Azure** na stronie **Metoda uwierzytelniania** dla usługi Azure AD z poziomu **administracji > usług katalogowych**.

1. TMWS oczekuje potwierdzeń SAML w określonym formacie, dlatego należy dodać mapowania atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Ten zrzut ekranu pokazuje domyślne atrybuty:

    ![Atrybuty domyślne](common/default-attributes.png)

1. Oprócz atrybutów z poprzedniego zrzutu ekranu, TMWS oczekuje, że dwa więcej atrybutów do przesłania z powrotem w odpowiedzi SAML. Te atrybuty są pokazane w poniższej tabeli. Atrybuty są wstępnie wypełnione, ale można je zmienić w celu spełnienia wymagań.
    
    | Nazwa | Atrybut źródłowy|
    | --------------- | --------- |
    | sAMAccountName | User. nazwy pospolitej onpremisessamaccountname |
    | Głównej | user.userprincipalname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **certyfikat (base64)**. Wybierz link **pobierania** obok tej nazwy certyfikatu, aby pobrać certyfikat i zapisać go na komputerze:

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie rozwiązania Trend Micro Web Security (TMWS)** skopiuj odpowiedni adres URL lub adresy URL zgodnie z wymaganiami:

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**. Wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon` .  
   1. W polu **Nazwa użytkownika** wprowadź nazwę ***użytkownika *@* formacie *.* rozszerzenie***. Na przykład `B.Simon@contoso.com`.
   1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość wyświetlaną w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Przyznaj użytkownikowi testowemu usługi Azure AD dostęp do TMWS

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi TMWS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Trend Micro Web Security (TMWS)**.
1. Na stronie Przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**:

   ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** na liście **Użytkownicy** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Konfigurowanie ustawień synchronizacji użytkowników i grup w usłudze Azure AD

1. W lewym okienku wybierz pozycję **Azure Active Directory**.

1. W obszarze **Zarządzaj**wybierz pozycję **rejestracje aplikacji**, a następnie wybierz nową aplikację dla przedsiębiorstw w obszarze **wszystkie aplikacje**.

1. W obszarze **Zarządzaj**wybierz pozycję **Certyfikaty & wpisy tajne**.

1. W obszarze wpisy **tajne klienta** wybierz pozycję **nowy klucz tajny klienta**.

1. Na **ekranie Dodawanie wpisu tajnego klienta**opcjonalnie Dodaj opis i wybierz okres ważności dla wpisu tajnego klienta, a następnie wybierz pozycję **Dodaj**. Nowy wpis tajny klienta pojawi się w obszarze **tajne klienta** .

1. Zapisz wartość klucza tajnego klienta. Później wprowadzisz ją do TMWS.

1. W obszarze **Zarządzaj**wybierz pozycję **uprawnienia interfejsu API**. 

1. W oknie **uprawnienia interfejsu API** wybierz pozycję **Dodaj uprawnienie**.

1. Na karcie **interfejsy API firmy Microsoft** okna **uprawnienia interfejsu API żądania** wybierz opcję **Microsoft Graph** a następnie **uprawnienia aplikacji**.

1. Znajdź i Dodaj następujące uprawnienia: 

    * Group.Read.All
    * User. Read. All

1. Wybierz pozycję **Dodaj uprawnienia**. Zostanie wyświetlony komunikat z potwierdzeniem, że Twoje ustawienia zostały zapisane. Nowe uprawnienia pojawiają się w oknie **uprawnienia interfejsu API** .

1. W obszarze **wyrażanie zgody** wybierz opcję **Udziel zgody administratora na *konto administratora* (katalog domyślny)**, a następnie wybierz pozycję **tak**. Zostanie wyświetlony komunikat z prośbą o potwierdzenie zgody administratora na żądane uprawnienia.

1. Wybierz pozycję **Omówienie**. 

1. Zapisz identyfikator **aplikacji (klienta)** w okienku **Directory (tenant) ID** po prawej stronie. Później wprowadzisz te informacje do TMWS. Możesz również wybrać **niestandardowe nazwy domen** w obszarze **Azure Active Directory > zarządzania** i rejestrowania nazwy domeny widocznej w okienku po prawej stronie.

## <a name="configure-tmws-sso"></a>Konfigurowanie logowania jednokrotnego TMWS

Wykonaj następujące kroki, aby skonfigurować Logowanie jednokrotne TMWS na stronie aplikacji.

1. Zaloguj się do konsoli zarządzania TMWS i przejdź do pozycji **Administracja**  >  **Użytkownicy & uwierzytelniania**  >  **usługi katalogowej**.

1. Zaznacz **tutaj** w górnym obszarze ekranu.

1. Na stronie **Metoda uwierzytelniania** wybierz pozycję **Azure AD**.

1. Wybierz opcję **włączone** lub **wyłączone** , aby określić, czy zezwolić użytkownikom usługi Azure AD w organizacji na odwiedzanie witryn sieci Web za pomocą TMWS, jeśli ich dane nie są zsynchronizowane z TMWS.

    > [!NOTE]
    > Użytkownicy, którzy nie są synchronizowani z usługi Azure AD, mogą być uwierzytelniani tylko za pośrednictwem znanych bram TMWS lub dedykowanego portu dla organizacji.

1. W sekcji **Ustawienia dostawcy tożsamości** wykonaj następujące czynności:

    a. W polu **adres URL usługi** wprowadź wartość **adresu URL logowania** skopiowaną z Azure Portal.

    b. W polu **atrybut nazwy logowania** wprowadź **nazwę zgłoszenia użytkownika** z atrybutem Source **User. nazwy pospolitej onpremisessamaccountname** w Azure Portal.

    c. W polu **publiczny certyfikat SSL** Użyj pobranego **certyfikatu (Base64)** z Azure Portal.

1. W sekcji **Ustawienia synchronizacji** wykonaj następujące kroki:

    a. W polu **dzierżawca** wprowadź **Identyfikator katalogu (dzierżawy)** lub **niestandardową wartość nazwy domeny** z Azure Portal.

    b. W polu **Identyfikator aplikacji** wprowadź wartość **identyfikatora aplikacji (klienta)** z Azure Portal.

    c. W polu **klucz tajny klienta** wprowadź **klucz tajny klienta** z Azure Portal.

    d. Wybierz pozycję **harmonogram synchronizacji** , aby przeprowadzić synchronizację z usługą Azure AD ręcznie lub zgodnie z harmonogramem. Jeśli wybierzesz opcję **ręcznie**, za każdym razem, gdy istnieją zmiany Active Directory informacji o użytkowniku, pamiętaj, aby wrócić do strony **usług katalogowych** i przeprowadzić ręczną synchronizację, tak aby informacje w TMWS pozostawały aktualne.

    e. Wybierz pozycję **Testuj połączenie** , aby sprawdzić, czy można pomyślnie nawiązać połączenie z usługą Azure AD.
    
    f. Wybierz pozycję **Zapisz**.
 
 > [!NOTE]
 > Aby uzyskać więcej informacji na temat konfigurowania TMWS za pomocą usługi Azure AD, zobacz [Konfigurowanie ustawień usługi Azure AD na TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

Po skonfigurowaniu usługi Azure AD i określeniu w usłudze Azure AD jako metody uwierzytelniania użytkownika możesz zalogować się do serwera proxy TMWS, aby zweryfikować konfigurację. Gdy logowanie za pomocą usługi Azure AD sprawdzi Twoje konto, możesz odwiedzić Internet.

> [!NOTE]
> TMWS nie obsługuje testowania logowania jednokrotnego z poziomu portalu usługi Azure AD, w obszarze **Przegląd**rejestracji jednokrotnej  >  **Single sign-on**  >  **Skonfiguruj Logowanie jednokrotne przy użyciu protokołu SAML**do  >  **testowania** nowej aplikacji dla przedsiębiorstw.

1. Wyczyść przeglądarkę wszystkich plików cookie, a następnie uruchom ponownie przeglądarkę. 

1. Wskaż przeglądarkę serwerowi proxy TMWS. Aby uzyskać szczegółowe informacje, zobacz [przekazywanie ruchu przy użyciu plików PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Odwiedź witrynę internetową. TMWS przekieruje Cię do portalu TMWS.

1. Określ konto Active Directory (format: *domena* \\ *sAMAccountName* lub *sAMAccountName* @ *domain*), adres e-mail lub nazwa UPN, a następnie wybierz pozycję **Zaloguj**się. TMWS wysyła Cię do okna logowania do usługi Azure AD.

1. W oknie Logowanie do usługi Azure AD wprowadź poświadczenia konta usługi Azure AD. Teraz należy zalogować się do TMWS.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integracji aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Trend Micro Web Security z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić funkcje Trend Micro Web Security za pomocą zaawansowanej widoczności i kontroli](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

