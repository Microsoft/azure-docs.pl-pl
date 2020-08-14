---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu łącznika SAML Maverics Identity Orchestrator | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Maverics Identity SAML Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9cad791f-8746-4584-bf4e-e281b709fb2b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e923e051116b70b2db35d1ac710a40941305aae
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214325"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą łącznika SAML programu Maverics Identity Orchestrator

## <a name="introduction"></a>Wprowadzenie

Usługa strat zapewnia prosty sposób integracji aplikacji lokalnych z usługą Azure AD na potrzeby uwierzytelniania i kontroli dostępu.

W tym przewodniku przedstawiono sposób konfigurowania programu Maverics Identity Orchestrator &trade; w następujący sposób:
* Przyrostowe Migrowanie użytkowników z lokalnego systemu tożsamości do usługi Azure AD podczas logowania do starszej aplikacji lokalnej.
* Kierowanie żądań logowania ze starszego produktu do zarządzania dostępem do sieci Web, takiego jak SiteMinder urzędu certyfikacji lub Oracle Access Manager do usługi Azure AD.
* Uwierzytelniaj użytkowników w aplikacjach lokalnych, które są chronione przy użyciu nagłówków HTTP lub plików cookie związanych z sesją, po uwierzytelnieniu użytkownika w usłudze Azure AD.

Usługa strat zapewnia oprogramowanie, które wdraża lokalnie lub w chmurze, aby wykrywać, łączyć i organizować dostawców tożsamości w celu tworzenia rozproszonego zarządzania tożsamościami dla przedsiębiorstw hybrydowych i wielochmurowych.

Ten samouczek pokazuje, jak przeprowadzić migrację lokalnej aplikacji sieci Web, która jest obecnie chroniona przez starszy produkt zarządzania dostępem do sieci Web (CA SiteMinder) do korzystania z usługi Azure AD na potrzeby uwierzytelniania i kontroli dostępu.
1. Instalowanie programu Maverics Identity Orchestrator&trade;
2. Zarejestruj aplikację przedsiębiorstwa za pomocą usługi Azure AD i skonfiguruj ją tak, aby korzystała z łącznika Maverics usługi Azure AD SAML &trade; dla logowania jednokrotnego na podstawie protokołu SAML.
3. Integruj Maverics z SiteMinder i magazynem użytkowników LDAP.
4. Skonfiguruj Azure Key Vault i skonfiguruj Maverics, aby używać go jako dostawcy zarządzania kluczami tajnymi.
5. Zademonstrowanie migracji użytkowników i abstrakcji sesji za pomocą Maverics, aby zapewnić dostęp do lokalnej aplikacji sieci Web w języku Java.

Dodatkowe instrukcje dotyczące instalacji i konfiguracji można znaleźć na stronie https://strata.io/docs

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
- Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Orchestrator Identity Maverics. Aby uzyskać oprogramowanie Maverics, skontaktuj się z sales@strata.io

## <a name="install-maverics-identity-orchestratortrade"></a>Instalowanie programu Maverics Identity Orchestrator&trade;

Aby rozpocząć instalację programu Maverics Identity Orchestrator, zapoznaj się z instrukcjami dotyczącymi instalacji w witrynie https://strata.io/docs

## <a name="system-requirements"></a>Wymagania systemowe
### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>Zależności
* usługę systemd

## <a name="installation"></a>Instalacja

1. Uzyskaj najnowszy pakiet Maverics RPM. Skopiuj pakiet do systemu, w którym chcesz zainstalować oprogramowanie Maverics.

