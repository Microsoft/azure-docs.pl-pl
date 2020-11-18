---
title: Wdrażanie konfiguracji przy użyciu usługi GitOps w klastrze Kubernetes (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Korzystanie z GitOps dla konfiguracji klastra z obsługą usługi Azure ARC (wersja zapoznawcza)
keywords: GitOps, Kubernetes, K8s, Azure, ARC, Azure Kubernetes Service, kontenery
ms.openlocfilehash: ce6c754c308d2979db9b1b8eb36e7858e8a91c3c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659798"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Wdrażanie konfiguracji przy użyciu usługi GitOps w klastrze Kubernetes (wersja zapoznawcza)

GitOps to metoda deklarująca żądany stan konfiguracji Kubernetes (wdrożenia, przestrzenie nazw itd.) w repozytorium git, a następnie wdrożenie oparte na sondowanie i ściąganie tych konfiguracji w klastrze za pomocą operatora. Ten dokument obejmuje konfigurację takich przepływów pracy w klastrach Kubernetes z obsługą usługi Azure Arc.

Połączenie między klastrem a jednym lub wieloma repozytoriami git jest śledzone w Azure Resource Manager jako `sourceControlConfiguration` zasób rozszerzenia. `sourceControlConfiguration`Właściwości zasobu przedstawiają miejsce i sposób przepływu zasobów Kubernetes z usługi git do klastra. `sourceControlConfiguration`Dane są przechowywane w postaci zaszyfrowanej w bazie danych Azure Cosmos DB w celu zapewnienia poufności danych.

`config-agent`Działający w klastrze jest odpowiedzialny za obserwowanie nowych lub zaktualizowanych `sourceControlConfiguration` zasobów rozszerzeń w zasobie Kubernetes z włączoną funkcją Azure ARC, wdrożenie operatora strumieniowego w celu obejrzenia repozytorium git i propagowanie wszelkich aktualizacji wprowadzonych do programu `sourceControlConfiguration` . Istnieje również możliwość utworzenia wielu `sourceControlConfiguration` zasobów z `namespace` zakresem w tym samym klastrze Kubernetes z obsługą usługi Azure Arc w celu osiągnięcia wielu dzierżawców. W takim przypadku każdy operator może wdrażać tylko konfiguracje w odpowiedniej przestrzeni nazw.

Repozytorium git może zawierać wszystkie prawidłowe zasoby Kubernetes, w tym przestrzenie nazw, ConfigMaps, wdrożenia, DaemonSets itd.  Może również zawierać wykresy Helm na potrzeby wdrażania aplikacji. Typowym zestawem scenariuszy jest definiowanie konfiguracji bazowej dla organizacji, która może obejmować typowe role i powiązania platformy Azure, agentów monitorowania i rejestrowania oraz usług w całym klastrze.

Ten sam wzorzec może służyć do zarządzania większą kolekcją klastrów, które mogą być wdrażane w środowiskach heterogenicznych. Na przykład można mieć jedno repozytorium definiujące konfigurację bazową organizacji i zastosować je do dziesiątek klastrów Kubernetes jednocześnie. [Usługa Azure Policy umożliwia automatyzację](use-azure-policy.md) tworzenia `sourceControlConfiguration` z określonym zestawem parametrów we wszystkich zasobach Kubernetes z obsługą usługi Azure Arc w ramach zakresu (subskrypcji lub grupy zasobów).

Ten przewodnik wprowadzający przeprowadzi Cię przez proces stosowania zestawu konfiguracji z zakresem administratora klastra.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący Kubernetes połączony klaster usługi Azure Arc. Jeśli potrzebny jest podłączony klaster, zobacz temat [łączenie się z klastrem szybki start](./connect-cluster.md).

## <a name="create-a-configuration"></a>Utwórz konfigurację

[Przykładowe repozytorium](https://github.com/Azure/arc-k8s-demo) używane w tym dokumencie jest strukturalne wokół osoby będącej operatorem klastra, który chce udostępnić kilka przestrzeni nazw, wdrożyć typowe obciążenie i zapewnić konfigurację specyficzną dla zespołu. Użycie tego repozytorium powoduje utworzenie następujących zasobów w klastrze:

**Przestrzenie nazw:** `cluster-config` , `team-a` , `team-b` 
 **wdrożenie:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

`config-agent`Sonduje platformę Azure pod kątem nowych lub zaktualizowanych `sourceControlConfiguration` co 30 sekund, co jest maksymalny czas potrzebny `config-agent` na pobranie nowej lub zaktualizowanej konfiguracji.
W przypadku kojarzenia repozytorium prywatnego z programem `sourceControlConfiguration` upewnij się, że wykonano również kroki opisane w temacie [Zastosuj konfigurację z prywatnego repozytorium git](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Za pomocą rozszerzenia interfejsu wiersza polecenia platformy Azure dla programu `k8sconfiguration` Połączmy nasz połączony klaster z [przykładowym repozytorium git](https://github.com/Azure/arc-k8s-demo). Ta konfiguracja zostanie nadana nazwie `cluster-config` , nakazuje agentowi wdrożenie operatora w `cluster-config` przestrzeni nazw i nadanie `cluster-admin` uprawnień operatora.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Rozdzielczości**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>repozytorium — parametr adresu URL

Oto obsługiwane scenariusze dla wartości parametru--Repository-URL.

| Scenariusz | Format | Opis |
| ------------- | ------------- | ------------- |
| Publiczne repozytorium git | http [s]://Server/repo.git lub git://server/repo.git   | Publiczne repozytorium git  |
| Prywatne repozytorium git — klucze utworzone za pośrednictwem protokołu SSH – strumień | SSH://[user@] serwer/repozytorium. git lub [user@] serwer: repozytorium. git | Klucz publiczny wygenerowany przez strumień musi zostać dodany do konta użytkownika w dostawcy usługi git. Jeśli klucz wdrożenia zostanie dodany do repozytorium zamiast z konta użytkownika, Użyj zamiast `git@` `user@` . Więcej informacji można znaleźć [tutaj](#apply-configuration-from-a-private-git-repository) |

Te scenariusze są obsługiwane przez strumień, ale jeszcze nie przez sourceControlConfiguration.

| Scenariusz | Format | Opis |
| ------------- | ------------- | ------------- |
| Prywatne repozytorium git — HTTPS | https://server/repo.git | Wkrótce (będzie obsługiwać nazwę użytkownika/hasło, nazwę użytkownika/token, certyfikat) |
| Prywatne repozytorium git — klucze dostarczone przez użytkownika | SSH://[user@] serwer/repozytorium. git lub [user@] serwer: repozytorium. git | Już wkrótce |
| Prywatny Host git — SSH — niestandardowy known_hosts | SSH://[user@] serwer/repozytorium. git lub [user@] serwer: repozytorium. git | Już wkrótce |

#### <a name="additional-parameters"></a>Dodatkowe parametry

Aby dostosować Tworzenie konfiguracji, poniżej przedstawiono kilka dodatkowych parametrów:

`--enable-helm-operator` : *Opcjonalny* przełącznik umożliwiający włączenie obsługi wdrożeń wykresów Helm.

`--helm-operator-chart-values` : *Opcjonalne* wartości wykresu dla operatora Helm (jeśli włączone).  Na przykład '--Set Helm. Versions = v3 '.

`--helm-operator-chart-version` : *Opcjonalna* wersja wykresu dla operatora Helm (jeśli jest włączona). Wartość domyślna: "0.6.0".

`--operator-namespace` : *Opcjonalna* nazwa przestrzeni nazw operatora. Wartość domyślna: "default"

`--operator-params` : Parametry *opcjonalne* dla operatora. Musi być określona w cudzysłowie pojedynczym. Na przykład ```--operator-params='--git-readonly --git-path=releases' ```

Opcje obsługiwane w--operator-params

| Opcja | Opis |
| ------------- | ------------- |
| --git-branch  | Gałąź repozytorium git do użycia na potrzeby manifestów Kubernetes. Wartość domyślna to "Master". |
| --git-Path  | Ścieżka względna w repozytorium Git na potrzeby strumieniowego lokalizowania manifestów Kubernetes. |
| --git-ReadOnly | Repozytorium Git będzie uznawane za tylko do odczytu; Strumień nie będzie próbować zapisywać do niego. |
| --Manifest-Generation  | Jeśli ta funkcja jest włączona, strumień będzie szukać metadanych. strumień. YAML i Run Kustomize lub innych generatorów manifestów. |
| --git-sondowa-Interval  | Okres, w którym można sondować repozytorium git dotyczące nowych zatwierdzeń. Wartość domyślna to "5 m" (5 minut). |
| --Sync-Collector-Collection  | Jeśli ta funkcja jest włączona, strumień spowoduje usunięcie utworzonych przez siebie zasobów, ale nie są już dostępne w usłudze git. |
| --git-Label  | Etykieta, aby śledzić postęp synchronizacji, używany do tagowania gałęzi git.  Wartość domyślna to "strumień-Sync". |
| --git-User  | Nazwa użytkownika dla zatwierdzenia git. |
| --git-email  | Wiadomość e-mail do użycia na potrzeby zatwierdzenia git. |

* Jeśli nie ustawiono opcji "--Git-User" lub "--Git-email" (co oznacza, że nie chcesz mieć strumienia do zapisu w repozytorium), a następnie--plik git-ReadOnly zostanie automatycznie ustawiony (jeśli nie został jeszcze ustawiony).

* Jeśli enableHelmOperator ma wartość true, wówczas ciągi operatorInstanceName + operatorNamespace nie mogą zawierać więcej niż 47 znaków.  W przypadku niepowodzenia przestrzegania tego limitu zostanie wyświetlony następujący błąd:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Aby uzyskać więcej informacji, zobacz [Dokumentacja strumienia](https://aka.ms/FluxcdReadme).

> [!TIP]
> Istnieje możliwość utworzenia sourceControlConfiguration na Azure Portal, a także na karcie **konfiguracje** w bloku zasobów Kubernetes z funkcją Azure Arc.

## <a name="validate-the-sourcecontrolconfiguration"></a>Weryfikowanie sourceControlConfiguration

Za pomocą interfejsu wiersza polecenia platformy Azure Sprawdź, czy `sourceControlConfiguration` został pomyślnie utworzony.

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Należy pamiętać, że `sourceControlConfiguration` zasób jest aktualizowany ze stanem zgodności, komunikatami i informacjami o debugowaniu.

**Rozdzielczości**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
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
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Gdy `sourceControlConfiguration` jest tworzony, kilka rzeczy odbywa się pod okapem:

1. Monitory usługi Azure Arc `config-agent` Azure Resource Manager dla nowych lub zaktualizowanych konfiguracji ( `Microsoft.KubernetesConfiguration/sourceControlConfiguration` )
1. `config-agent` Zauważ nową `Pending` konfigurację
1. `config-agent` Odczytuje właściwości konfiguracji i przygotowuje się do wdrożenia zarządzanego wystąpienia programu `flux`
    * `config-agent` tworzy docelowy obszar nazw
    * `config-agent` przygotowuje konto usługi Kubernetes z odpowiednimi uprawnieniami ( `cluster` lub `namespace` zakresem)
    * `config-agent` wdraża wystąpienie programu `flux`
    * `flux` generuje klucz SSH i rejestruje klucz publiczny
1. `config-agent` raportuje stan z powrotem do `sourceControlConfiguration`

Podczas procesu aprowizacji `sourceControlConfiguration` wystąpią pewne zmiany stanu. Monitoruj postęp przy użyciu `az k8sconfiguration show ...` powyższego polecenia:

1. `complianceStatus` -> `Pending`: reprezentuje Stany początkowe i w toku
1. `complianceStatus` -> `Installed`: udało `config-agent` się pomyślnie skonfigurować klaster i wdrożyć go `flux` bez błędu
1. `complianceStatus` -> `Failed`: `config-agent` Wystąpił błąd podczas wdrażania `flux` , szczegółowe informacje powinny być dostępne w `complianceStatus.message` treści odpowiedzi

## <a name="apply-configuration-from-a-private-git-repository"></a>Zastosuj konfigurację z prywatnego repozytorium git

Jeśli używasz prywatnego repozytorium git, musisz wykonać jedno zadanie, aby zamknąć pętlę: Dodaj klucz publiczny wygenerowany przez program `flux` jako **klucz wdrożenia** w repozytorium.

**Pobieranie klucza publicznego przy użyciu interfejsu wiersza polecenia platformy Azure**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Pobierz klucz publiczny z Azure Portal**

1. W Azure Portal przejdź do zasobu połączonego klastra.
2. Na stronie zasób wybierz pozycję konfiguracje i sprawdź listę konfiguracji dla tego klastra.
3. Wybierz konfigurację, która używa prywatnego repozytorium git.
4. W otwartym oknie kontekstu w dolnej części okna Skopiuj **klucz publiczny repozytorium**.

Jeśli używasz usługi GitHub, użyj jednej z następujących dwóch opcji:

**Opcja 1: Dodawanie klucza publicznego do konta użytkownika**

1. Otwórz witrynę GitHub, kliknij ikonę profilu w prawym górnym rogu strony.
2. Kliknij pozycję **Ustawienia**
3. Kliknij **klucze SSH i GPG**
4. Kliknij **nowy klucz SSH**
5. Podaj tytuł
6. Wklej klucz publiczny (bez cudzysłowów)
7. Kliknij pozycję **Dodaj klucz SSH**

**Opcja 2: Dodaj klucz publiczny jako klucz wdrożenia do repozytorium git**

1. Otwórz witrynę GitHub, przejdź do repozytorium, kliknij pozycję **Ustawienia**, a następnie pozycję **Wdróż klucze** .
2. Kliknij pozycję **Dodaj klucz wdrożenia**
3. Podaj tytuł
4. Sprawdź **Zezwalanie na dostęp do zapisu**
5. Wklej klucz publiczny (bez cudzysłowów)
6. Kliknij pozycję **Dodaj klucz**

**Jeśli używasz repozytorium usługi Azure DevOps, Dodaj klucz do kluczy SSH**

1. W obszarze **Ustawienia użytkownika** w prawym górnym rogu (obok obrazu profilu) kliknij pozycję **klucze publiczne SSH**
1. Wybierz pozycję  **+ nowy klucz**
1. Podaj nazwę
1. Wklej klucz publiczny bez żadnych otaczających cudzysłowów
1. Kliknij przycisk **Dodaj**

## <a name="validate-the-kubernetes-configuration"></a>Weryfikowanie konfiguracji Kubernetes

Po `config-agent` zainstalowaniu wystąpienia zasoby, które znajdują się `flux` w repozytorium git, powinny zacząć przepływać do klastra. Sprawdź, czy zostały utworzone obszary nazw, wdrożenia i zasoby:

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

Inne zasoby wdrożone w ramach repozytorium konfiguracji można eksplorować:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Usuń konfigurację

Usuń `sourceControlConfiguration` przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal.  Po zainicjowaniu polecenia Delete `sourceControlConfiguration` zasób zostanie natychmiast usunięty na platformie Azure, ale może upłynąć do 1 godziny w celu pełnego usunięcia skojarzonych obiektów z klastra (mamy element zaległości, aby skrócić ten czas zwłoki).

> [!NOTE]
> Po utworzeniu sourceControlConfiguration z zakresem przestrzeni nazw istnieje możliwość, że użytkownicy z `edit` powiązaniem roli w przestrzeni nazw mogą wdrażać obciążenia w tej przestrzeni nazw. Gdy ten `sourceControlConfiguration` zakres przestrzeni nazw zostanie usunięty, przestrzeń nazw pozostaje nienaruszona i nie zostanie usunięta, aby uniknąć przerywania tych obciążeń.
> Wszelkie zmiany w klastrze, które były wynikiem wdrożeń z śledzonego repozytorium git, nie są usuwane po `sourceControlConfiguration` usunięciu.

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Rozdzielczości**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Następne kroki

- [Użyj Helm z konfiguracją kontroli źródła](./use-gitops-with-helm.md)
- [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)
