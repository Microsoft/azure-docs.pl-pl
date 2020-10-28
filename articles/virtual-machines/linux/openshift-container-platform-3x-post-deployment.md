---
title: OpenShift kontenerów platformy 3,11 w zadaniach po wdrożeniu platformy Azure
description: Dodatkowe zadania dla programu po wdrożeniu klastra OpenShift Container platform 3,11.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: dd967ad08b628f9073edfe548033f7e97845d047
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735108"
---
# <a name="post-deployment-tasks"></a>Zadania po wdrożeniu

Po wdrożeniu klastra OpenShift można skonfigurować dodatkowe elementy. W tym artykule omówiono następujące zagadnienia:

- Jak skonfigurować Logowanie jednokrotne przy użyciu Azure Active Directory (Azure AD)
- Jak skonfigurować dzienniki Azure Monitor na potrzeby monitorowania OpenShift
- Jak skonfigurować metryki i rejestrowanie
- Jak zainstalować otwarte Service Broker dla platformy Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurowanie logowania jednokrotnego przy użyciu Azure Active Directory

Aby użyć Azure Active Directory na potrzeby uwierzytelniania, najpierw musisz utworzyć rejestrację aplikacji usługi Azure AD. Ten proces obejmuje dwa kroki: Tworzenie rejestracji aplikacji i Konfigurowanie uprawnień.

### <a name="create-an-app-registration"></a>Tworzenie rejestracji aplikacji

Te kroki używają interfejsu wiersza polecenia platformy Azure w celu utworzenia rejestracji aplikacji oraz graficznego interfejsu użytkownika (portalu) w celu ustawienia uprawnień. Aby można było utworzyć rejestrację aplikacji, potrzebne są następujące pięć informacji:

- Nazwa wyświetlana: Nazwa rejestracji aplikacji (na przykład OCPAzureAD)
- Strona główna: adres URL konsoli OpenShift (na przykład `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Identyfikator URI identyfikatora: adres URL konsoli OpenShift (na przykład `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Adres URL odpowiedzi: główny publiczny adres URL i nazwa rejestracji aplikacji (na przykład `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD` )
- Hasło: bezpieczne hasło (użyj silnego hasła)

Poniższy przykład umożliwia utworzenie rejestracji aplikacji przy użyciu podanych powyżej informacji:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Jeśli polecenie zakończy się pomyślnie, otrzymasz dane wyjściowe JSON podobne do:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Zanotuj Właściwość appId zwracaną z polecenia w celu wykonania kolejnego kroku.

W witrynie Azure Portal:

1. Wybierz pozycję **Azure Active Directory**  >  **rejestracji aplikacji** .
2. Wyszukaj rejestrację aplikacji (na przykład OCPAzureAD).
3. W wynikach kliknij rejestrację aplikacji.
4. W obszarze **Ustawienia** wybierz pozycję **wymagane uprawnienia** .
5. W obszarze **wymagane uprawnienia** wybierz pozycję **Dodaj** .

   ![Rejestracja aplikacji](media/openshift-post-deployment/app-registration.png)

6. Kliknij pozycję krok 1. Wybierz pozycję Interfejs API, a następnie kliknij pozycję **Windows Azure Active Directory (Microsoft. Azure. ActiveDirectory)** . Kliknij pozycję **Wybierz** u dołu.

   ![Interfejs API wybierania rejestracji aplikacji](media/openshift-post-deployment/app-registration-select-api.png)

7. W kroku 2: wybierz pozycję uprawnienia, wybierz pozycję **Zaloguj się i odczytaj profil użytkownika** w obszarze **delegowane uprawnienia** , a następnie kliknij pozycję **Wybierz** .

   ![Dostęp do rejestracji aplikacji](media/openshift-post-deployment/app-registration-access.png)

8. Wybierz pozycję **Gotowe** .

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurowanie OpenShift na potrzeby uwierzytelniania w usłudze Azure AD

Aby skonfigurować OpenShift do korzystania z usługi Azure AD jako dostawcy uwierzytelniania, należy edytować plik/etc/Origin/Master/Master-config.YAML we wszystkich węzłach głównych.

Znajdź identyfikator dzierżawy, korzystając z następującego polecenia interfejsu CLI:

```azurecli
az account show
```

W pliku YAML Znajdź następujące wiersze:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Wstaw następujące wiersze bezpośrednio po powyższych wierszach:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Upewnij się, że tekst jest wyrównany prawidłowo w obszarze skojarzeni. Znajdź identyfikator dzierżawy, korzystając z następującego polecenia interfejsu CLI: ```az account show```

Uruchom ponownie usługi Master OpenShift na wszystkich węzłach głównych:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

W konsoli OpenShift są teraz wyświetlane dwie opcje uwierzytelniania: htpasswd_auth i [Rejestracja aplikacji].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitoruj OpenShift z dziennikami Azure Monitor

Istnieją trzy sposoby dodawania agenta Log Analytics do OpenShift.
- Zainstaluj agenta Log Analytics dla systemu Linux bezpośrednio w każdym węźle OpenShift
- Włącz Azure Monitor rozszerzenie maszyny wirtualnej w każdym węźle OpenShift
- Instalowanie agenta Log Analytics jako zestawu demonów OpenShift

Zapoznaj się z pełnymi [instrukcjami](../../azure-monitor/insights/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift) , aby uzyskać więcej szczegółów.

## <a name="configure-metrics-and-logging"></a>Konfigurowanie metryk i rejestrowania

W oparciu o gałąź Azure Resource Manager szablony dla platformy kontenerów OpenShift i OKD mogą dostarczyć parametry wejściowe umożliwiające włączenie metryk i rejestrowania w ramach instalacji.

Oferta platformy kontenera OpenShift w portalu Marketplace udostępnia również opcję włączania metryk i rejestrowania podczas instalacji klastra.

Jeśli metryki/rejestrowanie nie zostały włączone podczas instalacji klastra, można je łatwo włączyć po tym fakcie.

### <a name="azure-cloud-provider-in-use"></a>Dostawca chmury platformy Azure w użyciu

Używanie protokołu SSH z węzłem bastionu lub pierwszym węzłem głównym (na podstawie szablonu i gałęzi w użyciu) przy użyciu poświadczeń podanych podczas wdrażania. Wydaj następujące polecenie:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Dostawca chmury platformy Azure nie jest używany

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Zainstaluj otwarte Service Broker dla platformy Azure (OSBA)

Otwarcie Service Broker dla systemu Azure lub OSBA umożliwia udostępnienie Cloud Services platformy Azure bezpośrednio z poziomu programu OpenShift. OSBA w ramach implementacji interfejsu API Open Service Broker dla platformy Azure. Interfejs API Open Service Broker to specyfikacja, która definiuje wspólny język dla dostawców chmury, których natywne aplikacje w chmurze mogą używać do zarządzania usługami w chmurze bez blokowania.

Aby zainstalować OSBA na OpenShift, postępuj zgodnie z instrukcjami znajdującymi się tutaj: https://github.com/Azure/open-service-broker-azure#openshift-project-template . 
> [!NOTE]
> Wykonaj kroki opisane w sekcji szablon projektu OpenShift, a nie całej sekcji instalacji.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do platformy kontenerów OpenShift](https://docs.openshift.com)