2. Zainstaluj pakiet Maverics, zastępując nazwę pliku zamiast `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. Po zainstalowaniu Maverics będzie działać jako usługa w ramach programu `systemd` . Aby sprawdzić, czy usługa jest uruchomiona, uruchom następujące polecenie.

    `sudo systemctl status maverics`

Domyślnie Maverics jest instalowany w `/usr/local/bin` katalogu.

Po zainstalowaniu Maverics `maverics.yaml` plik domyślny zostanie utworzony w `/etc/maverics` katalogu. Przed edytowaniem konfiguracji w celu dołączenia `workflows` `connectors` pliku konfiguracji będzie wyglądać następująco:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>Opcje konfiguracji
### <a name="version"></a>Wersja
`version`Pole deklaruje, która wersja pliku konfiguracji jest używana. Jeśli nie zostanie określony, zostanie użyta Najnowsza wersja konfiguracji.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Adres nasłuchiwania
`listenAddress` deklaruje adres, na którym będzie nasłuchiwać usługa Orchestrator. Jeśli sekcja hosta adresu jest pusta, program Orchestrator nasłuchuje na wszystkich dostępnych adresach IP emisji pojedynczej i emisji dowolnej dla systemu lokalnego. Jeśli sekcja port adresu jest pusta, automatycznie wybierany jest numer portu.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls`Pole deklaruje mapę obiektów zabezpieczeń warstwy transportowej. Obiekty TLS mogą być używane przez łączniki, a także serwer Orchestrator. Wszystkie dostępne opcje protokołu TLS można znaleźć w `transport` dokumentacji pakietu.

