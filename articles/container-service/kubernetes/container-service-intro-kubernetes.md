---
title: (PRZESTARZAŁE) Wprowadzenie do usługi Azure Container Service dla rozwiązania Kubernetes
description: Usługa Azure Container Service dla rozwiązania Kubernetes ułatwia wdrażanie aplikacji opartych na kontenerach i zarządzanie nimi na platformie Azure.
author: gabrtv
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: c0ef7255a087dd5dc26532316deab337f9eff715
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76271583"
---
# <a name="deprecated-introduction-to-azure-container-service-for-kubernetes"></a>(PRZESTARZAŁE) Wprowadzenie do usługi Azure Container Service dla rozwiązania Kubernetes

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego artykułu z opisem użycia usługi Azure Kubernetes Service, zobacz [Omówienie usługi Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Usługa Azure Container Service dla rozwiązania Kubernetes upraszcza tworzenie i konfigurację klastra maszyn wirtualnych, które są wstępnie skonfigurowane do uruchamiania konteneryzowanych aplikacji, oraz zarządzanie nim. Umożliwia to używanie posiadanych umiejętności lub sięganie po duży i rosnący zasób wiedzy społeczności w celu wdrażania opartych na kontenerze aplikacji platformy Microsoft Azure i zarządzania nimi.

Za pomocą usługi Azure Container Service możesz korzystać z funkcji klasy korporacyjnej platformy Azure, zachowując jednocześnie przenośność aplikacji dzięki usłudze Kubernetes i formatowi obrazów Docker.

## <a name="using-azure-container-service-for-kubernetes"></a>Używanie usługi Azure Container Service ma potrzeby rozwiązania Kubernetes
Za pomocą usługi Azure Container Service chcemy zapewnić środowisko hostingu kontenerów za pomocą narzędzi i technologii typu open source, które już dziś są popularne wśród naszych klientów. W tym celu uwidaczniamy standardowe punkty końcowe interfejsu API rozwiązania Kubernetes. Za pomocą tych standardowych punktów końcowych można wykorzystać dowolne oprogramowanie, które jest w stanie komunikować się z klastrem usługi Kubernetes. Możesz wybrać narzędzie [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) lub [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Tworzenie klastra Kubernetes przy użyciu usługi Azure Container Service
Aby rozpocząć korzystanie z usługi Azure Container Service, musisz wdrożyć klaster usługi Azure Container Service przy użyciu [interfejsu wiersza polecenia platformy Azure](container-service-kubernetes-walkthrough.md) lub za pośrednictwem portalu (wyszukaj w witrynie Marketplace termin **Azure Container Service**). Jeśli jesteś użytkownikiem zaawansowanym, który musi mieć większą kontrolę nad szablonami usługi Azure Resource Manager, możesz użyć projektu [acs-engine](https://github.com/Azure/acs-engine) typu open source do kompilowania własnego niestandardowego klastra Kubernetes i wdrażania go za pomocą interfejsu wiersza polecenia `az`.

### <a name="using-kubernetes"></a>Korzystanie z rozwiązania Kubernetes
Narzędzie Kubernetes automatyzuje proces wdrażania i skalowania aplikacji konteneryzowanych oraz zarządzania nimi. Narzędzie to obejmuje bogaty zestaw funkcji, m.in.:
* automatyczne pakowanie pudełek,
* mechanizm samonaprawiania
* skalowanie w poziomie,
* odnajdywanie usług i równoważenie obciążenia,
* zautomatyzowane wprowadzanie i wycofywanie zmian,
* zarządzanie kluczami tajnymi i konfiguracją,
* aranżacja magazynu,
* wykonywanie partii zadań.

Diagram architektury rozwiązania Kubernetes wdrażanej za pomocą usługi Azure Container Service:

![Usługa Azure Container Service skoordynowana do użycia narzędzia Kubernetes.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>Filmy wideo

Obsługa klastra Kubernetes w usłudze Azure Container Service (Azure Friday, styczeń 2017 r.):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Narzędzia do tworzenia i wdrażania aplikacji w systemie Kubernetes (Azure OpenDev, czerwiec 2017 r.):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [przewodnikiem Szybki start dotyczącym rozwiązania Kubernetes](container-service-kubernetes-walkthrough.md), aby rozpocząć eksplorowanie usługi Azure Container Service już dziś.
