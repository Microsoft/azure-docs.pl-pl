---
title: 'Samouczek: integracja Azure Active Directory logowania jednokrotnego (SSO) z łącznikiem SAML programu Maverics Identity Orchestrator | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Maverics Identity SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: f4fe368e9a56e5ac4c9dfa2648ce4af15a1b5ac0
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684444"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Samouczek: integracja logowania jednokrotnego usługi Azure AD za pomocą łącznika SAML Maverics Identity Orchestrator

Usługa strata oferuje prosty sposób integracji aplikacji lokalnych z usługą Azure Active Directory (Azure AD) na potrzeby uwierzytelniania i kontroli dostępu.

W tym artykule omówiono sposób konfigurowania programu Maverics Identity Orchestrator w programie:
* Przyrostowe Migrowanie użytkowników z lokalnego systemu tożsamości do usługi Azure AD podczas logowania do starszej aplikacji lokalnej.
* Kierowanie żądań logowania ze starszego produktu do zarządzania dostępem do sieci Web, takiego jak SiteMinder urzędu certyfikacji lub Oracle Access Manager, do usługi Azure AD.
* Uwierzytelniaj użytkowników w aplikacjach lokalnych, które są chronione za pomocą nagłówków HTTP lub plików cookie związanych z sesją, po uwierzytelnieniu użytkownika w usłudze Azure AD.

Usługa strat oferuje oprogramowanie, które można wdrożyć lokalnie lub w chmurze. Pomaga w wykrywaniu, łączeniu i koordynowaniu dostawców tożsamości w celu tworzenia rozproszonego zarządzania tożsamościami dla przedsiębiorstw hybrydowych i wielochmurowych.

W tym samouczku pokazano, jak przeprowadzić migrację lokalnej aplikacji sieci Web, która jest obecnie chroniona przez starszy produkt zarządzania dostępem do sieci Web (CA SiteMinder), aby używać usługi Azure AD do uwierzytelniania i kontroli dostępu. Poniżej przedstawiono podstawowe kroki:
1. Zainstaluj program Maverics Identity Orchestrator.
2. Zarejestruj aplikację przedsiębiorstwa przy użyciu usługi Azure AD i skonfiguruj ją tak, aby korzystała z łącznika Maverics usługi Azure AD SAML dla logowania jednokrotnego (SSO) opartego na protokole SAML.
3. Integruj Maverics z SiteMinder i magazynem użytkowników protokołu Lightweight Directory Access Protocol (LDAP).
4. Skonfiguruj Magazyn kluczy platformy Azure i skonfiguruj Maverics do korzystania z niego jako dostawcy zarządzania kluczami tajnymi.
5. Zademonstrowanie migracji użytkowników i abstrakcji sesji za pomocą Maverics, aby zapewnić dostęp do lokalnej aplikacji sieci Web Java.

