---
title: 'Samouczek: wdrażanie konfiguracji przy użyciu usługi GitOps Azure Arc klastrze Kubernetes z włączoną obsługą usługi'
description: W tym samouczku pokazano stosowanie konfiguracji w Azure Arc Kubernetes z włączoną obsługą. Aby uzyskać koncepcyjne omówienie tego procesu, zobacz artykuł Configurations and GitOps - Azure Arc enabled Kubernetes (Konfiguracje i gitOps — Azure Arc rozwiązania Kubernetes).
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: 0f1172ffa0d29734e7ec005bf2812eeca215aa0d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484170"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Samouczek: wdrażanie konfiguracji przy użyciu usługi GitOps Azure Arc klastrze Kubernetes z włączoną obsługą usługi 

W tym samouczku zastosujemy konfiguracje przy użyciu usługi GitOps w Azure Arc Kubernetes z włączoną obsługą. Omawiane tematy:

> [!div class="checklist"]
> * Utwórz konfigurację w klastrze Kubernetes z włączoną Azure Arc przy użyciu przykładowego repozytorium Git.
> * Sprawdź, czy konfiguracja została pomyślnie utworzona.
> * Zastosuj konfigurację z prywatnego repozytorium Git.
> * Zweryfikuj konfigurację usługi Kubernetes.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Istniejący klaster Azure Arc z usługą Kubernetes.
    - Jeśli jeszcze nie połączono klastra, przewodnik Szybki start Connect [an Azure Arc enabled Kubernetes cluster (Łączenie klastra Kubernetes](quickstart-connect-cluster.md)z włączoną obsługą usługi Kubernetes).
- Znajomość zalet i architektury tej funkcji. Przeczytaj więcej w artykule [Configurations and GitOps - Azure Arc enabled Kubernetes (Konfiguracje i gitops — Azure Arc kubernetes).](conceptual-configurations.md)
- Zainstaluj rozszerzenie `k8s-configuration` interfejsu wiersza polecenia platformy Azure dla wersji >= 1.0.0:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > Jeśli rozszerzenie `k8s-configuration` jest już zainstalowane, możesz zaktualizować je do najnowszej wersji za pomocą następującego polecenia : `az extension update --name k8s-configuration`

## <a name="create-a-configuration"></a>Tworzenie konfiguracji

