---
title: Używanie dedykowanej puli do uruchamiania zadań podrzędnych
description: Skonfiguruj dedykowaną pulę obliczeniową (pulę agentów) w rejestrze, aby uruchomić zadanie Azure Container Registry.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: eeb9a71854f52da5c1a9f4befae93c377ad67b05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920311"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Uruchamianie zadania ACR w dedykowanej puli agentów

Skonfiguruj pulę maszyn wirtualnych zarządzaną przez platformę Azure (*pulę agentów*), aby umożliwić uruchamianie [Azure Container Registry zadań][acr-tasks] w dedykowanym środowisku obliczeniowym. Po skonfigurowaniu co najmniej jednej puli w rejestrze można wybrać pulę służącą do uruchamiania zadania zamiast domyślnego środowiska obliczeniowego usługi.

Pula agentów oferuje następujące informacje:

- **Obsługa sieci wirtualnych** — przypisywanie puli agentów do usługi Azure VNET, zapewniając dostęp do zasobów w sieci wirtualnej, takich jak rejestr kontenerów, Magazyn kluczy lub magazyn.
- **Skalowanie w razie potrzeby** — zwiększenie liczby wystąpień w puli agentów na potrzeby zadań intensywnie korzystających z obliczeń lub skalowania do zera. Opłaty są naliczane na podstawie alokacji puli. Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://azure.microsoft.com/pricing/details/container-registry/).
- **Elastyczne opcje** — wybierz spośród różnych [warstw puli](#pool-tiers) i opcji skalowania, aby sprostać potrzebom obciążeń zadań.
- **Zarządzanie platformą Azure** — pule zadań są poprawiane i obsługiwane przez platformę Azure, zapewniając alokację zarezerwowaną bez konieczności utrzymywania poszczególnych maszyn wirtualnych.

Ta funkcja jest dostępna w warstwie usługi kontenera **Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry SKU][acr-tiers].

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia mają zastosowanie](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

- Pule agentów zadań obsługują obecnie węzły systemu Linux. Węzły systemu Windows nie są obecnie obsługiwane.
- Pule agentów zadań są dostępne w wersji zapoznawczej w następujących regionach: zachodnie stany USA 2, Południowo-środkowe stany USA, Wschodnie stany USA 2, Wschodnie stany USA, środkowe stany USA, USGov Arizona, USGov Texas Instruments i USGov Wirginia.
- Dla każdego rejestru domyślny całkowity limit przydziału vCPU (rdzeń) wynosi 16 dla wszystkich standardowych pul agentów i ma wartość 0 dla izolowanych pul agentów. Otwórz [żądanie pomocy technicznej][open-support-ticket] , aby uzyskać dodatkowe alokacje.
- Obecnie nie można anulować zadania uruchomionego w puli agentów.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby skorzystać z kroków interfejsu wiersza polecenia platformy Azure w tym artykule, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.3.1 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli]. Lub Uruchom w [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Jeśli nie masz jeszcze rejestru kontenerów, [Utwórz go][create-reg-cli] (wymagana warstwa Premium) w regionie wersji zapoznawczej.

## <a name="pool-tiers"></a>Warstwy puli

Warstwy puli agentów zapewniają następujące zasoby na wystąpienie w puli.

|Warstwa    | Typ  |  Procesor CPU  |Pamięć (GB)  |
|---------|---------|---------|---------|
|S1     |  Standardowa    | 2       |    3     |
|S2     |  Standardowa    | 4       |    8     |
|S3     |  Standardowa    | 8       |   16     |
|I6     |  ogół    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Tworzenie puli agentów zadań i zarządzanie nią

### <a name="set-default-registry-optional"></a>Ustaw domyślny Rejestr (opcjonalnie)

Aby uprościć wykonywanie poleceń interfejsu wiersza polecenia platformy Azure, należy ustawić domyślny rejestr za pomocą polecenia [AZ Configure][az-configure] :

```azurecli
az configure --defaults acr=<registryName>
```

W poniższych przykładach przyjęto założenie, że domyślny rejestr został ustawiony. Jeśli nie, należy przekazać `--registry <registryName>` parametr w każdym `az acr` poleceniu.

### <a name="create-agent-pool"></a>Utwórz pulę agentów

Utwórz pulę agentów przy użyciu polecenia [AZ ACR nieznanej obiektu agentpool Create][az-acr-agentpool-create] . Poniższy przykład tworzy pulę warstwy S2 (4 procesor CPU/wystąpienie). Domyślnie pula zawiera 1 wystąpienie.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Tworzenie puli agentów i innych operacji zarządzania puli trwa kilka minut.

### <a name="scale-pool"></a>Pula skalowania

Skaluj rozmiar puli w górę lub w dół za pomocą polecenia [AZ ACR nieznanej obiektu agentpool Update][az-acr-agentpool-update] . Poniższy przykład skaluje pulę do 2 wystąpień. Można skalować do 0 wystąpień.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Tworzenie puli w sieci wirtualnej

### <a name="add-firewall-rules"></a>Dodawanie reguł zapory

Pule agentów zadań wymagają dostępu do następujących usług platformy Azure. Następujące reguły zapory należy dodać do wszystkich istniejących sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika.

| Kierunek | Protokół | Element źródłowy         | Port źródłowy | Element docelowy          | Port docelowy | Użyte    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | AzureKeyVault        | 443       | Domyślne |
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | Storage              | 443       | Domyślne |
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | EventHub             | 443       | Domyślne |
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | Usługi azureactivedirectory | 443       | Domyślne |
| Wychodzący  | TCP      | VirtualNetwork | Dowolne         | AzureMonitor         | 443       | Domyślne |

> [!NOTE]
> Jeśli zadania wymagają dodatkowych zasobów z publicznego Internetu, Dodaj odpowiednie reguły. Na przykład do uruchomienia zadania kompilacji platformy Docker, które pobiera obrazy podstawowe z usługi Docker Hub lub przywraca pakiet NuGet, są potrzebne dodatkowe reguły.

### <a name="create-pool-in-vnet"></a>Tworzenie puli w sieci wirtualnej

Poniższy przykład tworzy pulę agentów w podsieci *myvnet* sieci *:*

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

## <a name="run-task-on-agent-pool"></a>Uruchom zadanie w puli agentów

W poniższych przykładach pokazano, jak określić pulę agentów podczas kolejkowania zadania.

> [!NOTE]
> Aby użyć puli agentów w zadaniu ACR, upewnij się, że pula zawiera co najmniej jedno wystąpienie.
>

### <a name="quick-task"></a>Szybkie zadanie

Kolejkowanie szybkiego zadania w puli agentów przy użyciu polecenia [AZ ACR Build][az-acr-build] i przekazywanie `--agent-pool` parametru:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Zadanie wyzwolone automatycznie

Na przykład utwórz zaplanowane zadanie w puli agentów przy użyciu [AZ ACR Task Create][az-acr-task-create], przekazując `--agent-pool` parametr.

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

Aby sprawdzić konfigurację zadania, uruchom polecenie [AZ ACR Task Run][az-acr-task-run]:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Stan puli zapytań

Aby sprawdzić liczbę przebiegów aktualnie zaplanowanych w puli agentów, uruchom polecenie [AZ ACR nieznanej obiektu agentpool show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kompilacji i konserwacji obrazów kontenerów w chmurze, zapoznaj się z [serią samouczka zadań ACR](container-registry-tutorial-quick-task.md).



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
