---
title: Dostęp do zasobów Kubernetes z Azure Portal
description: Dowiedz się, jak korzystać z zasobów Kubernetes, aby zarządzać klastrem usługi Azure Kubernetes Service (AKS) z poziomu Azure Portal.
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: ce5dc74dc3625b2b1fed447c4e6480308267d32a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578673"
---
# <a name="access-kubernetes-resources-from-the-azure-portal"></a>Dostęp do zasobów Kubernetes z Azure Portal

Azure Portal obejmuje widok zasobów Kubernetes na potrzeby łatwego dostępu do zasobów Kubernetes w klastrze usługi Azure Kubernetes Service (AKS). Wyświetlanie Kubernetes zasobów z Azure Portal powoduje zmniejszenie przełączenia kontekstu między Azure Portal i `kubectl` narzędzia wiersza polecenia, usprawniając proces wyświetlania i edytowania zasobów Kubernetes. Podgląd zasobów zawiera obecnie wiele typów zasobów, takich jak wdrożenia, zbiory i zestawy replik.

Widok zasobów Kubernetes z Azure Portal zastępuje [dodatek pulpitu nawigacyjnego AKS][kubernetes-dashboard], który jest przestarzały.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wyświetlić Kubernetes zasoby w Azure Portal, wymagany jest klaster AKS. Każdy klaster jest obsługiwany, ale w przypadku korzystania z integracji Azure Active Directory (Azure AD) klaster musi używać [integracji usługi Azure AD zarządzanej przez AKS][aks-managed-aad]. Jeśli klaster używa starszej wersji usługi Azure AD, możesz uaktualnić klaster w portalu lub za pomocą [interfejsu wiersza polecenia platformy Azure][cli-aad-upgrade]. Możesz również [użyć Azure Portal,][portal-cluster] aby utworzyć nowy klaster AKS.

## <a name="view-kubernetes-resources"></a>Wyświetlanie zasobów Kubernetes

Aby wyświetlić zasoby Kubernetes, przejdź do klastra AKS w Azure Portal. Okienko nawigacji po lewej stronie służy do uzyskiwania dostępu do zasobów. Zasoby obejmują:

- **Przestrzenie nazw** wyświetlają przestrzenie nazw klastra. Filtr u góry listy przestrzeni nazw umożliwia szybkie filtrowanie i wyświetlanie zasobów przestrzeni nazw.
- W ramach **obciążeń** są wyświetlane informacje o wdrożeniach, zbiorach, zestawach, zestawach stanowych, zestawach demonów, zadaniach i zadaniach firmy CRONUS wdrożonych w klastrze. Zrzut ekranu poniżej przedstawia domyślny systemowy zasobniki w przykładowym klastrze AKS.
- **Usługi i ingresses** przedstawiają wszystkie zasoby usługi i transferu danych w klastrze.
- **Magazyn** przedstawia klasy usługi Azure Storage i informacje o trwałym woluminie.
- **Konfiguracja** pokazuje mapy konfiguracji klastra i wpisy tajne.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Kubernetes pod informacje wyświetlane w Azure Portal." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Wdrażanie aplikacji

W tym przykładzie użyjemy naszego przykładowego klastra AKS do wdrożenia aplikacji do głosowania platformy Azure z [przewodnika Szybki Start AKS][portal-quickstart].

1. Wybierz pozycję **Dodaj** z dowolnego widoku zasobów (przestrzeń nazw, obciążenia, usługi i Ingresses, magazyn lub Konfiguracja).
1. Wklej YAML do aplikacji do głosowania platformy Azure z [przewodnika Szybki Start AKS][portal-quickstart].
1. Wybierz pozycję **Dodaj** u dołu edytora YAML, aby wdrożyć aplikację. 

Po dodaniu pliku YAML w podglądzie zasobów zostaną wyświetlone zarówno usługi Kubernetes, które zostały utworzone: usługa wewnętrzna (Azure-głosowa-back), jak i usługa zewnętrzna (Azure-głosowa-Front), aby uzyskać dostęp do aplikacji do głosowania platformy Azure. Usługa zewnętrzna obejmuje połączony zewnętrzny adres IP, dzięki czemu można łatwo wyświetlić aplikację w przeglądarce.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Informacje o aplikacji do głosowania platformy Azure wyświetlane w Azure Portal." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Monitorowanie szczegółowych informacji o wdrożeniu

