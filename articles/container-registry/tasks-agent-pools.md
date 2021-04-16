---
title: Uruchamianie zadania za pomocą dedykowanej puli — zadania
description: Skonfiguruj dedykowaną pulę obliczeniową (pulę agentów) w rejestrze, aby uruchomić Azure Container Registry obliczeniowe.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 21db066b3f18106938d11fbd8e2cfe688c1ef276
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389557"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Uruchamianie zadania ACR w dedykowanej puli agentów

Skonfiguruj zarządzaną przez platformę Azure pulę maszyn wirtualnych *(pulę* agentów), aby umożliwić Azure Container Registry [zadań][acr-tasks] w dedykowanym środowisku obliczeniowym. Po skonfigurowaniu co najmniej jednej puli w rejestrze możesz wybrać pulę do uruchomienia zadania w miejsce domyślnego środowiska obliczeniowego usługi.

Pula agentów zapewnia:

- **Obsługa sieci wirtualnej** — przypisz pulę agentów do sieci wirtualnej platformy Azure, zapewniając dostęp do zasobów w sieci wirtualnej, takich jak rejestr kontenerów, magazyn kluczy lub magazyn.
- **Skaluj** zgodnie z potrzebami — zwiększ liczbę wystąpień w puli agentów dla zadań wymagających intensywnych obliczeń lub skaluj do zera. Rozliczenia są oparte na alokacji puli. Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://azure.microsoft.com/pricing/details/container-registry/).
- **Elastyczne opcje** — wybierz jedną z różnych [warstw puli i](#pool-tiers) opcji skalowania, aby spełnić potrzeby obciążeń zadań.
- **Zarządzanie platformą Azure** — pule zadań są poprawiane i konserwowane przez platformę Azure, zapewniając zarezerwowaną alokację bez konieczności obsługi poszczególnych maszyn wirtualnych.

Ta funkcja jest dostępna w warstwie usługi rejestru kontenerów **Premium.** Aby uzyskać informacje o warstwach i limitach usługi rejestru, zobacz [Azure Container Registry SKU][acr-tiers].

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej i obowiązują [pewne ograniczenia.](#preview-limitations) Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

- Pule agentów zadań obsługują obecnie węzły systemu Linux. Węzły systemu Windows nie są obecnie obsługiwane.
- Pule agentów zadań są dostępne w wersji zapoznawczej w następujących regionach: Zachodnie stany USA 2, Południowo-środkowe stany USA, Wschodnie stany USA 2, Wschodnie stany USA, Środkowe stany USA, Europa Zachodnia, Europa Północna, Kanada Środkowa, USGov Arizona, USGov Teksas i USGov Wirginia.
- Dla każdego rejestru domyślny całkowity limit przydziału procesorów wirtualnych (rdzeni) wynosi 16 dla wszystkich standardowych pul agentów i wynosi 0 dla izolowanych pul agentów. Otwórz [wniosek o pomoc techniczną w][open-support-ticket] celu dodatkowej alokacji.
- Obecnie nie można anulować uruchomienia zadania w puli agentów.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby wykonać kroki interfejsu wiersza polecenia platformy Azure opisane w tym artykule, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.3.1 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli]. Lub uruchom w [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Jeśli nie masz jeszcze rejestru [kontenerów,][create-reg-cli] utwórz go (wymagana warstwa Premium) w regionie w wersji zapoznawczej.

## <a name="pool-tiers"></a>Warstwy puli

Warstwy puli agentów zapewniają następujące zasoby dla każdego wystąpienia w puli.

|Warstwa    | Typ  |  Procesor CPU  |Pamięć (GB)  |
|---------|---------|---------|---------|
|S1     |  Standardowych    | 2       |    3     |
|S2     |  Standardowych    | 4       |    8     |
|S3     |  Standardowych    | 8       |   16     |
|I6     |  Izolowane    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Tworzenie puli agentów zadań i zarządzanie nimi

### <a name="set-default-registry-optional"></a>Ustaw rejestr domyślny (opcjonalnie)

Aby uprościć następujące polecenia interfejsu wiersza polecenia platformy Azure, ustaw rejestr domyślny, uruchamiając [polecenie az configure:][az-configure]

```azurecli
az configure --defaults acr=<registryName>
```

W poniższych przykładach przyjęto założenie, że ustawiono rejestr domyślny. Jeśli nie, przekaż `--registry <registryName>` parametr w każdym `az acr` poleceniu.

### <a name="create-agent-pool"></a>Tworzenie puli agentów

Utwórz pulę agentów za pomocą [polecenia az acr agentpool create.][az-acr-agentpool-create] Poniższy przykład tworzy pulę warstwy S2 (4 procesory CPU/wystąpienie). Domyślnie pula zawiera 1 wystąpienie.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Tworzenie puli agentów i innych operacji zarządzania pulą trwa kilka minut.

### <a name="scale-pool"></a>Skalowanie puli

Skaluj rozmiar puli w górę lub w dół za pomocą [polecenia az acr agentpool update.][az-acr-agentpool-update] Poniższy przykład skaluje pulę do 2 wystąpień. Można skalować do 0 wystąpień.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Tworzenie puli w sieci wirtualnej

### <a name="add-firewall-rules"></a>Dodawanie reguł zapory

Pule agentów zadań wymagają dostępu do następujących usług platformy Azure. Poniższe reguły zapory należy dodać do istniejących sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika.

| Kierunek | Protokół | Element źródłowy         | Port źródłowy | Element docelowy          | Dest Port | Użyte    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | AzureKeyVault        | 443       | Domyślne |
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | Storage              | 443       | Domyślne |
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | EventHub             | 443       | Domyślne |
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | AzureActiveDirectory | 443       | Domyślne |
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | AzureMonitor         | 443       | Domyślne |

> [!NOTE]
> Jeśli zadania wymagają dodatkowych zasobów z publicznego Internetu, dodaj odpowiednie reguły. Na przykład do uruchomienia zadania kompilacji platformy Docker, które ściąga obrazy podstawowe z Docker Hub lub przywraca pakiet NuGet, potrzebne są dodatkowe reguły.

### <a name="create-pool-in-vnet"></a>Tworzenie puli w sieci wirtualnej

Poniższy przykład tworzy pulę agentów w *podsieci mysubnet* sieci *myvnet:*

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Uruchamianie zadania w puli agentów

Poniższe przykłady pokazują, jak określić pulę agentów podczas kolejkowania zadania.

> [!NOTE]
> Aby użyć puli agentów w zadaniu usługi ACR, upewnij się, że pula zawiera co najmniej 1 wystąpienie.
>

### <a name="quick-task"></a>Szybkie zadanie

Za pomocą polecenia [az acr build][az-acr-build] przekaż parametr do kolejki szybkiego zadania w puli `--agent-pool` agentów:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Automatycznie wyzwalane zadanie

Na przykład utwórz zaplanowane zadanie w puli agentów za pomocą funkcji [az acr task create][az-acr-task-create], przekazując parametr `--agent-pool` .

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

Aby zweryfikować konfigurację zadania, uruchom [az acr task run][az-acr-task-run]:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Stan puli zapytań

Aby znaleźć liczbę przebiegów aktualnie zaplanowanych w puli agentów, uruchom [az acr agentpool show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kompilacji i konserwacji obrazu kontenera w chmurze, zapoznaj się z [zadania usługi ACR samouczka.](container-registry-tutorial-quick-task.md)



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
