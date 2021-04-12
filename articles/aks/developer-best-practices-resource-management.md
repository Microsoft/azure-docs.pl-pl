---
title: Najlepsze rozwiązania dotyczące zarządzania zasobami
titleSuffix: Azure Kubernetes Service
description: Poznaj najlepsze rozwiązania dla deweloperów aplikacji dotyczące zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105276"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dla deweloperów aplikacji do zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)

Podczas opracowywania i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS) istnieje kilka kluczowych zagadnień, które należy wziąć pod uwagę. Zarządzanie wdrożeniami aplikacji może mieć negatywny wpływ na środowisko użytkownika końcowego, które zapewnia usługa. Aby się powieść, weź pod uwagę niektóre najlepsze rozwiązania, które można wykonać podczas tworzenia i uruchamiania aplikacji w AKS.

Ten artykuł koncentruje się na uruchamianiu klastra i obciążeń z perspektywy deweloperów aplikacji. Aby uzyskać informacje o najlepszych rozwiązaniach administracyjnych, zobacz [najlepsze rozwiązania operatorów klastrów na potrzeby izolacji i zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. W tym artykule omówiono następujące zagadnienia:

> [!div class="checklist"]
> * Żądania zasobów i limity.
> * Sposoby tworzenia i wdrażania aplikacji za pomocą mostka do Kubernetes i Visual Studio Code.
> * Jak używać `kube-advisor` Narzędzia do sprawdzania problemów z wdrożeniami.

## <a name="define-pod-resource-requests-and-limits"></a>Definiuj żądania zasobów i limity

> **Wskazówki dotyczące najlepszych rozwiązań**
> 
> Ustaw żądania i limity dla wszystkich zasobników w manifestach YAML. Jeśli klaster AKS korzysta z *przydziałów zasobów* i nie definiujesz tych wartości, wdrożenie może zostać odrzucone.

Użyj żądań i limitów do zarządzania zasobami obliczeniowymi w klastrze AKS. Żądania i limity w programie informują Kubernetes Scheduler, które zasoby obliczeniowe mają być przypisane do usługi pod.

### <a name="pod-cpumemory-requests"></a>Na żądanie procesora CPU/pamięci
*Żądania pod* definiują określoną ilość czasu procesora i pamięci, która jest regularnie wymagana.

We własnych specyfikacjach **najlepiej** jest określić te żądania i limity na podstawie powyższych informacji. Jeśli nie dołączysz tych wartości, usługa Kubernetes Scheduler nie może uwzględnić zasobów wymaganych przez aplikacje w celu uzyskania pomocy w planowaniu decyzji.

Monitoruj wydajność aplikacji, aby dostosować żądania pod kątem. 
* W przypadku obniżenia poziomu żądań aplikacja może otrzymać obniżoną wydajność ze względu na przekroczenie harmonogramu węzła. 
* Jeśli żądania są zawyżone, aplikacja może zwiększyć zaplanowaną trudność.

### <a name="pod-cpumemory-limits"></a>W obszarze limity procesora/pamięci * * 
*Limity* powyżej ustawiają maksymalną ilość procesora CPU i pamięci, której może użyć. 

* *Limity pamięci* definiują, które zasobniki należy zabić, gdy węzły są niestabilne z powodu niewystarczających zasobów. Bez ustawionych prawidłowych limitów, w celu wypróbowania nanoszenia zasobów nie będzie można zabić. 
* Chociaż pod może okresowo przekroczyć *Limit CPU* , w przypadku przekroczenia limitu CPU nie zostanie on zabity. 

Limity pod określają, kiedy w obszarze została utracona Kontrola zużycia zasobów. W przypadku przekroczenia limitu, pod jest oznaczona do zabijania. To zachowanie utrzymuje kondycję węzła i minimalizuje wpływ na te, które współużytkują węzeł. W przypadku ustawienia wartości granicznej nie można ustawić najwyższej dostępnej wartości w danym węźle.

Należy unikać ustawiania limitu pod wyższego poziomu niż węzły mogą obsługiwać. Każdy węzeł AKS rezerwuje określoną ilość procesora CPU i pamięci dla podstawowych składników Kubernetes. Aplikacja może próbować użyć zbyt wielu zasobów w węźle, aby pozostałe zasobniki mogły zostać pomyślnie uruchomione.

Monitoruj wydajność aplikacji w różnym czasie w ciągu dnia lub tygodnia. Określ czasy popytu szczytowego i Dopasuj limity pod względem zasobów wymaganych do osiągnięcia maksymalnych potrzeb.

> [!IMPORTANT]
>
> We własnych specyfikacjach, zdefiniuj te żądania i limity na podstawie powyższych informacji. W przypadku niepowodzenia uwzględniania tych wartości program Kubernetes Scheduler nie może księgować zasobów wymaganych przez aplikacje, aby pomóc w podejmowaniu decyzji dotyczących planowania.

Jeśli harmonogram umieści miejsce na węźle z niewystarczającymi zasobami, wydajność aplikacji zostanie obniżona. Administratorzy klastra **muszą** ustawić *przydziały zasobów* w przestrzeni nazw, która wymaga ustawienia żądań i limitów zasobów. Aby uzyskać więcej informacji, zobacz [zasoby zasobów w klastrach AKS][resource-quotas].

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

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Zespoły deweloperów powinny wdrażać i debugować klaster AKS przy użyciu programu Bridge do Kubernetes.

Za pomocą programu Bridge to Kubernetes można opracowywać, debugować i testować aplikacje bezpośrednio w klastrze AKS. Deweloperzy w zespole współpracują nad kompilowaniem i testowaniem całego cyklu życia aplikacji. Można nadal korzystać z istniejących narzędzi, takich jak Visual Studio lub Visual Studio Code, z rozszerzeniem Kubernetes. 

Używanie zintegrowanego procesu tworzenia i testowania z usługą Bridge do Kubernetes zmniejsza potrzebę używania lokalnych środowisk testowych, takich jak [minikube][minikube]. Zamiast tego można tworzyć i testować klaster AKS, nawet zabezpieczone i izolowane klastry. 

> [!NOTE]
> Mostek do Kubernetes jest przeznaczony do użycia z aplikacjami, które działają na podst. i węzłach systemu Linux.

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>Użyj rozszerzenia Visual Studio Code (VS Code) dla Kubernetes

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Zainstaluj VS Code rozszerzenie i użyj go dla Kubernetes podczas pisania manifestów YAML. Można również użyć rozszerzenia dla zintegrowanego rozwiązania do wdrażania, które może pomóc właścicielom aplikacji, którzy rzadko współdziałają z klastrem AKS.

[Visual Studio Code rozszerzenie Kubernetes][vscode-kubernetes] ułatwia tworzenie i wdrażanie aplikacji w AKS. Rozszerzenie zapewnia:
* Funkcja IntelliSense dla Kubernetes zasobów, wykresów Helm i szablonów. 
* Przeglądaj, wdrażaj i edytuj możliwości dla zasobów Kubernetes z poziomu VS Code. 
* Sprawdzanie funkcji IntelliSense dla żądań zasobów lub limitów ustawionych w specyfikacjach pod:

    ![VS Code rozszerzenie dla ostrzeżenia Kubernetes o brakujących limitów pamięci](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Regularnie sprawdzaj problemy z aplikacjami za pomocą usługi polecenia-Advisor

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Regularnie uruchamiaj najnowszą wersję `kube-advisor` Narzędzia typu open source, aby wykrywać problemy w klastrze. Uruchom `kube-advisor` przed zastosowaniem przydziałów zasobów w istniejącym KLASTRZE AKS, aby znaleźć te, które nie mają zdefiniowanych żądań zasobów i limitów.

Narzędzie [polecenia-Advisor][kube-advisor] to skojarzony projekt typu "open source" AKS, który skanuje klaster Kubernetes i raportuje o zidentyfikowanych problemach. Jednym z przydatnych kontroli jest zidentyfikowanie, bez żądań zasobów i limitów.

Gdy `kube-advisor` Narzędzie może raportować żądania zasobów i nie ma ograniczeń w PodSpecs dla aplikacji systemu Windows i Linux, `kube-advisor` należy je zaplanować w systemie Linux pod. Użyj [selektora węzłów][k8s-node-selector] w konfiguracji pod, aby zaplanować uruchomienie w puli węzłów z określonym systemem operacyjnym.

W klastrze AKS, który hostuje wiele zespołów deweloperskich i aplikacji, łatwiej będzie śledzić te informacje przy użyciu żądań zasobów i limitów. Najlepszym rozwiązaniem jest regularne działanie `kube-advisor` w klastrach AKS.

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na sposobie uruchamiania klastra i obciążeń z perspektywy operatora klastra. Aby uzyskać informacje o najlepszych rozwiązaniach administracyjnych, zobacz [najlepsze rozwiązania operatorów klastrów na potrzeby izolacji i zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

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
