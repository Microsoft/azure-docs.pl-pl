---
title: Co to jest kontroler Azure Application Gateway transferu danych przychodzących?
description: Ten artykuł zawiera wprowadzenie do tego, co Application Gateway kontroler transferu danych przychodzących.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2564fd38056241fd48f58f5f6039bf64f92b6741
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714412"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Co to jest Application Gateway kontroler transferu danych przychodzących?
Application Gateway AGIC (transfer danych przychodzących) to aplikacja Kubernetes, która umożliwia klientom korzystającym z [usługi Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) korzystanie z natywnego [Application Gateway](https://azure.microsoft.com/services/application-gateway/) P7 modułu równoważenia obciążenia platformy Azure w celu udostępnienia oprogramowania chmury do Internetu. AGIC monitoruje klaster Kubernetes, na którym jest on hostowany, i ciągle aktualizuje Application Gateway, dzięki czemu wybrane usługi są dostępne w Internecie.

Kontroler transferu danych przychodzących działa we własnym zakresie na AKS klienta. AGIC monitoruje podzestaw zasobów Kubernetes dla zmian. Stan klastra AKS jest tłumaczony na Application Gateway konkretną konfigurację i stosowany do [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Zalety kontrolera Application Gateway transferu danych przychodzących
AGIC pomaga wyeliminować konieczność posiadania innego modułu równoważenia obciążenia/publicznego adresu IP przed klastrem AKS i unika wielu przeskoków w ścieżce danych, zanim żądania docierają do klastra AKS. Application Gateway rozmowy do zasobników przy użyciu prywatnego adresu IP bezpośrednio i nie wymagają usług NodePort i KubeProxy. Zapewnia to również lepszą wydajność wdrożeń.

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

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Różnica między wdrożeniem Helm i AKS Add-On
Istnieją dwa sposoby wdrażania AGIC dla klastra AKS. Pierwszy sposób polega na Helm; Druga z nich to AKS jako dodatek. Główną zaletą wdrażania AGIC jako dodatku AKS jest znacznie prostsze niż wdrażanie przez Helm. Aby uzyskać nową konfigurację, można wdrożyć nowe Application Gateway i nowy klaster AKS z włączoną funkcją AGIC jako dodatek w jednym wierszu w interfejsie wiersza polecenia platformy Azure. Dodatek to również w pełni zarządzana usługa, która zapewnia dodatkowe korzyści, takie jak aktualizacje automatyczne i zwiększona pomoc techniczna. Obie metody wdrażania AGIC (Helm i AKS) są w pełni obsługiwane przez firmę Microsoft. Ponadto dodatek umożliwia lepszą integrację z usługą AKS jako dodatkiem pierwszej klasy.

Dodatek AGIC jest nadal wdrażany jako na przykład w klastrze AKS klienta, jednak istnieje kilka różnic między wersją wdrożenia Helm i wersją dodatku AGIC. Poniżej znajduje się lista różnic między dwiema wersjami: 
  - Helm wdrożenia nie mogą być modyfikowane w dodatku AKS:
    - `verbosityLevel` zostanie domyślnie ustawiona na 5
    - `usePrivateIp` będzie domyślnie ustawiona na wartość false; może to zostać zastąpione przez [adnotację use-Private-IP](ingress-controller-annotations.md#use-private-ip)
    - `shared` nie jest obsługiwane w dodatku 
    - `reconcilePeriodSeconds` nie jest obsługiwane w dodatku
    - `armAuth.type` nie jest obsługiwane w dodatku
  - AGIC wdrożone za pośrednictwem Helm obsługuje ProhibitedTargets, co oznacza, że AGIC może skonfigurować Application Gateway przeznaczony dla klastrów AKS bez wpływu na inne istniejące założenia. Dodatek AGIC nie jest obecnie obsługiwany. 
  - Ponieważ dodatek AGIC jest usługą zarządzaną, klienci zostaną automatycznie zaktualizowani do najnowszej wersji dodatku AGIC, w przeciwieństwie do AGIC wdrożonego za pomocą Helm, gdzie klient musi ręcznie zaktualizować AGIC. 

> [!NOTE]
> Klienci mogą wdrażać tylko jeden dodatek AGIC na klastrze AKS, a każdy dodatek AGIC jest obecnie przeznaczony tylko do jednego Application Gateway. W przypadku wdrożeń, które wymagają więcej niż jednego AGIC na klaster lub wiele AGICs przeznaczonych dla jednego Application Gateway, należy nadal używać AGIC wdrożonych za pomocą Helm. 

## <a name="next-steps"></a>Następne kroki
- [**AKS Add-On Greenfield Deployment**](tutorial-ingress-controller-add-on-new.md): instrukcje dotyczące instalowania dodatku AGIC, AKS i Application Gateway dla infrastruktury pustej.
- [**AKS Add-On brownfield Deployment**](tutorial-ingress-controller-add-on-existing.md): Zainstaluj dodatek AGIC w klastrze AKS z istniejącym Application Gateway.
- [**Helm Greenfield Deployment**](ingress-controller-install-new.md): Instaluj AGIC za poorednictwem Helm, nowego klastra AKS i nowego Application Gateway w infrastrukturze z pustą.
- [**Helm brownfield Deployment**](ingress-controller-install-existing.md): Wdróż AGIC za poorednictwem Helm na istniejącym klastrze AKS i Application Gateway.