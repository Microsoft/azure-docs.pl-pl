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
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 19f6b0601afe9ad84f02c93d7f6e1ae3a71a06a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585098"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Integrowanie logowania jednokrotnego usługi Azure AD za pomocą łącznika SAML Maverics Identity Orchestrator

Usługa Maverics Identity Orchestrator zapewnia prostą metodę integracji aplikacji lokalnych z usługą Azure Active Directory (Azure AD) na potrzeby uwierzytelniania i kontroli dostępu. Maverics Orchestrator jest w stanie przeprowadzić modernizację uwierzytelniania i autoryzacji dla aplikacji, które aktualnie korzystają z nagłówków, plików cookie i innych własnościowych metod uwierzytelniania. Wystąpienia programu Orchestrator Maverics można wdrażać lokalnie lub w chmurze. 

Ten samouczek dostępu hybrydowego pokazuje, jak przeprowadzić migrację lokalnej aplikacji sieci Web, która jest obecnie chroniona przez starszy produkt zarządzania dostępem do sieci Web w celu korzystania z usługi Azure AD na potrzeby uwierzytelniania i kontroli dostępu. Poniżej przedstawiono podstawowe kroki:

1. Konfigurowanie programu Maverics Orchestrator
1. Serwer proxy aplikacji
1. Rejestrowanie aplikacji dla przedsiębiorstw w usłudze Azure AD
1. Uwierzytelnianie za pośrednictwem platformy Azure i Autoryzowanie dostępu do aplikacji
1. Dodawanie nagłówków do bezproblemowego dostępu do aplikacji
1. Pracuj z wieloma aplikacjami

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne w usłudze Orchestrator Identity Maverics. Aby uzyskać Maverics oprogramowanie, skontaktuj się z [sprzedażą strat](mailto:sales@strata.io).
* Co najmniej jedna aplikacja, która korzysta z uwierzytelniania opartego na nagłówkach. Przykłady działają w przypadku aplikacji o nazwie sonar, która jest hostowana w https://app.sonarsystems.com i aplikacji o nazwie Connectulum, hostowana w https://app.connectulum.com .
* Komputer z systemem Linux do hostowania programu Maverics Orchestrator
  * System operacyjny: RHEL 7,7 lub nowszy, CentOS 7 +
  * Dysk: >= 10 GB
  * Pamięć: >= 4 GB
  * Porty: 22 (SSH/SCP), 443, 7474
  * Dostęp do katalogu głównego dla zadań instalacyjnych/administracyjnych
  * Ruch wychodzący z serwera hostującym Maverics Identity Orchestrator do chronionej aplikacji

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Krok 1. Konfigurowanie programu Maverics Orchestrator

### <a name="install-maverics"></a>Zainstaluj Maverics

1. Pobierz najnowszą Maverics obr./min. Skopiuj pakiet do systemu, w którym chcesz zainstalować oprogramowanie Maverics.

1. Zainstaluj pakiet Maverics, zastępując nazwę pliku zamiast `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Po zainstalowaniu programu Maverics będzie on uruchamiany jako usługa w ramach programu `systemd` . Aby sprawdzić, czy usługa jest uruchomiona, wykonaj następujące polecenie:

   `sudo systemctl status maverics`

1. Aby ponownie uruchomić program Orchestrator i postępować zgodnie z dziennikami, możesz uruchomić następujące polecenie:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Po zainstalowaniu Maverics `maverics.yaml` plik domyślny zostanie utworzony w `/etc/maverics` katalogu. Przed edytowaniem konfiguracji w celu uwzględnienia `appgateways` i `connectors` , plik konfiguracji będzie wyglądać następująco:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Konfigurowanie systemu DNS

System DNS będzie przydatny, aby nie trzeba było zapamiętywać adresu IP serwera Orchestrator.

Edytuj plik hosts maszyny przeglądarki (komputera przenośnego) przy użyciu hipotetycznego adresu IP programu 12.34.56.78. W systemach operacyjnych opartych na systemie Linux ten plik znajduje się w lokalizacji `/etc/hosts` . W systemie Windows znajduje się w lokalizacji `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Aby upewnić się, że usługa DNS jest skonfigurowana zgodnie z oczekiwaniami, można wysłać żądanie do punktu końcowego stanu programu Orchestrator. W przeglądarce Zażądaj http://sonar.maverics.com:7474/status .

### <a name="configure-tls"></a>Konfigurowanie protokołu TLS

Komunikacja za pośrednictwem bezpiecznych kanałów w celu komunikowania się z koordynatorem ma kluczowe znaczenie dla zapewnienia bezpieczeństwa. Aby to osiągnąć, możesz dodać parę certyfikat/klucz w `tls` sekcji.

