---
title: Użyj planowanej konserwacji klastra usługi Azure Kubernetes Service (AKS) (wersja zapoznawcza)
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak używać planowanej konserwacji w usłudze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 8526d7c1c436074fbf6f838caf232e1abee06339
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670379"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Użyj planowanej konserwacji w celu zaplanowania okien obsługi dla klastra usługi Azure Kubernetes Service (AKS) (wersja zapoznawcza)

Klaster AKS ma regularne konserwacje wykonywane automatycznie. Domyślnie to działanie może wystąpić w dowolnym momencie. Planowana konserwacja pozwala zaplanować cotygodniowe okna obsługi, które zaktualizują płaszczyznę kontroli, a także polecenia-systemowe wystąpienia w wystąpieniu VMSS i minimalizują wpływ obciążeń. Po zaplanowaniu wszystkie czynności konserwacyjne zostaną wykonane w wybranym oknie. Możesz zaplanować co najmniej jedno cotygodniowe okna w klastrze, określając dzień lub zakres czasu w określonym dniu. Okna obsługi konfiguruje się za pomocą interfejsu wiersza polecenia platformy Azure.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Ograniczenia

W przypadku korzystania z planowanej konserwacji obowiązują następujące ograniczenia:

- AKS rezerwuje prawo, aby przerwać te okna dla nieplanowanych/reaktywnych operacji konserwacji, które są pilne lub krytyczne.
- Obecnie wykonywanie operacji konserwacyjnych jest uznawane za *najlepsze* i nie jest gwarantowane w określonym oknie.
- Aktualizacje nie mogą być blokowane przez więcej niż siedem dni.

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Wymagany jest również *AKS — wersja zapoznawcza* interfejsu wiersza polecenia platformy Azure w wersji 0.5.4 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej AKS* , używając polecenie [AZ Extension Add][az-extension-add] . Lub zainstalować wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Zezwalaj na konserwację w każdym poniedziałek o godzinie 1:10:00 do 2:10:00

Aby dodać okno obsługi, możesz użyć `az aks maintenanceconfiguration add` polecenia.

> [!IMPORTANT]
> Planowane okna obsługi są określane w uniwersalnym czasie koordynowanym (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

Następujące przykładowe dane wyjściowe przedstawiają okno obsługi od 1:10:00 do 2:10:00 co poniedziałek.

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

Aby umożliwić konserwację w dowolnym momencie w ciągu dnia, Pomiń parametr *Start-Hour* . Na przykład następujące polecenie ustawia okno obsługi dla pełnego dnia każdego tygodnia:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Dodawanie konfiguracji konserwacji przy użyciu pliku JSON

Można również użyć pliku JSON Utwórz okno obsługi zamiast używać parametrów. Utwórz `test.json` plik o następującej zawartości:

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

Powyższy plik JSON określa okna obsługi każdego wtorku o 1:10:00-3:10:00 i każdą środę o godzinie 1:10:00-2:10:00 i w 6:10:00-7:10:00. Istnieje również wyjątek od *2021-05-26T03:00:00Z* do *2021-05-30T12:00:00Z* , gdy konserwacja nie jest dozwolona, nawet jeśli pokrywa się z oknem obsługi. Poniższe polecenie dodaje okna obsługi z programu `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Aktualizowanie istniejącego okna obsługi

Aby zaktualizować istniejącą konfigurację konserwacji, użyj `az aks maintenanceconfiguration update` polecenia.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Wyświetl listę wszystkich okien obsługi w istniejącym klastrze

Aby wyświetlić wszystkie bieżące okna konfiguracji konserwacji w klastrze AKS, użyj `az aks maintenanceconfiguration list` polecenia.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

W danych wyjściowych poniżej widać, że istnieją dwa okna obsługi skonfigurowane dla myAKSCluster. Jedno okno jest w poniedziałek o godzinie 1:10:00, a inne okno w piątek o godzinie 4:10:00.

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

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Wyświetlanie określonego okna konfiguracji konserwacji w klastrze AKS

Aby wyświetlić określone okno konfiguracji konserwacji w klastrze AKS, użyj `az aks maintenanceconfiguration show` polecenia.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

Następujące przykładowe dane wyjściowe pokazują okno obsługi dla *ustawień domyślnych*:

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

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Usuń niektóre okna konfiguracji konserwacji w istniejącym klastrze AKS

Aby usunąć niektóre okna konfiguracji konserwacji w klastrze AKS, użyj `az aks maintenanceconfiguration delete` polecenia.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć Uaktualnianie klastra AKS, zobacz [Uaktualnianie klastra AKS][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[aks-upgrade]: upgrade-cluster.md
