---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu warstwy strat
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C za pomocą whoIam na potrzeby weryfikacji użytkownika
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 69355b58e36f435b5b5dbe94af72d55d9806b156
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557183"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Samouczek rozszerzania Azure AD B2C ochrony lokalnych aplikacji przy użyciu warstwy strat

W tym przykładowym samouczku dowiesz się, jak zintegrować program Azure Active Directory (AD) B2C z programem [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/).
Maverics Identity Orchestrator rozszerza Azure AD B2C ochrony lokalnych aplikacji. Łączy się on z dowolnym systemem tożsamości, nieprzezroczystie migruje użytkowników i poświadczenia, synchronizuje zasady i konfiguracje oraz abstrakcyjne uwierzytelnianie i zarządzanie sesjami. Korzystanie z przedsiębiorstw w warstwach strat może szybko przejść ze starszej wersji, aby Azure AD B2C bez ponownego zapisywania aplikacji. Oferuje ono następujące korzyści:

- **Pojedynczy Sign-On klienta (SSO) w lokalnych aplikacjach hybrydowych**: Azure AD B2C obsługuje logowanie jednokrotne klienta przy użyciu programu Maverics Identity Orchestrator. Użytkownicy logują się przy użyciu swoich kont hostowanych w usłudze Azure AD B2C lub dostawcy tożsamości (Social Identity Provider). Maverics rozszerza Logowanie jednokrotne do aplikacji zabezpieczonych historycznie przez starsze systemy tożsamości, takie jak Symantec SiteMinder.

- **Rozszerzając oparte na standardach Logowanie jednokrotne do aplikacji bez konieczności ponownego zapisywania**: Użyj Azure AD B2C do zarządzania dostępem użytkowników i włączania logowania jednokrotnego przy użyciu łączników SAML lub OIDC usługi Orchestrator Identity Maverics.