Microsoft Azure wymaga komunikacji za pośrednictwem protokołu TLS w przypadku korzystania z logowania jednokrotnego opartego na protokole SAML, aby uzyskać więcej informacji [na temat generowania](https://letsencrypt.org/getting-started/) certyfikatów.

`maverics`Klucz jest zarezerwowany dla serwera Orchestrator. Wszystkie inne klucze są dostępne i mogą być używane do wtryskiwania obiektu TLS do danego łącznika.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Pliki dołączane

`connectors` i `workflows` można je zdefiniować w własnych, oddzielnych plikach konfiguracji i przywoływanych przez `maverics.yaml` użycie `includeFiles` na poniższym przykładzie.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

Ten samouczek używa jednego `maverics.yaml` pliku konfiguracji.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>Używanie Azure Key Vault jako dostawcy kluczy tajnych

### <a name="secret-management"></a>Zarządzanie kluczami tajnymi

Maverics jest w stanie zintegrować z różnymi tajnymi rozwiązaniami do zarządzania w celu załadowania wpisów tajnych. Bieżące integracje obejmują plik, magazyn Hashicorp i Azure Key Vault. Jeśli nie zostanie określone żadne tajne rozwiązanie do zarządzania, Maverics będzie domyślnie ładować wpisy tajne w postaci zwykłego tekstu `maverics.yaml` .
Aby zadeklarować wartość jako wpis tajny w `maverics.yaml` pliku konfiguracji, zawiń wpis tajny przy użyciu nawiasów ostrych:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>Plik

Aby załadować wpisy tajne z pliku, Dodaj do pliku zmienną środowiskową `MAVERICS_SECRET_PROVIDER`  `/etc/maverics/maverics.env` z:

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

Następnie uruchom ponownie usługę maverics: `sudo systemctl restart maverics`

`secrets.yaml`Zawartość pliku może być uzupełniona dowolną liczbą `secrets` .
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Poniższe kroki pokazują, jak skonfigurować Azure Key Vault przy użyciu [Azure Portal](https://portal.azure.com) lub przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest):

1. [Zaloguj](https://portal.azure.com) się przy użyciu Azure Portal lub przy użyciu interfejsu wiersza polecenia:
    ```shell
    az login
    ```

2. [Utwórz nowy magazyn](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)lub użyj polecenia CLI:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Dodaj wpisy tajne do Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)lub przy użyciu polecenia CLI:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Zarejestruj aplikację za pomocą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)lub przy użyciu polecenia CLI:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [Autoryzuj aplikację, aby używała klucza tajnego](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault), lub za pomocą polecenia CLI:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Aby załadować wpisy tajne z magazynu kluczy platformy Azure, należy ustawić w pliku zmienną środowiskową `MAVERICS_SECRET_PROVIDER` `/etc/maverics/maverics.env` z poświadczeniami znalezionymi w azure-credentials.jspliku, używając następującego wzorca: `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

Następnie uruchom ponownie usługę maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Konfigurowanie aplikacji w usłudze Azure AD dla logowania jednokrotnego opartego na protokole SAML

1. W dzierżawie Azure Active Directory przejdź do `Enterprise applications` , Wyszukaj `Maverics Identity Orchestrator SAML Connector` i wybierz ją.

2. Na stronie "łącznik SAML usługi Orchestrator Identity Maverics" | Na stronie właściwości ustaw wartość `User assignment required?` nie, aby umożliwić aplikacji nowo migrowanym użytkownikom.

3. Na stronie "łącznik SAML usługi Orchestrator Identity Maverics" | Na stronie Przegląd wybierz pozycję, `Setup single sign-on` a następnie wybierz pozycję `SAML` .

4. Na stronie "łącznik SAML usługi Orchestrator Identity Maverics" | Logowanie oparte na protokole SAML, Edytuj podstawową konfigurację języka SAML.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

5. Ustaw `Entity ID` wpisanie adresu URL przy użyciu następującego wzorca: `https://<SUBDOMAIN>.maverics.org` . `Entity ID`Musi być unikatowa w aplikacjach w dzierżawie. Zapisz wartość wprowadzoną w tym miejscu do uwzględnienia w konfiguracji Maverics.

6. Ustaw adres URL odpowiedzi, używając następującego wzorca: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

7. Ustaw adres URL logowania, używając następującego wzorca: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` i kliknij przycisk Zapisz.

8. Przejdź do sekcji certyfikat podpisywania SAML, a następnie kliknij przycisk Kopiuj, aby skopiować adres URL metadanych federacji aplikacji i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Konfiguracja łącznika SAML usługi Orchestrator w programie Maverics Identity

Łącznik Maverics Identity Orchestrator Azure AD Connector obsługuje: 
- OpenID Connect
- Połączenie SAML 

1. Aby włączyć logowanie jednokrotne oparte na protokole SAML, ustaw polecenie `authType: saml` .

1. Utwórz wartość dla `samlMetadataURL` : `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. Teraz Zdefiniuj adres URL, na który na platformie Azure zostanie przekierowana zwrot z powrotem do aplikacji po zalogowaniu się przy użyciu poświadczeń platformy Azure.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. Skopiuj wartość z EntityID skonfigurowanego powyżej: `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Skopiuj wartość z adresu URL odpowiedzi, który będzie używany przez usługę Azure AD do publikowania odpowiedzi SAML.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. Wygeneruj klucz podpisywania JWT, używany do ochrony informacji o sesji programu Orchestrator Identity Maverics &trade; , za pomocą [Narzędzia OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Skopiuj odpowiedź do `jwtSigningKey` Właściwości config: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>Atrybuty i mapowanie atrybutów
Mapowanie atrybutu służy do definiowania mapowania atrybutów użytkownika z lokalnego katalogu użytkownika do usługi Azure AD, gdy użytkownicy są obsługiwani.

Atrybuty określają dane użytkownika, które mogą zostać zwrócone do aplikacji w ramach żądania, przekazana do plików cookie sesji lub przekazana do aplikacji w zmiennych nagłówka HTTP.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Konfigurowanie Maverics Identity Orchestrator Azure AD łącznik SAML Connector YAML

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

## <a name="migrate-users-to-azure-ad"></a>Migrowanie użytkowników do usługi Azure AD

Postępuj zgodnie z tą konfiguracją, aby stopniowo migrować użytkowników z produktu do zarządzania dostępem do sieci Web, takiego jak CA SiteMinder, Oracle Access Manager lub IBM Tivoli; katalog LDAP; lub bazy danych SQL.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Konfigurowanie uprawnień aplikacji w usłudze Azure AD w celu tworzenia użytkowników

1. W dzierżawie Azure Active Directory przejdź do, `App registrations` a następnie wybierz aplikację "Maverics Identity Orchestrator SAML Connector".

2. Na stronie "łącznik SAML usługi Orchestrator Identity Maverics" | Certyfikaty & wpisy tajne, wybierz pozycję, `New client secret` a następnie wybierz opcję wygaśnięcie. Kliknij przycisk Kopiuj, aby skopiować klucz tajny i zapisać go na komputerze.

3. Na stronie "łącznik SAML usługi Orchestrator Identity Maverics" | Uprawnienia interfejsu API wybierz pozycję, `Add permission` a następnie w obszarze uprawnienia do interfejsu API żądania wybierz pozycję `Microsoft Graph` , a następnie `Application permissions` . Na następnym ekranie wybierz pozycję, `User.ReadWrite.All` a następnie wybierz `Add permissions` . Spowoduje to powrót do uprawnień interfejsu API `Grant admin consent` .


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>Skonfiguruj YAML łącznika SAML programu Orchestrator Identity Maverics na potrzeby migracji użytkowników

Aby włączyć przepływ pracy migracji użytkowników, Dodaj te dodatkowe właściwości do pliku konfiguracji:
1. Ustaw adres URL programu Azure Graph: `graphURL: https://graph.microsoft.com`
1. Ustaw adres URL tokenu OAuth następujący po wzorcu: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. Ustaw klucz tajny klienta wygenerowany powyżej: `oauthClientSecret: <CLIENT SECRET>`


Końcowa konfiguracja łącznika usługi Orchestrator Azure AD programu Maverics Identity będzie wyglądać następująco:
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

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>Konfigurowanie łącznika Maverics zero &trade; dla SiteMinder

Łącznik SiteMinder służy do migrowania użytkowników do usługi Azure AD i logowania użytkowników do starszych aplikacji lokalnych chronionych przez usługę SiteMinder przy użyciu nowo utworzonych tożsamości i poświadczeń usługi Azure AD.

W tym samouczku SiteMinder został skonfigurowany do ochrony starszej aplikacji za pomocą uwierzytelniania opartego na formularzach i używania `SMSESSION` pliku cookie. Aby przeprowadzić integrację z aplikacją, która korzysta z uwierzytelniania i sesji za pośrednictwem nagłówków HTTP, należy dodać konfigurację emulacji nagłówka do łącznika.

Ten przykład mapuje `username` atrybut do `SM_USER` nagłówka http:
```yaml
  headers:
    SM_USER: username
```

Ustaw wartość `proxyPass` na lokalizację, do której żądania są przekazywane przez serwer proxy. Zazwyczaj jest to host chronionej aplikacji.

`loginPage` powinien być zgodny z adresem URL formularza logowania, który jest aktualnie używany przez SiteMinder podczas przekierowywania użytkowników do uwierzytelniania.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>Konfigurowanie łącznika kodu Maverics zero &trade; dla protokołu LDAP

Gdy aplikacje są chronione przez produkt Menedżera WAM, taki jak SiteMinder, tożsamości użytkowników i atrybuty są zwykle przechowywane w katalogu LDAP.

Ta konfiguracja łącznika pokazuje, jak połączyć się z katalogiem LDAP skonfigurowanym jako magazyn użytkowników dla programu SiteMinder, aby można było zbierać poprawne informacje o profilu użytkownika podczas procesu migracji, a odpowiedni użytkownik może zostać utworzony w usłudze Azure AD.

* `baseDN` Określa lokalizację w katalogu, względem której ma zostać przeprowadzone wyszukiwanie LDAP.

* `url` jest adresem i portem serwera LDAP, z którym ma zostać nawiązane połączenie.

* `serviceAccountUsername` to nazwa użytkownika używana do nawiązywania połączenia z serwerem LDAP, zwykle wyrażona jako nazwa wyróżniająca powiązania, na przykład `CN=Directory Manager` .

* `serviceAccountPassword` hasło używane do nawiązywania połączenia z serwerem LDAP. Ta wartość jest przechowywana w wcześniej skonfigurowanym wystąpieniu Azure Key Vault.  

* `userAttributes` definiuje listę atrybutów związanych z użytkownikiem, dla których ma zostać wyszukiwane zapytanie. Te atrybuty są później mapowane na odpowiednie atrybuty usługi Azure AD.

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

## <a name="configure-the-migration-workflow"></a>Konfigurowanie przepływu pracy migracji

Konfiguracja przepływu pracy migracji określa, w jaki sposób Maverics będzie migrować użytkowników z SiteMinder/LDAP do usługi Azure AD.

Ten przepływ pracy:
- Używa łącznika SiteMinder do proxy logowania SiteMinder. Poświadczenia użytkownika są weryfikowane przy użyciu uwierzytelniania SiteMinder, a następnie przechodzą do kolejnych kroków przepływu pracy.
- Pobiera atrybuty profilu użytkownika z magazynu użytkownika SiteMinder.
- Wysyła żądanie do Microsoft Graph interfejsu API w celu utworzenia użytkownika w dzierżawie usługi Azure AD.

Kroki:
1. Nadaj nazwę przepływowi pracy, np. SiteMinder do migracji do usługi Azure AD.
2. Określ `endpoint` ścieżkę, która jest ścieżką http, w której jest narażony przepływ pracy, który wyzwala ten `actions` przepływ pracy w odpowiedzi na żądania. `endpoint`Zazwyczaj odnosi się do aplikacji, która jest serwerem proxy, np. `/my_app` . Wartość musi zawierać zarówno ukośniki wiodące, jak i końcowe.
3. Dodaj odpowiednie `actions` do przepływu pracy.
    - Zdefiniuj `login` metodę łącznika SiteMinder. Wartość łącznika musi być zgodna z wartością nazwy w konfiguracji łącznika.
     - Zdefiniuj `getprofile` metodę dla łącznika LDAP.
     - Zdefiniuj `createuser` dla łącznika AzureAD.

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

1. Jeśli usługa Maverics nie jest już uruchomiona, należy ją uruchomić, wykonując następujące polecenie: `sudo systemctl start maverics`

2. Przejdź do adresu URL logowania proxy: `http://host.company.com/my_app` .
3. Podaj poświadczenia użytkownika służące do logowania do aplikacji, gdy są chronione przez SiteMinder.
4. Przejdź do strony głównej > użytkownicy | Wszyscy użytkownicy sprawdzają, czy użytkownik został utworzony w dzierżawie usługi Azure AD.  

## <a name="configure-the-session-abstraction-workflow"></a>Konfigurowanie przepływu pracy abstrakcji sesji

Przepływ pracy abstrakcji sesji przenosi uwierzytelnianie i kontrolę dostępu dla starszej lokalnej aplikacji sieci Web do usługi Azure AD.

Łącznik Azure używa metody, `login` Aby przekierować użytkownika do adresu URL logowania, przy założeniu, że nie istnieje sesja.

Po uwierzytelnieniu token sesji utworzony w wyniku jest przesyłany do Maverics, a metoda łącznika SiteMinder `emulate` jest używana do emulowania sesji opartej na plikach cookie i/lub sesji opartej na formacie, a następnie dekorować żądania z wszelkimi dodatkowymi atrybutami wymaganymi przez aplikację.

1. Nadaj nazwę przepływowi pracy, np. abstrakcję sesji SiteMinder.
2. Określ `endpoint` , który odnosi się do aplikacji będącej serwerem proxy. Wartość musi zawierać zarówno wiodące, jak i końcowe ukośniki, np. `/my_app/` .
3. Dodaj odpowiednie `actions` do przepływu pracy.
    - Zdefiniuj `login` metodę łącznika platformy Azure. `connector`Wartość musi być zgodna z `name` wartością w konfiguracji łącznika.
    - Zdefiniuj `emulate` metodę łącznika SiteMinder.

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

1. Przejdź do adresu URL aplikacji serwera proxy: `https://<AZURECOMPANY.COM>/<MY_APP>` . Użytkownik zostanie przekierowany do strony logowania serwera proxy.
2. Wprowadź poświadczenia użytkownika usługi Azure AD.
3. Użytkownik powinien zostać przekierowany do aplikacji tak, jakby został uwierzytelniony bezpośrednio przez SiteMinder.