Dodatkowe instrukcje dotyczące instalacji i konfiguracji można znaleźć w [witrynie sieci Web strat](https://www.strata.io).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
- Subskrypcja obsługująca Logowanie jednokrotne w usłudze Orchestrator Identity Maverics. Aby uzyskać Maverics oprogramowanie, skontaktuj się z [sprzedażą strat](mailto:sales@strata.io).

## <a name="install-maverics-identity-orchestrator"></a>Instalowanie programu Maverics Identity Orchestrator

Aby rozpocząć instalację programu Orchestrator Identity Maverics, zobacz [instrukcje dotyczące instalacji](https://www.strata.io).

### <a name="system-requirements"></a>Wymagania systemowe
* Obsługiwane systemy operacyjne
  * RHEL 7+
  * CentOS 7+

* Zależności
  * usługę systemd

### <a name="installation"></a>Instalacja

1. Uzyskaj najnowszy pakiet Maverics RedHat Package Manager (RPM). Skopiuj pakiet do systemu, w którym chcesz zainstalować oprogramowanie Maverics.

2. Zainstaluj pakiet Maverics, zastępując nazwę pliku zamiast `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. Po zainstalowaniu programu Maverics będzie on uruchamiany jako usługa w ramach programu `systemd` . Aby sprawdzić, czy usługa jest uruchomiona, wykonaj następujące polecenie:

    `sudo systemctl status maverics`

Domyślnie Maverics jest instalowany w katalogu */usr/local/bin* .

Po zainstalowaniu programu Maverics domyślny plik *Maverics. YAML* zostanie utworzony w katalogu */etc/maverics* . Przed edytowaniem konfiguracji w celu dołączenia `workflows` `connectors` pliku konfiguracji będzie wyglądać następująco:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Opcje konfiguracji
### <a name="version"></a>Wersja
`version`Pole deklaruje, która wersja pliku konfiguracji jest używana. Jeśli wersja nie jest określona, zostanie użyta Najnowsza wersja konfiguracji.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` deklaruje, który adres będzie nasłuchiwał na serwerze Orchestrator. Jeśli sekcja hosta adresu jest pusta, program Orchestrator nasłuchuje na wszystkich dostępnych adresach IP emisji pojedynczej i emisji dowolnej dla systemu lokalnego. Jeśli sekcja port adresu jest pusta, numer portu jest wybierany automatycznie.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls`Pole deklaruje mapę obiektów Transport Layer Security (TLS). Obiekty TLS mogą być używane przez łączniki i serwer Orchestrator. Wszystkie dostępne opcje protokołu TLS można znaleźć w `transport` dokumentacji pakietu.

Microsoft Azure wymaga komunikacji za pośrednictwem protokołu TLS w przypadku korzystania z logowania jednokrotnego opartego na protokole SAML. Aby uzyskać informacje o generowaniu certyfikatów, przejdź do [witryny sieci Web szyfrowaniemy](https://letsencrypt.org/getting-started/).

`maverics`Klucz jest zarezerwowany dla serwera Orchestrator. Wszystkie inne klucze są dostępne i mogą być używane do wtryskiwania obiektu TLS do danego łącznika.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Pliki dołączane

Można definiować `connectors` i `workflows` w swoich własnych plikach konfiguracji i odwoływać się do nich w pliku *maverics. YAML* przy użyciu programu `includeFiles` , na przykład:

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

W tym samouczku jest stosowany jeden plik konfiguracyjny *maverics. YAML* .

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Użyj Azure Key Vault jako dostawcy kluczy tajnych

### <a name="manage-secrets"></a>Zarządzanie wpisami tajnymi

Aby ładować wpisy tajne, Maverics można zintegrować z różnymi tajnymi rozwiązaniami do zarządzania. Bieżące integracje obejmują plik, magazyn Hashicorp i Azure Key Vault. Jeśli nie zostanie określone żadne tajne rozwiązanie do zarządzania, Maverics domyślnie ładuje wpisy tajne w postaci zwykłego tekstu z pliku *Maverics. YAML* .

Aby zadeklarować wartość jako wpis tajny w pliku konfiguracji *maverics. YAML* , należy ująć wpis tajny w nawiasy ostre:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Załaduj wpisy tajne z pliku

1. Aby załadować wpisy tajne z pliku, Dodaj zmienną środowiskową `MAVERICS_SECRET_PROVIDER` do pliku */etc/maverics/maverics.env* przy użyciu polecenia:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Uruchom ponownie usługę Maverics, uruchamiając:

   `sudo systemctl restart maverics`

Zawartość pliku Secret *. YAML* można wypełnić dowolną liczbą `secrets` .

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Konfigurowanie magazynu kluczy platformy Azure

Magazyn kluczy platformy Azure można skonfigurować przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

**Korzystanie z witryny Azure Portal**
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. [Utwórz nowy magazyn kluczy](../../key-vault/general/quick-create-portal.md).
1. [Dodaj wpisy tajne do magazynu kluczy](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).
1. [Zarejestruj aplikację w usłudze Azure AD](../develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
1. [Autoryzuj aplikację do korzystania z klucza tajnego](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

**Używanie interfejsu wiersza polecenia platformy Azure**

1. Otwórz [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest), a następnie wprowadź następujące polecenie:

    ```shell
    az login
    ```

1. Utwórz nowy magazyn kluczy, uruchamiając następujące polecenie:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Aby dodać wpisy tajne do magazynu kluczy, należy uruchomić następujące polecenie:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Aby zarejestrować aplikację w usłudze Azure AD, należy uruchomić następujące polecenie:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. Autoryzuj aplikację do korzystania z wpisu tajnego, uruchamiając następujące polecenie:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Aby załadować wpisy tajne z magazynu kluczy platformy Azure, należy ustawić zmienną środowiskową `MAVERICS_SECRET_PROVIDER` w pliku */etc/maverics/maverics.env* przy użyciu poświadczeń znalezionych w *azure-credentials.js* pliku w następującym formacie:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Uruchom ponownie usługę Maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Konfigurowanie aplikacji w usłudze Azure AD dla logowania jednokrotnego opartego na protokole SAML

1. W dzierżawie usługi Azure AD przejdź do pozycji **aplikacje przedsiębiorstwa**, Wyszukaj pozycję **MAVERICS Identity Orchestrator SAML Connector**, a następnie wybierz ją.

1. W okienku **Właściwości** łącznika SAML Maverics Identity Orchestrator Ustaw **wymagane przypisanie użytkownika?** na **nie** , aby umożliwić działanie aplikacji dla nowo zmigrowanych użytkowników.

1. W okienku **Przegląd** łącznika SAML programu Maverics Identity Orchestrator wybierz pozycję **Skonfiguruj Logowanie jednokrotne**, a następnie wybierz pozycję **SAML**.

1. W okienku **Logowanie oparte na SAML** łączniku SAML programu Orchestrator Identity Maverics (usługa) Edytuj **podstawową konfigurację SAML** , wybierając przycisk **Edytuj** (ikona ołówka).

   ![Zrzut ekranu przedstawiający przycisk edycji "Podstawowa konfiguracja SAML".](common/edit-urls.png)

1. Wprowadź **Identyfikator jednostki** , wpisując adres URL w następującym formacie: `https://<SUBDOMAIN>.maverics.org` . Identyfikator jednostki musi być unikatowy w aplikacjach w dzierżawie. Zapisz wartość wprowadzoną w tym miejscu do uwzględnienia w konfiguracji Maverics.

1. Wprowadź **adres URL odpowiedzi** w następującym formacie: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

1. Wprowadź **adres URL logowania** w następującym formacie: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` . 

1. Wybierz pozycję **Zapisz**.

1. W sekcji **certyfikat podpisywania SAML** wybierz przycisk **Kopiuj** , aby skopiować **adres URL metadanych federacji aplikacji**, a następnie zapisz go na komputerze.

    ![Zrzut ekranu przedstawiający przycisk kopiowania certyfikatu podpisywania SAML.](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Konfigurowanie łącznika SAML usługi Orchestrator dla programu Maverics Identity

Program Maverics Identity Orchestrator Azure AD Connector obsługuje OpenID Connect Connect i SAML Connect. Aby skonfigurować łącznik, wykonaj następujące czynności: 

1. Aby włączyć logowanie jednokrotne oparte na protokole SAML, ustaw polecenie `authType: saml` .

1. Utwórz wartość dla `samlMetadataURL` w następującym formacie: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .

1. Zdefiniuj adres URL, do którego zostanie przekierowany z powrotem na platformę Azure w aplikacji po zalogowaniu się użytkowników przy użyciu poświadczeń platformy Azure. Użyj następującego formatu: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>` .

1. Skopiuj wartość z wcześniej skonfigurowanego EntityID: `samlEntityID: https://<SUBDOMAIN>.maverics.org` .

1. Skopiuj wartość z adresu URL odpowiedzi, który będzie używany przez usługę Azure AD do publikowania odpowiedzi SAML: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>` .

1. Wygeneruj klucz podpisywania tokenu sieci Web (JWT) JSON, który jest używany do ochrony informacji o sesji programu Orchestrator Identity Maverics, za pomocą [Narzędzia OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Skopiuj odpowiedź do `jwtSigningKey` Właściwości konfiguracji: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==` .

## <a name="attributes-and-attribute-mapping"></a>Atrybuty i mapowanie atrybutów
Mapowanie atrybutu służy do definiowania mapowania atrybutów użytkownika z lokalnego katalogu użytkownika do dzierżawy usługi Azure AD po skonfigurowaniu użytkownika.

Atrybuty określają, które dane użytkownika mogą zostać zwrócone do aplikacji w ramach żądania, przekazana do plików cookie sesji lub przekazana do aplikacji w zmiennych nagłówka HTTP.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Skonfiguruj plik YAML łącznika SAML usługi Orchestrator dla programu Maverics Identity

Konfiguracja łącznika usługi Orchestrator AD programu Maverics Identity będzie wyglądać następująco:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Migrowanie użytkowników do dzierżawy usługi Azure AD

Postępuj zgodnie z tą konfiguracją, aby stopniowo migrować użytkowników z produktu do zarządzania dostępem do sieci Web, takich jak CA SiteMinder, Oracle Access Manager lub IBM Tivoli. Można je również migrować z katalogu LDAP (Lightweight Directory Access Protocol) lub bazy danych SQL.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Konfigurowanie uprawnień aplikacji w usłudze Azure AD w celu tworzenia użytkowników

1. W dzierżawie usługi Azure AD przejdź do `App registrations` i wybierz aplikację **łącznika SAML programu Maverics Identity Orchestrator** .

1. Na **łączniku SAML Maverics Identity Orchestrator W okienku certyfikaty & wpisy tajne** wybierz pozycję, `New client secret` a następnie wybierz opcję wygaśnięcie. Wybierz przycisk **Kopiuj** , aby skopiować klucz tajny i zapisać go na komputerze.

1. Na **łączniku SAML Maverics Identity Orchestrator Okienko uprawnień interfejsu API** , wybierz pozycję **Dodaj uprawnienie** , a następnie w okienku **uprawnienia do interfejsu api żądania** wybierz pozycję **Microsoft Graph** i **uprawnienia aplikacji**. 

1. Na następnym ekranie wybierz pozycję **User. ReadWrite. All**, a następnie wybierz pozycję **Dodaj uprawnienia**. 

1. Wróć do okienka **uprawnienia interfejsu API** , a następnie wybierz pozycję **Udziel zgody administratora**.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>Skonfiguruj plik YAML łącznika SAML programu Maverics Identity Orchestrator na potrzeby migracji użytkowników

Aby włączyć przepływ pracy migracji użytkowników, Dodaj te dodatkowe właściwości do pliku konfiguracji:
1. Wprowadź **adres URL programu Azure Graph** w następującym formacie: `graphURL: https://graph.microsoft.com` .
1. Wprowadź **adres URL tokenu OAuth** w następującym formacie: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .
1. Wprowadź wcześniej wygenerowany klucz tajny klienta w następującym formacie: `oauthClientSecret: <CLIENT SECRET>` .


Końcowy plik konfiguracji łącznika programu Orchestrator Maverics Identity Azure AD będzie wyglądać następująco:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>Konfigurowanie łącznika Maverics zero dla SiteMinder

Łącznik SiteMinder służy do migrowania użytkowników do dzierżawy usługi Azure AD. Użytkownicy programu logują się do starszych aplikacji lokalnych chronionych przez usługę SiteMinder przy użyciu nowo utworzonych tożsamości i poświadczeń usługi Azure AD.

W tym samouczku SiteMinder został skonfigurowany do ochrony starszej aplikacji przy użyciu uwierzytelniania opartego na formularzach i `SMSESSION` pliku cookie. Aby przeprowadzić integrację z aplikacją, która zużywa informacje o uwierzytelnianiu i sesji za pośrednictwem nagłówków HTTP, należy dodać konfigurację emulacji nagłówka do łącznika.

Ten przykład mapuje `username` atrybut do `SM_USER` nagłówka http:

```yaml
  headers:
    SM_USER: username
```

Ustaw `proxyPass` na lokalizację, do której żądania są przekazywane przez serwer proxy. Zazwyczaj ta lokalizacja jest hostem chronionej aplikacji.

`loginPage` powinien być zgodny z adresem URL formularza logowania, który jest aktualnie używany przez SiteMinder, gdy przekierowuje użytkowników w celu uwierzytelnienia.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Konfigurowanie łącznika kodu Maverics zero dla protokołu LDAP

Gdy aplikacje są chronione przez produkt zarządzania dostępem w sieci Web (WAM), taki jak SiteMinder, tożsamości użytkowników i atrybuty są zwykle przechowywane w katalogu LDAP.

Ta konfiguracja łącznika pokazuje, jak połączyć się z katalogiem LDAP. Łącznik jest skonfigurowany jako magazyn użytkowników dla programu SiteMinder, dzięki czemu można zbierać poprawne informacje o profilu użytkownika podczas procesu przepływu pracy migracji, a w usłudze Azure AD można utworzyć odpowiedni użytkownik.

* `baseDN` Określa lokalizację w katalogu, względem której ma zostać przeprowadzone wyszukiwanie LDAP.

* `url` jest adresem i portem serwera LDAP, z którym ma zostać nawiązane połączenie.

* `serviceAccountUsername` to nazwa użytkownika, która jest używana do nawiązywania połączenia z serwerem LDAP, zwykle wyrażona jako nazwa wyróżniająca powiązania (na przykład `CN=Directory Manager` ).

* `serviceAccountPassword` jest hasłem używanym do łączenia się z serwerem LDAP. Ta wartość jest przechowywana w wcześniej skonfigurowanym wystąpieniu magazynu kluczy platformy Azure.  

* `userAttributes` definiuje listę atrybutów związanych z użytkownikiem, dla których należy wykonać zapytanie. Te atrybuty są później mapowane na odpowiednie atrybuty usługi Azure AD.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>Konfigurowanie przepływu pracy migracji

Konfiguracja przepływu pracy migracji określa, w jaki sposób Maverics migruje użytkowników z SiteMinder lub LDAP do usługi Azure AD.

Ten przepływ pracy:
- Używa łącznika SiteMinder do proxy logowania SiteMinder. Poświadczenia użytkownika są weryfikowane przy użyciu uwierzytelniania SiteMinder, a następnie przechodzą do kolejnych kroków przepływu pracy.
- Pobiera atrybuty profilu użytkownika z magazynu użytkownika SiteMinder.
- Wysyła żądanie do Microsoft Graph interfejsu API w celu utworzenia użytkownika w dzierżawie usługi Azure AD.

Aby skonfigurować przepływ pracy migracji, wykonaj następujące czynności:

1. Nadaj nazwę przepływowi pracy (na przykład **SiteMinder do migracji usługi Azure AD**).
1. Określ `endpoint` ścieżkę, która jest ścieżką http, w której jest ujawniany przepływ pracy, wyzwalając ten `actions` przepływ pracy w odpowiedzi na żądania. `endpoint`Zwykle odpowiada aplikacji, która jest serwerem proxy (na przykład `/my_app` ). Wartość musi zawierać zarówno ukośniki wiodące, jak i końcowe.
1. Dodaj odpowiednie `actions` do przepływu pracy.

   a. Zdefiniuj `login` metodę łącznika SiteMinder. Wartość łącznika musi być zgodna z wartością nazwy w konfiguracji łącznika.

   b. Zdefiniuj `getprofile` metodę dla łącznika LDAP.

   c.  Zdefiniuj `createuser` metodę łącznika AzureAD.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Weryfikowanie przepływu pracy migracji

1. Jeśli usługa Maverics nie jest już uruchomiona, należy ją uruchomić, wykonując następujące polecenie: 

   `sudo systemctl start maverics`

1. Przejdź do adresu URL logowania proxy, `http://host.company.com/my_app` .
1. Podaj poświadczenia użytkownika, które są używane do logowania się do aplikacji, gdy jest ona chroniona przez SiteMinder.
4. Przejdź do pozycji użytkownicy **domowa**  >  **| Wszyscy użytkownicy** sprawdzają, czy użytkownik został utworzony w dzierżawie usługi Azure AD.  

### <a name="configure-the-session-abstraction-workflow"></a>Konfigurowanie przepływu pracy abstrakcji sesji

Przepływ pracy abstrakcji sesji przenosi uwierzytelnianie i kontrolę dostępu dla starszej lokalnej aplikacji sieci Web do dzierżawy usługi Azure AD.

Łącznik Azure używa metody, `login` Aby przekierować użytkownika do adresu URL logowania, przy założeniu, że sesja nie istnieje.

Po uwierzytelnieniu token sesji utworzony w wyniku jest przesyłany do Maverics. Metoda łącznika SiteMinder `emulate` jest używana do emulowania sesji opartej na plikach cookie lub sesji opartej na nagłówkach, a następnie dekorować żądanie przy użyciu wszelkich dodatkowych atrybutów wymaganych przez aplikację.

1. Nadaj nazwę przepływowi pracy (na przykład **abstrakcję sesji SiteMinder**).
1. Określ `endpoint` , który odnosi się do aplikacji, która jest serwerem proxy. Wartość musi zawierać zarówno ukośniki wiodące, jak i końcowe (na przykład `/my_app/` ).
1. Dodaj odpowiednie `actions` do przepływu pracy.

   a. Zdefiniuj `login` metodę łącznika platformy Azure. `connector`Wartość musi być zgodna z `name` wartością w konfiguracji łącznika.

   b. Zdefiniuj `emulate` metodę łącznika SiteMinder.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Weryfikowanie przepływu pracy abstrakcji sesji

1. Przejdź do adresu URL aplikacji serwera proxy, `https://<AZURECOMPANY.COM>/<MY_APP>` . 
    
    Nastąpi przekierowanie do strony logowania serwera proxy.

1. Wprowadź poświadczenia użytkownika usługi Azure AD.

   Należy przekierować do aplikacji, tak jakby były uwierzytelniane bezpośrednio przez SiteMinder.