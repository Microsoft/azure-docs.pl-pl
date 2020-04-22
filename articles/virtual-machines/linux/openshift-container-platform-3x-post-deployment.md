---
title: Platforma kontenerów OpenShift 3.11 w zadaniach po wdrożeniu platformy Azure
description: Dodatkowe zadania po wdrożeniu klastra platformy kontenerowej OpenShift 3.11.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 8d76588ae9124d34902659cc0149063400b6e766
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759522"
---
# <a name="post-deployment-tasks"></a>Zadania po wdrożeniu

Po wdrożeniu klastra OpenShift można skonfigurować dodatkowe elementy. Ten artykuł obejmuje:

- Jak skonfigurować logowanie jednokrotne przy użyciu usługi Azure Active Directory (Azure AD)
- Jak skonfigurować dzienniki usługi Azure Monitor do monitorowania programu OpenShift
- Jak skonfigurować dane i rejestrowanie
- Jak zainstalować open service broker dla platformy Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurowanie logowania jednokrotnego przy użyciu usługi Azure Active Directory

Aby używać usługi Azure Active Directory do uwierzytelniania, najpierw należy utworzyć rejestrację aplikacji usługi Azure AD. Ten proces obejmuje dwa kroki: tworzenie rejestracji aplikacji i konfigurowanie uprawnień.

### <a name="create-an-app-registration"></a>Tworzenie rejestracji aplikacji

Te kroki używają interfejsu wiersza polecenia platformy Azure do utworzenia rejestracji aplikacji, a gui (portal) do ustawiania uprawnień. Aby utworzyć rejestrację aplikacji, potrzebujesz następujących pięciu informacji:

- Nazwa wyświetlana: nazwa rejestracji aplikacji (na przykład OCPAzureAD)
- Strona główna: adres URL konsoli `https://masterdns343khhde.westus.cloudapp.azure.com/console`OpenShift (na przykład )
- Identyfikator URI: adres URL konsoli OpenShift (na przykład `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- Adres URL odpowiedzi: główny publiczny adres URL `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD`i nazwa rejestracji aplikacji (na przykład )
- Hasło: Bezpieczne hasło (użyj silnego hasła)

Poniższy przykład tworzy rejestrację aplikacji przy użyciu powyższych informacji:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Jeśli polecenie zakończy się pomyślnie, otrzymasz wyjście JSON podobne do:

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

Zanotuj appId właściwość zwrócona z polecenia dla późniejszego kroku.

W witrynie Azure Portal:

1. Wybierz pozycję**Rejestracja aplikacji** **usługi Azure Active Directory** > .
2. Wyszukaj rejestrację aplikacji (na przykład OCPAzureAD).
3. W wynikach kliknij rejestrację aplikacji.
4. W obszarze **Ustawienia**wybierz pozycję **Wymagane uprawnienia**.
5. W **obszarze Wymagane uprawnienia**wybierz pozycję **Dodaj**.

   ![Rejestracja aplikacji](media/openshift-post-deployment/app-registration.png)

6. Kliknij krok 1: Wybierz interfejs API, a następnie kliknij pozycję **Usługa Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory).** Kliknij **pozycję Wybierz** u dołu.

   ![Interfejs API wyboru rejestracji aplikacji](media/openshift-post-deployment/app-registration-select-api.png)

7. W kroku 2: Wybierz pozycję Uprawnienia, wybierz pozycję **Zaloguj się i przeczytaj profil użytkownika w** obszarze Uprawnienia **delegowane**, a następnie kliknij przycisk **Wybierz**.

   ![Dostęp do rejestracji aplikacji](media/openshift-post-deployment/app-registration-access.png)

8. Wybierz pozycję **Done** (Gotowe).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurowanie usługi OpenShift dla uwierzytelniania usługi Azure AD

Aby skonfigurować funkcję OpenShift do używania usługi Azure AD jako dostawcy uwierzytelniania, plik /etc/origin/master-config.yaml musi być edytowany we wszystkich węzłach głównych.

Znajdź identyfikator dzierżawy za pomocą następującego polecenia interfejsu wiersza polecenia:

```azurecli
az account show
```

W pliku yaml znajdź następujące wiersze:

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

Wstawić następujące wiersze bezpośrednio po poprzednich wierszach:

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

Upewnij się, że tekst jest wyrównany poprawnie w obszarze identityProviders. Znajdź identyfikator dzierżawy za pomocą następującego polecenia interfejsu wiersza polecenia:```az account show```

Uruchom ponownie usługi główne OpenShift we wszystkich węzłach głównych:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

W konsoli OpenShift są teraz widoczne dwie opcje uwierzytelniania: htpasswd_auth i [Rejestracja aplikacji].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitoruj openshift za pomocą dzienników usługi Azure Monitor

Istnieją trzy sposoby dodawania agenta usługi Log Analytics do funkcji OpenShift.
- Zainstaluj agenta usługi Log Analytics dla systemu Linux bezpośrednio w każdym węźle OpenShift
- Włącz rozszerzenie maszyny wirtualnej usługi Azure Monitor w każdym węźle OpenShift
- Instalowanie agenta usługi Log Analytics jako zestawu demonów OpenShift

Przeczytaj pełne [instrukcje,](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) aby uzyskać więcej informacji.

## <a name="configure-metrics-and-logging"></a>Konfigurowanie danych i rejestrowania

Na podstawie gałęzi szablony usługi Azure Resource Manager dla platformy kontenerów OpenShift i OKD mogą dostarczać parametry wejściowe umożliwiające metryki i rejestrowanie w ramach instalacji.

Oferta OpenShift Container Platform Marketplace zawiera również opcję włączania metryk i rejestrowania podczas instalacji klastra.

Jeśli metryki / rejestrowanie nie została włączona podczas instalacji klastra, można je łatwo włączyć po fakcie.

### <a name="azure-cloud-provider-in-use"></a>Dostawca chmury Azure w użyciu

SSH do węzła bastionu lub pierwszego węzła głównego (na podstawie szablonu i gałęzi w użyciu) przy użyciu poświadczeń podanych podczas wdrażania. Wydać następujące polecenie:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Dostawca chmury Azure nie jest używany

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Zainstaluj open service broker dla platformy Azure (OSBA)

Otwórz brokera usług dla platformy Azure lub OSBA, umożliwia aprowizyjanie usług w chmurze Azure bezpośrednio z OpenShift. OSBA w implementacji interfejsu API open service broker dla platformy Azure. Interfejs API Open Service Broker to specyfikacja, która definiuje wspólny język dla dostawców chmury, których aplikacje natywne w chmurze mogą używać do zarządzania usługami w chmurze bez blokady.

Aby zainstalować OSBA na OpenShift, postępuj https://github.com/Azure/open-service-broker-azure#openshift-project-templatezgodnie z instrukcjami znajdującymi się tutaj: . 
> [!NOTE]
> Wykonaj tylko kroki opisane w sekcji Szablon projektu OpenShift, a nie w całej sekcji Instalowanie.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do platformy kontenerowej OpenShift](https://docs.openshift.com)
