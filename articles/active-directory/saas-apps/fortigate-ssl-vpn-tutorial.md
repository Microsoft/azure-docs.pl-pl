---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą FortiGate SSL sieci VPN | Microsoft Docs'
description: Informacje o krokach, które należy wykonać, aby zintegrować sieci VPN FortiGate SSL z usługą Azure Active Directory (Azure AD).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 187903bfbf75ada45b9a539acd1157dfe730747a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331118"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą FortiGate SSL sieci VPN

W tym samouczku dowiesz się, jak zintegrować sieci VPN FortiGate SSL z usługą Azure Active Directory (Azure AD). W przypadku integrowania sieci VPN FortiGate SSL z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto może uzyskać dostęp do sieci VPN SSL FortiGate.
* Zezwól użytkownikom na automatyczne logowanie do FortiGate sieci VPN SSL przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja sieci VPN FortiGate SSL z włączonym logowaniem jednokrotnym (SSO).

## <a name="tutorial-description"></a>Opis samouczka

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

FortiGate SSL sieci VPN obsługuje logowanie jednokrotne zainicjowane przez usługę SP.

Po skonfigurowaniu sieci VPN FortiGate SSL można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>Dodawanie sieci VPN FortiGate SSL z galerii

Aby skonfigurować integrację sieci VPN FortiGate SSL z usługą Azure AD, musisz dodać sieć VPN FortiGate SSL z galerii do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź **Fortigate SSL sieci VPN** w polu wyszukiwania.
1. Wybierz pozycję **FORTIGATE SSL sieci VPN** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla sieci VPN FortiGate SSL

Należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą sieci VPN FortiGate SSL przy użyciu użytkownika testowego o nazwie B. Simon. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w sieci VPN FortiGate SSL.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu sieci VPN FortiGate SSL, należy wykonać następujące czynności wysokiego poziomu:

