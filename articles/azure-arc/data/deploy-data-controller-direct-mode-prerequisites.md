---
title: Wymagania wstępne | Tryb połączenia bezpośredniego
description: Wymagania wstępne dotyczące wdrażania kontrolera danych w trybie połączenia bezpośredniego.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716314"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>Wdrażanie kontrolera danych — tryb połączenia bezpośredniego (wymagania wstępne)

W tym artykule opisano sposób przygotowania do wdrożenia kontrolera danych na usługi danych z obsługą usługi Azure Arc w trybie połączenia bezpośredniego.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

W podsumowaniu wysokiego poziomu wymagania wstępne obejmują:

1. Instalowanie narzędzi
1. Dodawanie rozszerzeń
1. Tworzenie jednostki usługi i konfigurowanie ról dla metryk
1. Łączenie klastra Kubernetes z platformą Azure przy użyciu Azure Arc kubernetes

Po zakończeniu tych wymagań wstępnych można wdrożyć [Azure Arc danych | Tryb połączenia bezpośredniego.](deploy-data-controller-direct-mode.md)

Pozostałe sekcje tego artykułu identyfikują wymagania wstępne.

## <a name="install-tools"></a>Instalowanie narzędzi

- Helm w wersji 3.3+ ([zainstaluj](https://helm.sh/docs/intro/install/))
- Interfejs wiersza polecenia platformy Azure[(zainstaluj](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Dodawanie rozszerzeń dla interfejsu wiersza polecenia platformy Azure

Ponadto wymagane są również następujące rozszerzenia az:
- Rozszerzenie `k8s-extension` interfejsu wiersza polecenia platformy Azure (0.2.0)
- Interfejs wiersza polecenia platformy Azure `customlocation` (0.1.0)

Przykład `az` i jego rozszerzenia interfejsu wiersza polecenia to:

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>Tworzenie jednostki usługi i konfigurowanie ról dla metryk

Postępuj zgodnie z instrukcjami opisanymi w [artykule Przekazywanie metryk](upload-metrics-and-logs-to-azure-monitor.md) i utwórz jednostkę usługi, a następnie przyznaj role zgodnie z opisem w artykule. 

Informacje ClientID, TenantID i Client Secret spn będą wymagane podczas wdrażania Azure Arc [danych.](deploy-data-controller-direct-mode.md) 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Łączenie klastra Kubernetes z platformą Azure przy użyciu Azure Arc kubernetes

Aby wykonać to zadanie, wykonaj kroki opisane w tesłudze Connect an existing Kubernetes cluster to Azure arc (Łączenie istniejącego klastra [Kubernetes z usługą Azure Arc).](../kubernetes/quickstart-connect-cluster.md)

## <a name="next-steps"></a>Następne kroki

Po zakończeniu tych wymagań wstępnych można wdrożyć [Azure Arc danych | Tryb połączenia bezpośredniego](deploy-data-controller-direct-mode.md).