- **Łatwa konfiguracja**: usługa Azure AD B2C udostępnia prosty interfejs użytkownika krok po kroku służący do łączenia łączników SAML lub OIDC usługi Orchestrator Identity Maverics z Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](./tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

- Wystąpienie [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do przechowywania wpisów tajnych, które są używane przez program Maverics Identity Orchestrator. Służy do łączenia się z Azure AD B2C lub innymi dostawcami atrybutów, takimi jak katalog LDAP (Lightweight Directory Access Protocol) lub baza danych.

- Wystąpienie programu [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) zainstalowane i działające na maszynie wirtualnej platformy Azure lub wybranym przez siebie serwerze lokalnym. Aby uzyskać informacje o tym, jak uzyskać oprogramowanie i uzyskać dostęp do dokumentacji instalacji i konfiguracji, skontaktuj się z firmą [stratą](https://www.strata.io/contact/)

- Aplikacja lokalna, która przechodzi ze starszego systemu tożsamości do Azure AD B2C.

## <a name="scenario-description"></a>Opis scenariusza

Integracja z Mavericsą warstwy strat obejmuje następujące składniki:

- **Azure AD B2C**: serwer autoryzacji, który jest odpowiedzialny za Weryfikowanie poświadczeń użytkownika. Uwierzytelnieni użytkownicy mogą uzyskiwać dostęp do aplikacji lokalnych przy użyciu konta lokalnego przechowywanego w katalogu Azure AD B2C.

- **Zewnętrzna dostawcy tożsamościa społeczna lub Enterprise**: może to być dowolny Dostawca usługi OpenID Connect Connect, Facebook, Google lub GitHub. Zobacz informacje dotyczące korzystania z [zewnętrznego dostawców tożsamości](./technical-overview.md#external-identity-providers) z Azure AD B2C.  

- **Maverics Identity Orchestrator**: usługa, która organizuje Logowanie użytkowników i nieprzezroczystie przekazuje tożsamość do aplikacji za pośrednictwem nagłówków HTTP.

Na poniższym diagramie architektury przedstawiono implementację.

![Obraz przedstawia architekturę Azure AD B2C integracji z usługą strata Maverics, aby umożliwić dostęp do aplikacji hybrydowych](./media/partner-strata/strata-architecture-diagram.png)

| Kroki | Opis |
|:-------|:---------------|
| 1. | Użytkownik wysyła żądanie dostępu do aplikacji hostowanej lokalnie. Maverics Identity proxy programu Orchestrator żądania wykonane przez użytkownika do aplikacji.|
| 2. | Koordynator sprawdza stan uwierzytelniania użytkownika. Jeśli nie otrzyma tokenu sesji lub podany token sesji jest nieprawidłowy, wysyła użytkownika do Azure AD B2C na potrzeby uwierzytelniania.|
| 3. | Azure AD B2C wysyła żądanie uwierzytelnienia do skonfigurowanego dostawcy tożsamości społecznościowego.|
| 4. | Dostawcy tożsamości wzywa użytkownika do poświadczeń. W zależności od dostawcy tożsamości użytkownik może wymagać uwierzytelniania wieloskładnikowego (MFA).|
| 5. | Dostawcy tożsamości wysyła odpowiedź uwierzytelniania z powrotem do Azure AD B2C. Opcjonalnie podczas tego kroku użytkownik może utworzyć konto lokalne w katalogu Azure AD B2C.|
| 6. | Azure AD B2C wysyła żądanie użytkownika do punktu końcowego określonego podczas rejestracji aplikacji programu Orchestrator w dzierżawie Azure AD B2C.|
| 7. | Program Orchestrator szacuje zasady dostępu i oblicza wartości atrybutów do uwzględnienia w nagłówkach HTTP przesyłanych do aplikacji. W tym kroku Koordynator może wywoływać dodatkowych dostawców atrybutów, aby pobrać informacje konieczne do poprawnego ustawienia wartości nagłówka. Koordynator ustawia wartości nagłówka i wysyła żądanie do aplikacji.|
| 8. | Użytkownik jest teraz uwierzytelniony i ma dostęp do aplikacji.|

## <a name="get-maverics-identity-orchestrator"></a>Pobierz Maverics Identity Orchestrator
Aby uzyskać oprogramowanie, którego będziesz używać do integrowania starszej aplikacji lokalnej z Azure AD B2C, skontaktuj się z firmą [stratą](https://www.strata.io/contact/). Po uzyskaniu oprogramowania postępuj zgodnie z poniższymi instrukcjami, aby określić wymagania wstępne dotyczące programu Orchestrator i wykonać wymagane kroki instalacji i konfiguracji.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Konfigurowanie dzierżawy Azure AD B2C

1. **Rejestrowanie aplikacji**

   a. [Zarejestruj koordynatora jako aplikację](./tutorial-register-applications.md?tabs=app-reg-ga) w dzierżawie Azure AD B2C.
   >[!Note]
   >Podczas konfigurowania wystąpienia programu Orchestrator będziesz potrzebować nazwy i identyfikatora dzierżawy, identyfikatora klienta, klucza tajnego klienta, skonfigurowanych oświadczeń i identyfikatora URI przekierowania.

   b. Udziel uprawnień Microsoft MS interfejs API programu Graph do aplikacji. Aplikacja będzie potrzebować następujących uprawnień: `offline_access` , `openid` .

   c. Dodaj identyfikator URI przekierowania dla aplikacji. Ten identyfikator URI będzie zgodny z `oauthRedirectURL` parametrem konfiguracji łącznika Azure AD B2C programu Orchestrator, na przykład `https://example.com/oidc-endpoint` .

2. **Utwórz przepływ użytkownika**: Utwórz proces tworzenia [konta i](./tutorial-create-user-flows.md)logowania.

3. **Dodaj dostawcy tożsamości**: Wybierz, aby zalogować się do użytkownika przy użyciu konta lokalnego lub [dostawcy tożsamości](./add-identity-provider.md)w wersji społecznej lub korporacyjnej.

4. **Zdefiniuj atrybuty użytkownika**: Zdefiniuj atrybuty, które mają być zbierane podczas tworzenia konta.

5. **Określ oświadczenia aplikacji**: Określ atrybuty, które mają zostać zwrócone do aplikacji za pośrednictwem wystąpienia programu Orchestrator. Program Orchestrator wykorzystuje atrybuty z oświadczeń zwracanych przez Azure AD B2C i może pobierać dodatkowe atrybuty z innych podłączonych systemów tożsamości, takich jak katalogi i bazy danych LDAP. Te atrybuty są ustawiane w nagłówkach HTTP i wysyłane do nadrzędnej aplikacji lokalnej.

## <a name="configure-maverics-identity-orchestrator"></a>Konfigurowanie programu Maverics Identity Orchestrator

W poniższych sekcjach przeprowadzimy Cię przez kroki wymagane do skonfigurowania wystąpienia programu Orchestrator. Aby uzyskać dodatkową pomoc techniczną i dokumentację, skontaktuj się z firmą [strat](https://www.strata.io/contact/).

### <a name="maverics-identity-orchestrator-server-requirements"></a>Wymagania dotyczące serwera Orchestrator Identity Maverics

Wystąpienie programu Orchestrator można uruchomić na dowolnym serwerze, lokalnym lub w infrastrukturze chmury publicznej według dostawcy, takiego jak Azure, AWS lub GCP.

- System operacyjny: REHL 7,7 lub nowszy, CentOS 7 +

- Dysk: 10 GB (mały)

- Pamięć: 16 GB

- Porty: 22 (SSH/SCP), 443, 80

- Dostęp do katalogu głównego dla zadań instalacyjnych/administracyjnych

- Maverics Identity Orchestrator działa `maverics` jako użytkownik `systemd`

- Ruch wychodzący z serwera hostingowego Maverics Identity Orchestrator z możliwością uzyskania dostępu do dzierżawy usługi Azure AD.

### <a name="install-maverics-identity-orchestrator"></a>Instalowanie programu Maverics Identity Orchestrator

1. Uzyskaj najnowszy pakiet Maverics RPM. Umieść pakiet w systemie, w którym chcesz zainstalować Maverics. Jeśli kopiujesz plik na host zdalny, [punkt połączenia](https://www.ssh.com/ssh/scp/) usługi jest użytecznym narzędziem.

2. Aby zainstalować pakiet Maverics, uruchom następujące polecenie, zastępując nazwę pliku zamiast `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Domyślnie Maverics jest instalowany w `/usr/local/bin` katalogu.

3. Po zainstalowaniu Maverics będzie działać jako usługa w ramach programu `systemd` .  Aby sprawdzić, czy usługa Maverics jest uruchomiona, uruchom następujące polecenie:

   `sudo service maverics status`

  Jeśli instalacja programu Orchestrator zakończyła się pomyślnie, powinien zostać wyświetlony komunikat podobny do tego:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Jeśli uruchomienie usługi Maverics nie powiedzie się, wykonaj następujące polecenie, aby zbadać problem:

   `journalctl --unit=maverics.service --reverse`

   Najnowszy wpis dziennika zostanie wyświetlony na początku danych wyjściowych.

Po zainstalowaniu Maverics `maverics.yaml` plik domyślny zostanie utworzony w `/etc/maverics` katalogu.

Skonfiguruj w programie Orchestrator ochronę aplikacji. Integruj się z Azure AD B2C, przechowuj i pobieraj wpisy tajne z [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9). Zdefiniuj lokalizację, z której koordynator powinien odczytać swoją konfigurację.

### <a name="supply-configuration-using-environment-variables"></a>Określ konfigurację przy użyciu zmiennych środowiskowych

Podaj konfigurację w wystąpieniach programu Orchestrator przy użyciu zmiennych środowiskowych.

`MAVERICS_CONFIG`

Ta zmienna środowiskowa instruuje wystąpienie programu Orchestrator, które YAML pliki konfiguracji i gdzie można je znaleźć podczas uruchamiania lub ponownego uruchomienia. Ustaw zmienną środowiskową w programie `/etc/maverics/maverics.env` .

### <a name="create-the-orchestrators-tls-configuration"></a>Tworzenie konfiguracji protokołu TLS programu Orchestrator

`tls`W polu `maverics.yaml` deklarowane są konfiguracje zabezpieczeń warstwy transportu używane przez wystąpienie programu Orchestrator. Łączniki mogą używać obiektów TLS i serwera Orchestrator.

`maverics`Klucz jest zarezerwowany dla serwera Orchestrator. Wszystkie inne klucze są dostępne i mogą być używane do wtryskiwania obiektu TLS do danego łącznika.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Konfigurowanie łącznika Azure AD B2C

Koordynatorzy wykorzystują łączniki do integracji z dostawcami uwierzytelniania i atrybutów. W takim przypadku ta Brama aplikacji Orchestrator używa łącznika Azure AD B2C jako dostawcy uwierzytelniania i atrybutów. Azure AD B2C korzysta z dostawcy tożsamości społecznościowych do uwierzytelniania, a następnie pełni rolę dostawcy atrybutu do programu Orchestrator, przekazując atrybuty w nagłówkach HTTP.  

Konfiguracja tego łącznika odnosi się do aplikacji zarejestrowanej w dzierżawie Azure AD B2C.

1. Skopiuj identyfikator klienta, klucz tajny oraz identyfikator URI przekierowania z konfiguracji aplikacji w dzierżawie.

2. Nadaj łącznikowi nazwę, pokazany tutaj `azureADB2C` i ustaw łącznik jako `type` `azure` . Zanotuj nazwę łącznika, ponieważ ta wartość jest używana w innych parametrów konfiguracji poniżej.

3. Dla tej integracji `authType` należy ustawić wartość `oidc` .

4. Ustaw identyfikator klienta skopiowany w kroku 1 jako wartość `oauthClientID` parametru.

5. Ustaw klucz tajny klienta skopiowany w kroku 1 jako wartość `oauthClientSecret` parametru.

6. Ustaw identyfikator URI przekierowania, który został skopiowany w kroku 1 jako wartość `oauthRedirectURL` parametru.

7. Łącznik Azure AD B2C OIDC używa dobrze znanego punktu końcowego OIDC w celu odnajdywania metadanych, w tym adresów URL i kluczy podpisywania. Ustaw wartość `oidcWellKnownURL` na punkt końcowy dzierżawy.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Zdefiniuj Azure AD B2C jako dostawcę uwierzytelniania

Dostawca uwierzytelniania określa sposób uwierzytelniania dla użytkownika, który nie przedstawił prawidłowej sesji w ramach żądania zasobu aplikacji. Konfiguracja w dzierżawie Azure AD B2C określa, jak zakwestionować użytkownika pod kątem poświadczeń i zastosować dodatkowe zasady uwierzytelniania. Na przykład, aby wymagać drugiego czynnika do ukończenia procesu uwierzytelniania i zdecydować, które oświadczenia należy zwrócić do bramy aplikacji programu Orchestrator po pomyślnym uwierzytelnieniu.

Wartość `authProvider` musi być zgodna z wartością łącznika `name` .

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Ochrona aplikacji lokalnej przy użyciu bramy aplikacji programu Orchestrator

Konfiguracja bramy aplikacji programu Orchestrator deklaruje sposób, w jaki Azure AD B2C powinna chronić aplikację oraz jak użytkownicy powinni uzyskiwać dostęp do aplikacji.

1. Utwórz nazwę bramy aplikacji. W celu uzyskania identyfikatora aplikacji można użyć przyjaznej nazwy lub w pełni kwalifikowana nazwa hosta.

2. Ustaw wartość `location` . W poniższym przykładzie jest używany katalog główny aplikacji, `/` ale może to być dowolna ścieżka URL aplikacji.

3. Zdefiniuj chronioną aplikację `upstream` przy użyciu hosta: Konwencja portu: `https://example.com:8080` .

4. Ustaw wartości błędów i nieautoryzowanych stron.

5. Zdefiniuj nazwy i wartości atrybutów nagłówka HTTP, które muszą zostać dostarczone do aplikacji, aby ustanowić uwierzytelnianie i kontrolować dostęp do aplikacji. Nazwy nagłówków są arbitralne i zwykle odpowiadają konfiguracji aplikacji. Wartość atrybutu jest określana przez łącznik, który je dostarcza. W poniższym przykładzie wartości zwracane z Azure AD B2C są poprzedzone nazwą łącznika, `azureADB2C` gdzie sufiks jest nazwą atrybutu, który zawiera wymaganą wartość, na przykład `given_name` .

6. Ustaw, aby zasady były oceniane i wymuszane. Zdefiniowano trzy akcje: `allowUnauthenticated` , `allowAnyAuthenticated` , i `allowIfAny` . Każda akcja jest skojarzona z, `resource` a zasady są oceniane dla tego programu `resource` .

>[!NOTE]
>Zarówno `headers` , jak i `policies` za pomocą rozszerzeń usługi JavaScript lub GoLang, można zaimplementować dowolną logikę, która znacząco rozszerza możliwości domyślne.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Użyj Azure Key Vault jako dostawcy kluczy tajnych

Ważne jest zabezpieczenie wpisów tajnych używanych przez program Orchestrator do łączenia się z usługą Azure AD B2C i innym systemem tożsamości. Maverics będzie domyślnie ładować wpisy tajne w postaci zwykłego tekstu, `maverics.yaml` ale w tym samouczku będziesz używać Azure Key Vault jako dostawcy kluczy tajnych.

Postępuj zgodnie z instrukcjami, aby [utworzyć nowe Key Vault](../key-vault/secrets/quick-create-portal.md) , których wystąpienie programu Orchestrator będzie używać jako dostawcy kluczy tajnych. Dodaj wpisy tajne do magazynu i zwróć uwagę na `SECRET NAME` poszczególne wpisy tajne. Na przykład `AzureADB2CClientSecret`.

Aby zadeklarować wartość jako wpis tajny w `maverics.yaml` pliku konfiguracji, zawiń wpis tajny przy użyciu nawiasów ostrych:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

Wartość określona w nawiasach kątowych musi odpowiadać `SECRET NAME` podanemu wpisowi tajnemu w Azure Key Vault.

Aby załadować wpisy tajne z Azure Key Vault, należy ustawić zmienną środowiskową `MAVERICS_SECRET_PROVIDER` w pliku `/etc/maverics/maverics.env` z poświadczeniami znalezionymi w azure-credentials.jspliku, używając następującego wzorca:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Umieść wszystko razem

Poniżej przedstawiono sposób, w jaki konfiguracja programu Orchestrator zostanie wyświetlona po zakończeniu konfiguracji wymienionych powyżej.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>Testowanie przepływu

1. Przejdź do adresu URL aplikacji lokalnej, `https://example.com/sonar/dashboard` .

2. Koordynator powinien przekierować do strony skonfigurowanej w przepływie użytkownika.

3. Wybierz dostawcy tożsamości z listy na stronie.

4. Po przekierowaniu do dostawcy tożsamości podaj swoje poświadczenia zgodnie z żądaniem, łącznie z tokenem usługi MFA, jeśli jest to wymagane przez ten dostawcy tożsamości.

5. Po pomyślnym uwierzytelnieniu należy przekierować do Azure AD B2C, który przekazuje żądanie aplikacji do identyfikatora URI przekierowania programu Orchestrator.

6. Program Orchestrator szacuje zasady, oblicza nagłówki i wysyła użytkownika do aplikacji nadrzędnej.  

7. Powinna zostać wyświetlona żądana aplikacja.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
