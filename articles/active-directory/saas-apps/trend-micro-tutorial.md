---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Trend Micro Web Security (TMWS) Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Trend Micro Web Security (TMWS).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82083414"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Trend Micro Web Security (TMWS)

W tym samouczku dowiesz się, jak zintegrować usługę Trend Micro Web Security (TMWS) z Azure Active Directory (Azure AD). W przypadku integracji usługi Trend Micro Web Security (TMWS) z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Trend Micro Web Security (TMWS).
* Zezwól użytkownikom na automatyczne logowanie do usługi Trend Micro Web Security (TMWS) przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Trend Micro (TMWS).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Trend Micro Web Security (TMWS) obsługuje zainicjowane przez usługę **SP** Logowanie jednokrotne
* Po skonfigurowaniu programu Trend Micro Web Security (TMWS) można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Dodawanie Trend Micro Web Security (TMWS) z galerii

Aby skonfigurować integrację usługi Trend Micro Web Security (TMWS) w usłudze Azure AD, musisz dodać Trend Micro Web Security (TMWS) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Trend Micro Web Security (TMWS)** w polu wyszukiwania.
1. Wybierz pozycję **Trend Micro Web Security (TMWS)** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi Trend Micro Web Security (TMWS)

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Trend Micro Web Security (TMWS) przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Trend Micro Web Security (TMWS).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Trend Micro Web Security (TMWS), wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
    1. **[Konfigurowanie ustawień synchronizacji użytkowników i grup w usłudze Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** — Konfigurowanie ustawień synchronizacji użytkowników i grup w usłudze Azure AD
1. **[Skonfiguruj Logowanie jednokrotne w usłudze Trend Micro Web Security (TMWS)](#configure-trend-micro-web-security-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Trend Micro Web Security (TMWS)** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego identyfikatora. Te wartości można uzyskać w obszarze **Ustawienia dostawcy usług dla obszaru portalu administracyjnego platformy Azure** na ekranie **Metoda uwierzytelniania** dla usługi Azure AD z poziomu **administracji > usług katalogowych**.

1. Aplikacja Trend Micro Web Security (TMWS) oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Trend Micro Web Security (TMWS) oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa | Atrybut źródłowy|
    | --------------- | --------- |
    | sAMAccountName | User. nazwy pospolitej onpremisessamaccountname |
    | Głównej | user.userprincipalname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie rozwiązania Trend Micro Web Security (TMWS)** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Trend Micro Web Security (TMWS).

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Trend Micro Web Security (TMWS)**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Konfigurowanie ustawień synchronizacji użytkowników i grup w usłudze Azure AD

1. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory**.

1. W obszarze **Zarządzanie**kliknij pozycję **rejestracje aplikacji** a następnie kliknij pozycję Nowa aplikacja dla przedsiębiorstw w obszarze **wszystkie aplikacje** .

1. W obszarze **Zarządzanie**kliknij pozycję **Certyfikaty & wpisy tajne**.

1. W wyświetlonym obszarze wpisy tajne klienta kliknij pozycję **Nowy wpis tajny klienta**.

1. Na wyświetlonym ekranie Dodawanie wpisu tajnego klienta opcjonalnie Dodaj opis i wybierz okres ważności dla tego klucza tajnego klienta, a następnie kliknij przycisk **Dodaj**. Nowo dodany klucz tajny klienta zostanie wyświetlony w obszarze klucz tajny klienta.

1. Zapisz wartość. Później wpisz informacje do TMWS.

1. W obszarze **Zarządzanie**kliknij pozycję **uprawnienia interfejsu API**. 

1. Na ekranie uprawnienia interfejsu API, który zostanie wyświetlony, kliknij przycisk **Dodaj uprawnienie**.

1. Na karcie interfejsy API firmy Microsoft na ekranie Zażądaj uprawnień interfejsu API, który zostanie wyświetlony, kliknij przycisk **Microsoft Graph** a następnie **uprawnienia aplikacji**.

1. Znajdź i Dodaj następujące uprawnienia: 

    * Group.Read.All
    * User. Read. All

1. Kliknij pozycję **Dodaj uprawnienia**. Zostanie wyświetlony komunikat z potwierdzeniem, że ustawienia zostały pomyślnie zapisane. Nowo dodane uprawnienia pojawiają się na ekranie uprawnienia interfejsu API.

1. W obszarze wyrażanie zgody kliknij pozycję **Udziel zgody administrator na < konto administratora > (katalog domyślny)** , a następnie **tak**. Zostanie wyświetlony komunikat z informacją o pomyślnym udzieleniu zgody administratora na żądane uprawnienia.

1. Kliknij pozycję **Przegląd**. 

1. W wyświetlonym prawym okienku Zapisz identyfikator aplikacji (klienta) i identyfikator katalogu (dzierżawy). Później wpisz informacje do TMWS. Możesz również kliknąć pozycję **niestandardowe nazwy domen** w obszarze Azure **Active Directory > zarządzania** i rejestrowania nazwy domeny w okienku po prawej stronie.

## <a name="configure-trend-micro-web-security-sso"></a>Konfigurowanie logowania jednokrotnego dla zabezpieczeń sieci Web w usłudze Trend Micro

1. Zaloguj się do konsoli zarządzania TMWS i przejdź do pozycji **Administracja** > **Użytkownicy & uwierzytelniania** > **usługi katalogowej**.

1. Kliknij tutaj w górnym obszarze ekranu.

1. Na wyświetlonym ekranie Metoda uwierzytelniania kliknij pozycję **Azure AD**.

1. Kliknij przycisk **Włącz** lub **Wyłącz** , aby zdecydować, czy zezwolić użytkownikom usługi AD w organizacji na odwiedzanie witryn sieci Web za pomocą TMWS, jeśli ich dane nie są zsynchronizowane z TMWS.

    > [!NOTE]
    > Użytkownicy niezsynchronizowani z usługi Azure AD mogą być uwierzytelniani tylko za pośrednictwem znanych bram TMWS lub dedykowanego portu dla organizacji.

1. W sekcji **Ustawienia dostawcy tożsamości** wykonaj następujące czynności:

    a. W polu **adres URL usługi** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal

    b. W polu **atrybut nazwy logowania** wklej nazwę wystąpienia użytkownika z atrybutem źródło **User. nazwy pospolitej onpremisessamaccountname** z Azure Portal.

    c. W polu **publiczny certyfikat SSL** Użyj pobranego **certyfikatu (Base64)** z Azure Portal.

1. W sekcji **Ustawienia synchronizacji** wykonaj następujące czynności:

    a. W polu **dzierżawca** Użyj **identyfikatora katalogu (dzierżawy)** lub **niestandardowej wartości nazwy domeny** z Azure Portal.

    b. W polu **Identyfikator aplikacji** wartość **identyfikatora aplikacji (klienta)** z Azure Portal.

    c. W polu **klucz tajny klienta** Użyj **klucza tajnego klienta** z Azure Portal.

    d. W polu **harmonogram synchronizacji** wybierz opcję synchronizacji z usługą Azure AD ręcznie lub zgodnie z harmonogramem. Jeśli wybierzesz opcję ręcznie, za każdym razem, gdy istnieją zmiany Active Directory informacji o użytkowniku, pamiętaj, aby powrócić do ekranu usług katalogowych i przeprowadzić ręczną synchronizację, tak aby informacje w TMWS pozostawały aktualne.

    e. Kliknij przycisk **Testuj połączenie** , aby sprawdzić, czy można pomyślnie nawiązać połączenie z usługą Azure AD. 
    
    f. Kliknij przycisk **Zapisz**.
 
 > [!NOTE]
 > Aby uzyskać więcej informacji na temat konfigurowania usługi Trend Micro Web Security w usłudze Azure AD, zapoznaj się z [tym](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) dokumentem.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

Po pomyślnym skonfigurowaniu usługi Azure AD i wybraniu w usłudze Azure AD jako metody uwierzytelniania użytkownika możesz zalogować się do serwera proxy TMWS, aby zweryfikować konfigurację. Po zweryfikowaniu konta przez logowanie za pomocą usługi Azure AD możesz odwiedzić Internet.

> [!NOTE]
> TMWS nie obsługuje testowania logowania jednokrotnego z poziomu portalu usługi Azure AD, w obszarze przegląd > Logowanie jednokrotne > skonfigurować Logowanie jednokrotne przy użyciu protokołu SAML > dla nowej aplikacji dla przedsiębiorstw.

1. Wyczyść przeglądarkę wszystkich plików cookie, a następnie uruchom ponownie przeglądarkę. 

1. Wskaż przeglądarkę serwerowi proxy TMWS. Aby uzyskać szczegółowe informacje, zobacz [przekazywanie ruchu przy użyciu plików PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Odwiedź witrynę internetową. TMWS przekieruje Cię do portalu TMWS.

1. Określ konto Active Directory (format: domain\sAMAccountName lub sAMAccountName@domain) lub adres e-mail lub nazwę UPN, a następnie kliknij przycisk **Zaloguj**się. TMWS wysyła do logowania do usługi Azure AD.

1. Na stronie logowania do usługi Azure AD wpisz poświadczenia konta usługi AD. Należy pomyślnie zalogować się do TMWS.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Trend Micro Web Security (TMWS) z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić program Trend Micro Web Security (TMWS) z zaawansowanymi możliwościami widoczności i kontroli](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

