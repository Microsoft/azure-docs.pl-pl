---
title: Dołączanie usługi Azure ARC z Azure Monitor dla kontenerów (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Dołączanie usługi Azure ARC z Azure Monitor dla kontenerów
keywords: Kubernetes, łuk, Azure, K8s, kontenery
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680735"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Dołączanie Azure Monitor kontenerów z łukiem (wersja zapoznawcza)

Dołączanie [kontenerów z włączonymi Azure Monitormi](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) do klastrów Kubernetes z obsługą usługi Azure Arc.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* [Wersje Kubernetes](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* Dystrybucje systemu Linux dla węzłów klastra (Master & Worker) — Ubuntu (18,04 LTS i 16,04 LTS)
* Minimalne uprawnienie roli RBAC współautor w subskrypcji platformy Azure dla klastra Kubernetes z obsługą usługi Azure Arc
* W pełni kwalifikowany identyfikator zasobu platformy Azure dla klastra Kubernetes z obsługą usługi Azure Arc
* Kontekst Kubeconfig klastra Kubernetes
* Agent monitorowania wymaga, aby cAdvisor na Kubelet działał na bezpiecznym porcie: 10250 lub niezabezpieczony port: 10255 na wszystkich węzłach w celu ściągnięcia metryk wydajności   
* Zaleca się skonfigurowanie portu Kubelet cAdvisor w celu zabezpieczenia portu: 10250.
* Agent monitorowania wymaga następujących portów wychodzących i domen do wysłania danych monitorowania do Azure Monitor zaplecza (jeśli jest blokowany przez serwer proxy/zaporę)
    -  *. ods.opinsights.azure.com 443
    -  *. oms.opinsights.azure.com 443
    -  *. blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>Dołączanie

### <a name="using-helm-chart"></a>Używanie wykresu HELM

### <a name="option-1-using-powershell--script"></a>Opcja 1. używanie skryptu programu PowerShell

1. Pobierz skrypt dołączania

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. Zainstaluj program [PowerShell Core](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) na komputerze deweloperskim, aby wykonać skrypt dołączania programu PowerShell.

3. Logowanie na platformie Azure

    ```console
    az login --use-device-code
    ```

4. Wykonaj Poniższy skrypt za pomocą klastra Azure Arc K8s i kontekstu klastra Kubernetes

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>Opcja 2: używanie skryptu bash

> **Porada:** Skrypt używa funkcji bash 4, więc upewnij się, że bash jest aktualna. Bieżącą wersję można sprawdzić za pomocą programu `bash --version` .

1. Pobierz skrypt dołączania

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. Wykonaj Poniższy skrypt za pomocą klastra Azure Arc K8s i kontekstu klastra Kubernetes

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>Konfigurowanie zbierania danych agenta

Domyślnie agent nie zbiera dzienników stdout i stderr kontenerów w przestrzeni nazw polecenia-system.
Zapoznaj się z tematem https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config , aby skonfigurować agenta przy użyciu żądanych ustawień zbierania danych.

## <a name="configure-scraping-of-prometheus-metrics"></a>Konfigurowanie wycinków metryk Prometheus

Azure Monitor dla kontenerów powoduje odzyskanie metryk Prometheus i pozyskanie do Azure Monitor zaplecza.
Zapoznaj się z instrukcjami dotyczącymi https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration sposobu konfigurowania odpadków Prometheus.

## <a name="user-interface"></a>Interfejs użytkownika

Przejdź do programu, aby wyświetlić dołączany https://aka.ms/azmon-containers-azurearc klaster.

## <a name="disable-monitoring"></a>Wyłącz monitorowanie

Jeśli chcesz wyłączyć monitorowanie z jakiegoś powodu, możesz po prostu usunąć Azure Monitor kontenerów HELM for Containers, aby zatrzymać zbieranie i pobieranie danych monitorowania do Azure Monitor zaplecza kontenerów.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)

