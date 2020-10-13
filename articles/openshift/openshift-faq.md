---
title: Często zadawane pytania dotyczące usługi Azure Red Hat OpenShift
description: Poniżej znajdują się odpowiedzi na często zadawane pytania dotyczące Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a8b5ec48b64341ad9eabd087d7ee20bb703198c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816239"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift — często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Microsoft Azure Red Hat OpenShift.

## <a name="installation-and-upgrade"></a>Instalacja i uaktualnianie

### <a name="which-azure-regions-are-supported"></a>Które regiony platformy Azure są obsługiwane?

Aby uzyskać listę obsługiwanych regionów dla usługi Azure Red Hat OpenShift 4. x, zobacz [dostępne regiony](https://docs.openshift.com/aro/4/welcome/index.html#available-regions).

Listę obsługiwanych regionów dla usługi Azure Red Hat OpenShift 3,11 można znaleźć w temacie [produkty dostępne według regionów](supported-resources.md#azure-regions).

### <a name="what-virtual-machine-sizes-can-i-use"></a>Jakie rozmiary maszyn wirtualnych można użyć?

Aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych dla usługi Azure Red Hat OpenShift 4, zobacz [obsługiwane zasoby dla systemu Azure Red Hat OpenShift 4](support-policies-v4.md).

Aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych dla systemu Azure Red Hat OpenShift 3,11, zobacz [obsługiwane zasoby dla systemu Azure Red Hat OpenShift 3,11](supported-resources.md).

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Jaka jest maksymalna liczba zasobników w klastrze Red Hat OpenShift systemu Azure?  Jaka jest maksymalna liczba zasobników na węzeł na platformie Azure Red Hat OpenShift?

Rzeczywista liczba obsługiwanych pojemności zależy od wymagań dotyczących pamięci, procesora i magazynu aplikacji.

Azure Red Hat OpenShift 4. x ma limit 250 na węzła i limit węzłów obliczeniowych 100. W tym przypadku Maksymalna liczba numerów w klastrze obsługiwanych przez klaster do 250 &times; 100 = 25 000.

W przypadku usługi Azure Red Hat OpenShift 3,11 obowiązuje limit 50 dla każdego węzła i limit liczby węzłów obliczeniowych. W tym przypadku Maksymalna liczba numerów w klastrze obsługiwanych przez klaster do 50 &times; 20 = 1 000.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Czy klaster może zawierać węzły obliczeniowe w wielu regionach świadczenia usługi Azure?

Nie. Wszystkie węzły w klastrze Red Hat OpenShift platformy Azure muszą pochodzić z tego samego regionu świadczenia usługi Azure.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>Czy klaster można wdrożyć w wielu strefach dostępności?

Tak. Dzieje się tak automatycznie, Jeśli klaster zostanie wdrożony w regionie świadczenia usługi Azure, który obsługuje strefy dostępności. Aby uzyskać więcej informacji, zobacz [strefy dostępności](../availability-zones/az-overview.md#availability-zones).

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Czy węzły płaszczyzny kontroli są abstrakcją, ponieważ są one w usłudze Azure Kubernetes Service (AKS)?

Nie. Wszystkie zasoby, w tym węzły główne klastra, są uruchamiane w ramach subskrypcji klienta. Te typy zasobów są umieszczane w grupie zasobów tylko do odczytu.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Czy klaster znajduje się w ramach subskrypcji klienta? 

Aplikacja zarządzana przez platformę Azure znajduje się w zablokowanej grupie zasobów z subskrypcją klienta. Klienci mogą wyświetlać obiekty w tej grupie zasobów, ale nie je modyfikować.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Czy na platformie Azure Red Hat OpenShift udostępniono inne klientów? Czy wszystko jest niezależne?

Każdy klaster usługi Azure Red Hat OpenShift jest przeznaczony dla danego klienta i przebywa w ramach subskrypcji klienta. 

### <a name="are-infrastructure-nodes-available"></a>Czy węzły infrastruktury są dostępne?

W przypadku klastrów Azure Red Hat OpenShift 4. x węzły infrastruktury nie są obecnie dostępne.

W przypadku klastrów z systemem Red Hat OpenShift 3,11 węzły infrastruktury są domyślnie uwzględniane.

## <a name="how-do-i-handle-cluster-upgrades"></a>Jak mogę obsługiwać uaktualnienia klastra?

Aby uzyskać informacje na temat uaktualnień, konserwacji i obsługiwanych wersji, zobacz [Przewodnik dotyczący cyklu pomocy technicznej](support-lifecycle.md).

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>Jak będzie aktualizowany system operacyjny hosta i oprogramowanie OpenShift?

Systemy operacyjne hosta i oprogramowanie OpenShift są aktualizowane w ramach systemu Azure Red Hat OpenShift zużywa pomocnicze wersje i poprawki z platformy kontenera OpenShift.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>Co to jest proces ponownego uruchomienia zaktualizowanego węzła?

Węzły są ponownie uruchamiane w ramach uaktualnienia.

## <a name="cluster-operations"></a>Operacje klastra

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>Czy mogę używać Prometheus do monitorowania aplikacji?

Prometheus jest wstępnie zainstalowana i skonfigurowana dla klastrów Red Hat OpenShift 4. x platformy Azure. Dowiedz się więcej o [monitorowaniu klastra](https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html).

W przypadku klastrów z systemem Red Hat OpenShift 3,11 można wdrożyć Prometheus w przestrzeni nazw oraz monitorować aplikacje w przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [Wdrażanie wystąpienia Prometheus w klastrze Red Hat OpenShift platformy Azure](howto-deploy-prometheus.md).

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Czy mogę używać Prometheus do monitorowania metryk związanych z kondycją i pojemnością klastra?

Na platformie Azure Red Hat OpenShift 4. x: tak.

Na platformie Azure Red Hat OpenShift 3,11: nie.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Czy dzienniki bazowych maszyn wirtualnych są przesyłane strumieniowo do systemu analizy dzienników klienta?

Dzienniki z bazowych maszyn wirtualnych są obsługiwane przez usługę zarządzaną i nie są widoczne dla klientów.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>Jak klient może uzyskać dostęp do metryk, takich jak procesor CPU/pamięć na poziomie węzła, aby podjąć działania w celu skalowania, debugowania problemów itp.? Nie mogę uruchomić polecenia kubectl góry w klastrze Red Hat OpenShift platformy Azure.

W przypadku klastrów Azure Red Hat OpenShift 4. x OpenShift Konsola sieci Web zawiera wszystkie metryki na poziomie węzła. Aby uzyskać więcej informacji, zobacz dokumentację Red Hat dotyczącą [wyświetlania informacji o klastrze](https://docs.openshift.com/aro/4/web_console/using-dashboard-to-get-cluster-information.html).

W przypadku klastrów z systemem Red Hat OpenShift 3,11 klienci mogą uzyskać dostęp do metryk procesora/pamięci na poziomie węzła przy użyciu polecenia `oc adm top nodes` lub `kubectl top nodes` roli klastra klienta-administratora. Klienci mogą również uzyskać dostęp do metryk procesora/pamięci `pods` za pomocą polecenia `oc adm top pods` lub `kubectl top pods` .

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>W przypadku skalowania w górę wdrożenia, jak domeny błędów platformy Azure są mapowane na umieszczenie pod, aby upewnić się, że wszystkie zasobniki usługi nie zostaną oddzielone przez awarię w jednej domenie błędów?

W przypadku korzystania z zestawów skalowania maszyn wirtualnych na platformie Azure domyślnie są używane pięć domen błędów. Każde wystąpienie maszyny wirtualnej w zestawie skalowania zostanie umieszczone w jednej z tych domen błędów. Dzięki temu aplikacje wdrożone w węzłach obliczeniowych w klastrze będą znajdować się w osobnych domenach błędów.

Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniej liczby domen błędów dla zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md).

### <a name="is-there-a-way-to-manage-pod-placement"></a>Czy istnieje sposób, aby zarządzać umieszczaniem pod?

Klienci mają możliwość uzyskiwania węzłów i wyświetlania etykiet jako administrator klienta. Pozwoli to na docelową maszynę wirtualną w zestawie skalowania.

Należy zachować ostrożność w przypadku używania określonych etykiet:

- Nazwa hosta nie może być używana. Nazwa hosta jest często obracana z uaktualnieniami i aktualizacjami i jest zagwarantowana do zmiany.
- Jeśli klient ma żądanie dotyczące określonych etykiet lub strategii wdrażania, można to osiągnąć, ale wymagałoby to wysiłków inżynieryjnych i nie jest to obecnie obsługiwane.

Aby uzyskać więcej informacji, zobacz [kontrolowanie umieszczania pod](https://docs.openshift.com/aro/4/nodes/scheduling/nodes-scheduler-about.html).

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Czy rejestr obrazów jest dostępny zewnętrznie, aby można było używać narzędzi takich jak Jenkins?

W przypadku klastrów 4. x należy uwidocznić bezpieczny rejestr i skonfigurować uwierzytelnianie. Aby uzyskać więcej informacji, zobacz następującą dokumentację Red Hat:

- [Uwidacznianie rejestru](https://docs.openshift.com/aro/4/registry/securing-exposing-registry.html)
- [Uzyskiwanie dostępu do rejestru](https://docs.openshift.com/aro/4/registry/accessing-the-registry.html)

W przypadku klastrów 3,11 jest dostępny rejestr platformy Docker. Rejestr platformy Docker jest dostępny z programu `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` . Możesz również użyć Azure Container Registry.

## <a name="networking"></a>Networking

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Czy mogę wdrożyć klaster w istniejącej sieci wirtualnej?

W klastrach 4. x można wdrożyć klaster w istniejącej sieci wirtualnej.

W klastrach 3,11 nie można wdrożyć klastra w istniejącej sieci wirtualnej. Klaster Red Hat OpenShift 3,11 można połączyć z istniejącą siecią wirtualną za pośrednictwem komunikacji równorzędnej.

### <a name="is-cross-namespace-networking-supported"></a>Czy obsługa sieci między przestrzenią nazw jest obsługiwana?

Administratorzy klienta i indywidualnego projektu mogą dostosowywać sieci obejmujące wiele przestrzeni nazw (w tym odrzucanie ich) na podstawie projektu za pomocą `NetworkPolicy` obiektów.

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Próbuję przejść do sieci wirtualnej w innej subskrypcji, ale nie udało się uzyskać błędu CIDR sieci wirtualnej.

W subskrypcji z siecią wirtualną upewnij się, że zarejestrowano `Microsoft.ContainerService` dostawcę przy użyciu następującego polecenia: `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Czy można określić zakresy adresów IP do wdrożenia w prywatnej sieci wirtualnej, unikając konfliktów z innymi firmowymi sieci wirtualnych po utworzeniu komunikacji równorzędnej?

W klastrach 4. x można określić własne zakresy adresów IP.

W przypadku klastrów 3,11 usługa Azure Red Hat OpenShift obsługuje wirtualne sieci równorzędne i umożliwia klientowi dostarczanie sieci wirtualnej do komunikacji równorzędnej oraz sieci wirtualnej CIDR, w której będzie działać sieć OpenShift.

Sieć wirtualna utworzona przez usługę Azure Red Hat OpenShift będzie chroniona i nie będzie akceptować zmian konfiguracji. Sieć wirtualna, która jest podłączona do sieci równorzędnej, jest kontrolowana przez klienta i znajduje się w jego subskrypcji.

### <a name="is-the-software-defined-network-module-configurable"></a>Czy moduł sieci zdefiniowanej przez oprogramowanie jest konfigurowalny?

Sieci zdefiniowanej przez oprogramowanie jest `openshift-ovs-networkpolicy` i nie można jej konfigurować.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Który moduł równoważenia obciążenia platformy Azure jest używany przez usługę Azure Red Hat OpenShift?  Czy jest to wersja standardowa czy podstawowa i czy jest konfigurowana?

W systemie Azure Red Hat OpenShift jest stosowana standardowa Azure Load Balancer i nie można jej konfigurować.

## <a name="permissions"></a>Uprawnienia

### <a name="can-an-admin-manage-users-and-quotas"></a>Czy administrator może zarządzać użytkownikami i przydziałami?

Tak. Administrator Red Hat OpenShift systemu Azure może zarządzać użytkownikami i przydziałami, a także uzyskiwać dostęp do wszystkich projektów utworzonych przez użytkownika.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Czy mogę ograniczyć klaster tylko do określonych użytkowników usługi Azure AD?

Tak. Można ograniczyć, którzy użytkownicy usługi Azure AD mogą logować się do klastra przez skonfigurowanie aplikacji usługi Azure AD. Aby uzyskać szczegółowe informacje, zobacz [How to: ograniczanie aplikacji do zestawu użytkowników](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

### <a name="can-i-restrict-users-from-creating-projects"></a>Czy mogę ograniczyć użytkownikom możliwość tworzenia projektów?

Tak. Zaloguj się do klastra jako administrator i wykonaj następujące polecenie:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją OpenShift dotyczącą wyłączania samoobsługowego udostępniania dla wersji klastra:

- [Wyłączanie samoobsługowego udostępniania w klastrach 4,3](https://docs.openshift.com/aro/4/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [Wyłączanie samoobsługowego udostępniania w klastrach 3,11](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Które prawa systemu UNIX (w IaaS) są dostępne dla węzłów wzorców/infrastruktury/aplikacji?

W przypadku klastrów 4. x dostęp do węzła jest dostępny za pomocą roli klastra-administratora. Aby uzyskać więcej informacji, zobacz [Omówienie RBAC](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html).

W przypadku klastrów 3,11 dostęp do węzła jest zabroniony.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Jakie są prawa OCP? Klaster-administrator? Projekt — administrator?

W przypadku klastrów 4. x rola Administrator klastra jest dostępna. Aby uzyskać więcej informacji, zobacz [Omówienie RBAC](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html).

Aby uzyskać więcej informacji na temat klastrów 3,11, zobacz [Omówienie administrowania klastrami](https://docs.openshift.com/aro/admin_guide/index.html) .

### <a name="which-identity-providers-are-available"></a>Którzy dostawcy tożsamości są dostępni?

W przypadku klastrów 4. x należy skonfigurować własnego dostawcę tożsamości. Aby uzyskać więcej informacji, zobacz dokumentację Red Hat dotyczącą [konfigurowania tożsamości prodivers](https://docs.openshift.com/aro/4/authentication/identity_providers/configuring-ldap-identity-provider.html).

W przypadku klastrów 3,11 można korzystać z integracji usługi Azure AD. 

## <a name="storage"></a>Magazyn

### <a name="is-data-on-my-cluster-encrypted"></a>Czy dane w moim klastrze są szyfrowane?

Domyślnie dane są szyfrowane w stanie spoczynku. Platforma Azure Storage automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje dane przed pobraniem. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku](../storage/common/storage-service-encryption.md).

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>Czy dane są przechowywane w etcd zaszyfrowane na platformie Azure Red Hat OpenShift?

W przypadku klastrów z systemem Red Hat OpenShift 4 dane nie są szyfrowane domyślnie, ale istnieje możliwość włączenia szyfrowania. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący [szyfrowania etcd](https://docs.openshift.com/container-platform/4.3/authentication/encrypting-etcd.html).

W przypadku klastrów 3,11 dane nie są szyfrowane na poziomie etcd. Opcja włączenia szyfrowania jest obecnie nieobsługiwana. OpenShift obsługuje tę funkcję, ale działania inżynieryjne są wymagane do przetworzenia ich na mapie drogowej. Dane są szyfrowane na poziomie dysku. Aby uzyskać więcej informacji, zapoznaj się z artykułem [szyfrowanie danych w warstwie magazynu](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Czy można wybrać dowolne trwałe rozwiązanie magazynu, takie jak OCS? 

W przypadku klastrów 4. x dysk platformy Azure (Premium_LRS) jest skonfigurowany jako domyślna Klasa magazynu. Dodatkowe dostawcy magazynu i szczegółowe informacje dotyczące konfiguracji (w tym plik platformy Azure) znajdują się w dokumentacji Red Hat w [magazynie trwałym](https://docs.openshift.com/aro/4/storage/understanding-persistent-storage.html).

W przypadku klastrów 3,11 domyślnie są dostępne dwie klasy magazynu: jeden dla dysku platformy Azure (Premium_LRS) i jeden dla plików platformy Azure.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>Czy te dane klienta są przechowywane poza regionem klastra?

Nie. Wszystkie dane utworzone w klastrze ARO są przechowywane w regionie klastra.