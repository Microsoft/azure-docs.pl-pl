---
title: Obsługa uaktualnień węzłów AKS za pomocą akcji GitHub
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak aktualizować węzły AKS za pomocą akcji usługi GitHub
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 6876cf1e5044246492e249d8a61060cbeac46f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217961"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>Automatyczne stosowanie aktualizacji zabezpieczeń do węzłów usługi Azure Kubernetes Service (AKS) przy użyciu akcji GitHub

Aktualizacje zabezpieczeń są kluczową częścią utrzymywania bezpieczeństwa i zgodności klastra usługi AKS z najnowszymi poprawkami dla bazowego systemu operacyjnego. Te aktualizacje obejmują poprawki zabezpieczeń systemu operacyjnego lub aktualizacje jądra. Niektóre aktualizacje wymagają ponownego uruchomienia węzła w celu ukończenia procesu.

Uruchomienie `az aks upgrade` programu umożliwia nieprzerwany sposób stosowania aktualizacji. Polecenie obsługuje stosowanie najnowszych aktualizacji do wszystkich węzłów klastra, cordoning i opróżnianie ruchu do węzłów oraz ponowne uruchamianie węzłów, a następnie Zezwalanie na ruch do zaktualizowanych węzłów. W przypadku aktualizowania węzłów za pomocą innej metody AKS nie uruchamia automatycznie ponownie węzłów.

> [!NOTE]
> Główną różnicą między `az aks upgrade` używaniem a `--node-image-only` flagą jest to, że w przypadku użycia tej metody uaktualniane są tylko obrazy węzłów. W przypadku pominięcia zostanie uaktualniona zarówno obraz węzła, jak i wersja płaszczyzny kontroli Kubernetes. Aby uzyskać bardziej szczegółowe informacje, można zapoznać się z dokumentacją dotyczącą [uaktualnień zarządzanych w węzłach][managed-node-upgrades-article] i dokumentacji [dotyczącej uaktualniania klastra][cluster-upgrades-article] .

Wszystkie węzły Kubernetes są uruchamiane w standardowej maszynie wirtualnej platformy Azure. Te maszyny wirtualne mogą być oparte na systemie Windows lub Linux. Maszyny wirtualne z systemem Linux używają obrazu Ubuntu z systemem operacyjnym skonfigurowanym do automatycznego sprawdzania dostępności aktualizacji co noc.

W przypadku korzystania z `az aks upgrade` polecenia usługa Azure CLI tworzy wznoszące nowe węzły z najnowszymi aktualizacjami zabezpieczeń i jądra, te węzły są początkowo odizolowywane, aby uniemożliwić ich zaplanowanie do momentu ukończenia aktualizacji. Po zakończeniu usługa Azure cordons (sprawia, że węzeł jest niedostępny do planowania nowych obciążeń) i opróżnia (przenosi istniejące obciążenia do innego węzła) starsze węzły i uncordon nowe, efektywnie przekazując wszystkie zaplanowane aplikacje do nowych węzłów.

Ten proces jest lepszy niż aktualizowanie jądra opartych na systemie Linux ręcznie, ponieważ system Linux wymaga ponownego uruchomienia po zainstalowaniu nowej aktualizacji jądra. W przypadku ręcznego aktualizowania systemu operacyjnego należy również ponownie uruchomić maszynę wirtualną, ręcznie cordoning i opróżniając wszystkie aplikacje.

W tym artykule pokazano, jak można zautomatyzować proces aktualizacji węzłów AKS. Użyjemy akcji usługi GitHub i interfejsu wiersza polecenia platformy Azure do utworzenia zadania aktualizacji opartego na programie `cron` , które są uruchamiane automatycznie.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

W tym artykule przyjęto również założenie, że masz konto w usłudze [GitHub][github] , w którym można tworzyć swoje działania.

## <a name="create-a-timed-github-action"></a>Tworzenie akcji GitHub z przekroczeniem czasu

`cron` to narzędzie, które umożliwia uruchamianie zestawu poleceń lub zadań zgodnie z automatycznym harmonogramem. Aby utworzyć zadanie aktualizowania węzłów AKS na automatycznym harmonogramie, musisz mieć repozytorium do hostowania akcji. Zwykle akcje usługi GitHub są konfigurowane w tym samym repozytorium co aplikacja, ale można użyć dowolnego repozytorium. W tym artykule będziemy korzystać z [repozytorium profilów][profile-repository]. Jeśli go nie masz, Utwórz nowe repozytorium o takiej samej nazwie jak nazwa użytkownika usługi GitHub.

1. Przejdź do repozytorium w usłudze GitHub
1. Kliknij kartę **Akcje** w górnej części strony.
1. Jeśli przepływ pracy został już skonfigurowany w tym repozytorium, nastąpi przekierowanie do listy ukończonych uruchomień, w tym przypadku kliknij przycisk **Nowy przepływ pracy** . Jeśli jest to Twój pierwszy przepływ pracy w repozytorium, w witrynie GitHub zostanie zaprezentowana część szablonów projektu, a następnie kliknij link **Konfiguruj swój przepływ pracy** poniżej tekstu opisu.
1. Zmień przepływ pracy `name` i `on` Tagi podobne do poniższego. Akcje GitHub używają tej samej [składni firmy POSIX][cron-syntax] w systemie, co system z systemem Linux. W tym harmonogramie poinformujemy, że przepływ pracy jest uruchamiany co 15 dni o godzinie 3am.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Utwórz nowe zadanie przy użyciu poniższego. To zadanie ma nazwę `upgrade-node` , działa na agencie Ubuntu i łączy się z kontem interfejsu wiersza polecenia platformy Azure w celu wykonania wymaganych kroków w celu uaktualnienia węzłów.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure w przepływie pracy