Przykładowe [repozytorium](https://github.com/Azure/arc-k8s-demo) używane w tym artykule jest ustrukturyzowane wokół osoby operatora klastra. Manifesty w tym repozytorium aprowizują kilka przestrzeni nazw, wdrażają obciążenia i zapewniają konfigurację specyficzną dla zespołu. Użycie tego repozytorium z usługą GitOps powoduje utworzenie następujących zasobów w klastrze:

* Przestrzenie nazw: `cluster-config` , `team-a` , `team-b`
* Wdrażania: `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

Sonduje `config-agent` platformę Azure pod temat nowych lub zaktualizowanych konfiguracji. To zadanie potrwa do 30 sekund.

Jeśli skojarzysz repozytorium prywatne z konfiguracją, wykonaj poniższe kroki opisane w artykule Apply configuration from a private Git repository (Stosowanie konfiguracji [z prywatnego repozytorium Git).](#apply-configuration-from-a-private-git-repository)

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj rozszerzenia interfejsu wiersza polecenia platformy Azure dla polecenia , `k8s-configuration` aby połączyć połączony klaster z [przykładowe repozytorium Git.](https://github.com/Azure/arc-k8s-demo) 
1. Nadaj tej konfiguracji nazwę `cluster-config` .
1. Poinstruuj agenta, aby wdrożył operator w przestrzeni `cluster-config` nazw .
1. Nadaj operatorowi `cluster-admin` uprawnienia.

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

### <a name="use-a-public-git-repository"></a>Korzystanie z publicznego repozytorium Git

| Parametr | Format |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] lub git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Używanie prywatnego repozytorium Git z kluczami utworzonymi przez SSH i Flux

Dodaj klucz publiczny wygenerowany przez platformę Flux do konta użytkownika u dostawcy usług Git. Jeśli klucz zostanie dodany do repozytorium, a nie do konta użytkownika, użyj wartości zamiast w `git@` `user@` adresie URL. 

Przejdź do sekcji [Zastosuj konfigurację z prywatnego repozytorium Git,](#apply-configuration-from-a-private-git-repository) aby uzyskać więcej szczegółów.


| Parametr | Format | Uwagi
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] lub user@server:repo [.git] | `git@` może zastąpić `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Używanie prywatnego repozytorium Git z kluczami SSH i kluczami dostarczonymi przez użytkownika

Podaj własny klucz prywatny bezpośrednio lub w pliku. Klucz musi być w [formacie PEM i](https://aka.ms/PEMformat) kończyć się nowym wierszem (\n). 

Dodaj skojarzony klucz publiczny do konta użytkownika w dostawcy usług Git. Jeśli klucz zostanie dodany do repozytorium, a nie do konta użytkownika, użyj `git@` zamiast . `user@` 

Przejdź do sekcji [Zastosuj konfigurację z prywatnego repozytorium Git,](#apply-configuration-from-a-private-git-repository) aby uzyskać więcej szczegółów.  

| Parametr | Format | Uwagi |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] lub user@server:repo [.git] | `git@` może zastąpić `user@` |
| `--ssh-private-key` | Klucz zakodowany w formacie base64 w [formacie PEM](https://aka.ms/PEMformat) | Bezpośrednie podanie klucza |
| `--ssh-private-key-file` | pełna ścieżka do pliku lokalnego | Podaj pełną ścieżkę do pliku lokalnego, który zawiera klucz formatu PEM

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Używanie prywatnego hosta Git z programem SSH i znanymi hostami dostarczonymi przez użytkownika

Operator Flux przechowuje listę typowych hostów Git w pliku znanych hostów w celu uwierzytelnienia repozytorium Git przed nawiązaniem połączenia SSH. Jeśli używasz *nietypowego* repozytorium Git lub własnego hosta Git, możesz podać klucz hosta, aby strumień Flux rozpoznał Twoje repozytorium. 

Podobnie jak klucze prywatne, możesz podać known_hosts zawartości bezpośrednio lub w pliku. Podczas dostarczania własnej zawartości należy użyć [known_hosts formatu](https://aka.ms/KnownHostsFormat)zawartości wraz z jednym z powyższych scenariuszy kluczy SSH.

| Parametr | Format | Uwagi |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] lub user@server:repo [.git] | `git@` może zastąpić `user@` |
| `--ssh-known-hosts` | Zakodowane w formacie base64 | Bezpośrednie zapewnianie zawartości znanych hostów |
| `--ssh-known-hosts-file` | pełna ścieżka do pliku lokalnego | Podaj zawartość znanych hostów w pliku lokalnym |

### <a name="use-a-private-git-repository-with-https"></a>Używanie prywatnego repozytorium Git z protokołem HTTPS

| Parametr | Format | Uwagi |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[.git] | Protokół HTTPS z uwierzytelnianiem podstawowym |
| `--https-user` | nieprzetworzone lub zakodowane w formacie base64 | Nazwa użytkownika protokołu HTTPS |
| `--https-key` | nieprzetworzone lub zakodowane w formacie base64 | Osobisty token dostępu HTTPS lub hasło

>[!NOTE]
>* Pakiet operatorów programu Helm w wersji 1.2.0 lub nowsza obsługuje prywatne uwierzytelnianie wersji programu Helm dla protokołu HTTPS.
>* Wersja helm protokołu HTTPS nie jest obsługiwana w przypadku klastrów zarządzanych usługi AKS.
>* Jeśli potrzebujesz strumienia w celu uzyskania dostępu do repozytorium Git za pośrednictwem serwera proxy, musisz zaktualizować agentów Azure Arc przy użyciu ustawień serwera proxy. Aby uzyskać więcej informacji, zobacz [Connect using an outbound proxy server (Nawiązywanie połączenia przy użyciu serwera proxy ruchu wychodzącego).](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server)


## <a name="additional-parameters"></a>Dodatkowe parametry

Dostosuj konfigurację za pomocą następujących parametrów opcjonalnych:

| Parametr | Opis |
| ------------- | ------------- |
| `--enable-helm-operator`| Przełącz się, aby włączyć obsługę wdrożeń pakietu Helm. |
| `--helm-operator-params` | Wartości wykresów dla operatora Helm (jeśli są włączone). Na przykład `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Wersja wykresu dla operatora Helm (jeśli jest włączona). Użyj wersji 1.2.0+. Wartość domyślna: "1.2.0". |
| `--operator-namespace` | Nazwa przestrzeni nazw operatora. Ustawienie domyślne: "default". Maksymalnie: 23 znaki. |
| `--operator-params` | Parametry operatora. Musi być podany w pojedynczych cudzysłowach. Na przykład ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Opcje obsługiwane w programie  `--operator-params` :

| Opcja | Opis |
| ------------- | ------------- |
| `--git-branch`  | Gałąź repozytorium Git do użycia dla manifestów kubernetes. Wartość domyślna to "master". Nowsze repozytoria mają gałąź główną o nazwie . W `main` takim przypadku należy ustawić wartość `--git-branch=main` . |
| `--git-path`  | Ścieżka względna w repozytorium Git dla strumienia Flux w celu zlokalizowania manifestów kubernetes. |
| `--git-readonly` | Repozytorium Git będzie uznawane za tylko do odczytu. Strumień nie będzie próbował zapisywać w nim danych. |
| `--manifest-generation`  | Jeśli ta opcja jest włączona, funkcja Flux będzie szukać pliku flux.yaml i uruchamia program Kustomize lub inne generatory manifestu. |
| `--git-poll-interval`  | Okres, w którym ma być sondowanych repozytorium Git dla nowych zatwierdzeń. Wartość domyślna `5m` to (5 minut). |
| `--sync-garbage-collection`  | Jeśli ta opcja jest włączona, strumień Flux usunie utworzone zasoby, ale nie będą już dostępne w usłudze Git. |
| `--git-label`  | Etykieta do śledzenia postępu synchronizacji. Służy do oznaczania gałęzi Git.  Wartość domyślna to `flux-sync`. |
| `--git-user`  | Nazwa użytkownika dla zatwierdzenia usługi Git. |
| `--git-email`  | Adres e-mail do użycia na użytek zatwierdzania usługi Git. 

Jeśli nie chcesz, aby strumień Flux zapisywał w repozytorium i nie został ustawiony, zostanie `--git-user` `--git-email` on ustawiony `--git-readonly` automatycznie.

Aby uzyskać więcej informacji, zobacz [dokumentację flux](https://aka.ms/FluxcdReadme).

> [!TIP]
> Konfigurację można utworzyć w witrynie Azure Portal na karcie **GitOps** Azure Arc zasobu Kubernetes.

## <a name="validate-the-configuration"></a>Weryfikowanie konfiguracji

Użyj interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy konfiguracja została pomyślnie utworzona.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Zasób konfiguracji zostanie zaktualizowany o informacje o stanie zgodności, komunikatach i debugowaniu.

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

Podczas tworzenia lub aktualizowania konfiguracji dzieje się kilka rzeczy:

1. Program Azure Arc monitoruje Azure Resource Manager dla nowych lub zaktualizowanych `config-agent` konfiguracji ( ) i zauważa `Microsoft.KubernetesConfiguration/sourceControlConfigurations` nową `Pending` konfigurację.
1. Obiekt `config-agent` odczytuje właściwości konfiguracji i tworzy docelową przestrzeń nazw.
1. Aplikacja Azure Arc konto usługi Kubernetes i mapuje je na `controller-manager` [klasterRoleBinding lub RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) dla odpowiednich uprawnień ( `cluster` lub `namespace` zakresu). Następnie wdraża wystąpienie klasy `flux` .
1. Jeśli używasz opcji SSH z kluczami wygenerowanym przez flux, program generuje klucz SSH i `flux` rejestruje klucz publiczny.
1. Stan `config-agent` jest zgłaszany z powrotem do zasobu konfiguracji na platformie Azure.

Podczas procesu aprowizowania zasób konfiguracji przechodzi przez kilka zmian stanu. Monitoruj postęp za pomocą `az k8s-configuration show ...` powyższego polecenia:

| Zmiana etapu | Opis |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Reprezentuje stany początkowe i w toku. |
| `complianceStatus` -> `Installed`  | `config-agent` pomyślnie skonfigurował klaster i został `flux` wdrożony bez błędu. |
| `complianceStatus` -> `Failed` | `config-agent` wystąpił błąd podczas wdrażania `flux` . Szczegółowe informacje znajdują się w `complianceStatus.message` treści odpowiedzi. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Stosowanie konfiguracji z prywatnego repozytorium Git

Jeśli używasz prywatnego repozytorium Git, musisz skonfigurować klucz publiczny SSH w swoim repozytorium. W przypadku podania lub wygenerowania klucza publicznego SSH przez strumień Flux. Klucz publiczny można skonfigurować dla określonego repozytorium Git lub użytkownika usługi Git, który ma dostęp do repozytorium. 

### <a name="get-your-own-public-key"></a>Uzyskiwanie własnego klucza publicznego

Jeśli wygenerowano własne klucze SSH, masz już klucze prywatne i publiczne.

#### <a name="get-the-public-key-using-azure-cli"></a>Uzyskiwanie klucza publicznego przy użyciu interfejsu wiersza polecenia platformy Azure 

Jeśli narzędzie Flux generuje klucze, użyj następującego polecenia w interfejsie wiersza polecenia platformy Azure.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Pobierz klucz publiczny z Azure Portal

W poniższej ilustracji Azure Portal, czy strumień generuje klucze.

1. W Azure Portal przejdź do połączonego zasobu klastra.
2. Na stronie zasobu wybierz pozycję "GitOps" i zobacz listę konfiguracji dla tego klastra.
3. Wybierz konfigurację, która używa prywatnego repozytorium Git.
4. W oknie kontekstowym, które zostanie otwarte, w dolnej części okna skopiuj klucz **publiczny Repozytorium**.

#### <a name="add-public-key-using-github"></a>Dodawanie klucza publicznego przy użyciu usługi GitHub

Skorzystaj z jednej z następujących opcji:

* Opcja 1. Dodaj klucz publiczny do konta użytkownika (dotyczy wszystkich repozytoriów na twoim koncie):  
    1. Otwórz repozytorium GitHub i kliknij ikonę profilu w prawym górnym rogu strony.
    2. Kliknij pozycję **Settings** (Ustawienia).
    3. Kliknij pozycję **Klucze SSH i GPG.**
    4. Kliknij pozycję **Nowy klucz SSH.**
    5. Należy podać tytuł.
    6. Wklej klucz publiczny bez cudzysłowów otaczających.
    7. Kliknij pozycję **Dodaj klucz SSH.**

* Opcja 2. Dodaj klucz publiczny jako klucz wdrażania do repozytorium Git (dotyczy tylko tego repozytorium):  
    1. Otwórz repozytorium GitHub i przejdź do repozytorium.
    1. Kliknij pozycję **Settings** (Ustawienia).
    1. Kliknij pozycję **Deploy keys (Wd wdrażaj klucze).**
    1. Kliknij pozycję **Dodaj klucz wdrażania.**
    1. W tym celu należy podać tytuł.
    1. Zaznacz pole **wyboru Zezwalaj na dostęp do zapisu.**
    1. Wklej klucz publiczny bez żadnych otaczających cudzysłowów.
    1. Kliknij pozycję **Dodaj klucz.**

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Dodawanie klucza publicznego przy użyciu Azure DevOps repozytorium

Aby dodać klucz do kluczy SSH, należy wykonać następujące czynności:

1. W **obszarze Ustawienia** użytkownika w prawym górnym rogu (obok obrazu profilu) kliknij pozycję Klucze publiczne **SSH.**
1. Wybierz **pozycję + Nowy klucz.**
1. Podać nazwę.
1. Wklej klucz publiczny bez żadnych otaczających cudzysłowów.
1. Kliknij pozycję **Dodaj**.

## <a name="validate-the-kubernetes-configuration"></a>Weryfikowanie konfiguracji usługi Kubernetes

Po zainstalowaniu wystąpienia zasoby przechowywane w repozytorium `config-agent` `flux` Git powinny zacząć przepływać do klastra. Sprawdź, czy przestrzenie nazw, wdrożenia i zasoby zostały utworzone za pomocą następującego polecenia:

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

Widzimy, `team-a` że utworzono `team-b` `itops` przestrzenie nazw , , `cluster-config` i .

Operator `flux` został wdrożony w przestrzeni `cluster-config` nazw zgodnie z zaleceniami zasobu konfiguracji:

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

Usuń konfigurację przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal. Po uruchomieniu polecenia delete zasób konfiguracji zostanie natychmiast usunięty na platformie Azure. Pełne usunięcie skojarzonych obiektów z klastra powinno nastąpić w ciągu 10 minut. Jeśli konfiguracja po usunięciu jest w stanie niepowodzeniem, całkowite usunięcie skojarzonych obiektów może potrwać do godziny.

Po usunięciu konfiguracji z zakresem przestrzeń nazw nie jest usuwana przez Azure Arc, aby `namespace` uniknąć przerywania istniejących obciążeń. W razie potrzeby możesz ręcznie usunąć tę przestrzeń nazw przy użyciu elementu `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Wszelkie zmiany w klastrze, które były wynikiem wdrożeń ze śledzone repozytorium Git, nie zostaną usunięte po usunięciu konfiguracji.

## <a name="next-steps"></a>Następne kroki

Przejść do następnego samouczka, aby dowiedzieć się, jak zaimplementować ciszą/cd za pomocą usługi GitOps.
> [!div class="nextstepaction"]
> [Implementowanie ciasnych/cd za pomocą usługi GitOps](./tutorial-gitops-ci-cd.md)
