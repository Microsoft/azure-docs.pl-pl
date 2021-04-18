---
title: 'Samouczek: integrowanie Azure Active Directory jednokrotnego z łącznikiem SAML programu Maverics Identity Orchestrator | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między usługą Azure Active Directory i łącznikiem SAML programu Maverics Identity Orchestrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 402f6cd6961108cdf1e9c94fb4f93309fbf15ead
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599030"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Integrowanie logowania pojedynczego usługi Azure AD z łącznikiem SAML programu Maverics Identity Orchestrator

Program Maverics Identity Orchestrator firmy Strata zapewnia prosty sposób integrowania aplikacji lokalnych z usługą Azure Active Directory (Azure AD) w celu uwierzytelniania i kontroli dostępu. Program Maverics Orchestrator może zmodernizować uwierzytelnianie i autoryzację dla aplikacji, które obecnie korzystają z nagłówków, plików cookie i innych zastrzeżonych metod uwierzytelniania. Wystąpienia usługi Maverics Orchestrator można wdrażać lokalnie lub w chmurze. 

W tym samouczku dotyczącym dostępu hybrydowego pokazano, jak przeprowadzić migrację lokalnej aplikacji internetowej, która jest obecnie chroniona przez starszy produkt do zarządzania dostępem do Internetu, w celu użycia usługi Azure AD do uwierzytelniania i kontroli dostępu. Poniżej przedstawiono podstawowe kroki:

1. Konfigurowanie orkiestratora maverics
1. Serwer proxy aplikacji
1. Rejestrowanie aplikacji dla przedsiębiorstw w usłudze Azure AD
1. Uwierzytelnianie za pośrednictwem platformy Azure i autoryzowanie dostępu do aplikacji
1. Dodawanie nagłówków w celu bezproblemowego dostępu do aplikacji
1. Praca z wieloma aplikacjami

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja łącznika SAML programu Maverics Identity Orchestrator z obsługą logowania jednokrotnego. Aby uzyskać oprogramowanie Maverics, skontaktuj się z działem [sprzedaży warstwy](mailto:sales@strata.io).
* Co najmniej jedna aplikacja, która korzysta z uwierzytelniania opartego na nagłówku. Przykłady działają na aplikacji o nazwie Connectulum hostowanej pod nazwą `https://app.connectulum.com` .
* Maszyna z systemem Linux do hostowania orkiestratora usługi Maverics
  * System operacyjny: RHEL 7.7 lub wyższy, CentOS 7+
  * Dysk: >= 10 GB
  * Pamięć: >= 4 GB
  * Porty: 22 (SSH/SCP), 443, 7474
  * Dostęp główny do zadań instalacji/administracyjnych
  * Ruch wychodzący z sieci z serwera hostującym program Maverics Identity Orchestrator do chronionej aplikacji

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Krok 1. Konfigurowanie orkiestratora maverics

### <a name="install-maverics"></a>Instalowanie oprogramowania Maverics

1. Pobierz najnowszą wersję rozwiązania Maverics RPM. Skopiuj pakiet do systemu, w którym chcesz zainstalować oprogramowanie Maverics.