W tym `steps` kluczu zdefiniujesz wszystkie prace, które zostaną wykonane przez przepływ pracy w celu uaktualnienia węzłów.

Pobierz i zaloguj się do interfejsu wiersza polecenia platformy Azure.

1. Po prawej stronie ekranu akcji usługi GitHub Znajdź *pasek wyszukiwania w witrynie Marketplace* i wpisz ciąg **"Logowanie do platformy Azure"**.
1. Otrzymasz w rezultacie akcję o nazwie **Logowanie platformy Azure** opublikowaną **przez platformę Azure**:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="Wyniki wyszukiwania pokazują dwa wiersze, pierwsza akcja nosi nazwę &quot;Azure Login', a druga&quot; Azure Container Registry Login'":::

1. Kliknij pozycję **Logowanie na platformie Azure**. Na następnym ekranie kliknij **ikonę kopiowania** w prawym górnym rogu przykładu kodu.

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Okienko wyników akcji logowania platformy Azure z przykładowym kodem poniżej, czerwony kwadrat wokół ikony kopiowania podświetla pozycję":::

1. Wklej następujące elementy pod `steps` kluczem:

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie, aby wygenerować nową nazwę użytkownika i hasło.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    Dane wyjściowe powinny wyglądać podobnie jak w poniższym kodzie JSON:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **W nowym oknie przeglądarki** przejdź do repozytorium GitHub i Otwórz kartę **Ustawienia** repozytorium. Kliknij pozycję wpisy **tajne** , a następnie kliknij pozycję **Nowy wpis tajny repozytorium**.
1. W obszarze *Nazwa* Użyj `AZURE_CREDENTIALS` .
1.  W polu *wartość* Dodaj całą zawartość z danych wyjściowych poprzedniego kroku, w którym została utworzona nowa nazwa użytkownika i hasło.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Formularz pokazujący AZURE_CREDENTIALS jako tytuł tajny oraz dane wyjściowe wykonanego polecenia wklejonego jako kod JSON":::

1. Kliknij pozycję **Dodaj klucz tajny**.

Interfejs wiersza polecenia używany przez akcję zostanie zarejestrowany na koncie platformy Azure i będzie gotowy do uruchamiania poleceń.

Aby utworzyć kroki wykonywania poleceń interfejsu wiersza polecenia platformy Azure.

1. Przejdź do **strony Wyszukiwanie** w witrynie *GitHub Marketplace* po prawej stronie ekranu i Wyszukaj *akcję interfejsu wiersza polecenia platformy Azure*. Wybierz *akcję interfejsu wiersza polecenia platformy Azure na platformie Azure*.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Wynik wyszukiwania dla elementu &quot;Akcja interfejsu wiersza polecenia platformy Azure&quot; z pierwszym wynikiem pokazywanym przez platformę Azure":::

1. Kliknij przycisk Kopiuj w *wyniku witryny GitHub Marketplace* i wklej zawartość akcji w edytorze głównym poniżej kroku *logowania platformy Azure* , podobne do następujących:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > Można oddzielić `-g` `-n` Parametry i z polecenia, dodając je do wpisów tajnych, podobnie jak w poprzednich krokach. Zastąp `{resourceGroupName}` `{aksClusterName}` symbole zastępcze i odpowiednimi kluczami tajnymi, na przykład `${{secrets.RESOURCE_GROUP_NAME}}` i `${{secrets.AKS_CLUSTER_NAME}}`

1. Zmień nazwę pliku na `upgrade-node-images`.
1. Kliknij przycisk **Rozpocznij Zatwierdź**, Dodaj tytuł komunikatu i Zapisz przepływ pracy.

Po utworzeniu zatwierdzenia przepływ pracy zostanie zapisany i będzie gotowy do wykonania.

> [!NOTE]
> Aby uaktualnić pulę jednego węzła zamiast wszystkich pul węzłów w klastrze, należy dodać `--name` parametr do `az aks nodepool upgrade` polecenia, aby określić nazwę puli węzłów. Na przykład:
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>Ręczne uruchamianie akcji usługi GitHub

Przepływ pracy można uruchomić ręcznie, oprócz zaplanowanego uruchomienia, dodając nowy `on` wyzwalacz o nazwie `workflow_dispatch` . Gotowy plik powinien wyglądać podobnie do poniższego YAML:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Następne kroki

- Informacje o najnowszych obrazach węzłów można znaleźć w informacjach o [wersji AKS](https://github.com/Azure/AKS/releases) .
- Dowiedz się, jak uaktualnić wersję Kubernetes z [uaktualnieniem klastra AKS][cluster-upgrades-article].
- Dowiedz się więcej o wielu pulach węzłów oraz jak uaktualnić pule węzłów za pomocą [tworzenia wielu pul węzłów i zarządzania nimi][use-multiple-node-pools].
- Dowiedz się więcej o [pulach węzłów systemowych][system-pools]
- Aby dowiedzieć się, jak zaoszczędzić koszty przy użyciu wystąpień obliczeniowych, zobacz [Dodawanie puli węzłów dodatkowych do AKS][spot-pools]

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
