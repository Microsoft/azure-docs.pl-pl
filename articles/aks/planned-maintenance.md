---
title: Używanie planowanej konserwacji dla klastra Azure Kubernetes Service (AKS) (wersja zapoznawcza)
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak używać planowanej konserwacji w Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: f1e0822e77d8466b1b9796041fbdba53c3f9c91f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782913"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Planowanie okien obsługi dla klastra usługi Azure Kubernetes Service (AKS) za pomocą planowanej konserwacji (wersja zapoznawcza)

Klaster usługi AKS regularnie przeprowadza konserwację. Domyślnie ta praca może odbywać się w dowolnym momencie. Planowana konserwacja umożliwia zaplanowanie cotygodniowych okien obsługi, które aktualizują płaszczyznę sterowania, a także zasobniki kube-system w wystąpieniu usługi VMSS i minimalizują wpływ na obciążenie. Po zaplanowaniu cała konserwacja będzie mieć miejsce w wybranym oknie. Możesz zaplanować co najmniej jedno cotygodniowe okna w klastrze, określając dzień lub zakres czasu w określonym dniu. Okna obsługi są konfigurowane przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Ograniczenia

W przypadku korzystania z planowanej konserwacji obowiązują następujące ograniczenia:

- Usługa AKS zastrzega sobie prawo do przerwania tych okien w przypadku nieplanowanych/reaktywnych operacji konserwacji, które są pilne lub krytyczne.
- Obecnie wykonywanie operacji konserwacji jest uznawane za najbardziej *pracowisze* i nie ma gwarancji, że zostaną one wykonywane w określonym oknie.
- Aktualizacje nie mogą być blokowane przez więcej niż siedem dni.

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Potrzebne jest również rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* w wersji 0.5.4 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* za pomocą [polecenia az extension add.][az-extension-add] Możesz też zainstalować wszystkie dostępne aktualizacje za pomocą [polecenia az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Zezwalaj na konserwację w każdy poniedziałek od 1:00 do 2:00

Aby dodać okno obsługi, możesz użyć `az aks maintenanceconfiguration add` polecenia .

> [!IMPORTANT]
> Okna planowanej konserwacji są określone w Uniwersalny czas koordynowany (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

Następujące przykładowe dane wyjściowe pokazują okno obsługi od 1:00 do 2:00 w każdy poniedziałek.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Aby umożliwić konserwację w dowolnym momencie w ciągu dnia, pomiń *parametr godzina rozpoczęcia.* Na przykład następujące polecenie ustawia okno obsługi dla całego dnia w każdy poniedziałek:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Dodawanie konfiguracji konserwacji za pomocą pliku JSON

Możesz również użyć pliku JSON, aby utworzyć okno obsługi zamiast używać parametrów. Utwórz `test.json` plik o następującej zawartości:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

Powyższy plik JSON określa okna obsługi w każdy wtorek o godzinie 1:00–3:00 i w każdą środę o 1:00–2:00 i o 6:00–7:00. Istnieje również wyjątek od *2021-05-26T03:00:00Z* do *2021-05-30T12:00:00Z,* w którym konserwacja nie jest dozwolona, nawet jeśli nakłada się na okno obsługi. Następujące polecenie dodaje okna obsługi z polecenia `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Aktualizowanie istniejącego okna obsługi

Aby zaktualizować istniejącą konfigurację konserwacji, użyj `az aks maintenanceconfiguration update` polecenia .

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Lista wszystkich okien obsługi w istniejącym klastrze

Aby wyświetlić wszystkie bieżące okna konfiguracji konserwacji w klastrze usługi AKS, użyj `az aks maintenanceconfiguration list` polecenia .

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

W poniższych danych wyjściowych widać, że istnieją dwa okna obsługi skonfigurowane dla myAKSCluster. Jedno okno jest w poniedziałek o godzinie 1:00, a drugie w piątek o 4:00.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Wyświetlanie określonego okna konfiguracji konserwacji w klastrze usługi AKS

Aby wyświetlić konkretne okno konfiguracji konserwacji w klastrze usługi AKS, użyj `az aks maintenanceconfiguration show` polecenia .

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

Następujące przykładowe dane wyjściowe pokazują domyślne okno *obsługi*:

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Usuwanie określonego okna konfiguracji konserwacji w istniejącym klastrze usługi AKS

Aby usunąć określone okno konfiguracji konserwacji w klastrze usługi AKS, użyj `az aks maintenanceconfiguration delete` polecenia .

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć uaktualnianie klastra usługi AKS, zobacz [Uaktualnianie klastra usługi AKS][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
