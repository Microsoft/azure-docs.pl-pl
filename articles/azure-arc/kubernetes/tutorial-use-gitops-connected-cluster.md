---
title: 'Samouczek: wdrażanie konfiguracji przy użyciu GitOps na klastrze Kubernetes z włączonym usługą Azure Arc'
description: W tym samouczku pokazano, jak zastosować konfiguracje w klastrze Kubernetes z włączoną funkcją Azure Arc. Aby uzyskać informacje o pojęciach związanych z tym procesem, zobacz artykuł konfiguracje i GitOps — usługa Azure ARC z włączoną obsługą Kubernetes.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 987fb5745b6528eb96b4237f698b3ae371d69287
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731823"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Samouczek: wdrażanie konfiguracji przy użyciu GitOps na klastrze Kubernetes z włączonym usługą Azure Arc 

W ramach tego samouczka nastąpi zastosowanie konfiguracji przy użyciu programu GitOps w klastrze Kubernetes z włączoną funkcją Azure Arc. Omawiane tematy:

> [!div class="checklist"]
> * Utwórz konfigurację w klastrze Kubernetes z obsługą usługi Azure ARC przy użyciu przykładowego repozytorium git.
> * Sprawdź, czy konfiguracja została pomyślnie utworzona.
> * Zastosuj konfigurację z prywatnego repozytorium git.
> * Sprawdź poprawność konfiguracji Kubernetes.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Istniejący klaster z włączonym usługą Azure Arc Kubernetes.
    - Jeśli jeszcze nie nawiązano połączenia z klastrem, zapoznaj [się z naszymi krokami Przewodnik Szybki Start dotyczący usługi Azure Arc Kubernetes](quickstart-connect-cluster.md).
- Zrozumienie korzyści i architektury tej funkcji. Więcej informacji można znaleźć w temacie [konfiguracje i GitOps — artykuł Kubernetes z obsługą usługi Azure Arc](conceptual-configurations.md).

## <a name="create-a-configuration"></a>Utwórz konfigurację

