---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z programem Trend Micro Web Security (TMWS) | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem Trend Micro Web Security(TMWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083414"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Samouczek: Integracja logowania jednokrotnego usługi Azure Active Directory z usługą Trend Micro Web Security (TMWS)

W tym samouczku dowiesz się, jak zintegrować program Trend Micro Web Security(TMWS) z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Trend Micro Web Security(TMWS) z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do programu Trend Micro Web Security(TMWS).
* Włącz automatyczne logowanie użytkowników do usługi Trend Micro Web Security(TMWS) za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją logowania jednokrotnego trend micro Web Security (TMWS).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Trend Micro Web Security (TMWS) obsługuje sso inicjowane przez **SP**
* Po skonfigurowaniu programu Trend Micro Web Security (TMWS) można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Dodawanie programu Trend Micro Web Security (TMWS) z galerii

Aby skonfigurować integrację programu Trend Micro Web Security (TMWS) z usługą Azure AD, należy dodać program Trend Micro Web Security (TMWS) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **trend Trend Micro Web Security (TMWS)** w polu wyszukiwania.
1. Wybierz **pozycję Trend Micro Web Security (TMWS)** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu Trend Micro Web Security(TMWS)

Konfigurowanie i testowanie sytua usługi Azure AD za pomocą programu Trend Micro Web Security (TMWS) przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik sygnatora sygnowania zabezpieczeń działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Trend Micro Web Security(TMWS).

Aby skonfigurować i przetestować syg jako jeden na dział usługi Azure AD za pomocą programu Trend Micro Web Security(TMWS), wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
    1. **[Konfigurowanie ustawień synchronizacji użytkowników i grup w usłudze Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** — konfigurowanie ustawień synchronizacji użytkowników i grup w usłudze Azure AD
1. **[Skonfiguruj logowanie jednokrotne trend micro web security (TMWS)](#configure-trend-micro-web-security-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Trend Micro Web Security(TMWS)** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego identyfikatora. Te wartości można uzyskać w obszarze Ustawienia dostawcy usług dla obszaru **Portal administracyjny platformy Azure** na ekranie Metoda **uwierzytelniania** dla usługi Azure AD z **usługi Administracyjne > Usługi katalogowe**.

1. Aplikacja Trend Micro Web Security (TMWS) oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji Trend Micro Web Security (TMWS) oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa | Atrybut źródłowy|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | Upn | user.userprincipalname |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie trendu Mikro zabezpieczenia sieci Web (TMWS)** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do trendu Micro Web Security (TMWS).

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Trend Micro Web Security(TMWS).**
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Konfigurowanie ustawień synchronizacji użytkowników i grup w usłudze Azure AD

1. W lewej nawigacji kliknij pozycję **Azure Active Directory**.

1. W obszarze **Zarządzanie**kliknij pozycję **Rejestracje aplikacji,** a następnie kliknij nową aplikację przedsiębiorstwa w obszarze **Wszystkie aplikacje.**

1. W obszarze **Zarządzanie**kliknij pozycję **Certyfikaty & wpisy tajne**.

1. W obszarze Jawne wpisy tajne klienta kliknij pozycję **Nowy klucz tajny klienta**.

1. Na wyświetlonym ekranie Dodaj klucz tajny klienta opcjonalnie dodaj opis i wybierz okres wygaśnięcia dla tego klucza tajnego klienta, a następnie kliknij przycisk **Dodaj**. Nowo dodany klucz tajny klienta pojawia się w obszarze wpisy tajne klienta.

1. Zapisz wartość. Później należy wpisać informacje w TMWS.

1. W obszarze **Zarządzanie**kliknij pozycję **Uprawnienia interfejsu API**. 

1. Na wyświetlonym ekranie uprawnień interfejsu API kliknij pozycję **Dodaj uprawnienie**.

1. Na karcie Interfejsy API firmy Microsoft na wyświetlonym ekranie Uprawnień interfejsu API żądania kliknij pozycję **Microsoft Graph,** a następnie **pozycję Uprawnienia aplikacji**.

1. Znajdź i dodaj następujące uprawnienia: 

    * Group.Read.All
    * User.Read.All

1. Kliknij pozycję **Dodaj uprawnienia**. Zostanie wyświetlony komunikat potwierdzający pomyślne zapisanie ustawień. Nowo dodane uprawnienia są wyświetlane na ekranie uprawnień interfejsu API.

1. W obszarze Zgoda na udzielenie kliknij pozycję **Udzielaj zgody administratora, aby < konto administratora > (katalog domyślny),** a następnie **przycisk Tak**. Pojawia się komunikat potwierdzający, że zgoda administratora na żądane uprawnienia została pomyślnie udzielona.

1. Kliknij pozycję **Przegląd**. 

1. W wyświetlonym prawym okienku nagraj identyfikator aplikacji (klienta) i identyfikator katalogu (dzierżawy). Później należy wpisać informacje w TMWS. Można również kliknąć **niestandardowe nazwy domen** w obszarze Azure **Active Directory > Zarządzać** i rejestrować nazwę domeny w prawym okienku.

## <a name="configure-trend-micro-web-security-sso"></a>Konfigurowanie aplikacji SYG programu Trend Micro Web Security

1. Zaloguj się do konsoli zarządzania TMWS i przejdź do **usługi Administracja** > **użytkownikami & usługi katalogowe uwierzytelniania** > **Directory Services**.

1. Kliknij tutaj górny obszar ekranu.

1. Na wyświetlonym ekranie Metoda uwierzytelniania kliknij pozycję **Azure AD**.

1. Kliknij **przycisk Włącz** lub **Wyłącz,** aby zdecydować, czy zezwolić użytkownikom usługi AD w organizacji na odwiedzanie witryn sieci Web za pośrednictwem usługi TMWS, jeśli ich dane nie są zsynchronizowane z usługą TMWS.

    > [!NOTE]
    > Użytkownicy niezsynchronizowani z usługi Azure AD mogą być uwierzytelnieni tylko za pośrednictwem znanych bram TMWS lub dedykowanego portu dla twojej organizacji.

1. W sekcji **Ustawienia dostawcy tożsamości** wykonaj następujące czynności:

    a. W polu **Adres URL usługi** wklej wartość adresu URL **logowania** skopiowaną z witryny Azure portal

    b. W polu **atrybutu Nazwa logowania** wklej nazwę oświadczenia użytkownika z atrybutem source **user.onpremisessamaccountname** z witryny Azure portal.

    d. W polu **Certyfikat publicznego SSL** użyj pobranego **certyfikatu (Base64)** z witryny Azure portal.

1. W sekcji **Ustawienia synchronizacji** wykonaj następujące czynności:

    a. W polu **Dzierżawa** użyj **identyfikatora katalogu (dzierżawy)** lub niestandardowej wartości **nazwy domeny** z witryny Azure portal.

    b. W polu **Identyfikator aplikacji** wartość **identyfikatora aplikacji (klienta)** z witryny Azure portal.

    d. W polu **klucz tajny klienta** użyj **klucza tajnego klienta** z witryny Azure portal.

    d. W polu **Harmonogram synchronizacji** wybierz, aby zsynchronizować z usługą Azure AD ręcznie lub zgodnie z harmonogramem. Jeśli wybierzesz opcję Ręcznie, za każdym razem, gdy występują zmiany w informacjach o użytkowniku usługi Active Directory, pamiętaj, aby wrócić do ekranu Usługi katalogowe i wykonać ręczną synchronizację, aby informacje w tmws pozostały aktualne.

    e. Kliknij **przycisk Testuj połączenie,** aby sprawdzić, czy usługa Azure AD może być pomyślnie nawiązywać połączenia. 
    
    f. Kliknij pozycję **Zapisz**.
 
 > [!NOTE]
 > Aby uzyskać więcej informacji na temat konfigurowania programu Trend Micro Web Security za pomocą usługi Azure AD, zapoznaj się z [tym](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) dokumentem.

## <a name="test-sso"></a>Test SSO 

Po pomyślnym skonfigurowaniu usługi Azure AD i określeniu usługi Azure AD jako metody uwierzytelniania użytkownika można zalogować się do serwera proxy TMWS w celu zweryfikowania konfiguracji. Po weryfikowaniu konta usługi Azure AD można odwiedzić Internet.

> [!NOTE]
> Usługa TMWS nie obsługuje testowania logowania jednokrotnego z portalu usługi Azure AD w obszarze Omówienie > logowanie jednokrotne > Konfigurowanie logowania jednokrotnego za pomocą testu SAML > nowej aplikacji dla przedsiębiorstwa.

1. Wyczyść przeglądarkę wszystkich plików cookie, a następnie uruchom ponownie przeglądarkę. 

1. Skieruj przeglądarkę na serwer proxy TMWS. Aby uzyskać szczegółowe informacje, zobacz [Przekazywanie ruchu za pomocą plików PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Odwiedź dowolną stronę internetową. TMWS przekieruje Cię do portalu Zniewolonego TMWS.

1. Określ konto usługi Active Directory (format: domena\sAMAccountName lub sAMAccountName@domain) lub adres e-mail lub nazwę UPN, a następnie kliknij przycisk **Zaloguj**. Usługa TMWS wysyła do logowania usługi Azure AD.

1. Podczas logowania usługi Azure AD wpisz poświadczenia konta usługi AD. Należy pomyślnie zalogować się do TMWS.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program Trend Micro Web Security (TMWS) w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić trend Micro Web Security (TMWS) dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