Aby wygenerować certyfikat z podpisem własnym i klucz dla serwera Orchestrator, uruchom następujące polecenie w `/etc/maverics` katalogu:

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> W przypadku środowisk produkcyjnych prawdopodobnie zechcesz użyć certyfikatu podpisanego przez znany urząd certyfikacji, aby uniknąć ostrzeżeń w przeglądarce. [Zaszyfrujmy](https://letsencrypt.org/) to dobra i bezpłatna opcja, jeśli szukasz zaufanego urzędu certyfikacji.

Teraz Użyj nowo wygenerowanego certyfikatu i klucza dla programu Orchestrator. Plik konfiguracyjny powinien teraz zawierać następujący kod:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Aby upewnić się, że protokół TLS jest skonfigurowany zgodnie z oczekiwaniami, należy ponownie uruchomić usługę Maverics i wysłać żądanie do punktu końcowego stanu. W przeglądarce Zażądaj https://sonar.maverics.com/status .

## <a name="step-2-proxy-an-application"></a>Krok 2. serwer proxy aplikacji

Następnie skonfiguruj podstawowe proxy w programie Orchestrator przy użyciu programu `appgateways` . Ten krok pomaga sprawdzić, czy program Orchestrator ma łączność niezbędną do chronionej aplikacji.

Plik konfiguracyjny powinien teraz zawierać następujący kod:

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

Aby upewnić się, że proxy działa zgodnie z oczekiwaniami, należy ponownie uruchomić usługę Maverics, a następnie wysłać żądanie do aplikacji za pomocą serwera proxy Maverics. W przeglądarce Zażądaj https://sonar.maverics.com . Opcjonalnie można wykonać żądanie do określonych zasobów aplikacji, na przykład `https://sonar.maverics.com/RESOURCE` , gdzie `RESOURCE` jest prawidłowym zasobem aplikacji chronionej aplikacji nadrzędnej.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Krok 3. rejestrowanie aplikacji dla przedsiębiorstw w usłudze Azure AD

Teraz Utwórz nową aplikację dla przedsiębiorstw w usłudze Azure AD, która będzie używana do uwierzytelniania użytkowników końcowych.

> [!NOTE]
> W przypadku korzystania z funkcji usługi Azure AD, takich jak dostęp warunkowy, ważne jest, aby utworzyć aplikację dla przedsiębiorstw dla aplikacji lokalnej. Pozwala to na dostęp warunkowy dla aplikacji, ocenę ryzyka dla aplikacji, przypisane uprawnienia do aplikacji i tak dalej. Ogólnie rzecz biorąc, aplikacja dla przedsiębiorstw w usłudze Azure AD jest mapowana na łącznik platformy Azure w Maverics.

Aby zarejestrować aplikację przedsiębiorstwa w usłudze Azure AD:

1. W dzierżawie usługi Azure AD przejdź do pozycji **aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Nowa aplikacja**. W galerii usługi Azure AD Wyszukaj pozycję **Maverics Identity Orchestrator SAML Connector**, a następnie wybierz ją.

1. W okienku **Właściwości** łącznika SAML programu Orchestrator Identity Maverics ustaw pozycję **wymagane przypisanie użytkownika** na wartość **nie** , aby umożliwić działanie aplikacji dla wszystkich użytkowników w katalogu.

1. W okienku **Przegląd** łącznika SAML programu Maverics Identity Orchestrator wybierz pozycję **Skonfiguruj Logowanie jednokrotne**, a następnie wybierz pozycję **SAML**.

1. W okienku **Logowanie oparte na SAML** łączniku SAML programu Orchestrator Identity Maverics (usługa) Edytuj **podstawową konfigurację SAML** , wybierając przycisk **Edytuj** (ikona ołówka).

   ![Zrzut ekranu przedstawiający przycisk edycji "Podstawowa konfiguracja SAML".](common/edit-urls.png)

1. Wprowadź **Identyfikator jednostki** `https://sonar.maverics.com` . Identyfikator jednostki musi być unikatowy w aplikacjach w dzierżawie i może być dowolną wartością. Ta wartość zostanie użyta podczas definiowania `samlEntityID` pola dla łącznika platformy Azure w następnej sekcji.

1. Wprowadź **adres URL odpowiedzi** `https://sonar.maverics.com/acs` . Ta wartość zostanie użyta podczas definiowania `samlConsumerServiceURL` pola dla łącznika platformy Azure w następnej sekcji.

1. Wprowadź **adres URL logowania** `https://sonar.maverics.com/` . To pole nie jest używane przez Maverics, ale jest wymagane w usłudze Azure AD, aby umożliwić użytkownikom uzyskiwanie dostępu do aplikacji za pomocą portalu Moje aplikacje usługi Azure AD.

1. Wybierz pozycję **Zapisz**.

1. W sekcji **certyfikat podpisywania SAML** wybierz przycisk **Kopiuj** , aby skopiować wartość **adresu URL metadanych federacji aplikacji** , a następnie zapisz ją na komputerze.

   ![Zrzut ekranu przedstawiający przycisk kopiowania certyfikatu podpisywania SAML.](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Krok 4. uwierzytelnianie za pośrednictwem platformy Azure i Autoryzowanie dostępu do aplikacji

Następnie należy umieścić utworzoną przez siebie aplikację korporacyjną przez skonfigurowanie łącznika Azure w Maverics. Ta `connectors` Konfiguracja sparowana z `idps` blokiem umożliwia koordynatorowi uwierzytelnianie użytkowników.

Plik konfiguracyjny powinien teraz zawierać następujący kod. Pamiętaj, aby zastąpić `METADATA_URL` wartość adresu URL metadanych federacji aplikacji z poprzedniego kroku.

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

Aby upewnić się, że uwierzytelnianie działa zgodnie z oczekiwaniami, należy ponownie uruchomić usługę Maverics, a następnie wysłać żądanie do zasobu aplikacji za pomocą serwera proxy Maverics. Przed uzyskaniem dostępu do zasobu należy przekierować do platformy Azure w celu uwierzytelnienia.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Krok 5. Dodawanie nagłówków w celu bezproblemowego dostępu do aplikacji

Nie wysyłamy jeszcze nagłówków do aplikacji nadrzędnej. Dodajmy `headers` do żądania w miarę przechodzenia przez serwer proxy Maverics, aby umożliwić aplikacji nadrzędnej zidentyfikowanie użytkownika.

Plik konfiguracyjny powinien teraz zawierać następujący kod:

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

Aby upewnić się, że uwierzytelnianie działa zgodnie z oczekiwaniami, należy wysłać żądanie do zasobu aplikacji za pomocą serwera proxy Maverics. Aplikacja chroniona powinna teraz odbierać nagłówki w żądaniu. 

Edytuj klucze nagłówków, jeśli aplikacja oczekuje różnych nagłówków. Wszystkie oświadczenia, które powracają z usługi Azure AD w ramach przepływu SAML, są dostępne do użycia w nagłówkach. Na przykład możesz dołączyć inny nagłówek `secondary_email: azureSonarApp.email` , gdzie `azureSonarApp` to nazwa łącznika i `email` jest to Deklaracja zwrócona z usługi Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Krok 6. Współpraca z wieloma aplikacjami

Teraz przyjrzyjmy się wymaganiom serwera proxy dla wielu aplikacji, które znajdują się na różnych hostach. Aby wykonać ten krok, skonfiguruj inną bramę aplikacji, inną aplikację przedsiębiorstwa w usłudze Azure AD i inny łącznik.

Plik konfiguracyjny powinien teraz zawierać następujący kod:

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

Być może zauważono, że kod dodaje `host` pole do definicji bramy aplikacji. To `host` pole umożliwia programowi Maverics Orchestrator odróżnienie tego, który host nadrzędny jest ruchem serwera proxy.

Aby upewnić się, że nowo dodana Brama aplikacji działa zgodnie z oczekiwaniami, należy wykonać żądanie https://connectulum.maverics.com .

## <a name="advanced-scenarios"></a>Scenariusze zaawansowane

### <a name="identity-migration"></a>Migracja tożsamości

Nie można przetworzyć narzędzia do zarządzania dostępem do sieci Web na koniec okresu istnienia, ale nie masz możliwości migrowania użytkowników bez masowego resetowania haseł? Program Maverics Orchestrator obsługuje migrację tożsamości za pomocą programu `migrationgateways` .

### <a name="web-server-gateways"></a>Bramy serwera sieci Web

Nie chcesz używać ruchu sieciowego i serwera proxy za pomocą programu Maverics Orchestrator? To nie jest problem. Program Maverics Orchestrator można sparować z bramami serwera sieci Web (moduły), aby zaoferować te same rozwiązania bez proxy.

## <a name="wrap-up"></a>Zawijanie

W tym momencie zainstalowano program Maverics Orchestrator, utworzono i skonfigurowano aplikację przedsiębiorstwa w usłudze Azure AD i skonfigurowano koordynatora na serwerze proxy do chronionej aplikacji przy jednoczesnym wymaganiu uwierzytelniania i wymuszania zasad. Aby dowiedzieć się więcej o tym, jak program Maverics Orchestrator może być używany w przypadku przypadków użycia rozproszonego zarządzania tożsamościami, [skontaktuj się z firmą stratą](mailto:sales@strata.io).

## <a name="next-steps"></a>Następne kroki

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)
