---
title: Co to jest kontroler Azure Application Gateway transferu danych przychodzących?
description: Ten artykuł zawiera wprowadzenie do tego, co Application Gateway kontroler transferu danych przychodzących.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335801"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Co to jest Application Gateway kontroler transferu danych przychodzących?
Application Gateway AGIC (transfer danych przychodzących) to aplikacja Kubernetes, która umożliwia klientom korzystającym z [usługi Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) korzystanie z natywnego [Application Gateway](https://azure.microsoft.com/services/application-gateway/) P7 modułu równoważenia obciążenia platformy Azure w celu udostępnienia oprogramowania chmury do Internetu. AGIC monitoruje klaster Kubernetes, na którym jest on hostowany, i ciągle aktualizuje Application Gateway, dzięki czemu wybrane usługi są dostępne w Internecie.

Kontroler transferu danych przychodzących działa we własnym zakresie na AKS klienta. AGIC monitoruje podzestaw zasobów Kubernetes dla zmian. Stan klastra AKS jest tłumaczony na Application Gateway konkretną konfigurację i stosowany do [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Zalety kontrolera Application Gateway transferu danych przychodzących
AGIC umożliwia wdrożenie w celu kontrolowania wielu klastrów AKS z pojedynczym Application Gateway kontrolerem danych przychodzących. AGIC pomaga również wyeliminować konieczność posiadania innego modułu równoważenia obciążenia/publicznego adresu IP przed klastrem AKS i unika wielu przeskoków w ścieżce danych, zanim żądania docierają do klastra AKS. Application Gateway rozmowy do zasobników przy użyciu prywatnego adresu IP bezpośrednio i nie wymagają usług NodePort i KubeProxy. Zapewnia to również lepszą wydajność wdrożeń.

Kontroler transferu danych przychodzących jest obsługiwany wyłącznie przez Standard_v2 i WAF_v2 jednostki SKU, które również zapewniają korzyści skalowania automatycznego. Application Gateway mogą reagować na zwiększenie lub zmniejszenie obciążenia ruchu i odpowiednio skalować, bez zużywania żadnych zasobów z klastra AKS.

Użycie Application Gateway oprócz usługi AGIC ułatwia również ochronę klastra AKS przez zapewnienie funkcjonalności TLS i zapory aplikacji sieci Web (WAF).

![Azure Application Gateway i AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC konfiguruje się za pośrednictwem [zasobu](https://kubernetes.io/docs/user-guide/ingress/)transferu Kubernetes, a także usług i wdrożeń/zasobników. Udostępnia wiele funkcji, korzystając z natywnego modułu równoważenia obciążenia usługi Azure Application Gateway P7. Aby nazwać kilka:
  - Routing adresów URL
  - Koligacja na podstawie pliku cookie
  - Zakończenie protokołu TLS
  - Kompleksowa protokół TLS
  - Obsługa publicznych, prywatnych i hybrydowych witryn sieci Web
  - Zintegrowana Zapora aplikacji sieci Web

AGIC jest w stanie obsłużyć wiele przestrzeni nazw i ma ProhibitedTargets, co oznacza, że AGIC można skonfigurować Application Gateway przeznaczonych dla klastrów AKS bez wpływu na inne istniejące punkty końcowe. 

## <a name="next-steps"></a>Następne kroki

- [**Wdrożenie Greenfield**](ingress-controller-install-new.md): instrukcje dotyczące instalowania AGIC, AKS i Application Gateway w przypadku niepustej infrastruktury.
- [**Wdrożenie brownfield**](ingress-controller-install-existing.md): Zainstaluj AGIC na istniejących AKS i Application Gateway.