Klastry AKS z włączonymi [Azure monitorami dla kontenerów][enable-monitor] mogą szybko wyświetlać wdrożenia i inne szczegółowe informacje. W widoku zasobów Kubernetes użytkownicy mogą zobaczyć stan na żywo poszczególnych wdrożeń, w tym użycie procesora CPU i pamięci, a także przejść do usługi Azure monitor, aby uzyskać bardziej szczegółowe informacje o określonych węzłach i kontenerach. Oto przykład szczegółowych informacji dotyczących wdrażania z przykładowego klastra AKS:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Szczegółowe informacje o wdrożeniu wyświetlane w Azure Portal." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Edytuj YAML

Widok zasobów Kubernetes zawiera również edytor YAML. Wbudowany edytor YAML oznacza, że można aktualizować lub tworzyć usługi i wdrożenia z poziomu portalu i natychmiast zastosować zmiany.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Edytor YAML dla usługi Kubernetes wyświetlanej w Azure Portal.":::

Po przeprowadzeniu edycji YAML zmiany są stosowane, wybierając pozycję **Przegląd + Zapisz**, potwierdź zmiany, a następnie Zapisz ponownie.

>[!WARNING]
> Wykonywanie bezpośrednich zmian produkcyjnych za pomocą interfejsu użytkownika lub interfejsu wiersza polecenia nie jest zalecane. należy użyć [najlepszych rozwiązań integracji ciągłej (ci) i ciągłego wdrażania (CD)](kubernetes-action.md). Możliwości zarządzania Kubernetesami w witrynie Azure Portal i edytor YAML zostały stworzone w celu uczenia i przeprowadzenia nowego wdrożenia w ramach ustawienia tworzenia i testowania.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ta sekcja dotyczy typowych problemów i kroków związanych z rozwiązywaniem problemów.

### <a name="unauthorized-access"></a>Nieautoryzowany dostęp

Aby uzyskać dostęp do zasobów Kubernetes, musisz mieć dostęp do klastra AKS, interfejsu API Kubernetes oraz obiektów Kubernetes. Upewnij się, że jesteś administratorem klastra lub użytkownikiem z odpowiednimi uprawnieniami dostępu do klastra AKS. Aby uzyskać więcej informacji na temat zabezpieczeń klastra, zobacz [Opcje dostępu i tożsamości dla AKS][concepts-identity].

>[!NOTE]
> Widok zasobów Kubernetes w witrynie Azure Portal jest obsługiwany tylko przez [Klastry z obsługą usługi AAD](managed-aad.md) lub klastry z obsługą usługi AAD. W przypadku korzystania z klastra z włączoną obsługą usługi AAD użytkownik lub tożsamość muszą mieć odpowiednie role/powiązania ról umożliwiające dostęp do interfejsu API Kubernetes, a także uprawnienia do ściągania [użytkownika `kubeconfig` ](control-kubeconfig-access.md).

### <a name="enable-resource-view"></a>Włącz widok zasobów

W przypadku istniejących klastrów może być konieczne włączenie widoku zasobów Kubernetes. Aby włączyć widok zasobów, postępuj zgodnie z monitami w portalu dla klastra.

> [!TIP]
> Można dodać funkcję AKS dla [**dozwolonych zakresów adresów IP serwera interfejsu API**](api-server-authorized-ip-ranges.md) , aby ograniczyć dostęp serwera API tylko do publicznego punktu końcowego zapory. Inna opcja dla takich klastrów jest aktualizowana `--api-server-authorized-ip-ranges` w celu uwzględnienia dostępu do lokalnego komputera klienckiego lub zakresu adresów IP (z którego portalu jest przeglądany). Aby zezwolić na ten dostęp, potrzebny jest publiczny adres IPv4 komputera. Ten adres można znaleźć za pomocą poniższego polecenia lub przez wyszukiwanie w przeglądarce internetowej "co to jest mój adres IP".
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak uzyskać dostęp do zasobów Kubernetes dla klastra AKS. Zobacz artykuł [Deployments and YAML Manifests][deployments] , aby uzyskać bardziej szczegółowe informacje o zasobach klastra i plikach YAML, do których uzyskuje się dostęp przy użyciu podglądu zasobów Kubernetes.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/containers/container-insights-enable-existing-clusters.md
[portal-cluster]: kubernetes-walkthrough-portal.md