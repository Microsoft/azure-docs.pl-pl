---
title: Otwieranie usługi Service Mesh (wersja zapoznawcza)
description: Otwieranie usługi Service Mesh (SYSTEM) w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: bbc07a7ee3f996c778cfc1b9d1764f10a613c50b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782949"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Otwieranie dodatku AKS usługi Service Mesh (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

[Open Service Mesh (INDIA)](https://docs.openservicemesh.io/) to lekkie, rozszerzalne, natywne dla chmury siatki usług, które umożliwiają użytkownikom jednolite zarządzanie, zabezpieczanie i uzyskiwanie łatwych w pracy funkcji obserwacji dla wysoce dynamicznych środowisk mikrousług.

SYSTEMOWA uruchamia płaszczyznę sterowania opartą na aplikacji Envoy na platformie Kubernetes, można ją skonfigurować za pomocą interfejsów API [SMI](https://smi-spec.io/) i działa przez wstrzyknięcie serwera proxy envoy jako kontenera sidecar obok każdego wystąpienia aplikacji. Serwer proxy usługi Envoy zawiera i wykonuje reguły dotyczące zasad kontroli dostępu, implementuje konfigurację routingu i przechwytuje metryki. Płaszczyzna sterowania stale konfiguruje proxy, aby upewnić się, że zasady i reguły routingu są aktualne i że są w dobrej kondycji.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Możliwości i funkcje

SYSTEM ZARZĄDZANIA dostępem do sieci udostępnia następujący zestaw funkcji i możliwości, które zapewniają natywną siatkę usług w chmurze dla klastrów Azure Kubernetes Service (AKS):

- Zabezpieczanie komunikacji między usługami przez włączenie mTLS

- Łatwe dołączanie aplikacji do siatki przez włączenie automatycznego iniekcji bocznej serwera proxy aplikacji Envoy

- Łatwe i przejrzyste konfiguracje dotyczące zmiany ruchu we wdrożeniach

- Możliwość definiowania i wykonywania zasad precyzyjnej kontroli dostępu dla usług

- Wgląd i szczegółowe informacje o metrykach aplikacji na potrzeby debugowania i monitorowania usług

- Integracja z zewnętrznymi usługami/rozwiązaniami do zarządzania certyfikatami z podłączanym interfejsem

## <a name="scenarios"></a>Scenariusze

SYSTEM KONFIGURACJI może pomóc we wdrożeniach usługi AKS w następujących scenariuszach:

- Zapewnianie zaszyfrowanej komunikacji między punktami końcowymi usługi wdrożonych w klastrze

- Autoryzacja ruchu dla ruchu HTTP/HTTPS i TCP w siatce

- Konfiguracja ważonych kontroli ruchu między co najmniej dwiema usługami dla wdrożeń A/B lub canary

- Zbieranie i wyświetlanie wskaźników KPI z ruchu aplikacji

## <a name="osm-service-quotas-and-limits-preview"></a>Limity przydziału i limity usługi LIMITY (wersja zapoznawcza)

Ograniczenia limitów przydziałów i limitów usług w wersji zapoznawczej DLA USŁUGI MOŻNA znaleźć na stronie Limity [przydziału i limity regionalne](https://docs.microsoft.com/azure/aks/quotas-skus-regions)usługi AKS.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> Nie próbuj instalować funkcji DOSTĘP Z pliku binarnego przy użyciu programu `osm install` . Spowoduje to zainstalowanie rozwiązania ZAKS, które nie jest zintegrowane jako dodatek dla usług AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Rejestrowanie funkcji w `AKS-OpenServiceMesh` wersji zapoznawczej

Aby utworzyć klaster usługi AKS, który może używać dodatku open service mesh, należy włączyć `AKS-OpenServiceMesh` flagę funkcji w subskrypcji.

Zarejestruj `AKS-OpenServiceMesh` flagę funkcji za pomocą [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Wyświetlanie zarejestrowanego stanu może potrwać _kilka minut._ Sprawdź stan rejestracji za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy _zasobów Microsoft.ContainerService_ za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Instalowanie dodatku Open Service Mesh (ICH) Azure Kubernetes Service (AKS) dla nowego klastra usługi AKS

W nowym scenariuszu wdrażania klastra usługi AKS rozpoczniesz od zupełnie nowego wdrożenia klastra usługi AKS, które umożliwia dodanie DODATKU W operacji tworzenia klastra.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład umożliwia utworzenie grupy zasobów o nazwie _myOsmAksGroup_ w _lokalizacji eastus2_ (region):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Wdrażanie klastra usługi AKS z włączonym dodatku THE

Teraz wdrożysz nowy klaster usługi AKS z włączonym dodatku THE.

> [!NOTE]
> Należy pamiętać, że następujące polecenie wdrożenia usługi AKS korzysta z dysków efemeralnych systemu operacyjnego. Więcej informacji na temat efemeralnych dysków systemu operacyjnego dla [aks można znaleźć tutaj](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os)

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Uzyskiwanie poświadczeń dostępu do klastra usługi AKS

Uzyskaj poświadczenia dostępu dla nowego zarządzanego klastra Kubernetes.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Włączanie dodatku Open Service Mesh (ONE) Azure Kubernetes Service (AKS) dla istniejącego klastra usługi AKS

W przypadku istniejącego scenariusza klastra usługi AKS należy włączyć dodatek DODATKU DO ISTNIEJĄCEGO klastra usługi AKS, który został już wdrożony.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Włączanie dodatku DODATKU DO ISTNIEJĄCEGO KLASTRA USŁUGI AKS

Aby włączyć dodatek DODATKU DODATKU AKS, należy uruchomić `az aks enable-addons --addons` polecenie przekazujące parametr `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Powinny zostać wyświetlone dane wyjściowe podobne do danych wyjściowych pokazanych poniżej, aby potwierdzić, że dodatek AKSINSTAL ZOSTAŁ zainstalowany.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Weryfikowanie instalacji dodatku DODATKU AKS

Istnieje kilka poleceń, które należy uruchomić, aby sprawdzić, czy wszystkie składniki dodatku AKSŁĄCZYĆ są włączone i uruchomione:

Najpierw możemy odpytować profile dodatków klastra, aby sprawdzić włączony stan zainstalowanych dodatków. Następujące polecenie powinno zwrócić wartość "true".

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Następujące polecenia `kubectl` będą zgłaszać stan kontrolera domeny .

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Uzyskiwanie dostępu do dodatku AKS ZAKS

Obecnie można uzyskać dostęp do konfiguracji kontrolera DNS i skonfigurować go za pośrednictwem mapy konfiguracji. Aby wyświetlić ustawienia konfiguracji kontrolera DNS, należy odpytować mapę konfiguracji za pośrednictwem programu , `kubectl` aby wyświetlić jego ustawienia konfiguracji.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Dane wyjściowe mapy konfiguracji MODELU powinny wyglądać następująco:

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Zwróć **uwagę permissive_traffic_policy_mode** że wartość jest skonfigurowana na **wartość true.** Tryb zasad ruchu permisywnych w CELU jest trybem, w którym [wymuszanie zasad](https://smi-spec.io/) ruchu SMI jest pomijane. W tym trybie SERWERY PROXY automatycznie odnajdują usługi, które są częścią siatki usług, i programują reguły zasad ruchu na każdej sidecar serwera proxy envoy, aby móc komunikować się z tymi usługami.

> [!WARNING]
> Przed przystąpieniem sprawdź, czy tryb zasad ruchu sieciowego jest ustawiony na wartość true, jeśli nie, zmień go na **true** przy użyciu poniższego polecenia

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Wdrażanie nowej aplikacji, która ma być zarządzana przez dodatek Open Service Mesh (AZURE KUBERNETES SERVICE) (AKS)

### <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach szczegółowo opisanej w tym przewodniku założono, że utworzono klaster usługi AKS (z włączoną obsługą kontroli RBAC na platformie Kubernetes) i nawiążesz połączenie z klastrem (jeśli potrzebujesz pomocy dotyczącej dowolnego z tych elementów, zobacz przewodnik Szybki start usługi AKS i zainstalowano dodatek `1.19+` `kubectl` AKS ONE. [](./kubernetes-walkthrough.md)

Musisz mieć zainstalowane następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
- Rozszerzenie `aks-preview` w wersji 0.5.5 lub nowszej
- SYSTEM w wersji 0.8.0 lub nowszej
- apt-get install jq

### <a name="create-namespaces-for-the-application"></a>Tworzenie przestrzeni nazw dla aplikacji

W tym przewodniku będziemy używać aplikacji sklepu z książkami THE, która ma następujące usługi Kubernetes:

- bookbuyer
- bookthief
- Księgarni
- bookwarehouse

Utwórz przestrzenie nazw dla każdego z tych składników aplikacji.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Dołączanie przestrzeni nazw, które mają być zarządzane przez usługę DOSTĘPNĄ

Dodanie przestrzeni nazw do siatki MESH umożliwi kontrolerowi THE automatyczne wstrzyknięcie kontenerów serwera proxy sidecar envoy do aplikacji. Uruchom następujące polecenie, aby do dołączać przestrzenie nazw aplikacji sklepu z książkami THE.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Wdrażanie aplikacji sklepu z książkami w klastrze usługi AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Wszystkie dane wyjściowe wdrożenia zostały podsumowane poniżej.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>Punkt kontrolny: Co zainstalowano?

Przykładowa aplikacja księgarni to aplikacja wielowarstwowa, która składa się z czterech usług: księgarni, księgarni, księgarni i księgarni. Zarówno usługa bookbuyer, jak i bookthief komunikują się z usługą księgarni w celu pobierania książek z usługi księgarni. Usługa księgarni pobiera książki z usługi księgarni w celu dostarczania książkibujerowi i księgarni. Jest to prosta aplikacja wielowarstwowa, która dobrze pokazuje, jak można użyć siatki usług do ochrony i autoryzowania komunikacji między usługami aplikacji. Kontynuując ten przewodnik, będziemy włączanie i wyłączanie zasad interfejsu usługi Service Mesh (SMI), aby zezwalać usługom na komunikację za pośrednictwem systemu ETHERNET i zezwalać na nie. Poniżej znajduje się diagram architektury instalacji aplikacji sklepu z książkami.

![Architektura aplikacji BOOKBUYER](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Weryfikowanie aplikacji księgarni uruchomionej w klastrze usługi AKS

W tej chwili wdrożyliśmy aplikację mulit-container magazynu książek, ale jest ona dostępna tylko z poziomu klastra usługi AKS. W kolejnych samouczkach znajdziesz pomoc w ujawnianiu aplikacji poza klastrem za pośrednictwem kontrolera ruchu wychodzącego. Na razie będziemy korzystać z przekazywania portów w celu uzyskania dostępu do aplikacji bookbuyer w klastrze usługi AKS, aby sprawdzić, czy kupuje książki w usłudze księgarni.

Aby sprawdzić, czy aplikacja działa wewnątrz klastra, użyjemy portu do wyświetlenia interfejsu użytkownika składników bookbuyer i bookthief.

Najpierw pobierzmy nazwę zasobnika bookbuyer

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Zasobnik bookbuyer będzie mieć dołączenie unikatowej nazwy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Gdy będziemy mieć nazwę zasobnika, możemy teraz użyć polecenia port-forward, aby skonfigurować tunel z systemu lokalnego do aplikacji w klastrze usługi AKS. Uruchom następujące polecenie, aby skonfigurować port do przodu dla lokalnego portu systemowego 8080. Ponownie użyj określonej nazwy zasobnika bookbuyer.

> [!NOTE]
> W przypadku wszystkich poleceń przekazywania portów najlepiej jest użyć dodatkowego terminalu, aby można było kontynuować pracę w tym przewodniku i nie rozłączać tunelu. Najlepiej jest również ustanowić tunel do przodu portów poza Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Powinny zostać wyświetlony dane wyjściowe podobne do tych.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Gdy sesja przekierowania portów jest na miejscu, przejdź do następującego adresu URL w przeglądarce `http://localhost:8080` . W przeglądarce powinien być teraz wyświetlony interfejs użytkownika aplikacji bookbuyer podobny do poniższego.

![Obraz interfejsu użytkownika aplikacji BOOKbuyer](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Zauważysz również, że łączna liczba zakupionych książek nadal zwiększa się do usługi księgarni w wersji 1. Usługa księgarni w wersji 2 nie została jeszcze wdrożona. Wdrożymy usługę księgarni w wersji 2, gdy zademonstr będziemy przedstawiać zasady podziału ruchu SMI.

Możesz również sprawdzić to samo dla usługi bookthief.

```azurecli-interactive
kubectl get pod -n bookthief
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Zasobnik bookthief będzie mieć dołączoną unikatową nazwę.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Przekieruj do zasobnika bookthief.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

W przeglądarce przejdź do następującego adresu `http://localhost:8080` URL. Powinno być widać, że księgarnia obecnie kradzieży książek z usługi księgarni! W dalszej części wdrożymy zasady ruchu, aby zatrzymać księgę.

![Obraz interfejsu użytkownika aplikacji BOOKTHIEF](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Wyłącz tryb ruchu permisywnej JEDNOSTKI SSM dla siatki

Jak wspomniano wcześniej podczas wyświetlania konfiguracji klastra DOSTĘP, konfiguracja JEDNOSTKI ZABEZPIECZEŃ domyślnie umożliwia włączenie zasad trybu ruchu. W tym trybie wymuszanie zasad ruchu jest pomijane, a system SIDE automatycznie odnajduje usługi, które są częścią siatki usług, i programuje reguły zasad ruchu w każdej sidecar serwera proxy usługi Envoy, aby móc komunikować się z tymi usługami.

Teraz wyłączymy zasady dotyczące trybu ruchu, a w klastrze będą potrzebne jawne zasady [SMI](https://smi-spec.io/) wdrożone w klastrze, aby umożliwić komunikację w siatce z każdej usługi. Aby wyłączyć tryb ruchu, uruchom następujące polecenie, aby zaktualizować właściwość configmap, zmieniając wartość z `true` na `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Zasobnik bookthief będzie mieć dołączoną unikatową nazwę.

```Output
configmap/osm-config patched
```

Aby sprawdzić, czy tryb ruchu sieciowego jest wyłączony, przekieruj z powrotem do zasobnika bookbuyer lub bookthief, aby wyświetlić interfejs użytkownika w przeglądarce i sprawdzić, czy zakupione książki lub skradzione książki już się nie zwiększa. Upewnij się, że odświeżysz przeglądarkę. Jeśli przyrost został zatrzymany, zasady zostały zastosowane poprawnie. Udało Ci się zatrzymać księgową przed kradzieżą książek, ale ani księgarnia nie może kupić w księgarni, ani księgarni nie może pobierać książek z księgarni. Następnie zaim implementujemy [zasady SMI,](https://smi-spec.io/) aby zezwolić tylko na usługi w siatce, do których chcesz się z tym komunikować.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Stosowanie zasad dostępu do ruchu interfejsu SMI (Service Mesh Interface)

Teraz, gdy wyłączyliśmy całą komunikację w siatce, pozwólmy naszej usłudze bookbuyer na komunikowanie się z naszą usługą księgarni w celu zakupu książek i umożliwienie naszej usłudze księgarni komunikowania się z naszą usługą księgarni w celu pobierania książek do sprzedaży.

Wd wdrażaj [następujące zasady SMI.](https://smi-spec.io/)

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Powinny pojawić się dane wyjściowe podobne do poniższych.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Teraz możesz skonfigurować sesję przekazywania portów dla zasobników księgarni lub księgarni i zobaczyć, że metryki dotyczące zakupionych książek i sprzedanych książek z powrotem się zwiększają. Możesz również zrobić to samo dla zasobnika bookthief, aby sprawdzić, czy nadal nie jest już w stanie wykraść książek.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Stosowanie zasad podziału ruchu interfejsu SMI (Service Mesh Interface)

Na ostatnim pokazie utworzymy zasady podziału ruchu [SMI,](https://smi-spec.io/) aby skonfigurować wagę komunikacji między jedną usługą a wieloma usługami jako zaplecza. Funkcja podziału ruchu umożliwia stopniowe przenoszenie połączeń do jednej usługi między usługami przez ważony ruch w skali od 0 do 100.

Na poniższej ilustracji przedstawiono diagram zasad podziału ruchu [SMI](https://smi-spec.io/) do wdrożenia. Wdrożymy dodatkową księgarnię w wersji 2, a następnie podzielimy ruch przychodzący z księgarni, ważon 25% ruchu do usługi księgarni w wersji 1 i 75% do usługi księgarni w wersji 2.

![Diagram podziału ruchu dla bookbuyer w systemach BOOK](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Wdzysuj usługę księgarni w wersji 2.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

Powinny zostać wyświetlone następujące dane wyjściowe.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Teraz wd wdrażają zasady podziału ruchu, aby podzielić ruch bookbuyer między dwiema usługami księgarni w wersji 1 i 2.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Powinny zostać wyświetlone następujące dane wyjściowe.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Skonfiguruj tunel do przekazywania portów do zasobnika bookbuyer. Powinny być teraz dostępne książki zakupione w usłudze księgarni w wersji 2. Jeśli nadal będziesz obserwować przyrost zakupów, zauważysz szybszy przyrost zakupów w usłudze sklepu z książkami w wersji 2.

![Interfejs użytkownika książek ze książkami dla książek ZESI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Zarządzanie istniejącymi wdrożonmi aplikacjami, które mają być zarządzane przez dodatek Open Service Mesh (AZURE KUBERNETES SERVICE) (AKS)

### <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach szczegółowo opisanej w tym przewodniku założono, że wcześniej włączono dodatek AKS DLA klastra usługi AKS. Jeśli tak nie jest, przed podjęciem kolejnej czynności zapoznaj się z sekcją Włączanie dodatku Azure Kubernetes Service [(AKS) usługi Open Service Mesh (AKS)](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) dla istniejącego klastra usługi AKS. Ponadto klaster usługi AKS musi mieć wersję Kubernetes i nowszą, mieć włączoną funkcję RBAC usługi Kubernetes i nawiążeć połączenie z klastrem (jeśli potrzebujesz pomocy dotyczącej dowolnego z tych elementów, zobacz przewodnik Szybki start usługi AKS i zainstalowano dodatek `1.19+` `kubectl` AKS ONE. [](./kubernetes-walkthrough.md)

Musisz mieć zainstalowane następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
- Rozszerzenie `aks-preview` w wersji 0.5.5 lub nowszej
- SYSTEM w wersji 0.8.0 lub nowszej
- apt-get install jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Weryfikowanie zasad trybu ruchu permisywnej usługi Open Service Mesh (ICH)

Tryb zasad ruchu permisywnych JEDNOSTKI SMI jest trybem, w którym wymuszanie zasad ruchu [SMI](https://smi-spec.io/) jest pomijane. W tym trybie SYSTEM AUTOMATYCZNIE odnajduje usługi, które są częścią siatki usług, i programuje reguły zasad ruchu w każdej sidecar serwera proxy usługi Envoy, aby móc komunikować się z tymi usługami.

Aby sprawdzić bieżący tryb ruchu sieciowego z dostępem do klastra, uruchom następujące polecenie:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Dane wyjściowe mapy konfiguracji MODELU powinny wyglądać następująco:

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Jeśli konfiguracja **permissive_traffic_policy_mode** na wartość **true,** możesz bezpiecznie dołączać przestrzenie nazw bez żadnych zakłóceń w komunikacji między usługami. Jeśli **dla** permissive_traffic_policy_mode skonfigurowano wartość **false,** należy upewnić się, że wdrożono poprawne manifesty zasad dostępu do ruchu [SMI,](https://smi-spec.io/) a także upewnić się, że masz konto usługi reprezentujące każdą usługę wdrożoną w przestrzeni nazw. Postępuj zgodnie ze wskazówkami dla dołączania istniejących wdrożonych aplikacji przy użyciu zasad ruchu permisyjnie skonfigurowanego jako False [(Fałsz) w usłudze Open Service Mesh (OPEN Service Mesh)](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Dołączanie istniejących wdrożonych aplikacji przy użyciu zasad ruchu permisywnych MESH (Open Service Mesh) skonfigurowanych jako True

Pierwszą rzeczą, jaką należy wykonać, jest dodanie wdrożonych przestrzeni nazw aplikacji do rozwiązania DO ZARZĄDZANIA.

```azurecli-interactive
osm namespace add bookstore
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Następnie przyjrzymy się bieżącego wdrożeniu zasobnika w przestrzeni nazw . Uruchom następujące polecenie, aby wyświetlić zasobniki w wyznaczonej przestrzeni nazw.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Powinny zostać wyświetlony następujące podobne dane wyjściowe:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Zwróć uwagę **na kolumnę READY** z **wartością 1/1,** co oznacza, że zasobnik aplikacji ma tylko jeden kontener. Następnie konieczne będzie ponowne uruchomienie wdrożeń aplikacji, tak aby system KLUCZY można było wstrzyknąć kontener serwera proxy aplikacji Envoy do zasobnika aplikacji. Pobierzmy listę wdrożeń w przestrzeni nazw .

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Teraz ponownie uruchomimy wdrożenie, aby wstrzyknąć kontener serwera proxy sidecar aplikacji Envoy do zasobnika aplikacji. Uruchom następujące polecenie.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
deployment.apps/bookbuyer restarted
```

Jeśli ponownie przyjrzymy się zasobnikom w przestrzeni nazw :

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Teraz zauważysz, że kolumna **READY (GOTOWE)** zawiera teraz **2/2** kontenery gotowe do użycia w zasobniku. Drugi kontener to serwer proxy aplikacji Envoy sidecar.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Możemy dokładniej zbadać zasobnik, aby wyświetlić serwer proxy envoy, uruchamiając polecenie describe w celu wyświetlenia konfiguracji.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Sprawdź, czy aplikacja nadal działa po wstrzyknięciu serwera proxy aplikacji Envoy.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Dołączanie istniejących wdrożonych aplikacji przy użyciu zasad ruchu permisywnych MESH (Open Service Mesh) skonfigurowanych jako Fałsz

W przypadku ustawienia konfiguracji JEDNOSTKI SMI dla zasad ruchu permisywnego na wartość , system SYSTEM będzie wymagał jawnego wdrożenia zasad dostępu do ruchu SMI dla komunikacji między usługami w `false` klastrze. [](https://smi-spec.io/) Obecnie w ramach autoryzowania komunikacji między usługami używane są również konta usługi Kubernetes. Aby upewnić się, że istniejące wdrożone aplikacje będą komunikować się, gdy będą zarządzane przez siatkę MESH, musimy sprawdzić istnienie konta usługi do wykorzystania, zaktualizować wdrożenie aplikacji przy użyciu informacji o koncie usługi i zastosować zasady dostępu do ruchu [SMI.](https://smi-spec.io/)

#### <a name="verify-kubernetes-service-accounts"></a>Weryfikowanie kont usługi Kubernetes

Sprawdź, czy masz konto usługi kubernetes w przestrzeni nazw, w których wdrożono aplikację.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

Poniżej znajduje się konto usługi o nazwie `bookbuyer` w przestrzeni nazw bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Jeśli nie masz konta usługi wymienionego na liście innej niż konto domyślne, musisz utworzyć je dla swojej aplikacji. Użyj następującego polecenia jako przykładu, aby utworzyć konto usługi w przestrzeni nazw wdrożonej aplikacji.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Wyświetlanie bieżącej specyfikacji wdrożenia aplikacji

Jeśli trzeba było utworzyć konto usługi z wcześniejszej sekcji, istnieje prawdopodobieństwo, że wdrożenie aplikacji nie jest skonfigurowane z określonym w `serviceAccountName` specyfikacji wdrożenia. Specyfikację wdrożenia aplikacji możemy wyświetlić za pomocą następujących poleceń:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Lista wdrożeń zostanie wyświetlona w danych wyjściowych.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Teraz opiszemy wdrożenie jako sprawdzenie, czy w sekcji Szablon zasobnika znajduje się konto usługi.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

W tym konkretnym wdrożeniu widać, że istnieje konto usługi skojarzone z wdrożeniem wymienione w sekcji Szablon zasobnika. To wdrożenie korzysta z konta usługi bookbuyer. Jeśli właściwość Konto  usługi: nie jest widzisz, wdrożenie nie jest skonfigurowane do używania konta usługi.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Istnieje kilka technik aktualizowania wdrożenia w celu dodania konta usługi Kubernetes. Zapoznaj się z dokumentacją rozwiązania Kubernetes dotyczącą [wbudowanego](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) aktualizowania wdrożenia lub [konfigurowania kont usług dla zasobników.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) Po zaktualizowaniu specyfikacji wdrożenia przy użyciu konta usługi ponownie wdróż wdrożenie (kubectl apply -f your-deployment.yaml) w klastrze.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Wdrażanie niezbędnych zasad interfejsu usługi Service Mesh (SMI)

Ostatnim krokiem umożliwiającym przepływ autoryzowanego ruchu w siatce jest wdrożenie niezbędnych zasad dostępu do ruchu [SMI](https://smi-spec.io/) dla aplikacji. Ilość konfiguracji, którą można osiągnąć za pomocą zasad dostępu ruchu [SMI,](https://smi-spec.io/) wykracza poza zakres tego przewodnika, ale szczegółowo opiszemy niektóre typowe składniki specyfikacji i pokażemy, jak skonfigurować proste zasady TrafficTarget i HTTPRouteGroup, aby umożliwić komunikację między usługami dla aplikacji.

Specyfikacja [SMI](https://smi-spec.io/) [**Traffic Access Control**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) umożliwia użytkownikom definiowanie zasad kontroli dostępu dla ich aplikacji. Skupimy się na **zasobach interfejsu API TrafficTarget** i **HTTPRoutGroup.**

Zasób TrafficTarget składa się z trzech głównych ustawień konfiguracji docelowych, reguł i źródeł. Poniżej przedstawiono przykładowy element TrafficTarget.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

W powyższej specyfikacji TrafficTarget oznacza konto usługi skonfigurowane dla `destination` docelowej usługi źródłowej. Pamiętaj, że konto usługi dodane wcześniej do wdrożenia będzie używane do autoryzowania dostępu do wdrożenia, do którym jest dołączone. Sekcja `rules` , w tym konkretnym przykładzie, definiuje typ ruchu HTTP, który jest dozwolony przez połączenie. Można skonfigurować szczegółowe wzorce wyrażenia regularnego dla nagłówków HTTP, aby określić, jaki ruch jest dozwolony za pośrednictwem protokołu HTTP. Sekcja `sources` jest komunikacją pochodzącą z usługi. Ta specyfikacja odczytuje, że księgarnia musi komunikować się z księgarnią.

Zasób HTTPRouteGroup składa się z jednego lub tablicy dopasowania informacji nagłówka HTTP i jest wymagany dla specyfikacji TrafficTarget. W poniższym przykładzie widać, że grupa HTTPRouteGroup autoryzuje trzy akcje HTTP, dwie akcje GET i jedną grupę POST.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Jeśli nie znasz typu ruchu HTTP, który aplikacja frontony wykonuje do innych warstw aplikacji, ponieważ specyfikacja TrafficTarget wymaga reguły, możesz utworzyć odpowiednik reguły zezwalania na wszystkie przy użyciu poniższej specyfikacji dla grupy HTTPRouteGroup.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

Po skonfigurowaniu specyfikacji TrafficTarget i HTTPRouteGroup można je umieścić w jednym pliku YAML i wdrożyć. Poniżej przedstawiono przykładową konfigurację sklepu z książkami.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Odwiedź [witrynę SMI,](https://smi-spec.io/) aby uzyskać bardziej szczegółowe informacje na temat specyfikacji.

### <a name="manage-the-applications-namespace-with-osm"></a>Zarządzanie przestrzenią nazw aplikacji za pomocą usługi DOSTĘP DO SIECI

Następnie skonfigurujemy usługę DOSTĘPNĄ do zarządzania przestrzenią nazw i ponownie uruchomimy wdrożenia, aby serwer proxy usługi Envoy sidecar został wstrzyknięty do aplikacji.

Uruchom następujące polecenie, aby skonfigurować przestrzeń `azure-vote` nazw, która ma być zarządzana przez moją usługę DOSTĘPNĄ.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Następnie uruchom ponownie wdrożenia `azure-vote-front` i za pomocą następujących `azure-vote-back` poleceń.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Jeśli wyświetlamy zasobniki dla przestrzeni nazw, zobaczymy etap READY zarówno elementu , jak i jako 2/2, co oznacza, że wraz z aplikacją został wstrzyknięty serwer `azure-vote`  `azure-vote-front` proxy `azure-vote-back` sidecar envoy.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Samouczek: wdrażanie aplikacji zarządzanej przez usługę Open Service Mesh (GIN) przy użyciu danych przychodzących NGINX

Open Service Mesh (IT) to lekki, rozszerzalny, natywny dla chmury siatka usług, która umożliwia użytkownikom jednolite zarządzanie, zabezpieczanie i uzyskiwanie możliwości obserwacji w wysoce dynamicznych środowiskach mikrousług.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> - Wyświetlanie bieżącej konfiguracji klastra FILTRU
> - Utwórz przestrzenie nazw dla rozwiązania ONES do zarządzania wdrożonych aplikacji w przestrzeniach nazw
> - Dołączanie przestrzeni nazw, które mają być zarządzane przez usługę DOSTĘPNĄ
> - Wdrażanie aplikacji przykładowej
> - Weryfikowanie aplikacji uruchomionej w klastrze usługi AKS
> - Tworzenie kontrolera ruchu przychodzących NGINX używanego do aplikacji
> - Uwidocznij usługę za pośrednictwem Azure Application Gateway przychodzących do Internetu

### <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach szczegółowo instrukcje opisane w tym artykule zakładają, że utworzono klaster usługi AKS (kubernetes i nowsza z włączoną obsługą kontroli RBAC na platformie Kubernetes) i nawiążesz połączenie z klastrem (jeśli potrzebujesz pomocy dotyczącej dowolnego z tych elementów, zobacz przewodnik Szybki start usługi AKS i zainstalowano dodatek `1.19+` `kubectl` AKS ONE. [](./kubernetes-walkthrough.md)

Musisz mieć zainstalowane następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
- Rozszerzenie `aks-preview` w wersji 0.5.5 lub nowszej
- WERSJA WERSJA 0.8.0 lub nowsza
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Wyświetlanie i weryfikowanie bieżącej konfiguracji klastra FILTRU

Po włączeniu dodatku DODATKU DO USŁUGI AKS w klastrze usługi AKS można wyświetlić bieżące parametry konfiguracji w pliku kubernetes ConfigMap. Uruchom następujące polecenie, aby wyświetlić właściwości ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Dane wyjściowe pokazują bieżącą konfigurację KLASTRA.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Zwróć **uwagę permissive_traffic_policy_mode** że wartość jest skonfigurowana na **wartość true.** Tryb zasad ruchu permisywnych w CELU jest trybem, w którym [wymuszanie zasad](https://smi-spec.io/) ruchu SMI jest pomijane. W tym trybie SERWERY PROXY automatycznie odnajdują usługi, które są częścią siatki usług, i programują reguły zasad ruchu na każdej sidecar serwera proxy envoy, aby móc komunikować się z tymi usługami.

### <a name="create-namespaces-for-the-application"></a>Tworzenie przestrzeni nazw dla aplikacji

W tym samouczku będziemy używać aplikacji ZE sklepu z książkami, która ma następujące składniki aplikacji:

- bookbuyer
- bookthief
- Księgarni
- bookwarehouse

Utwórz przestrzenie nazw dla każdego z tych składników aplikacji.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Dołączanie przestrzeni nazw, które mają być zarządzane przez usługę DOSTĘPNĄ

Dodanie przestrzeni nazw do siatki MESH umożliwi kontrolerowi SUM automatyczne wstrzyknięcie kontenerów serwera proxy sidecar envoy do aplikacji. Uruchom następujące polecenie, aby do dołączać przestrzenie nazw aplikacji sklepu z książkami THE.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Wdrażanie aplikacji sklepu z książkami w klastrze usługi AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Wszystkie dane wyjściowe wdrożenia zostały podsumowane poniżej.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Aktualizowanie usługi Bookbuyer

Zaktualizuj usługę bookbuyer do prawidłowej konfiguracji portu przychodzącego przy użyciu następującego manifestu usługi.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Weryfikowanie aplikacji sklepu z książkami uruchomionej w klastrze usługi AKS

W tej chwili wdrożyliśmy aplikację kontenera magazynu książek, ale jest ona dostępna tylko z poziomu klastra usługi AKS. Później dodamy kontroler ruchu Azure Application Gateway, aby uwidocznić aplikację poza klastrem usługi AKS. Aby sprawdzić, czy aplikacja działa wewnątrz klastra, użyjemy portu do wyświetlenia interfejsu użytkownika składnika bookbuyer.

Najpierw pobierzmy nazwę zasobnika bookbuyer

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Zasobnik bookbuyer będzie miał dołączenie unikatowej nazwy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Po nazwie zasobnika możemy teraz użyć polecenia port-forward, aby skonfigurować tunel z systemu lokalnego do aplikacji w klastrze usługi AKS. Uruchom następujące polecenie, aby skonfigurować przekazywanie portów dla lokalnego portu systemowego 8080. Ponownie użyj określonej nazwy zasobnika bookbuyer.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Powinny zostać wyświetlony dane wyjściowe podobne do tych.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Gdy sesja przekierowania portów jest na miejscu, przejdź do następującego adresu URL w przeglądarce `http://localhost:8080` . W przeglądarce powinien być teraz wyświetlony interfejs użytkownika aplikacji bookbuyer podobny do poniższego.

![Obraz aplikacji BOOKbuyer dla interfejsu użytkownika NGINX](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Tworzenie kontrolera ruchu przychodzących NGINX w Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

Użyjemy kontrolera ruchu wychodzącego, aby uwidocznić aplikację zarządzaną przez technologię ONA w Internecie. Aby utworzyć kontroler ruchu przychodzących, użyj programu Helm, aby zainstalować pakiet nginx-ingress. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchomionych replik kontrolera ruchu wychodzącego, upewnij się, że w klastrze usługi AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Nie należy go uruchamiać w węzłach z systemem Windows Server. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o _nazwie ingress-basic_. Określ przestrzeń nazw dla własnego środowiska zgodnie z potrzebami.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Po utworzeniu usługi równoważenia obciążenia Kubernetes dla kontrolera ruchu przychodzących NGINX jest przypisywany dynamiczny publiczny adres IP, jak pokazano w następujących przykładowych danych wyjściowych:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Nie utworzono jeszcze żadnych reguł ruchu przychodzących, więc domyślna strona 404 kontrolera ruchu przychodzących NGINX jest wyświetlana w przypadku przeglądania do wewnętrznego adresu IP. Reguły ruchu wychodzącego są konfigurowane w poniższych krokach.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Uwidocznij usługę bookbuyer w Internecie

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>Wyświetlanie dzienników serwera NGINX

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

Dane wyjściowe pokazują stan kontrolera ruchu przychodzących NGINX po pomyślnym zastosowaniu reguły ruchu przychodzących:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Wyświetlanie usług NGINX i usługi bookbuyer na zewnątrz

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Ponieważ nazwa hosta w manifeście przychodzącym jest nazwą psuedo używaną do testowania, nazwa DNS nie będzie dostępna w Internecie. Alternatywnie możemy użyć programu curl i przełączyć nagłówek nazwy hosta do publicznego adresu IP serwera NGINX i otrzymać kod 200, który pomyślnie łączy nas z usługą bookbuyer.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Samouczek: wdrażanie aplikacji zarządzanej przez usługę Open Service Mesh (MESH) Azure Application Gateway dodatku AKS dla danych przychodzących

Open Service Mesh (INDIA) to lekkie, rozszerzalne, natywne dla chmury siatki usług, które umożliwiają użytkownikom jednolite zarządzanie, zabezpieczanie i uzyskiwanie łatwych w pracy funkcji obserwacji dla wysoce dynamicznych środowisk mikrousług.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> - Wyświetlanie bieżącej konfiguracji klastra REKORDU KLASTRA
> - Tworzenie przestrzeni nazw na platformie DNS w celu zarządzania wdrożonych aplikacji w przestrzeniach nazw
> - Dołączanie przestrzeni nazw, które mają być zarządzane przez usługę DOSTĘP
> - Wdrażanie aplikacji przykładowej
> - Weryfikowanie aplikacji uruchomionej w klastrze usługi AKS
> - Tworzenie Azure Application Gateway, który będzie używany jako kontroler ruchu wychodzącego dla aplikacji
> - Uwidocznij usługę za pośrednictwem Azure Application Gateway przychodzących do Internetu

### <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach szczegółowo opisanej w tym artykule założono, że utworzono klaster usługi AKS (z włączoną obsługą kontroli RBAC na platformie Kubernetes), nawiążesz połączenie z klastrem (jeśli potrzebujesz pomocy dotyczącej dowolnego z tych elementów, zobacz przewodnik Szybki start usługi `1.19+` `kubectl` [AKS,](./kubernetes-walkthrough.md)zainstalowano dodatek AKS QUICK i zostanie utworzony nowy Azure Application Gateway dla danych przychodzących.

Musisz mieć zainstalowane następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
- Rozszerzenie `aks-preview` w wersji 0.5.5 lub nowszej
- Klaster AKS w wersji 1.19 lub Azure CNI sieci (dołączony do sieci wirtualnej platformy Azure)
- SYSTEM w wersji 0.8.0 lub nowszej
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Wyświetlanie i weryfikowanie bieżącej konfiguracji klastra TREND

Po włączeniu dodatku DODATKU DLA usługi AKS w klastrze usługi AKS można wyświetlić bieżące parametry konfiguracji w pliku ConfigMap programu Kubernetes w pliku do konfigurowania aplikacji. Uruchom następujące polecenie, aby wyświetlić właściwości ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Dane wyjściowe pokazują bieżącą konfigurację REKORDU dla klastra.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Zwróć **uwagę permissive_traffic_policy_mode** że dla ustawienia skonfigurowano wartość **true.** Tryb zasad ruchu nachyliczania ruchu w TRYBIE, w którym [wymuszanie zasad](https://smi-spec.io/) ruchu SMI jest pomijane. W tym trybie SYSTEM AUTOMATYCZNIE odnajduje usługi, które są częścią siatki usług, i programuje reguły zasad ruchu w każdej sidecar serwera proxy usługi Envoy, aby móc komunikować się z tymi usługami.

### <a name="create-namespaces-for-the-application"></a>Tworzenie przestrzeni nazw dla aplikacji

W tym samouczku będziemy używać aplikacji sklepu z książkami THE, która ma następujące składniki aplikacji:

- bookbuyer
- bookthief
- Księgarni
- bookwarehouse

Utwórz przestrzenie nazw dla każdego z tych składników aplikacji.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Dołączanie przestrzeni nazw, które mają być zarządzane przez usługę DOSTĘP

Jeśli dodasz przestrzenie nazw do siatki MESH, umożliwi to kontrolerowi WOLUMINU automatyczne wstrzyknięcie kontenerów serwera proxy aplikacji Envoy sidecar do aplikacji. Uruchom następujące polecenie, aby do dołączać przestrzenie nazw aplikacji sklepu z książkami w układzie CZAS.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Wdrażanie aplikacji księgarni w klastrze usługi AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Wszystkie dane wyjściowe wdrożenia zostały podsumowane poniżej.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Aktualizowanie usługi Bookbuyer

Zaktualizuj usługę bookbuyer do prawidłowej konfiguracji portu przychodzącego przy użyciu następującego manifestu usługi.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Weryfikowanie aplikacji księgarni uruchomionej w klastrze usługi AKS

W tej chwili wdrożyliśmy aplikację z wieloma kontenerami magazynu książek, ale jest ona dostępna tylko z poziomu klastra usługi AKS. Później dodamy kontroler ruchu Azure Application Gateway, aby uwidocznić aplikację poza klastrem usługi AKS. Aby sprawdzić, czy aplikacja jest uruchomiona wewnątrz klastra, użyjemy portu do wyświetlenia interfejsu użytkownika składnika bookbuyer.

Najpierw pobierzmy nazwę zasobnika bookbuyer

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Zasobnik bookbuyer będzie mieć dołączenie unikatowej nazwy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Gdy będziemy mieć nazwę zasobnika, możemy teraz użyć polecenia port-forward, aby skonfigurować tunel z systemu lokalnego do aplikacji w klastrze usługi AKS. Uruchom następujące polecenie, aby skonfigurować port do przodu dla lokalnego portu systemowego 8080. Ponownie użyj określonej nazwy zasobnika bookbuyer.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Powinny zostać wyświetlony dane wyjściowe podobne do tych.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Gdy sesja przekierowania portów jest na miejscu, przejdź do następującego adresu URL w przeglądarce `http://localhost:8080` . W przeglądarce powinien być teraz wyświetlony interfejs użytkownika aplikacji bookbuyer podobny do poniższego.

![Obraz aplikacji BOOKbuyer dla App Gateway UI](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Tworzenie aplikacji Azure Application Gateway uwidocznić aplikację bookbuyer poza klastrem usługi AKS

> [!NOTE]
> W poniższych kierunkach utworzymy nowe wystąpienie klasy Azure Application Gateway do użycia dla ruchu wychodzącego. Jeśli masz istniejącą Azure Application Gateway chcesz użyć, przejdź do sekcji dotyczącej włączania dodatku kontrolera ruchu Application Gateway ruchu Application Gateway ruchu wychodzącego.

#### <a name="deploy-a-new-application-gateway"></a>Wdrażanie nowego Application Gateway

> [!NOTE]
> Odwołujemy się do istniejącej dokumentacji dotyczącej włączania Application Gateway kontrolera ruchu przychodzących dla istniejącego klastra usługi AKS. W tym celu w wywłaszszono pewne modyfikacje, aby dopasować je do materiałów ZOI. Bardziej szczegółową dokumentację na ten temat można znaleźć [tutaj.](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)

Teraz wdrożysz nową usługę Application Gateway, aby zasymulować istniejący klaster Application Gateway, którego chcesz użyć do równoważenia obciążenia ruchu do klastra usługi AKS _myCluster._ Nazwa bramy Application Gateway _myApplicationGateway,_ ale najpierw musisz utworzyć zasób publicznego adresu IP. o nazwie _myPublicIp_ i nowej sieci wirtualnej o nazwie _myVnet_ z przestrzenią adresową 11.0.0.0/8 oraz podsiecią z przestrzenią adresową 11.1.0.0/16 o nazwie _mySubnet_ i wdrożyć adres Application Gateway w podsieci _mySubnet_ przy użyciu adresu _myPublicIp._

W przypadku korzystania z klastra usługi AKS Application Gateway w oddzielnych sieciach wirtualnych przestrzenie adresowe dwóch sieci wirtualnych nie mogą się nakładać. Domyślna przestrzeń adresowa wdrażana przez klaster usługi AKS to 10.0.0.0/8, dlatego dla prefiksu adresu sieci wirtualnej usługi Application Gateway ustawiliśmy wartość 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Application Gateway kontrolera ruchu wychodzącego (AGIC)  obsługuje tylko jednostki SKU Application Gateway v2  (standardowe i WAF), a nie jednostki SKU Application Gateway v1.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Włączanie dodatku AGIC dla istniejącego klastra usługi AKS za pomocą interfejsu wiersza polecenia platformy Azure

Jeśli chcesz nadal korzystać z interfejsu wiersza polecenia platformy Azure, możesz nadal włączyć dodatek AGIC w utworzonym klastrze usługi AKS _myCluster_ i określić dodatek AGIC, aby użyć istniejącego utworzonego Application Gateway _myApplicationGateway._

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Możesz sprawdzić, Azure Application Gateway dodatek usługi AKS został włączony za pomocą następującego polecenia.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

To polecenie powinno pokazywać dane wyjściowe w następujący `true` sposób: .

#### <a name="peer-the-two-virtual-networks-together"></a>Komunikacja równorzędna dwóch sieci wirtualnych

Ponieważ klaster usługi AKS został wdrożony we własnej sieci wirtualnej, Application Gateway w innej sieci wirtualnej, konieczne będzie połączenie równorzędne dwóch sieci wirtualnych w celu przepływu ruchu z sieci Application Gateway do zasobników w klastrze. Komunikacja równorzędna dwóch sieci wirtualnych wymaga uruchomienia polecenia interfejsu wiersza polecenia platformy Azure dwa razy, aby upewnić się, że połączenie jest dwukierunkowe. Pierwsze polecenie spowoduje utworzenie połączenia komunikacji równorzędnej z Application Gateway wirtualnej do sieci wirtualnej usługi AKS; Drugie polecenie spowoduje utworzenie połączenia komunikacji równorzędnej w innym kierunku.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Uwidocznij usługę bookbuyer w Internecie

Zastosuj następujący manifest przychodzący do klastra AKS, aby uwidocznić usługę bookbuyer w Internecie za pośrednictwem Azure Application Gateway.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Powinny zostać wyświetlone następujące dane wyjściowe

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Ponieważ nazwa hosta w manifeście przychodzącym jest nazwą pseudo używaną do testowania, nazwa DNS nie będzie dostępna w Internecie. Alternatywnie możemy użyć programu curl i przełączyć nagłówek nazwy hosta do publicznego adresu IP usługi Azure Application Gateway i odebrać kod 200, który pomyślnie łączy nas z usługą bookbuyer.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Powinny zostać wyświetlone następujące dane wyjściowe

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>Rozwiązywanie problemów

- [Dokumentacja dotycząca rozwiązywania problemów z platformą AGIC](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Dodatkowe narzędzia do rozwiązywania problemów są dostępne w repozytorium GitHub usługi AGIC](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Monitorowanie i wgląd w usługę Open Service Mesh (MESH) przy użyciu Azure Monitor i usługi Applications Insights

Usługi Azure Monitor i Azure Application Insights pomagają zmaksymalizować dostępność i wydajność aplikacji i usług, zapewniając kompleksowe rozwiązanie do zbierania, analizowania i działania na podstawie danych telemetrycznych z chmury i środowisk lokalnych.

Dodatek THE AKS będzie miał głęboką integrację z obiema tymi usługami platformy Azure i zapewni bezsekwne środowisko platformy Azure do wyświetlania kluczowych wskaźników wydajności dostarczanych przez metryki ROZWIĄZANIA DOC i reagowania na nie. Aby uzyskać więcej informacji na temat sposobu włączania i konfigurowania tych usług dla dodatku AKS , odwiedź stronę [Azure Monitor for THE](https://aka.ms/azmon/osmpreview) ( Informacje na temat włączania i konfigurowania tych usług).

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Samouczek: ręczne wdrażanie systemów Prometheus, Grafana i Jaeger w celu wyświetlenia metryk open service mesh (KU) w celu obserwacji

> [!WARNING]
> Instalacja oprogramowania Prometheus, Grafana i Jaeger jest dostarczana jako ogólne wskazówki, które pokazują, w jaki sposób te narzędzia mogą być używane do wyświetlania danych metryk SYSTEMU SYSTEM. Wskazówek dotyczących instalacji nie należy używać w przypadku instalacji produkcyjnej. Zapoznaj się z dokumentacją każdego narzędzia, aby dowiedzieć się, jak najlepiej dopasować instalacje do twoich potrzeb. Najważniejszą z nich będzie brak trwałego magazynu, co oznacza, że wszystkie dane zostaną utracone po zakończeniu pracy zasobników Prometheus Grafana i/lub Jaeger.

Usługa Open Service Mesh (ALERT) generuje szczegółowe metryki związane z całym ruchem w obrębie siatki. Te metryki zapewniają wgląd w zachowanie aplikacji w siatce, pomagając użytkownikom rozwiązywać problemy, konserwować i analizować aplikacje.

Obecnie SYSTEM zbiera metryki bezpośrednio z serwerów proxy sidecar (Envoy). System UMOŻLIWIA rozbudowane metryki ruchu przychodzącego i wychodzącego dla wszystkich usług w siatce. Dzięki tym metrykom użytkownik może uzyskać informacje o ogólnym natężeniu ruchu, błędach w ruchu i czasie odpowiedzi na żądania.

System ALERT używa systemu Prometheus do zbierania i przechowywania spójnych metryk ruchu i statystyk dla wszystkich aplikacji uruchomionych w siatce. Prometheus to zestaw narzędzi do monitorowania i alertów typu open source, który jest często używany w środowiskach Kubernetes i Service Mesh (ale nie tylko).

Każda aplikacja, która jest częścią siatki, jest uruchamiana w zasobniku zawierającym sidecar envoy, który uwidacznia metryki (metryki serwera proxy) w formacie Prometheus. Ponadto każdy zasobnik, który jest częścią siatki, ma adnotacje Prometheus, co umożliwia serwerowi Prometheus dynamiczne skrobanie aplikacji. Ten mechanizm automatycznie umożliwia wycinki metryk za każdym razem, gdy do siatki zostanie dodana nowa przestrzeń nazw/zasobnik/usługa.

Metryki ALERT można wyświetlać za pomocą narzędzia Grafana, czyli oprogramowania do wizualizacji i analizy typu open source. Umożliwia wykonywanie zapytań, wizualizowanie i eksplorowanie metryk oraz tworzenie alertów.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> - Tworzenie i wdrażanie wystąpienia prometheus
> - Konfigurowanie aplikacjiDPOWIEDZIALNOŚCI w celu umożliwienia zdyskusowania prometheus
> - Aktualizowanie mapy konfiguracji Prometheus
> - Tworzenie i wdrażanie wystąpienia Grafana
> - Konfigurowanie aplikacji Grafana przy użyciu źródła danych Prometheus
> - Importowanie pulpitu nawigacyjnego INTERFEJSU UŻYTKOWNIKA dla programu Grafana
> - Tworzenie i wdrażanie wystąpienia Jaeger
> - Konfigurowanie śledzenia Jaegera dla urządzenia DEBUG

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Wdrażanie i konfigurowanie wystąpienia Prometheus na platformieKOWY

Użyjemy usługi Helm do wdrożenia wystąpienia prometheus. Uruchom następujące polecenia, aby zainstalować prometheus za pośrednictwem programu Helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Jeśli instalacja powiodła się, powinny zostać wyświetlonych podobne dane wyjściowe poniżej. Zanotuj port serwera Prometheus i nazwę DNS klastra. Te informacje będą później używane przez program do konfigurowania prometheus jako źródła danych dla usługi Grafana.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Konfigurowanie aplikacji DOSTĘP do zezwalania na złomowanie prometheus

Aby upewnić się, że składniki ZAKU są skonfigurowane dla wycinki Prometheus, należy sprawdzić konfigurację prometheus_scraping **znajdującą** się w pliku konfiguracyjnym z plikiem config o wysokiej jakości. Wyświetl konfigurację za pomocą następującego polecenia:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

Dane wyjściowe poprzedniego polecenia powinny być `true` zwracane, jeśli skonfigurowano dla aplikacji Prometheus wycinki. Jeśli zwracana wartość to `false` , musimy zaktualizować konfigurację tak, aby zawierała wartość `true` . Uruchom następujące polecenie, aby **włączyć wytłanianie** w programie PROMETHEUS:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Powinny zostać wyświetlone następujące dane wyjściowe.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Aktualizowanie mapy Prometheus Configmap

Domyślna instalacja systemu Prometheus będzie zawierać dwie mapy konfiguracji kubernetes. Listę map konfiguracji prometheus można wyświetlić za pomocą następującego polecenia.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Będziemy musieli zastąpić konfigurację prometheus.yml znajdującą się w **stable-prometheus-server** configmap z następującą konfiguracją SERWERA. Istnieje kilka technik edytowania plików w celu wykonania tego zadania. Prostym i bezpiecznym sposobem jest wyeksportowanie mapy konfiguracji, utworzenie jej kopii do utworzenia kopii zapasowej, a następnie edytowanie jej za pomocą edytora, takiego jak Visual Studio kodu.

> [!NOTE]
> Jeśli nie masz zainstalowanego Visual Studio Code możesz pobrać i zainstalować go [tutaj.](https://code.visualstudio.com/Download)

Najpierw wyeksportujmy **mapę konfiguracji stable-prometheus-server,** a następnie skopiujmy dane do kopii zapasowej.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Następnie otwórzmy plik przy użyciu Visual Studio Code do edycji.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Po otwarciu mapy konfiguracji w edytorze Visual Studio Code zastąp plik prometheus.yml poniższymi konfiguracjami PROTOKOŁU I zapisz plik.

> [!WARNING]
> Niezwykle ważne jest, aby zachować strukturę wcięcia pliku yaml. Wszelkie zmiany w strukturze plików yaml mogą spowodować, że mapa konfiguracji nie będzie mogła zostać ponownie zastosowana.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Zastosuj zaktualizowany plik yaml configmap za pomocą następującego polecenia.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Może zostać wyświetlony komunikat o braku wymaganej adnotacji kubernetes. Na razie można to zignorować.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Sprawdź, czy aplikacja Prometheus jest skonfigurowana do zsypowania punktów końcowych siatki INTERFEJSU i interfejsu API APLIKACJI

Aby sprawdzić, czy aplikacja Prometheus jest poprawnie skonfigurowana do zdjęć punkty końcowe siatki MESH i interfejsu API, przekażemy port do zasobnika Prometheus i wyświetlmy konfigurację docelową. Uruchom następujące polecenia.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Otwórz przeglądarkę, aby `http://localhost:9090/targets`

Jeśli przewiniesz w dół, wszystkie punkty końcowe metryki SMI powinny być w stanie **UP,** a także inne metryki ŹRÓDLA zdefiniowane zgodnie z poniższymi ilustracjami.

![Obraz interfejsu użytkownika metryk docelowych APLIKACJI Prometheus](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Wdrażanie i konfigurowanie wystąpienia Grafana na platformieLAN

Użyjemy usługi Helm do wdrożenia wystąpienia Grafana. Uruchom następujące polecenia, aby zainstalować program Grafana za pośrednictwem programu Helm:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Następnie pobierzemy domyślne hasło Grafana, aby zalogować się do witryny Grafana.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Zanotuj hasło Grafana.

Następnie pobierzemy zasobnik Grafana, aby przekierowyć go do pulpitu nawigacyjnego Grafana w celu zalogowania.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Otwórz przeglądarkę, aby `http://localhost:3000`

Na poniższym ekranie logowania wprowadź **admin** jako nazwę użytkownika i użyj przechwyconego wcześniej hasła Grafana.

![Obraz interfejsu użytkownika strony logowania APLIKACJI Grafana](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Konfigurowanie źródła danych Grafana Prometheus

Po pomyślnym zalogowaniu się do aplikacji Grafana następnym krokiem jest dodanie prometheus jako źródeł danych dla Grafana. W tym celu przejdź do ikony konfiguracji w menu po lewej stronie i wybierz pozycję Źródła danych, jak pokazano poniżej.

![Obraz interfejsu użytkownika strony ŹRÓDLE danych Grafana](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Kliknij przycisk **Dodaj źródło danych i** wybierz pozycję Prometheus w obszarze baz danych szeregów czasu.

![Obraz interfejsu użytkownika strony wyboru Grafana Datasources](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Na **poniższej stronie Configure your Prometheus data source** (Konfigurowanie źródła danych Prometheus) wprowadź nazwę FQDN klastra Kubernetes dla usługi Prometheus dla ustawienia adresu URL protokołu HTTP. Domyślna WQDN powinna być `stable-prometheus-server.default.svc.cluster.local` . Po wprowadzeniu punktu końcowego usługi Prometheus przewiń w dół strony i wybierz pozycję Zapisz & **Test.** Powinno zostać wyświetlony zielony pole wyboru wskazujące, że źródło danych działa.

#### <a name="importing-osm-dashboards"></a>Importowanie pulpitów nawigacyjnych CRM

Pulpity nawigacyjne INTERFEJSU SĄ dostępne za pośrednictwem:

- [Nasze repozytorium i](https://github.com/grafana/grafana)można importować jako obiekty blob JSON za pośrednictwem internetowego portalu administracyjnego
- lub [online w Grafana.com](https://grafana.com/grafana/dashboards/14145)

Aby zaimportować pulpit nawigacyjny, poszukaj znaku `+` w menu po lewej stronie i wybierz pozycję `import` .
Możesz bezpośrednio zaimportować pulpit nawigacyjny według jego identyfikatora w jednakowy `Grafana.com` sposób. Na przykład nasz pulpit nawigacyjny używa identyfikatora , możesz użyć identyfikatora bezpośrednio `OSM Mesh Details` `14145` w formularzu i wybrać pozycję `import` :

![Obraz interfejsu użytkownika importu strony pulpitu nawigacyjnego SYSTEMU Grafana](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Po wybraniu opcji importowania automatycznie pojawi się zaimportowany pulpit nawigacyjny.

![Obraz interfejsu użytkownika strony szczegółów siatki pulpitu nawigacyjnego Grafana Grafana](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Wdrażanie i konfigurowanie operatora Jaeger na platformie Kubernetes dla systemu SYSTEM

[Jaeger to](https://www.jaegertracing.io/) system śledzenia typu open source używany do monitorowania i rozwiązywania problemów z systemami rozproszonymi. Można ją wdrożyć za pomocą rozwiązania IT jako nowego wystąpienia. Można też użyć własnego wystąpienia. Poniższe instrukcje wdrażają nowe wystąpienie jaeger w przestrzeni `jaeger` nazw w klastrze usługi AKS.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Wdrażanie aplikacji Jaeger w klastrze usługi AKS

Zastosuj następujący manifest, aby zainstalować program Jaeger:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>Włączanie śledzenia dla dodatku CZAS

Następnie należy włączyć śledzenie dla dodatku DOSTĘP.

> [!NOTE]
> Obecnie właściwości śledzenia nie są obecnie dostępne w mapie konfiguracji w pliku do konfiguracji. Stanie się to widoczna w nowej wersji dodatku AKS DLA.

Uruchom następujące polecenie, aby włączyć śledzenie dla dodatku DOSTĘP:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Wyświetlanie interfejsu użytkownika jaeger z przekazywaniem portów

Interfejs użytkownika jaeger działa na porcie 16686. Aby wyświetlić internetowy interfejs użytkownika, możesz użyć funkcji kubectl port-forward:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

W przeglądarce powinna zostać wyświetlony lista rozwijana Usługa, która umożliwia wybranie aplikacji wdrożonych przez pokaz sklepu z książkami. Wybierz usługę, aby wyświetlić wszystkie jej zakresy. Jeśli na przykład wybierzesz bookbuyer z 1-godzinnym lookbackiem, możesz zobaczyć jego interakcje z księgarniami v1 i bookstore-v2 posortowane według czasu.

![Obraz interfejsu użytkownika strony śledzenia JAEGER](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Wybierz dowolny element, aby wyświetlić go bardziej szczegółowo. Wybierz wiele elementów, aby porównać ślady. Na przykład można porównać interakcje księgarni z księgarnią i księgarnią v2 w określonym momencie w czasie.

Możesz również wybrać kartę Architektura systemu, aby wyświetlić wykres sposobu interakcji/komunikacji różnych aplikacji. Dzięki temu można dowiedzieć się, jak ruch przepływa między aplikacjami.

![Obraz interfejsu użytkownika architektury systemu ISO Jaeger](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Przewodniki rozwiązywania problemów z dodawaniem dodatku AKS w usłudze Open Service Mesh (SYSTEM)

Podczas wdrażania dodatku AKS DLA SYSTEMU OD CZASU DO CZASU może wystąpić problem. Poniższe przewodniki pomagają w rozwiązywaniu problemów z błędami i rozwiązywaniu typowych problemów.

### <a name="verifying-and-troubleshooting-osm-components"></a>Weryfikowanie i rozwiązywanie problemów ze składnikami SYSTEM

#### <a name="check-osm-controller-deployment"></a>Sprawdzanie wdrożenia kontrolera APLIKACJI

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Kontroler KONDYCJI w dobrej kondycji będzie wyglądać w ten sposób:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Sprawdzanie zasobnika kontrolera THE Controller

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Zasobnik w dobrej kondycji w 2018 roku będzie wyglądać tak:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Mimo że w pewnym momencie został eksmitowany jeden kontroler, mamy jeszcze jeden, który jest GOTOWY 1/1 i Uruchomiony z 0 ponownymi uruchomieniami. Jeśli kolumna READY ma wartości inne niż 1/1, siatka usług będzie w stanie przerwanym.
Kolumna READY z wartością 0/1 wskazuje, że kontener płaszczyzny sterowania ulega awarii — musimy pobrać dzienniki. Zobacz sekcję Pobierz dzienniki kontrolera POMOC TECHNICZNA PLATFORMY AZURE Center poniżej. Kolumna READY z liczbą większą niż 1 po ciągu / wskazuje, że są zainstalowane boczne. Kontroler TERAZ najprawdopodobniej nie będzie działał z dołączonymi do niego przyczepkami.

> [!NOTE]
> Od wersji 0.8.2 kontroler OFC nie jest w trybie HA i będzie uruchamiany w wdrożonej z licznikiem replik 1 — pojedynczym zasobniku. Zasobnik ma sondy kondycji i w razie potrzeby zostanie ponownie uruchomiony przez kubelet.

#### <a name="check-osm-controller-service"></a>Sprawdź usługę kontrolera ELITE

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Usługa kontrolera TERAZ w dobrej kondycji będzie wyglądać w ten sposób:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> Adres CLUSTER-IP będzie inny. Nazwa usługi i port(S) muszą być takie same jak w powyższym przykładzie.

#### <a name="check-osm-controller-endpoints"></a>Sprawdzanie punktów końcowych kontrolera APLIKACJI

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Punkty końcowe kontrolera KONDYCJI w dobrej kondycji będą wyglądać w ten sposób:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Sprawdzanie wdrożenia iniekcji DOC

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Wdrożenie iniekcji INIEKCJI w dobrej kondycji wyglądałoby tak:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Sprawdzanie zasobnika iniekcji DO WSTRZYKIWANIA

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Zasobnik iniekcji WSTRZYKIWANIA w dobrej kondycji będzie wyglądać tak:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Sprawdzanie usługi WSTRZYKIWANIA

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Usługa iniekcji WSTRZYKIWANIA w dobrej kondycji będzie wyglądać tak:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Sprawdzanie punktów końcowych APLIKACJI

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Punkt końcowy BARDZO w dobrej kondycji będzie wyglądać w ten sposób:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Sprawdzanie sprawdzania poprawności i wyciszania webhook

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Prawidłowa kondycja kondycji sprawdzania poprawności webhook wyglądałaby tak:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Modyfikujący w dobrej kondycji poziomej kondycji webhook będzie wyglądać w ten sposób:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Sprawdź usługę i pakiet urzędu certyfikacji w witrynie Webhook Sprawdzania poprawności

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Dobrze skonfigurowana konfiguracja sprawdzania poprawności konfiguracji urządzenia webhook wyglądałaby dokładnie tak:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Sprawdź usługę i pakiet urzędu certyfikacji dla modyfikowanego zestawu webhook

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Dobrze skonfigurowana konfiguracja mutowania webhook wyglądałaby dokładnie tak:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Sprawdź, czy kontroler SYSTEM SYSTEMOWY nadał pakietowi urzędu certyfikacji czek sprawdzania poprawności (lub wyciszania)

> [!NOTE]
> Od wersji 0.8.2 Ważne jest, aby wiedzieć, że składnik AKS RP instaluje element webhook sprawdzania poprawności, AKS Reconciler zapewnia, że istnieje, ale kontroler JEDNOSTKI JEST tym, który wypełnia pakiet urzędu certyfikacji.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Ta liczba wskazuje liczbę bajtów lub rozmiar pakietu urzędu certyfikacji. Jeśli ta liczba jest pusta, 0 lub liczba poniżej 1000, oznacza to, że pakiet urzędu certyfikacji nie jest poprawnie aprowowany. Bez poprawnego pakietu urzędu certyfikacji podczas sprawdzania poprawności elementu webhook występuje błąd i użytkownik nie może wprowadzać zmian w pliku ConfigMap w przestrzeni nazw kube-system.

Przykładowy błąd, gdy pakiet urzędu certyfikacji jest nieprawidłowy:

- Próba zmiany pliku ConfigMap w pliku do konfiguracji:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Błąd:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Informacje dotyczące sytuacji, w których konfiguracja **sprawdzania poprawności** urządzenia webhook ma nieprawidłowy certyfikat:

- Opcja 1 — ponowne uruchomienie kontrolera STEROWANIA — spowoduje to ponowne uruchomienie kontrolera THE. Po uruchomieniu zastąpi pakiet urzędu certyfikacji dla zarówno modyfikujących, jak i ważnych webhook.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Opcja 2 — opcja 2. Usuń walidujący webhook — usunięcie walidacyjnego webhook sprawia, że pliki `osm-config` ConfigMap nie są już weryfikowane. Każda poprawka zostanie przejścia. W pewnym momencie uzgadnianie usługi AKS upewni się, że istnieje ważnych webhook i ponownie go utworzyć. Może być konieczne ponowne uruchomienie kontrolera THE, aby szybko ponownie napisać pakiet urzędu certyfikacji.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Opcja 3 — usuwanie i stosowanie poprawek: następujące polecenie spowoduje usunięcie ważnych elementy webhook, co pozwoli nam dodać dowolne wartości i natychmiast spróbuje zastosować poprawkę. Najprawdopodobniej uzgadniający AKS nie będzie mieć wystarczająco dużo czasu, aby uzgodnić i przywrócić ważnych webhook daje nam możliwość zastosowania zmiany w ostateczności:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Sprawdź `osm-config` **ConfigMap**

> [!NOTE]
> Kontroler DNS nie wymaga, aby mapa ConfigMap była obecna `osm-config` w przestrzeni nazw kube-system. Kontroler ma uzasadnione wartości domyślne dla konfiguracji i może działać bez niego.

Sprawdź istnienie:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Sprawdzanie zawartości pliku ConfigMap z poleceniem do konfiguracji

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` Wartości ConfigMap:

| Klucz                              | Typ   | Dozwolone wartości                                          | Wartość domyślna                          | Funkcja                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Wyjście                           | bool   | wartość true, false                                             | `"false"`                              | Włącza ruch wychodzący w siatce.                                                                                                                                                                                                             |
| enable_debug_server              | bool   | wartość true, false                                             | `"true"`                               | Umożliwia punktowi końcowemu debugowania w zasobniku debug-controller wyświetlanie informacji dotyczących siatki, takich jak połączenia serwera proxy, certyfikaty i zasady SMI.                                                                                    |
| enable_privileged_init_container | bool   | wartość true, false                                             | `"false"`                              | Włącza kontenery privileged init dla zasobników w siatce. W przypadku false kontenery init mają tylko NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | ciąg | trace, debug, info, warning, warn, error, critical, off | `"error"`                              | Ustawia poziom szczegółowości rejestrowania dla bocznej aplikacji Envoy proxy, która ma zastosowanie tylko do nowo utworzonych zasobników przyłączanych do siatki. Aby zaktualizować poziom dziennika dla istniejących zasobników, uruchom ponownie wdrożenie za pomocą programu `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | ciąg | rozdzielana przecinkami lista zakresów adresów IP w postaci a.b.c.d/x | `-`                                    | Globalna lista zakresów adresów IP do wykluczenia z przechwycenia ruchu wychodzącego przez serwer proxy usługi Sidecar.                                                                                                                                    |
| permissive_traffic_policy_mode   | bool   | wartość true, false                                             | `"false"`                              | Ustawienie wartości `true` na , włącza tryb zezwalania wszystkim w siatce, czyli brak wymuszania zasad ruchu w siatce. W przypadku ustawienia na wartość , włącza zasady ruchu typu "odmów wszystkim" w siatce, tj. element jest niezbędny do komunikowania `false` `SMI Traffic Target` się usług. |
| prometheus_scraping              | bool   | wartość true, false                                             | `"true"`                               | Włącza skrobanie metryk Prometheus na proxych zbocznymi.                                                                                                                                                                                 |
| service_cert_validity_duration   | ciąg | 24h, 1h30 m (dowolny czas trwania)                          | `"24h"`                                | Ustawia czas trwania ważności certyfikatu usługi reprezentowany jako sekwencja liczb dziesiętnych z opcjonalnym ułamkiem i sufiksem jednostkowym.                                                                                             |
| tracing_enable                   | bool   | wartość true, false                                             | `"false"`                              | Włącza śledzenie Jaegera dla siatki.                                                                                                                                                                                                    |
| tracing_address                  | ciąg | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | Adres wdrożenia jaeger, jeśli śledzenie jest włączone.                                                                                                                                                                                |
| tracing_endpoint                 | ciąg | /api/v2/spans                                           | /api/v2/spans                          | Punkt końcowy do śledzenia danych, jeśli śledzenie jest włączone.                                                                                                                                                                                          |
| tracing_port                     | int    | dowolna wartość całkowita o wartości niezerowej                              | `"9411"`                               | Port, na którym jest włączone śledzenie.                                                                                                                                                                                                       |
| use_https_ingress                | bool   | wartość true, false                                             | `"false"`                              | Włącza ruch przychodzący HTTPS dla siatki.                                                                                                                                                                                                      |
| config_resync_interval           | ciąg | w obszarze 1 minuta powoduje wyłączenie tego                            | 0 (wyłączone)                           | Po podano wartość powyżej 1 m (60s), kontroler THE wyśle wszystkie dostępne konfiguracje do każdego połączonej aplikacji Envoy w danym interwale                                                                                                    |

#### <a name="check-namespaces"></a>Sprawdzanie przestrzeni nazw

> [!NOTE]
> Przestrzeń nazw kube-system nigdy nie będzie uczestniczyć w siatce usług i nigdy nie będzie oznaczona i/lub oznaczona poniższym kluczem/wartościami.

Używamy polecenia `osm namespace add` , aby dołączyć przestrzenie nazw do danej siatki usług.
Jeśli przestrzeń nazw k8s jest częścią siatki (lub aby była częścią siatki), muszą być spełnione następujące kwestie:

Wyświetlanie adnotacji za pomocą

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Musi być obecna następująca adnotacja:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Wyświetl etykiety za pomocą

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Musi być obecna następująca etykieta:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Jeśli przestrzeń nazw nie ma adnotacji lub nie jest oznaczona za pomocą iniekcji WSTRZYKIWANIA, nie zostaną dodawane `"openservicemesh.io/sidecar-injection": "enabled"` `"openservicemesh.io/monitored-by": "osm"` etykiety sidecars aplikacji Envoy.

> Uwaga: Po `osm namespace add` wywołaniu tylko **nowe** zasobniki zostaną wstrzykniętą do bocznej aplikacji Envoy. Istniejące zasobniki muszą zostać ponownie uruchomione za pomocą `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Sprawdź identyfikatory CRD SMI:

Sprawdź, czy klaster ma wymagane listy CLD:

```azurecli-interactive
kubectl get crds
```

W klastrze muszą być zainstalowane następujące elementy:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Pobierz wersje CLD zainstalowane za pomocą tego polecenia:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Oczekiwane dane wyjściowe:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

Kontroler SYSTEM PLIKÓW w wersji 0.8.2 wymaga następujących wersji:

- traffictargets.access.smi-spec.io — [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io — [wersja 1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io — [wersja 1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io — nie jest obsługiwana
- trafficsplits.split.smi-spec.io — [wersja 1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Jeśli brakuje dysków C CRD, zainstaluj je w klastrze za pomocą następujących poleceń:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Wyłączanie dodatku Open Service Mesh (ICH) dla klastra usługi AKS

Aby wyłączyć dodatek DOSTĘPNĄ, uruchom następujące polecenie:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
