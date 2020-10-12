---
title: Jak zatrzymać monitorowanie klastra platformy Azure i systemu Red Hat OpenShift v4 | Microsoft Docs
description: W tym artykule opisano, jak można zatrzymać monitorowanie klastra Red Hat OpenShift i Red Hat OpenShift w wersji 4 przy użyciu Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: bf61457b9c8cff40eb3fee2c93c7184fbaae6db5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091151"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Jak zatrzymać monitorowanie klastra platformy Azure i systemu Red Hat OpenShift v4

Po włączeniu monitorowania rozwiązania Azure Red Hat OpenShift i Red Hat OpenShift w wersji 4. x można zatrzymać monitorowanie klastra przy użyciu Azure Monitor dla kontenerów, jeśli zdecydujesz, że nie chcesz już ich monitorować. W tym artykule pokazano, jak to zrobić.  

## <a name="how-to-stop-monitoring-using-helm"></a>Jak zatrzymać monitorowanie za pomocą Helm

1. Aby najpierw zidentyfikować Azure Monitor kontenerów Helm wersja wykresu zainstalowana w klastrze, uruchom następujące polecenie Helm.

    ```
    helm list
    ```

    Dane wyjściowe będą wyglądać następująco:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-Containers-Release-1* reprezentuje wersję wykresu helm dla Azure monitor dla kontenerów.

2. Aby usunąć wersję wykresu, uruchom następujące polecenie Helm.

    `helm delete <releaseName>`

    Przykład:

    `helm delete azmon-containers-release-1`

    Spowoduje to usunięcie wersji z klastra. Możesz sprawdzić, uruchamiając `helm list` polecenie:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Zmiana konfiguracji może potrwać kilka minut. Ponieważ usługa Helm śledzi wersje nawet po ich usunięciu, można przeprowadzić inspekcję historii klastra, a nawet cofnąć usunięcie wersji za pomocą programu `helm rollback` .

## <a name="next-steps"></a>Następne kroki

Jeśli obszar roboczy Log Analytics został utworzony tylko w celu obsługi monitorowania klastra i nie jest już wymagany, trzeba go ręcznie usunąć. Jeśli nie wiesz, jak usunąć obszar roboczy, zobacz temat [Usuwanie obszaru roboczego usługi Azure log Analytics](../platform/delete-workspace.md).