1. Zainstaluj pakiet Maverics, zastępując nazwę pliku nazwą `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Po zainstalowaniu aplikacji Maverics zostanie ona uruchamiana jako usługa w ramach programu `systemd` . Aby sprawdzić, czy usługa jest uruchomiona, wykonaj następujące polecenie:

   `sudo systemctl status maverics`

1. Aby ponownie uruchomić program Orchestrator i postępować zgodnie z dziennikami, możesz uruchomić następujące polecenie:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Po zainstalowaniu aplikacji Maverics w katalogu `maverics.yaml` zostanie utworzony plik `/etc/maverics` domyślny. Przed rozpoczęciem edytowania konfiguracji w celu `appgateways` dołączyć pliki i `connectors` plik konfiguracji będzie wyglądać podobnie do następującego z:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Konfigurowanie systemu DNS

System DNS będzie przydatny, aby nie trzeba było pamiętać adresu IP serwera programu Orchestrator.

Edytuj plik hostów komputera przeglądarki (twojego komputera przenośnego) przy użyciu hipotetycznego adresu IP programu Orchestrator 12.34.56.78. W systemach operacyjnych Linux ten plik znajduje się w pliku `/etc/hosts` . W systemie Windows znajduje się on `C:\windows\system32\drivers\etc` w .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Aby upewnić się, że system DNS jest skonfigurowany zgodnie z oczekiwaniami, możesz wykonać żądanie do punktu końcowego stanu programu Orchestrator. W przeglądarce http://sonar.maverics.com:7474/status zażądaj .

### <a name="configure-tls"></a>Konfigurowanie protokołu TLS

Komunikacja za pośrednictwem bezpiecznych kanałów w celu komunikowania się z orkiestratorem ma kluczowe znaczenie dla zachowania bezpieczeństwa. Aby to osiągnąć, możesz dodać parę certyfikatów/kluczy `tls` w swojej sekcji.

Aby wygenerować certyfikat i klucz z podpisem własnym dla serwera programu Orchestrator, uruchom następujące polecenie w katalogu `/etc/maverics` :

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> W przypadku środowisk produkcyjnych prawdopodobnie będziesz używać certyfikatu podpisanego przez znany urząd certyfikacji, aby uniknąć ostrzeżeń w przeglądarce. [Let's Encrypt](https://letsencrypt.org/) jest dobrą i bezpłatną opcją, jeśli szukasz zaufanego urzędu certyfikacji.

Teraz użyj nowo wygenerowanego certyfikatu i klucza dla orkiestratora. Plik konfiguracji powinien teraz zawierać ten kod:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Aby upewnić się, że usługa TLS jest skonfigurowana zgodnie z oczekiwaniami, uruchom ponownie usługę Maverics i wyślij żądanie do punktu końcowego stanu.

## <a name="step-2-proxy-an-application"></a>Krok 2. Serwer proxy aplikacji

Następnie skonfiguruj podstawowe serwer proxy w programie Orchestrator przy użyciu programu `appgateways` . Ten krok ułatwia sprawdzenie, czy program Orchestrator ma niezbędną łączność z chronioną aplikacją.

Plik konfiguracji powinien teraz zawierać ten kod:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Aby upewnić się, że serwer proxy działa zgodnie z oczekiwaniami, uruchom ponownie usługę Maverics i wyślij żądanie do aplikacji za pośrednictwem serwera proxy usługi Maverics. Opcjonalnie możesz zażądać określonych zasobów aplikacji.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Krok 3. Rejestrowanie aplikacji dla przedsiębiorstw w usłudze Azure AD

Teraz utwórz nową aplikację dla przedsiębiorstw w usłudze Azure AD, która będzie używana do uwierzytelniania użytkowników końcowych.

> [!NOTE]
> W przypadku korzystania z funkcji usługi Azure AD, takich jak dostęp warunkowy, ważne jest, aby utworzyć aplikację dla przedsiębiorstw na aplikację lokalną. Umożliwia to dostęp warunkowy dla aplikacji, ocenę ryzyka dla aplikacji, przypisane uprawnienia dla aplikacji i tak dalej. Ogólnie rzecz biorąc, aplikacja dla przedsiębiorstw w usłudze Azure AD mapuje się na łącznik platformy Azure w usłudze Maverics.

Aby zarejestrować aplikację dla przedsiębiorstw w usłudze Azure AD:

1. W dzierżawie usługi Azure AD przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Nowa aplikacja.** W galerii usługi Azure AD wyszukaj łącznik SAML programu **Maverics Identity Orchestrator,** a następnie wybierz go.

1. W okienku Właściwości łącznika SAML  programu Maverics Identity Orchestrator ustaw opcję Wymagane przypisanie **użytkownika?** na wartość **Nie,** aby umożliwić aplikacji pracę dla wszystkich użytkowników w katalogu.

1. W okienku Maverics Identity Orchestrator SAML Connector **Overview** (Omówienie łącznika SAML programu Maverics Identity Orchestrator) wybierz pozycję **Set up single sign-on**(Konfigurowanie logowania pojedynczego), a następnie wybierz pozycję **SAML**.

1. W okienku logowania opartego na łączniku **SAML** łącznika SAML programu Maverics Identity Orchestrator edytuj podstawową konfigurację **saml,** wybierając przycisk Edytuj **(ikona** ołówka).

   ![Zrzut ekranu przedstawiający przycisk Edycji "Podstawowa konfiguracja saml".](common/edit-urls.png)

1. Wprowadź identyfikator **jednostki** `https://sonar.maverics.com` . Identyfikator jednostki musi być unikatowy w aplikacjach w dzierżawie i może być dowolną wartością. Użyjesz tej wartości podczas definiowania pola `samlEntityID` dla łącznika platformy Azure w następnej sekcji.

1. Wprowadź adres **URL odpowiedzi** `https://sonar.maverics.com/acs` . Użyjesz tej wartości podczas definiowania pola `samlConsumerServiceURL` dla łącznika platformy Azure w następnej sekcji.

1. Wprowadź **adres URL logowania** `https://sonar.maverics.com/` . To pole nie będzie używane przez aplikację Maverics, ale jest wymagane w usłudze Azure AD, aby umożliwić użytkownikom uzyskiwanie dostępu do aplikacji za pośrednictwem portalu usługi Azure AD Moje aplikacje Portal.