1. **[Skonfiguruj Logowanie jednokrotne w usłudze Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
    1. **[Udziel użytkownikowi testowemu dostępu](#grant-access-to-the-test-user)** do usługi Azure AD Logowanie jednokrotne dla tego użytkownika.
1. **[Skonfiguruj logowanie JEDNOkrotne SSL sieci VPN Fortigate](#configure-fortigate-ssl-vpn-sso)** na stronie aplikacji.
    1. **Utwórz użytkownika testowego sieci VPN FORTIGATE SSL** jako odpowiednik do reprezentacji usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-single-sign-on)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **sieci VPN Fortigate SSL** w sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz przycisk ołówek dla **podstawowej konfiguracji SAML** , aby edytować ustawienia:

   ![Zrzut ekranu pokazujący przycisk ołówka służący do edytowania podstawowej konfiguracji języka SAML.](common/edit-urls.png)

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wprowadź następujące wartości:

    a. W polu **adres URL logowania** wprowadź adres URL we wzorcu `https://<FQDN>/remote/login` .

    b. W polu **Identyfikator** wprowadź adres URL we wzorcu `https://<FQDN>/remote/saml/metadata` .

    c. W polu **adres URL odpowiedzi** wprowadź adres URL we wzorcu `https://<FQDN>/remote/saml/login` .

    d. W polu **adres URL wylogowywania** wprowadź adres URL we wzorcu `https://<FQDN>/remote/saml/logout` .

    > [!NOTE]
    > Te wartości są tylko wzorcami. Musisz użyć rzeczywistego **adresu URL logowania**, **identyfikatora**, **adresu URL odpowiedzi**i **adresu URL wylogowywania**. Aby uzyskać rzeczywiste wartości, skontaktuj się z [zespołem obsługi klienta sieci VPN FORTIGATE SSL](mailto:tac_amer@fortinet.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja sieci VPN FortiGate SSL oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu, na którym są wyświetlane domyślne atrybuty.](common/default-attributes.png)

1. W poniższej tabeli przedstawiono dwa dodatkowe oświadczenia wymagane przez sieć VPN FortiGate SSL. Nazwy tych oświadczeń muszą być zgodne z nazwami użytymi w sekcji **Konfiguracja wiersza polecenia Fortigate** w tym samouczku. 

   | Nazwa |  Atrybut źródłowy|
   | ------------ | --------- |
   | nazwa użytkownika | user.userprincipalname |
   | group | User. Groups |
   
   Aby utworzyć te dodatkowe oświadczenia:
   
   1. Obok pozycji **atrybuty użytkownika & oświadczenia**wybierz pozycję **Edytuj**.
   1. Wybierz pozycję **Dodaj nowe**zgłoszenie.
   1. W obszarze **Nazwa wprowadź nazwę** **użytkownika**.
   1. Dla **atrybutu Source**wybierz pozycję **User. userPrincipalName**.
   1. Wybierz pozycję **Zapisz**.
   1. Wybierz pozycję **Dodaj zgłoszenie do grupy**.
   1. Wybierz pozycję **Wszystkie grupy**.
   1. Seect **Dostosuj nazwę** pola wyboru.
   1. W obszarze **Nazwa**wprowadź polecenie **Grupuj**.
   1. Wybierz pozycję **Zapisz**.   

1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz łącze **pobierania** obok pozycji **certyfikat (base64)** , aby pobrać certyfikat i zapisać go na komputerze:

    ![Zrzut ekranu przedstawiający link pobierania certyfikatu.](common/certificatebase64.png)

1. W sekcji **Konfigurowanie FORTIGATE SSL sieci VPN** skopiuj odpowiedni adres URL lub adresy URL zgodnie z wymaganiami:

    ![Zrzut ekranu pokazujący adresy URL konfiguracji.](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**. Wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące czynności:
   1. W polu **Nazwa** wprowadź wartość **B. Simon**.  
   1. W polu **Nazwa użytkownika** wprowadź \<username> @ \<companydomain> . \<extension> . Na przykład `B.Simon@contoso.com`.
   1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość wyświetlaną w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

#### <a name="grant-access-to-the-test-user"></a>Udzielanie dostępu użytkownikowi testowemu

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie temu użytkownikowi dostępu do FortiGate SSL sieci VPN.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **FORTIGATE SSL sieci VPN**.
1. Na stronie Przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**:

   ![Zrzut ekranu pokazujący opcję Użytkownicy i grupy.](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** :

    ![Zrzut ekranu pokazujący przycisk Dodaj użytkownika.](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** na liście **Użytkownicy** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

#### <a name="create-a-security-group-for-the-test-user"></a>Utwórz grupę zabezpieczeń dla użytkownika testowego

W tej sekcji utworzysz grupę zabezpieczeń w Azure Active Directory dla użytkownika testowego. FortiGate będzie używać tej grupy zabezpieczeń w celu przyznania użytkownikom dostępu do sieci za pośrednictwem sieci VPN.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**. Następnie wybierz pozycję **grupy**.
1. Wybierz pozycję **Nowa grupa** w górnej części ekranu.
1. We właściwościach **nowej grupy** wykonaj następujące czynności:
   1. Z listy **Typ grupy** wybierz pozycję **zabezpieczenia**.
   1. W polu **Nazwa grupy** wpisz **FortiGateAccess**.
   1. W polu **Opis grupy** wprowadź wartość **Grupa, aby udzielić dostępu do sieci VPN Fortigate**.
   1. Aby **można było przypisać role usługi Azure AD do ustawień grupy (wersja zapoznawcza)** , wybierz pozycję **nie**.
   1. W polu **Typ członkostwa** wybierz pozycję **przypisano**.
   1. W obszarze **Członkowie**wybierz pozycję **nie wybrano żadnych członków**.
   1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy **Użytkownicy** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
   1. Wybierz przycisk **Utwórz**.
1. Po powrocie do sekcji **grupy** w Azure Active Directory Znajdź grupę **dostępu Fortigate** i zanotuj **Identyfikator obiektu**. Będzie ona potrzebna później.

### <a name="configure-fortigate-ssl-vpn-sso"></a>Konfigurowanie logowania jednokrotnego w sieci VPN FortiGate SSL

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Przekazywanie certyfikatu protokołu SAML Base64 do urządzenia FortiGate

Po zakończeniu konfiguracji protokołu SAML aplikacji FortiGate w dzierżawie został pobrany certyfikat SAML szyfrowany algorytmem Base64. Musisz przekazać ten certyfikat do urządzenia FortiGate:

1. Zaloguj się do portalu zarządzania urządzenia FortiGate.
1. W lewym okienku wybierz pozycję **system**.
1. W obszarze **system**wybierz pozycję **Certyfikaty**.
1. Wybierz pozycję **Importuj**  >  **certyfikat zdalny**.
1. Przejdź do certyfikatu pobranego ze wdrożenia aplikacji FortiGate w dzierżawie platformy Azure, wybierz go, a następnie wybierz **przycisk OK**.

Po przekazaniu certyfikatu Zanotuj jego nazwę w obszarze Certyfikaty **systemowe**  >  **Certificates**  >  **certyfikat zdalny**. Domyślnie zostanie nazwany REMOTE_Cert_*n*, gdzie *N* jest wartością całkowitą.

#### <a name="complete-fortigate-command-line-configuration"></a>Ukończ konfigurację wiersza polecenia FortiGate

Poniższe kroki wymagają skonfigurowania adresu URL wylogowywania platformy Azure. Ten adres URL zawiera znak zapytania (?). Należy wykonać określone kroki, aby pomyślnie przesłać ten znak. Nie można wykonać tych kroków z konsoli interfejsu wiersza polecenia FortiGate. Zamiast tego Ustanów sesję SSH z urządzeniem FortiGate przy użyciu narzędzia, takiego jak. Jeśli urządzenie FortiGate jest maszyną wirtualną platformy Azure, możesz wykonać następujące kroki z konsoli szeregowej dla maszyn wirtualnych platformy Azure.

Aby wykonać te kroki, należy zarejestrować zarejestrowane wcześniej wartości:

- Identyfikator jednostki
- Adres URL odpowiedzi
- Adres URL wylogowywania
- Adres URL logowania platformy Azure
- Identyfikator usługi Azure AD
- Adres URL wylogowywania platformy Azure
- Nazwa certyfikatu SAML Base64 (REMOTE_Cert_*N*)

1. Ustanów sesję SSH z urządzeniem FortiGate i zaloguj się przy użyciu konta administratora FortiGate.
1. Uruchom następujące polecenia:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > Adres URL wylogowania platformy Azure zawiera `?` znak. Należy wprowadzić specjalną sekwencję klawiszy, aby poprawnie podać ten adres URL do konsoli szeregowej FortiGate. Ten adres URL jest zwykle `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > Aby wprowadzić adres URL wylogowywania platformy Azure w konsoli szeregowej, wprowadź wartość `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Następnie wybierz kombinację klawiszy CTRL + V i wklej resztę adresu URL, aby zakończyć wiersz: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

#### <a name="configure-fortigate-for-group-matching"></a>Konfigurowanie FortiGate na potrzeby dopasowywania grup

W tej sekcji skonfigurujesz FortiGate do rozpoznawania identyfikatora obiektu grupy zabezpieczeń zawierającej użytkownika testowego. Ta konfiguracja umożliwi FortiGateom podejmowanie decyzji o dostępie na podstawie członkostwa w grupie.

Aby wykonać te kroki, wymagany jest identyfikator obiektu grupy zabezpieczeń FortiGateAccess utworzonej wcześniej w tym samouczku.

1. Ustanów sesję SSH z urządzeniem FortiGate i zaloguj się przy użyciu konta administratora FortiGate.
1. Uruchom następujące polecenia:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>Tworzenie portali sieci VPN FortiGate i zasad zapory

W tej sekcji opisano konfigurowanie portali sieci VPN FortiGate i zasad zapory, które przyznają dostęp do grupy zabezpieczeń FortiGateAccess utworzonej wcześniej w tym samouczku.

Współpraca z [zespołem pomocy technicznej Fortigate](mailto:tac_amer@fortinet.com) w celu dodania portali sieci VPN i zasad zapory do platformy sieci VPN Fortigate. Ten krok należy wykonać przed użyciem rejestracji jednokrotnej.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka sieci VPN FortiGate SSL w panelu dostępu należy automatycznie zalogować się do sieci VPN FortiGate SSL, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Firma Microsoft i FortiGate zaleca korzystanie z platformy Fortinet VPN Client, FortiClient w celu uzyskania najlepszego środowiska użytkownika końcowego.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integracji aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj FortiGate SSL sieci VPN z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
