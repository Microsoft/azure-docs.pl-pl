---
title: Najlepsze rozwiązania dotyczące zarządzania zasobami
titleSuffix: Azure Kubernetes Service
description: Poznaj najlepsze rozwiązania dla deweloperów aplikacji dotyczące zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 693cabac616dca8e108a2029c173a5e1b71c2695
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516730"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dla deweloperów aplikacji do zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)

Podczas opracowywania i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS) istnieje kilka kluczowych zagadnień, które należy wziąć pod uwagę. Zarządzanie wdrożeniami aplikacji może mieć negatywny wpływ na środowisko użytkownika końcowego, które zapewnia usługa. Aby pomóc w pomyślnym zapewnieniu, należy wziąć pod uwagę niektóre najlepsze rozwiązania, które można wykonać podczas tworzenia i uruchamiania aplikacji w AKS.

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na sposobie uruchamiania klastra i obciążeń z perspektywy deweloperów aplikacji. Aby uzyskać informacje o najlepszych rozwiązaniach administracyjnych, zobacz [najlepsze rozwiązania operatorów klastrów na potrzeby izolacji i zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. W tym artykule omówiono następujące zagadnienia:

> [!div class="checklist"]
> * Co to są żądania zasobów i limity
> * Sposoby tworzenia i wdrażania aplikacji za pomocą mostka do Kubernetes i Visual Studio Code
> * Jak używać `kube-advisor` Narzędzia do sprawdzania problemów z wdrożeniami

## <a name="define-pod-resource-requests-and-limits"></a>Definiuj żądania zasobów i limity

**Wskazówki dotyczące najlepszych** rozwiązań — Ustaw żądania i limity dla wszystkich zasobników w manifestach YAML. Jeśli klaster AKS korzysta z *przydziałów zasobów*, wdrożenie może zostać odrzucone, jeśli nie zostaną zdefiniowane te wartości.

Podstawowym sposobem zarządzania zasobami obliczeniowymi w klastrze AKS jest użycie żądań i limitów. Te żądania i limity pozwalają usłudze Kubernetes Scheduler wiedzieć, jakie zasoby obliczeniowe należy przypisać.

* W obszarze **żądania procesora CPU/pamięci** definiuje określoną ilość czasu procesora i pamięci, która jest regularnie wymagana.
    * Gdy planista usługi Kubernetes próbuje umieścić element na węźle, żądania poniżej są używane do określenia, który węzeł ma wystarczające zasoby dostępne do zaplanowania.
    * Jeśli żądanie nie zostanie skonfigurowane, zostanie ono ustawione domyślnie na określony limit.
    * Bardzo ważne jest, aby monitorować wydajność aplikacji w celu dostosowania tych żądań. Jeśli są wykonywane niewystarczające żądania zasobów, aplikacja może otrzymać obniżoną wydajność z powodu zbyt krótkiego planowania węzła. Jeśli żądania są zawyżone, aplikacja może zwiększyć zaplanowaną trudność.
* **Limity procesora CPU/pamięci** są maksymalną ilością procesora i pamięci, której może użyć. Limity pamięci definiują, które z nich mają zostać zabite w przypadku niestabilności węzła z powodu niewystarczających zasobów. Bez odpowiednich limitów ustawionych zasobniki zostaną zabite, dopóki obciążenie zasobów nie zostanie zniesione. Program może lub nie może przekroczyć limitu CPU przez określony czas, ale nie zostanie zamknięty w przypadku przekroczenia limitu procesora CPU. 
    * Limity powyżej ułatwiają zdefiniowanie, kiedy w obszarze została utracona Kontrola zużycia zasobów. Po przekroczeniu limitu wartość w obszarze jest określana jako priorytetowe w celu utrzymania kondycji węzła i zminimalizowania wpływu na te, które współużytkują węzeł.
    * W przypadku ustawienia wartości granicznej nie można ustawić najwyższej dostępnej wartości w danym węźle.
    * Nie ustawiaj limitu pod wyższe niż nie można obsługiwać węzłów. Każdy węzeł AKS rezerwuje określoną ilość procesora CPU i pamięci dla podstawowych składników Kubernetes. Aplikacja może próbować użyć zbyt wielu zasobów w węźle, aby pozostałe zasobniki mogły zostać pomyślnie uruchomione.
    * Z tego powodu bardzo ważne jest, aby monitorować wydajność aplikacji w różnym czasie w ciągu dnia lub tygodnia. Określ, kiedy szczytowe zapotrzebowanie ma, i Dopasuj limity pod względem zasobów wymaganych do osiągnięcia maksymalnych potrzeb aplikacji.

We własnych specyfikacjach **najlepiej** jest określić te żądania i limity na podstawie powyższych informacji. Jeśli nie dołączysz tych wartości, usługa Kubernetes Scheduler nie może uwzględnić zasobów wymaganych przez aplikacje w celu uzyskania pomocy w planowaniu decyzji.

Jeśli harmonogram umieści miejsce na węźle z niewystarczającymi zasobami, wydajność aplikacji zostanie obniżona. Zdecydowanie zaleca się, aby administratorzy klastrów ustawiali *limity przydziału zasobów* w przestrzeni nazw, która wymaga ustawienia żądań i limitów zasobów. Aby uzyskać więcej informacji, zobacz [zasoby zasobów w klastrach AKS][resource-quotas].

Podczas definiowania żądania lub limitu procesora CPU wartość jest mierzona w jednostkach procesora CPU. 
* *1,0* CPU odpowiada jednemu podstawowemu RDZENIU procesora CPU w węźle. 
* Ten sam pomiar jest używany w procesorach GPU.
* Można zdefiniować ułamki mierzone w millicores. Na przykład *100 mln* jest *0,1* podstawowego rdzeń vCPU.

W poniższym przykładowym przykładzie dla pojedynczego NGINX pod, żądania pod *100 mln* czasu procesora CPU i *128Mi* pamięci. Limity zasobów dla pod są ustawione na *250m* procesora CPU i *256Mi* pamięć:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Aby uzyskać więcej informacji na temat pomiarów zasobów i przydziałów, zobacz [Zarządzanie zasobami obliczeniowymi dla kontenerów][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Opracowywanie i debugowanie aplikacji w klastrze AKS

**Wskazówki dotyczące najlepszych** rozwiązań — zespoły programistyczne powinny wdrażać i DEBUGOWAĆ klaster AKS przy użyciu programu Bridge do Kubernetes.

Za pomocą programu Bridge to Kubernetes można opracowywać, debugować i testować aplikacje bezpośrednio w klastrze AKS. Deweloperzy w zespole współpracują ze sobą, aby kompilować i testować cały cykl życia aplikacji. Można nadal korzystać z istniejących narzędzi, takich jak Visual Studio lub Visual Studio Code. Rozszerzenie jest zainstalowane dla mostka Kubernetes, który umożliwia tworzenie bezpośrednio w klastrze AKS.

Ten zintegrowany proces tworzenia i testowania z mostkiem do Kubernetes zmniejsza potrzebę używania lokalnych środowisk testowych, takich jak [minikube][minikube]. Zamiast tego można tworzyć i testować klaster AKS. Ten klaster może być zabezpieczony i izolowany, jak wspomniano w poprzedniej sekcji dotyczącej używania przestrzeni nazw do logicznego izolowania klastra.

Mostek do Kubernetes jest przeznaczony do użycia z aplikacjami, które działają na podst. i węzłach systemu Linux.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Użyj rozszerzenia Visual Studio Code dla Kubernetes

**Wskazówki dotyczące najlepszych** rozwiązań — Zainstaluj i użyj rozszerzenia vs Code Kubernetes podczas pisania manifestów YAML. Można również użyć rozszerzenia dla zintegrowanego rozwiązania do wdrażania, które może pomóc właścicielom aplikacji, którzy rzadko współdziałają z klastrem AKS.

[Visual Studio Code rozszerzenie Kubernetes][vscode-kubernetes] ułatwia tworzenie i wdrażanie aplikacji w AKS. Rozszerzenie udostępnia funkcję IntelliSense dla zasobów Kubernetes oraz wykresów i szablonów Helm. Możesz również przeglądać, wdrażać i edytować zasoby Kubernetes z poziomu VS Code. Rozszerzenie udostępnia również kontrolę IntelliSense dla żądań zasobów lub limitów ustawionych w specyfikacjach pod:

![VS Code rozszerzenie dla ostrzeżenia Kubernetes o brakujących limitów pamięci](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Regularnie sprawdzaj problemy z aplikacjami za pomocą usługi polecenia-Advisor

**Wskazówki dotyczące najlepszych** rozwiązań — regularnie uruchamiaj najnowszą wersję `kube-advisor` Narzędzia Open Source, aby wykrywać problemy w klastrze. W przypadku zastosowania przydziałów zasobów w istniejącym klastrze AKS najpierw uruchom polecenie, `kube-advisor` Aby znaleźć informacje o jednostkach, dla których zdefiniowano żądania zasobów i limity.

Narzędzie [polecenia-Advisor][kube-advisor] to SKOJARZONY projekt AKS typu open source, który skanuje klaster Kubernetes i raportuje o znalezionych problemach. Jednym z przydatnych kontroli jest zidentyfikowanie, które nie mają żądań zasobów i limitów.

Narzędzie polecenia-Advisor może raportować żądania zasobów i limitów braku w PodSpecs dla aplikacji systemu Windows, a także aplikacji z systemem Linux, ale narzędzie Advisor polecenia musi zostać zaplanowane w systemie Linux pod. Można zaplanować uruchomienie w puli węzłów z określonym systemem operacyjnym przy użyciu [selektora węzłów][k8s-node-selector] w konfiguracji pod.

W klastrze AKS, który hostuje wiele zespołów i aplikacji programistycznych, może być trudno śledzić zestaw na platformie i nie tylko te żądania zasobów i limity. Najlepszym rozwiązaniem jest regularne działanie `kube-advisor` w klastrach AKS.

## <a name="next-steps"></a>Następne kroki

W tym artykule dotyczącym najlepszych rozwiązań opisano sposób uruchamiania klastra i obciążeń z perspektywy operatora klastra. Aby uzyskać informacje o najlepszych rozwiązaniach administracyjnych, zobacz [najlepsze rozwiązania operatorów klastrów na potrzeby izolacji i zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

Aby zaimplementować niektóre z tych najlepszych rozwiązań, zobacz następujące artykuły:

* [Programowanie przy użyciu programu Bridge do Kubernetes][btk]
* [Sprawdź, czy występują problemy z usługą polecenia — Advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
