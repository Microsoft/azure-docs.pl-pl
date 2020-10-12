---
title: Co to jest kontroler Azure Application Gateway transferu danych przychodzących?
description: Ten artykuł zawiera wprowadzenie do tego, co Application Gateway kontroler transferu danych przychodzących.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: c1bd41587e4f56fb0a7f3eb8285d301751f558d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84668104"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Co to jest Application Gateway kontroler transferu danych przychodzących?
Application Gateway AGIC (transfer danych przychodzących) to aplikacja Kubernetes, która umożliwia klientom korzystającym z [usługi Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) korzystanie z natywnego [Application Gateway](https://azure.microsoft.com/services/application-gateway/) P7 modułu równoważenia obciążenia platformy Azure w celu udostępnienia oprogramowania chmury do Internetu. AGIC monitoruje klaster Kubernetes, na którym jest on hostowany, i ciągle aktualizuje Application Gateway, dzięki czemu wybrane usługi są dostępne w Internecie.

Kontroler transferu danych przychodzących działa we własnym zakresie na AKS klienta. AGIC monitoruje podzestaw zasobów Kubernetes dla zmian. Stan klastra AKS jest tłumaczony na Application Gateway konkretną konfigurację i stosowany do [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

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
Istnieją dwa sposoby wdrażania AGIC dla klastra AKS. Pierwszy sposób polega na Helm; Druga z nich to AKS jako dodatek. Główną zaletą wdrażania AGIC jako dodatku AKS jest znacznie prostsze niż wdrażanie przez Helm. Aby uzyskać nową konfigurację, można wdrożyć nowe Application Gateway i nowy klaster AKS z włączoną funkcją AGIC jako dodatek w jednym wierszu w interfejsie wiersza polecenia platformy Azure. Dodatek to również w pełni zarządzana usługa, która zapewnia dodatkowe korzyści, takie jak aktualizacje automatyczne i zwiększona pomoc techniczna. AGIC wdrożone za pomocą Helm nie są obsługiwane przez AKS, jednak AGIC wdrożony jako dodatek AKS jest obsługiwany przez AKS. 

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
> Metoda dodatku AGIC AKS dla wdrożenia jest obecnie dostępna w wersji zapoznawczej. Nie zalecamy uruchamiania obciążeń produkcyjnych na funkcjach dostępnych w wersji zapoznawczej, więc jeśli chcesz wiedzieć chcesz ją wypróbować, zalecamy skonfigurowanie nowego klastra w celu przetestowania go w programie. 

W poniższych tabelach przedstawiono, które scenariusze są obecnie obsługiwane w przypadku Helm wdrożenia i wersji AKS dodatku AGIC. 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS — dodatek AGIC (pojedynczy klaster AKS)
|                  |1 Application Gateway |dwie bramy aplikacji |
|------------------|---------|--------|
|**1 AGIC**|Tak, jest to obsługiwane |Nie, to jest w naszym zaległości |
|**2 + AGICs**|Nie, tylko 1 AGIC obsługiwane/klaster |Nie, tylko 1 AGIC obsługiwane/klaster |

### <a name="helm-deployed-agic-single-aks-cluster"></a>Helm wdrożone AGIC (pojedynczy klaster AKS)
|                  |1 Application Gateway |dwie bramy aplikacji |
|------------------|---------|--------|
|**1 AGIC**|Tak, jest to obsługiwane |Nie, to jest w naszym zaległości |
|**2 + AGICs**|Musi używać współużytkowanych funkcji ProhibitedTarget i oglądać oddzielne przestrzenie nazw |Tak, jest to obsługiwane |

### <a name="helm-deployed-agic-2-aks-clusters"></a>Helm wdrożone AGIC (2 + AKS klastrów)
|                  |1 Application Gateway |dwie bramy aplikacji |
|------------------|---------|--------|
|**1 AGIC**|NIE DOTYCZY |NIE DOTYCZY |
|**2 + AGICs**|Musi używać funkcji Shared ProhibitedTarget |Nie dotyczy |

## <a name="next-steps"></a>Następne kroki
- [**AKS Add-On Greenfield Deployment**](tutorial-ingress-controller-add-on-new.md): instrukcje dotyczące instalowania dodatku AGIC, AKS i Application Gateway dla infrastruktury pustej.
- [**AKS Add-On brownfield Deployment**](tutorial-ingress-controller-add-on-existing.md): Zainstaluj dodatek AGIC w klastrze AKS z istniejącym Application Gateway.
- [**Helm Greenfield Deployment**](ingress-controller-install-new.md): Instaluj AGIC za poorednictwem Helm, nowego klastra AKS i nowego Application Gateway w infrastrukturze z pustą.
- [**Helm brownfield Deployment**](ingress-controller-install-existing.md): Wdróż AGIC za poorednictwem Helm na istniejącym klastrze AKS i Application Gateway.

