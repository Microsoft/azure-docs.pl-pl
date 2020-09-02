---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą FortiGate SSL sieci VPN | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i sieci VPN FortiGate SSL.
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
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299715"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą FortiGate SSL sieci VPN

W tym samouczku dowiesz się, jak zintegrować sieci VPN FortiGate SSL z usługą Azure Active Directory (Azure AD). W przypadku integrowania sieci VPN FortiGate SSL z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do sieci VPN FortiGate SSL.
* Zezwól użytkownikom na automatyczne logowanie do FortiGate sieci VPN SSL przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) sieci VPN FortiGate SSL.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* FortiGate SSL sieci VPN obsługuje protokół SSO zainicjowany przez usługę **SP**
* Po skonfigurowaniu sieci VPN FortiGate SSL można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>Dodawanie sieci VPN FortiGate SSL z galerii

Aby skonfigurować integrację sieci VPN FortiGate SSL z usługą Azure AD, musisz dodać sieć VPN FortiGate SSL z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Fortigate SSL sieci VPN** w polu wyszukiwania.
1. Wybierz pozycję **FORTIGATE SSL VPN** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla sieci VPN FortiGate SSL

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą sieci VPN FortiGate SSL przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w sieci VPN FortiGate SSL.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu sieci VPN FortiGate SSL, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania JEDNOkrotnego protokołu SSL](#configure-fortigate-ssl-vpn-sso)** w usłudze Fortigate — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **Utwórz użytkownika testowego sieci VPN FORTIGATE SSL** , aby uzyskać odpowiednik B. Simon w sieci VPN Fortigate SSL, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **sieci VPN Fortigate SSL** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<FQDN>/remote/login`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<FQDN>/remote/saml/metadata`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://><FQDN/remote/saml/login`

    d. W polu tekstowym **adres URL wylogowywania** wpisz adres URL, używając następującego wzorca: `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, identyfikatora, adresu URL odpowiedzi i adresu URL wylogowywania. Skontaktuj się z [zespołem obsługi klienta sieci VPN FORTIGATE SSL](mailto:tac_amer@fortinet.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja sieci VPN FortiGate SSL oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja sieci VPN SSL FortiGate oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | ------------ | --------- |
    | nazwa użytkownika | user.userprincipalname |
    | group | User. Groups |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Na stronie **Konfigurowanie FORTIGATE SSL sieci VPN** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do FortiGate protokołu SSL w sieci VPN.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **FORTIGATE SSL sieci VPN**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-a-security-group-for-the-test-user"></a>Utwórz grupę zabezpieczeń dla użytkownika testowego

W tej sekcji utworzysz grupę zabezpieczeń w Azure Active Directory dla użytkownika testowego. Ta grupa zabezpieczeń będzie używana przez FortiGate do udzielenia użytkownikowi dostępu do sieci za pośrednictwem sieci VPN.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **grupy**.
1. Wybierz pozycję **Nowa grupa** w górnej części ekranu.
1. We właściwościach **nowej grupy** wykonaj następujące kroki:
   1. W polu **Typ grupy** wybierz pozycję **zabezpieczenia**.
   1. W polu **Nazwa** wprowadź wartość `FortiGateAccess`.
   1. W polu **Opis grupy** wprowadź wartość `Group for granting FortiGate VPN access` .
   1. Aby **można było przypisać role usługi Azure AD do ustawień grupy (wersja zapoznawcza)** , wybierz pozycję **nie**.
   1. W polu **Typ członkostwa** wybierz pozycję **przypisano**.
   1. W obszarze **Członkowie**wybierz pozycję **nie wybrano żadnych członków**.
   1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
   1. Wybierz przycisk **Utwórz**.
1. Po powrocie do bloku **grupy** w Azure Active Directory Znajdź grupę **dostępu Fortigate** i zanotuj **Identyfikator obiektu** do późniejszego użycia.

## <a name="configure-fortigate-ssl-vpn-sso"></a>Konfigurowanie logowania jednokrotnego w sieci VPN FortiGate SSL

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Przekazywanie certyfikatu protokołu SAML Base64 do urządzenia FortiGate

Po zakończeniu konfiguracji SAML aplikacji FortiGate w dzierżawie pobrano certyfikat SAML szyfrowany algorytmem Base64. Ta wartość musi być przekazywana do urządzenia FortiGate:

1. Zaloguj się do portalu zarządzania urządzenia FortiGate.
1. W menu po lewej stronie kliknij pozycję **system**.
1. W obszarze **system**kliknij pozycję **Certyfikaty**.
1. Kliknij pozycję **Importuj**  ->  **certyfikat zdalny**.
1. Przejdź do certyfikatu pobranego ze wdrożenia aplikacji FortiGate w dzierżawie platformy Azure, wybierz go, a następnie kliknij przycisk **OK** .

Po przekazaniu certyfikatu Zanotuj jego nazwę w obszarze Certyfikaty **systemowe**  >  **Certificates**  >  **certyfikat zdalny**. Domyślnie zostanie nadana nazwa REMOTE_Cert_**n** , gdzie **n** jest wartością całkowitą.

### <a name="perform-fortigate-command-line-configuration"></a>Wykonaj FortiGate konfigurację wiersza polecenia

Poniższe kroki wymagają skonfigurowania adresu URL wylogowania platformy Azure. Ten adres URL zawiera znak zapytania (?). Specjalne kroki są wymagane do pomyślnego przesłania tego znaku. Nie można wykonać kroków z poziomu konsoli interfejsu wiersza polecenia FortiGate. Zamiast tego Ustanów sesję SSH z FortiGate applicance przy użyciu narzędzia, takiego jak. Jeśli urządzenie FortiGate jest maszyną wirtualną platformy Azure, możesz wykonać następujące kroki z konsoli szeregowej maszyny wirtualnej platformy Azure.

Aby wykonać te kroki, potrzebne są zarejestrowane wcześniej wartości:

- Identyfikator jednostki
- Adres URL odpowiedzi
- Adres URL wylogowywania
- Adres URL logowania platformy Azure
- Identyfikator usługi Azure AD
- Adres URL wylogowywania platformy Azure
- Nazwa certyfikatu protokołu SAML języka Base64 (REMOTE_Cert_N)

1. Ustanów sesję SSH z FortiGate applicance i zaloguj się przy użyciu konta administratora FortiGate.
1. Wykonaj następujące polecenia:

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
   > **Adres URL wylogowania platformy Azure** zawiera `?` znak. Należy wprowadzić specjalną sekwencję klawiszy, aby poprawnie podać adres URL w konsoli szeregowej FortiGate. Zwykle jest to adres URL `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > Aby wprowadzić adres URL wylogowywania platformy Azure w konsoli szeregowej, wprowadź wartość `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Następnie wybierz kombinację klawiszy CTRL + V i wklej resztę adresu URL, aby zakończyć wiersz: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

### <a name="configure-fortigate-for-group-matching"></a>Konfigurowanie FortiGate na potrzeby dopasowywania grup

W tej sekcji skonfigurujesz FortiGate do rozpoznawania identyfikatora obiektu grupy zabezpieczeń, w której znajduje się użytkownik testowy. Pozwoli to FortiGate na podejmowanie decyzji o dostępie na podstawie tego członkostwa w grupie.

Aby wykonać te kroki, wymagany jest identyfikator obiektu grupy zabezpieczeń **FortiGateAccess** utworzonej wcześniej

1. Ustanów sesję SSH z FortiGate Applicance i zaloguj się przy użyciu konta administratora FortiGate.
1. Wykonaj następujące polecenia:

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

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>Tworzenie portali sieci VPN FortiGate i zasad zapory

W tej sekcji skonfigurujesz FortiGate portale sieci VPN i zasady zapory zezwalające na dostęp do grupy zabezpieczeń, **FortiGateAccess** utworzone powyżej.

Współpraca z [zespołem pomocy technicznej Fortigate](mailto:tac_amer@fortinet.com) w celu dodania portali sieci VPN i zasad zapory do platformy sieci VPN Fortigate. Te kroki należy wykonać przed użyciem rejestracji jednokrotnej.

## <a name="test-single-sign-on"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka sieci VPN FortiGate SSL w panelu dostępu należy automatycznie zalogować się do sieci VPN FortiGate SSL, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Firma Microsoft i FortiGate zaleca korzystanie z klienta Fortinet VPN, FortiClient w celu uzyskania najlepszego środowiska użytkownika końcowego.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj FortiGate SSL sieci VPN z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
