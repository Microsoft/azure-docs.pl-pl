---
title: Wdrażanie konfiguracji przy użyciu metodyki GitOps w klastrach platformy Kubernetes z włączoną usługą Azure Arc (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Użyj GitOps, aby skonfigurować klaster Kubernetes z włączonym usługą Azure ARC (wersja zapoznawcza)
keywords: GitOps, Kubernetes, K8s, Azure, ARC, Azure Kubernetes Service, AKS, kontenery
ms.openlocfilehash: 072bfc8c243eb9b69e06366961019b88b67e0941
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392242"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Wdrażanie konfiguracji przy użyciu metodyki GitOps w klastrach platformy Kubernetes z włączoną usługą Azure Arc (wersja zapoznawcza)

W odniesieniu do Kubernetes, GitOps jest celem deklarowania żądanego stanu konfiguracji klastra Kubernetes (wdrożenia, przestrzenie nazw itp.) w repozytorium git. Po tej deklaracji następuje rozmieszczenie i wdrażanie tych konfiguracji klastra przy użyciu operatora. 

W tym artykule opisano konfigurację przepływów pracy GitOps w klastrach Kubernetes z obsługą usługi Azure Arc.

Połączenie między klastrem a repozytorium git jest tworzone jako `Microsoft.KubernetesConfiguration/sourceControlConfigurations` zasób rozszerzenia w Azure Resource Manager. `sourceControlConfiguration`Właściwości zasobu przedstawiają miejsce i sposób przepływu zasobów Kubernetes z usługi git do klastra. `sourceControlConfiguration`Dane są przechowywane w postaci zaszyfrowanej w bazie danych Azure Cosmos DB w celu zapewnienia poufności danych.

`config-agent`Działający w klastrze jest odpowiedzialny za:
* Śledzenie nowych lub zaktualizowanych `sourceControlConfiguration` zasobów rozszerzeń w zasobie usługi Azure ARC z włączonym Kubernetes.
* Wdrożenie operatora strumieniowego w celu obejrzenia repozytorium git dla każdego z nich `sourceControlConfiguration` .
* Zastosowanie aktualizacji do dowolnego z nich `sourceControlConfiguration` . 

Można utworzyć wiele `sourceControlConfiguration` zasobów w tym samym klastrze Kubernetes z obsługą usługi Azure Arc w celu osiągnięcia wielu dzierżawców. Ogranicz wdrożenia do odpowiednich przestrzeni nazw, tworząc każdy `sourceControlConfiguration` z nich z innym `namespace` zakresem.

Repozytorium git może zawierać:
* YAML manifesty opisujące wszystkie ważne zasoby Kubernetes, w tym obszary nazw, ConfigMaps, wdrożenia, DaemonSets itp. 
* Wykresy Helm na potrzeby wdrażania aplikacji. 

Typowy zestaw scenariuszy obejmuje zdefiniowanie konfiguracji linii bazowej dla organizacji, takich jak typowe role i powiązania platformy Azure, agenci monitorowania lub rejestrowania lub usługi w całym klastrze.

Ten sam wzorzec może służyć do zarządzania większą kolekcją klastrów, które mogą być wdrażane w środowiskach heterogenicznych. Na przykład istnieje jedno repozytorium, które definiuje konfigurację bazową dla organizacji, która dotyczy wielu klastrów Kubernetes jednocześnie. [Azure Policy można zautomatyzować](use-azure-policy.md) tworzenie `sourceControlConfiguration` z określonym zestawem parametrów we wszystkich zasobach Kubernetes z obsługą usługi Azure Arc w ramach zakresu (subskrypcji lub grupy zasobów).

Wykonaj następujące kroki, aby dowiedzieć się, jak zastosować zestaw konfiguracji z `cluster-admin` zakresem.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Upewnij się, że masz już podłączony połączony klaster usługi Azure Arc Kubernetes. Jeśli potrzebujesz podłączonego klastra, zobacz temat [nawiązywanie połączenia z usługą Azure ARC z włączoną obsługą klastra Kubernetes](./connect-cluster.md).

## <a name="create-a-configuration"></a>Utwórz konfigurację

[Przykładowe repozytorium](https://github.com/Azure/arc-k8s-demo) używane w tym artykule jest strukturalne wokół osoby będącej operatorem klastra, który chce udostępnić kilka przestrzeni nazw, wdrożyć typowe obciążenie i zapewnić konfigurację specyficzną dla zespołu. Użycie tego repozytorium powoduje utworzenie następujących zasobów w klastrze:


* **Przestrzenie nazw:** `cluster-config` , `team-a` , `team-b`
* **Wdrożenie:**`cluster-config/azure-vote`
* **ConfigMap:**`team-a/endpoints`

`config-agent`Sonduje platformę Azure pod kątem nowych lub zaktualizowanych `sourceControlConfiguration` . To zadanie zajmie do 30 sekund.

W przypadku kojarzenia repozytorium prywatnego z programem `sourceControlConfiguration` należy wykonać kroki opisane w temacie [Zastosuj konfigurację z prywatnego repozytorium git](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Użyj rozszerzenia interfejsu wiersza polecenia platformy Azure, aby `k8sconfiguration` połączyć połączony klaster z [przykładowym repozytorium git](https://github.com/Azure/arc-k8s-demo). 
1. Nadaj nazwę tej konfiguracji `cluster-config` .
1. Nakazuje agentowi wdrożenie operatora w `cluster-config` przestrzeni nazw.
1. Nadaj uprawnienia operatora `cluster-admin` .

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Rozdzielczości**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

#### <a name="use-a-public-git-repo"></a>Używanie publicznego repozytorium git

| Parametr | Format |
| ------------- | ------------- |
| `--repository-url` | http [s]://Server/repo [. git] lub git://Server/repo [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Korzystanie z prywatnego repozytorium git z użyciem kluczy SSH i strumieniowego tworzenia

| Parametr | Format | Uwagi
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] lub user@server:repo [. git] | `git@` może zastąpić `user@`

> [!NOTE]
> Klucz publiczny wygenerowany przez strumień musi zostać dodany do konta użytkownika w dostawcy usługi git. Jeśli klucz zostanie dodany do repozytorium zamiast konta użytkownika, użyj `git@` zamiast `user@` adresu URL. Aby uzyskać więcej informacji, przejdź do sekcji [Zastosuj konfigurację z prywatnego repozytorium git](#apply-configuration-from-a-private-git-repository) .

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Korzystanie z prywatnego repozytorium git z kluczami SSH i dostarczonymi przez użytkownika

| Parametr | Format | Uwagi |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] lub user@server:repo [. git] | `git@` może zastąpić `user@` |
| `--ssh-private-key` | klucz szyfrowany algorytmem Base64 w [formacie PEM](https://aka.ms/PEMformat) | Podaj klucz bezpośrednio |
| `--ssh-private-key-file` | Pełna ścieżka do pliku lokalnego | Podaj pełną ścieżkę do pliku lokalnego, który zawiera klucz w formacie PEM

> [!NOTE]
> Podaj własny klucz prywatny bezpośrednio lub w pliku. Klucz musi mieć [Format PEM](https://aka.ms/PEMformat) i kończyć się znakiem nowego wiersza (\n).  Skojarzony klucz publiczny należy dodać do konta użytkownika w dostawcy usługi git. Jeśli klucz zostanie dodany do repozytorium zamiast konta użytkownika, Użyj zamiast `git@` `user@` . Aby uzyskać więcej informacji, przejdź do sekcji [Zastosuj konfigurację z prywatnego repozytorium git](#apply-configuration-from-a-private-git-repository) .

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Korzystanie z prywatnego hosta git z użyciem protokołów SSH i znanych przez użytkownika

| Parametr | Format | Uwagi |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] lub user@server:repo [. git] | `git@` może zastąpić `user@` |
| `--ssh-known-hosts` | kodowane algorytmem Base64 | Zapewnianie informacji o znanych hostach bezpośrednio |
| `--ssh-known-hosts-file` | Pełna ścieżka do pliku lokalnego | Podaj zawartość znanych hostów w pliku lokalnym |

> [!NOTE]
> Aby można było uwierzytelnić repozytorium git przed ustanowieniem połączenia SSH, operator strumieni przechowuje listę typowych hostów Git w rozpoznawanym pliku Hosts. Jeśli używasz nietypowego repozytorium Git lub własnego hosta git, może być konieczne podanie klucza hosta, aby upewnić się, że strumień może identyfikować repozytorium. Zawartość known_hosts można dostarczyć bezpośrednio lub w pliku. Użyj [known_hosts specyfikacji formatu zawartości](https://aka.ms/KnownHostsFormat) w połączeniu z jednym z określonych powyżej scenariuszy SSH podczas udostępniania zawartości.

#### <a name="use-a-private-git-repo-with-https"></a>Korzystanie z prywatnego repozytorium git z protokołem HTTPS

| Parametr | Format | Uwagi |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | HTTPS z uwierzytelnianiem podstawowym |
| `--https-user` | nieprzetworzone lub zakodowane algorytmem Base64 | Nazwa użytkownika protokołu HTTPS |
| `--https-key` | nieprzetworzone lub zakodowane algorytmem Base64 | Osobisty token dostępu HTTPS lub hasło

> [!NOTE]
> Prywatna autoryzacja wydania HTTPS Helm jest obsługiwana tylko w przypadku wykresu operatora Helm w wersji 1.2.0 + (domyślnie).
> Prywatne uwierzytelnianie w wersji Helm protokołu HTTPS nie jest obecnie obsługiwane w przypadku klastrów zarządzanych przez usługi Azure Kubernetes Services.
> Jeśli potrzebujesz strumieniowego dostępu do repozytorium Git za pomocą serwera proxy, musisz zaktualizować agentów usługi Azure ARC przy użyciu ustawień serwera proxy. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia przy użyciu serwera proxy wychodzącego](./connect-cluster.md#connect-using-an-outbound-proxy-server).

#### <a name="additional-parameters"></a>Dodatkowe parametry

Dostosuj konfigurację przy użyciu następujących parametrów opcjonalnych:

| Parametr | Opis |
| ------------- | ------------- |
| `--enable-helm-operator`| Przełącz, aby włączyć obsługę wdrożeń wykresów Helm. |
| `--helm-operator-params` | Wartości wykresu dla operatora Helm (jeśli jest włączony). Na przykład `--set helm.versions=v3`. |
| `--helm-operator-version` | Wersja wykresu dla operatora Helm (jeśli jest włączona). Użyj wersji 1.2.0 +. Wartość domyślna: "1.2.0". |
| `--operator-namespace` | Nazwa dla przestrzeni nazw operatora. Wartość domyślna: "default". Maks.: 23 znaki. |
| `--operator-params` | Parametry operatora. Musi być określona w cudzysłowie pojedynczym. Na przykład ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ``` 

##### <a name="options-supported-in----operator-params"></a>Opcje obsługiwane w  `--operator-params` :

| Opcja | Opis |
| ------------- | ------------- |
| `--git-branch`  | Gałąź repozytorium git do użycia na potrzeby manifestów Kubernetes. Wartość domyślna to "Master". Nowsze repozytoria mają gałąź główną o nazwie `main` , w tym przypadku należy ustawić `--git-branch=main` . |
| `--git-path`  | Ścieżka względna w repozytorium Git na potrzeby strumieniowego lokalizowania manifestów Kubernetes. |
| `--git-readonly` | Repozytorium Git będzie uznawane za tylko do odczytu; Strumień nie będzie próbować zapisywać do niego. |
| `--manifest-generation`  | Jeśli ta funkcja jest włączona, strumień będzie szukać metadanych. strumień. YAML i Run Kustomize lub innych generatorów manifestów. |
| `--git-poll-interval`  | Okres, w którym można sondować repozytorium git dotyczące nowych zatwierdzeń. Wartość domyślna to `5m` (5 minut). |
| `--sync-garbage-collection`  | Jeśli ta funkcja jest włączona, strumień spowoduje usunięcie utworzonych przez siebie zasobów, ale nie są już dostępne w usłudze git. |
| `--git-label`  | Etykieta do śledzenia postępu synchronizacji. Używane do tagowania gałęzi git.  Wartość domyślna to `flux-sync`. |
| `--git-user`  | Nazwa użytkownika dla zatwierdzenia git. |
| `--git-email`  | Wiadomość e-mail do użycia na potrzeby zatwierdzenia git. 

Jeśli nie chcesz, aby strumieniowo zapisywać w repozytorium i `--git-user` lub `--git-email` nie zostały ustawione, `--git-readonly` zostanie automatycznie ustawione.

Aby uzyskać więcej informacji, zobacz [dokumentację strumienia](https://aka.ms/FluxcdReadme).

> [!TIP]
> Można utworzyć `sourceControlConfiguration` w Azure Portal na karcie **GitOps** zasobu Kubernetes usługi Azure Arc.

## <a name="validate-the-sourcecontrolconfiguration"></a>Weryfikowanie sourceControlConfiguration

Użyj interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy program `sourceControlConfiguration` został pomyślnie utworzony.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`Zasób zostanie zaktualizowany ze stanem zgodności, komunikatami i informacjami o debugowaniu.

**Rozdzielczości**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

Gdy `sourceControlConfiguration` jest tworzony lub aktualizowany, kilka rzeczy odbywa się pod okapem:

1. Usługa Azure Arc `config-agent` monitoruje Azure Resource Manager w przypadku nowych lub zaktualizowanych konfiguracji ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) i zauważy nową `Pending` konfigurację.
1. `config-agent`Odczytuje właściwości konfiguracji i tworzy przestrzeń nazw docelowy.
1. Usługa Azure Arc `controller-manager` przygotowuje konto usługi Kubernetes z odpowiednim uprawnieniem ( `cluster` lub `namespace` zakresem), a następnie wdraża wystąpienie programu `flux` .
1. W przypadku używania opcji SSH z kluczami generowanymi strumieniowo program `flux` generuje klucz SSH i rejestruje klucz publiczny.
1. `config-agent`Stan raportów z powrotem do `sourceControlConfiguration` zasobu na platformie Azure.

Podczas procesu aprowizacji `sourceControlConfiguration` wystąpią pewne zmiany stanu. Monitoruj postęp przy użyciu `az k8sconfiguration show ...` powyższego polecenia:

| Zmiana etapu | Opis |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Reprezentuje Stany początkowe i w toku. |
| `complianceStatus` -> `Installed`  | `config-agent` udało się pomyślnie skonfigurować klaster i wdrożyć go `flux` bez błędu. |
| `complianceStatus` -> `Failed` | `config-agent` Napotkano błąd podczas wdrażania `flux` , szczegółowe informacje powinny być dostępne w `complianceStatus.message` treści odpowiedzi. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Zastosuj konfigurację z prywatnego repozytorium git

Jeśli używasz prywatnego repozytorium git, musisz skonfigurować klucz publiczny SSH w repozytorium. Klucz publiczny SSH będzie to ten, który generuje strumień lub który jest udostępniany przez użytkownika. Klucz publiczny można skonfigurować w odniesieniu do określonego repozytorium Git lub użytkownika git, który ma dostęp do repozytorium. 

### <a name="get-your-own-public-key"></a>Pobierz swój własny klucz publiczny

Jeśli wygenerowałeś własne klucze SSH, masz już klucze prywatne i publiczne.

#### <a name="get-the-public-key-using-azure-cli"></a>Pobieranie klucza publicznego przy użyciu interfejsu wiersza polecenia platformy Azure 

Poniższe elementy są przydatne, jeśli strumień generuje klucze.

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Pobierz klucz publiczny z Azure Portal

Poniższe elementy są przydatne, jeśli strumień generuje klucze.

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

**Rozdzielczości**

```console
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

Ten `flux` operator został wdrożony w `cluster-config` przestrzeni nazw, co zostało przekazane przez nasz `sourceControlConfig` :

```console
kubectl -n cluster-config get deploy  -o wide
```

**Rozdzielczości**

```console
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

## <a name="delete-a-configuration"></a>Usuń konfigurację

Usuń `sourceControlConfiguration` przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal.  Po zainicjowaniu polecenia Delete `sourceControlConfiguration` zasób zostanie natychmiast usunięty na platformie Azure. Pełne usunięcie skojarzonych obiektów z klastra powinno wystąpić w ciągu 10 minut. Jeśli `sourceControlConfiguration` usunięcie jest w stanie niepowodzenia, pełne usunięcie skojarzonych obiektów może potrwać do godziny.

> [!NOTE]
> Po `sourceControlConfiguration` `namespace` utworzeniu zakresu z zakresem użytkownicy z `edit` powiązaniem roli w przestrzeni nazw mogą wdrażać obciążenia w tej przestrzeni nazw. Gdy ten `sourceControlConfiguration` zakres przestrzeni nazw zostanie usunięty, przestrzeń nazw pozostaje nienaruszona i nie zostanie usunięta, aby uniknąć przerywania tych obciążeń. W razie konieczności można ręcznie usunąć tę przestrzeń nazw za pomocą programu `kubectl` .  
> Wszelkie zmiany w klastrze, które były wynikiem wdrożeń z śledzonego repozytorium git, nie są usuwane po `sourceControlConfiguration` usunięciu.

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Rozdzielczości**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Następne kroki

- [Użyj Helm z konfiguracją kontroli źródła](./use-gitops-with-helm.md)
- [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)