[Przykładowe repozytorium](https://github.com/Azure/arc-k8s-demo) używane w tym artykule jest strukturalne wokół osoby będącej operatorem klastra. Manifesty w tym repozytorium zastrzegają kilka przestrzeni nazw, wdrażają obciążenia i udostępniają pewne konfiguracje specyficzne dla zespołu. Użycie tego repozytorium z programem GitOps powoduje utworzenie następujących zasobów w klastrze:

* Przestrzenie nazw: `cluster-config` , `team-a` , `team-b`
* Mieszczeniu `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

`config-agent`Sonduje platformę Azure pod kątem nowych lub zaktualizowanych konfiguracji. To zadanie zajmie do 30 sekund.

W przypadku kojarzenia repozytorium prywatnego z konfiguracją wykonaj poniższe kroki w temacie [apply Configuration from a Private git](#apply-configuration-from-a-private-git-repository).

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj rozszerzenia interfejsu wiersza polecenia platformy Azure, aby `k8s-configuration` połączyć połączony klaster z [przykładowym repozytorium git](https://github.com/Azure/arc-k8s-demo). 
1. Nadaj nazwę tej konfiguracji `cluster-config` .
1. Nakazuje agentowi wdrożenie operatora w `cluster-config` przestrzeni nazw.
1. Nadaj uprawnienia operatora `cluster-admin` .

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
      ```

### <a name="use-a-public-git-repository"></a>Używanie publicznego repozytorium git

| Parametr | Format |
| ------------- | ------------- |
| `--repository-url` | http [s]://Server/repo [. git] lub git://Server/repo [. git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Korzystanie z prywatnego repozytorium git z użyciem kluczy SSH i strumieniowego tworzenia

Dodaj klucz publiczny wygenerowany przez strumień do konta użytkownika w dostawcy usługi git. Jeśli klucz zostanie dodany do repozytorium zamiast konta użytkownika, użyj `git@` zamiast `user@` adresu URL. 

Aby uzyskać więcej informacji, przejdź do sekcji [Zastosuj konfigurację z prywatnego repozytorium git](#apply-configuration-from-a-private-git-repository) .


| Parametr | Format | Uwagi
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] lub user@server:repo [. git] | `git@` może zastąpić `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Korzystanie z prywatnego repozytorium git z kluczami SSH i dostarczonymi przez użytkownika

Podaj własny klucz prywatny bezpośrednio lub w pliku. Klucz musi mieć [Format PEM](https://aka.ms/PEMformat) i kończyć się znakiem nowego wiersza (\n). 

Dodaj skojarzony klucz publiczny do konta użytkownika w dostawcy usługi git. Jeśli klucz zostanie dodany do repozytorium zamiast konta użytkownika, Użyj zamiast `git@` `user@` . 

Aby uzyskać więcej informacji, przejdź do sekcji [Zastosuj konfigurację z prywatnego repozytorium git](#apply-configuration-from-a-private-git-repository) .  

| Parametr | Format | Uwagi |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] lub user@server:repo [. git] | `git@` może zastąpić `user@` |
| `--ssh-private-key` | klucz szyfrowany algorytmem Base64 w [formacie PEM](https://aka.ms/PEMformat) | Podaj klucz bezpośrednio |
| `--ssh-private-key-file` | Pełna ścieżka do pliku lokalnego | Podaj pełną ścieżkę do pliku lokalnego, który zawiera klucz w formacie PEM

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Korzystanie z prywatnego hosta git z użyciem protokołów SSH i znanych przez użytkownika

Operator strumień przechowuje listę typowych hostów Git w rozpoznawanym pliku hosts w celu uwierzytelnienia repozytorium git przed nawiązaniem połączenia SSH. Jeśli używasz *nietypowego* repozytorium Git lub własnego hosta git, możesz podać klucz hosta, aby strumień mógł identyfikować repozytorium. 

Podobnie jak w przypadku kluczy prywatnych, możesz podać zawartość known_hosts bezpośrednio lub w pliku. Dostarczając swoją zawartość, użyj [specyfikacji formatu zawartości known_hosts](https://aka.ms/KnownHostsFormat)wraz z dowolnym z powyższych scenariuszy dotyczących kluczy SSH.

| Parametr | Format | Uwagi |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] lub user@server:repo [. git] | `git@` może zastąpić `user@` |
| `--ssh-known-hosts` | kodowane algorytmem Base64 | Zapewnianie informacji o znanych hostach bezpośrednio |
| `--ssh-known-hosts-file` | Pełna ścieżka do pliku lokalnego | Podaj zawartość znanych hostów w pliku lokalnym |

### <a name="use-a-private-git-repository-with-https"></a>Korzystanie z prywatnego repozytorium git z protokołem HTTPS

| Parametr | Format | Uwagi |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | HTTPS z uwierzytelnianiem podstawowym |
| `--https-user` | nieprzetworzone lub zakodowane algorytmem Base64 | Nazwa użytkownika protokołu HTTPS |
| `--https-key` | nieprzetworzone lub zakodowane algorytmem Base64 | Osobisty token dostępu HTTPS lub hasło

>[!NOTE]
>* Wykres operatora Helm w wersji 1.2.0 + obsługuje uwierzytelnianie prywatne w wersji HTTPS Helm.
>* Wydanie HTTPS Helm nie jest obsługiwane w przypadku klastrów zarządzanych AKS.
>* Jeśli potrzebujesz strumieniowego dostępu do repozytorium Git za pomocą serwera proxy, musisz zaktualizować agentów usługi Azure ARC przy użyciu ustawień serwera proxy. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia przy użyciu serwera proxy wychodzącego](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server).


## <a name="additional-parameters"></a>Dodatkowe parametry

Dostosuj konfigurację przy użyciu następujących parametrów opcjonalnych:

| Parametr | Opis |
| ------------- | ------------- |
| `--enable-helm-operator`| Przełącz, aby włączyć obsługę wdrożeń wykresów Helm. |
| `--helm-operator-params` | Wartości wykresu dla operatora Helm (jeśli jest włączony). Na przykład `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Wersja wykresu dla operatora Helm (jeśli jest włączona). Użyj wersji 1.2.0 +. Wartość domyślna: "1.2.0". |
| `--operator-namespace` | Nazwa dla przestrzeni nazw operatora. Wartość domyślna: "default". Maks.: 23 znaki. |
| `--operator-params` | Parametry operatora. Musi być określona w cudzysłowie pojedynczym. Na przykład ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Opcje obsługiwane w  `--operator-params` :

| Opcja | Opis |
| ------------- | ------------- |
| `--git-branch`  | Gałąź repozytorium git do użycia na potrzeby manifestów Kubernetes. Wartość domyślna to "Master". Nowsze repozytoria mają gałąź główną o nazwie `main` , w tym przypadku należy ustawić `--git-branch=main` . |
| `--git-path`  | Ścieżka względna w repozytorium Git na potrzeby strumieniowego lokalizowania manifestów Kubernetes. |
| `--git-readonly` | Repozytorium Git będzie traktowane jako tylko do odczytu. Strumień nie będzie próbować zapisywać do niego. |
| `--manifest-generation`  | Jeśli ta funkcja jest włączona, strumień będzie szukać metadanych. strumień. YAML i Run Kustomize lub innych generatorów manifestów. |
| `--git-poll-interval`  | Okres, w którym ma być sondowanie repozytorium git dla nowych zatwierdzeń. Wartość domyślna to `5m` (5 minut). |
| `--sync-garbage-collection`  | Jeśli ta funkcja jest włączona, strumień spowoduje usunięcie utworzonych przez siebie zasobów, ale nie są już dostępne w usłudze git. |
| `--git-label`  | Etykieta do śledzenia postępu synchronizacji. Używane do tagowania gałęzi git.  Wartość domyślna to `flux-sync`. |
| `--git-user`  | Nazwa użytkownika dla zatwierdzenia git. |
| `--git-email`  | Wiadomość e-mail do użycia na potrzeby zatwierdzenia git. 

Jeśli nie chcesz, aby strumień zapisu do repozytorium i `--git-user` lub `--git-email` nie został ustawiony, `--git-readonly` zostanie automatycznie ustawiony.

Aby uzyskać więcej informacji, zobacz [dokumentację strumienia](https://aka.ms/FluxcdReadme).

> [!TIP]
> Konfigurację można utworzyć w Azure Portal na karcie **GitOps** zasobu Kubernetes w usłudze Azure Arc.

## <a name="validate-the-configuration"></a>Weryfikowanie konfiguracji

Użyj interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy konfiguracja została pomyślnie utworzona.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Zasób konfiguracji zostanie zaktualizowany ze stanem zgodności, komunikatami i informacjami o debugowaniu.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Po utworzeniu lub zaktualizowaniu konfiguracji następuje kilka rzeczy:

1. Usługa Azure Arc `config-agent` monitoruje Azure Resource Manager w przypadku nowych lub zaktualizowanych konfiguracji ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) i zauważy nową `Pending` konfigurację.
1. `config-agent`Odczytuje właściwości konfiguracji i tworzy przestrzeń nazw docelowy.
1. Usługa Azure Arc `controller-manager` tworzy konto usługi Kubernetes i mapuje je do [ClusterRoleBinding lub rolebinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) dla odpowiednich uprawnień ( `cluster` lub `namespace` zakresu). Następnie wdraża wystąpienie programu `flux` .
1. W przypadku używania opcji SSH z kluczami generowanymi strumieniowo program `flux` generuje klucz SSH i rejestruje klucz publiczny.
1. `config-agent`Raporty stanu z powrotem do zasobu konfiguracji na platformie Azure.

Podczas procesu aprowizacji zasób konfiguracji będzie przenoszony przez kilka zmian stanu. Monitoruj postęp przy użyciu `az k8s-configuration show ...` powyższego polecenia:

| Zmiana etapu | Opis |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Reprezentuje Stany początkowe i w toku. |
| `complianceStatus` -> `Installed`  | `config-agent` Pomyślnie skonfigurowano klaster i wdrożono go `flux` bez błędu. |
| `complianceStatus` -> `Failed` | `config-agent` Wystąpił błąd podczas wdrażania `flux` . Szczegóły znajdują się w `complianceStatus.message` treści odpowiedzi. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Zastosuj konfigurację z prywatnego repozytorium git

Jeśli używasz prywatnego repozytorium git, musisz skonfigurować klucz publiczny SSH w repozytorium. Użytkownik dostarczy lub strumień generuje klucz publiczny SSH. Klucz publiczny można skonfigurować w określonym repozytorium Git lub na użytkowniku git, który ma dostęp do repozytorium. 

### <a name="get-your-own-public-key"></a>Pobierz swój własny klucz publiczny

Jeśli wygenerowałeś własne klucze SSH, masz już klucze prywatne i publiczne.

#### <a name="get-the-public-key-using-azure-cli"></a>Pobieranie klucza publicznego przy użyciu interfejsu wiersza polecenia platformy Azure 

Jeśli strumień generuje klucze, użyj następujących poleceń w interfejsie wiersza polecenia platformy Azure.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Pobierz klucz publiczny z Azure Portal

Zapoznaj się z poniższym opisem w Azure Portal, jeśli strumień generuje klucze.

1. W Azure Portal przejdź do zasobu połączonego klastra.
2. Na stronie zasób wybierz pozycję "GitOps" i sprawdź listę konfiguracji dla tego klastra.
3. Wybierz konfigurację, która używa prywatnego repozytorium git.
4. W otwartym oknie kontekstu w dolnej części okna Skopiuj **klucz publiczny repozytorium**.

#### <a name="add-public-key-using-github"></a>Dodawanie klucza publicznego za pomocą usługi GitHub

Skorzystaj z jednej z następujących opcji:

* Opcja 1: Dodaj klucz publiczny do konta użytkownika (dotyczy wszystkich repozytoriów na Twoim koncie):  
    1. Otwórz witrynę GitHub i kliknij ikonę profilu w prawym górnym rogu strony.
    2. Kliknij pozycję **Settings** (Ustawienia).
    3. Kliknij pozycję **klucze SSH i GPG**.
    4. Kliknij pozycję **nowy klucz SSH**.
    5. Podaj tytuł.
    6. Wklej klucz publiczny bez żadnego otaczającego cudzysłowu.
    7. Kliknij pozycję **Dodaj klucz SSH**.

* Opcja 2: Dodaj klucz publiczny jako klucz wdrożenia do repozytorium git (dotyczy tylko tego repozytorium):  
    1. Otwórz witrynę GitHub i przejdź do repozytorium.
    1. Kliknij pozycję **Settings** (Ustawienia).
    1. Kliknij pozycję **Wdróż klucze**.
    1. Kliknij pozycję **Dodaj klucz wdrożenia**.
    1. Podaj tytuł.
    1. Zaznacz pole wyboru **Zezwalaj na dostęp do zapisu**.
    1. Wklej klucz publiczny bez żadnego otaczającego cudzysłowu.
    1. Kliknij pozycję **Dodaj klucz**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Dodawanie klucza publicznego przy użyciu repozytorium usługi Azure DevOps

Aby dodać klucz do kluczy SSH, wykonaj następujące kroki:

1. W obszarze **Ustawienia użytkownika** w prawym górnym rogu (obok obrazu profilu) kliknij pozycję **klucze publiczne SSH**.
1. Wybierz pozycję  **+ nowy klucz**.
1. Podaj nazwę.
1. Wklej klucz publiczny bez żadnego otaczającego cudzysłowu.
1. Kliknij pozycję **Dodaj**.

## <a name="validate-the-kubernetes-configuration"></a>Weryfikowanie konfiguracji Kubernetes

Po `config-agent` zainstalowaniu wystąpienia zasoby, które znajdują się `flux` w repozytorium git, powinny zacząć przepływać do klastra. Sprawdź, czy przestrzenie nazw, wdrożenia i zasoby zostały utworzone za pomocą następującego polecenia:

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Możemy zobaczyć, że `team-a` zostały `team-b` `itops` `cluster-config` utworzone przestrzenie nazw,,, i.

`flux`Operator został wdrożony w `cluster-config` przestrzeni nazw, zgodnie z podanym przez zasób konfiguracji:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Dalsza eksploracja

Inne zasoby wdrożone w ramach repozytorium konfiguracji można eksplorować przy użyciu:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usuń konfigurację przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal. Po uruchomieniu polecenia Delete zasób konfiguracji zostanie natychmiast usunięty na platformie Azure. Pełne usunięcie skojarzonych obiektów z klastra powinno wystąpić w ciągu 10 minut. Jeśli po usunięciu konfiguracja jest w stanie niepowodzenia, pełne usunięcie skojarzonych obiektów może potrwać do godziny.

Po `namespace` usunięciu konfiguracji z zakresem przestrzeń nazw nie jest usuwana przez łuk Azure, aby uniknąć przerywania istniejących obciążeń. W razie konieczności można ręcznie usunąć tę przestrzeń nazw za pomocą polecenia `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Po usunięciu konfiguracji wszystkie zmiany w klastrze, które były wynikiem wdrożeń z śledzonego repozytorium git, nie są usuwane.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak zaimplementować CI/CD z GitOps.
> [!div class="nextstepaction"]
> [Implementowanie ciągłej integracji/ciągłego wdrażania za pomocą GitOps](./tutorial-gitops-ci-cd.md)