1. Wybierz pozycję **Zapisz**.

1. W sekcji **Certyfikat podpisywania SAML** wybierz przycisk Kopiuj, aby skopiować wartość adres  **URL** metadanych federacji aplikacji, a następnie zapisz ją na komputerze.

   ![Zrzut ekranu przedstawiający przycisk kopiowania "Certyfikat podpisywania SAML".](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Krok 4. Uwierzytelnianie za pośrednictwem platformy Azure i autoryzowanie dostępu do aplikacji

Następnie umieść utworzoną aplikację dla przedsiębiorstw do użycia, konfigurując łącznik platformy Azure w usłudze Maverics. Ta `connectors` konfiguracja sparowana z `idps` blokiem umożliwia programowi Orchestrator uwierzytelnianie użytkowników.

Plik konfiguracji powinien teraz zawierać następujący kod. Pamiętaj, aby zastąpić wartość wartością adresu URL metadanych federacji aplikacji `METADATA_URL` z poprzedniego kroku.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Aby upewnić się, że uwierzytelnianie działa zgodnie z oczekiwaniami, uruchom ponownie usługę Maverics i wyślij żądanie do zasobu aplikacji za pośrednictwem serwera proxy usługi Maverics. Przed uzyskaniem dostępu do zasobu należy przekierować cię na platformę Azure w celu uwierzytelnienia.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Krok 5. Dodawanie nagłówków w celu bezproblemowego dostępu do aplikacji

Nie wysyłasz jeszcze nagłówków do aplikacji nadrzędnej. Dodajmy do żądania w miarę jego przejścia przez serwer proxy usługi Maverics, aby umożliwić nadrzędnej aplikacji `headers` identyfikację użytkownika.

Plik konfiguracji powinien teraz zawierać ten kod:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Aby upewnić się, że uwierzytelnianie działa zgodnie z oczekiwaniami, należy wykonać żądanie do zasobu aplikacji za pośrednictwem serwera proxy usługi Maverics. Chroniona aplikacja powinna teraz odbierać nagłówki w żądaniu. 

Jeśli aplikacja oczekuje różnych nagłówków, możesz edytować klucze nagłówków. Wszystkie oświadczenia, które pochodzą z usługi Azure AD w ramach przepływu SAML, są dostępne do użycia w nagłówkach. Na przykład możesz dołączyć inny nagłówek , gdzie to nazwa łącznika i jest oświadczeniem `secondary_email: azureSonarApp.email` `azureSonarApp` `email` zwróconym z usługi Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Krok 6. Praca z wieloma aplikacjami

Teraz przyjrzyjmy się wymaganiom serwera proxy dla wielu aplikacji, które znajdują się na różnych hostach. Aby osiągnąć ten krok, skonfiguruj inny App Gateway, inną aplikację dla przedsiębiorstw w usłudze Azure AD i inny łącznik.

Plik konfiguracji powinien teraz zawierać ten kod:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

Możesz zauważyć, że kod dodaje pole do definicji `host` App Gateway danych. Pole `host` umożliwia programowi Maverics Orchestrator rozróżnienie ruchu nadrzędnego hosta do serwera proxy.

Aby potwierdzić, że nowo App Gateway działa zgodnie z oczekiwaniami, należy wykonać żądanie na stronie `https://connectulum.maverics.com` .

## <a name="advanced-scenarios"></a>Scenariusze zaawansowane

### <a name="identity-migration"></a>Migracja tożsamości

Nie możesz znieść narzędzia do zarządzania dostępem do Internetu, ale nie masz możliwości migrowania użytkowników bez masowego resetowania haseł? Program Maverics Orchestrator obsługuje migrację tożsamości przy użyciu programu `migrationgateways` .

### <a name="web-server-gateways"></a>Bramy serwerów internetowych

Nie chcesz zmienić ruchu sieciowego i serwera proxy za pośrednictwem orkiestratora maverics? Nie jest to problem. Program Maverics Orchestrator można sparować z bramami serwerów internetowych (modułami), aby oferować te same rozwiązania bez użycia serwera proxy.

## <a name="wrap-up"></a>Wrap-up

W tym momencie zainstalowano program Maverics Orchestrator, utworzono i skonfigurowano aplikację przedsiębiorstwa w usłudze Azure AD oraz skonfigurowano program Orchestrator do serwera proxy chronionej aplikacji, jednocześnie wymagając uwierzytelniania i wymuszania zasad. Aby dowiedzieć się więcej o tym, jak program Maverics Orchestrator może być używany w przypadku użycia rozproszonego zarządzania tożsamościami, skontaktuj się [z warstwą](mailto:sales@strata.io).

## <a name="next-steps"></a>Następne kroki

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)
