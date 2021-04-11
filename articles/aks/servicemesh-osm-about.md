---
title: Otwórz siatkę usług (wersja zapoznawcza)
description: Otwórz siatkę usług (OSM) w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 0052c8d2f9b85c34d50a3e9d01253ecaf2d02bab
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106717"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Otwórz dodatek AKS usługi Service siatk (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

[Open Service siatk (OSM)](https://docs.openservicemesh.io/) to lekki, rozszerzalny, natywny, oparty na chmurze siatka usług, która umożliwia użytkownikom jednorodne zarządzanie, zabezpieczanie i uzyskiwanie wbudowanych funkcji związanych z obsługą w środowiskach wysoce dynamicznych.

OSM uruchamia płaszczyznę kontroli opartą na wysłannika na Kubernetes, może być skonfigurowana za pomocą interfejsów API [SMI](https://smi-spec.io/) i działa przez wstrzyknięcie serwera proxy wysłannika jako kontenera przyczepki obok każdego wystąpienia aplikacji. Serwer proxy wysłannika zawiera i wykonuje reguły dotyczące zasad kontroli dostępu, implementuje konfigurację routingu i przechwytuje metryki. Płaszczyzna kontroli ciągle konfiguruje serwery proxy w celu zapewnienia aktualności zasad i reguł routingu, a serwery proxy są w dobrej kondycji.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Możliwości i funkcje

Usługa OSM zapewnia następujący zestaw funkcji i funkcji w celu zapewnienia sieci natywnej usługi w chmurze dla klastrów usługi Azure Kubernetes Service (AKS):

- Zabezpieczanie komunikacji usługi przez włączenie mTLS

- Łatwe dołączanie aplikacji do siatki przez włączenie automatycznego wstrzykiwania przyczepki serwera proxy wysłannika

- Łatwe i przejrzyste konfiguracje dla ruchu przechodzącego na wdrożeniach

- Możliwość definiowania i wykonywania szczegółowych zasad kontroli dostępu dla usług

- Zauważalność i wgląd w metryki aplikacji na potrzeby debugowania i monitorowania usług

- Integracja z usługami/rozwiązaniami zarządzania certyfikatami zewnętrznymi za pomocą podłączanego interfejsu

## <a name="scenarios"></a>Scenariusze

OSM mogą pomóc we wdrażaniu AKS w następujących scenariuszach:

- Zapewnianie zaszyfrowanej komunikacji między punktami końcowymi usługi wdrożonymi w klastrze

- Autoryzacja ruchu HTTP/HTTPS i TCP w sieci

- Konfiguracja ukierunkowanych kontroli ruchu między co najmniej dwie usługi dla wdrożeń A/B lub Kanaryjskich

- Zbieranie i wyświetlanie wskaźników KPI z ruchu aplikacji

## <a name="osm-service-quotas-and-limits-preview"></a>Limity przydziałów i limitów usługi OSM (wersja zapoznawcza)

Ograniczenia dotyczące limitów przydziału i limitów usługi OSM w wersji zapoznawczej można znaleźć na [stronie przydziały AKS i limity regionalne](https://docs.microsoft.com/azure/aks/quotas-skus-regions).

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
> Nie należy próbować instalować OSM z pliku binarnego przy użyciu `osm install` . Spowoduje to zainstalowanie OSM, która nie jest zintegrowana z dodatkiem do AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Rejestrowanie `AKS-OpenServiceMesh` funkcji w wersji zapoznawczej

Aby utworzyć klaster AKS, który może korzystać z dodatku Open Service siatk, należy włączyć `AKS-OpenServiceMesh` flagę funkcji w subskrypcji.

Zarejestruj `AKS-OpenServiceMesh` flagę funkcji za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Wyświetlenie stanu _rejestracji_ może potrwać kilka minut. Sprawdź stan rejestracji za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów _Microsoft. ContainerService_ za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Zainstaluj dodatek Open Service siatk (OSM) Azure Kubernetes Service (AKS) dla nowego klastra AKS

Aby zapoznać się z nowym scenariuszem wdrażania klastra AKS, należy zacząć od nowego wdrożenia klastra AKS, włączając dodatek OSM w operacji tworzenia klastra.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie _myOsmAksGroup_ w lokalizacji _eastus2_ (region):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Wdróż klaster AKS z włączonym dodatkiem OSM

Teraz zostanie wdrożony nowy klaster AKS z włączonym dodatkiem OSM.

> [!NOTE]
> Weź pod uwagę następujące polecenie wdrażania AKS korzysta z dysków tymczasowych systemu operacyjnego. Więcej informacji o [dyskach tymczasowych systemów operacyjnych](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os) można znaleźć na stronie AKS

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Uzyskiwanie poświadczeń dostępu do klastra AKS

Uzyskaj poświadczenia dostępu dla nowego zarządzanego klastra Kubernetes.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Włącz dodatek "Open Service siatk (OSM) Azure Kubernetes Service (AKS)" dla istniejącego klastra AKS

W przypadku istniejącego scenariusza klastra AKS zostanie włączony dodatek OSM do istniejącego klastra AKS, który został już wdrożony.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Włączanie dodatku OSM do istniejącego klastra AKS

Aby włączyć dodatek AKS OSM, należy uruchomić `az aks enable-addons --addons` polecenie przekazanie parametru `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Powinny zostać wyświetlone dane wyjściowe podobne do danych wyjściowych przedstawionych poniżej, aby upewnić się, że dodatek AKS OSM został zainstalowany.

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

## <a name="validate-the-aks-osm-add-on-installation"></a>Weryfikowanie instalacji dodatku AKS OSM

Aby można było sprawdzić wszystkie składniki dodatku AKS OSM, dostępne są następujące polecenia:

Najpierw możemy zbadać profile dodatków klastra w celu sprawdzenia stanu włączenia zainstalowanych dodatków. Następujące polecenie powinno zwrócić wartość "true".

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Następujące `kubectl` polecenia będą zgłaszać stan kontrolera OSM.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Uzyskiwanie dostępu do dodatku AKS OSM

Obecnie można uzyskać dostęp do konfiguracji kontrolera OSM i skonfigurować ją za pośrednictwem configmap. Aby wyświetlić ustawienia konfiguracji kontrolera OSM, wykonaj zapytanie do OSM-config configmap za pośrednictwem, `kubectl` Aby wyświetlić jego ustawienia konfiguracji.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Dane wyjściowe OSM configmap powinny wyglądać następująco:

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

Zauważ, że **permissive_traffic_policy_mode** jest skonfigurowany na **wartość true**. Tryb zasady ograniczania ruchu w OSM to tryb, w którym pominięto wymuszanie zasad ruchu [SMI](https://smi-spec.io/) . W tym trybie program OSM automatycznie odnajduje usługi, które są częścią reguł zasad ruchu w sieci i narzędzi usług, na każdej przyczepce usługi wysłannika proxy, aby móc komunikować się z tymi usługami.

> [!WARNING]
> Przed kontynuowaniem sprawdź, czy dozwolony tryb zasad ruchu jest ustawiony na wartość true (prawda), jeśli nie zmienisz go na **true** przy użyciu poniższego polecenia

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Wdróż nową aplikację, która ma być zarządzana przez dodatek usługi Azure Kubernetes Service (AKS) OSM

### <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach opisanych w tym instruktażu założono, że utworzono klaster AKS (Kubernetes `1.19+` lub nowszy z włączoną obsługą kontroli dostępu Kubernetes), nawiązać `kubectl` połączenie z klastrem (Jeśli potrzebna jest pomoc dla każdego z tych elementów, zobacz [Przewodnik](./kubernetes-walkthrough.md)po programie AKS i ZAinstalowano dodatek AKS OSM).

Wymagane są następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
- `aks-preview`Rozszerzenie w wersji 0.5.5 lub nowszej
- OSM w wersji v 0.8.0 lub nowszej
- apt — Pobierz JQ instalacji

### <a name="create-namespaces-for-the-application"></a>Tworzenie przestrzeni nazw dla aplikacji

W tym instruktażu będziemy używać aplikacji OSM księgarni z następującymi usługami Kubernetes Services:

- bookbuyer
- bookthief
- księgarni
- bookwarehouse

Utwórz obszary nazw dla każdego z tych składników aplikacji.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Dołącz przestrzenie nazw, które mają być zarządzane przez OSM

Po dodaniu przestrzeni nazw do siatki OSM umożliwia kontrolerowi OSM automatyczne wprowadzanie kontenerów proxy przyczepki wysłannika do aplikacji. Uruchom następujące polecenie, aby dołączyć przestrzenie nazw aplikacji OSM księgarni.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Wdrażanie aplikacji księgarni w klastrze AKS

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

Wszystkie dane wyjściowe wdrożenia są zestawione poniżej.

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

### <a name="checkpoint-what-got-installed"></a>Punkt kontrolny: co zostało zainstalowane?

Przykładowa aplikacja księgarni to wielowarstwowa aplikacja, która składa się z czterech usług, takich jak bookbuyer, bookthief, księgarni i bookwarehouse. Usługa bookbuyer i bookthief komunikują się z usługą księgarni w celu pobrania książek z usługi księgarni. Usługa księgarni pobiera książki z usługi bookwarehouse w celu dostarczenia bookbuyer i bookthief. Jest to prosta aplikacja wielowarstwowa, która dobrze sprawdza, w jaki sposób można użyć siatki usług w celu ochrony i autoryzacji komunikacji między usługami aplikacji. W dalszym ciągu instruktażu będziemy włączać i wyłączać zasady interfejsu sieci (SMI) usługi, aby umożliwić i nie zezwalać na komunikację z usługą za pośrednictwem usługi OSM. Poniżej znajduje się diagram architektury, który został zainstalowany dla aplikacji księgarni.

![Architektura aplikacji OSM bookbuyer](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Weryfikowanie aplikacji księgarni działającej w klastrze AKS

Obecnie wdrożono aplikację kontenera księgarni Mulit, ale jest ona dostępna tylko z poziomu klastra AKS. Nowsze samouczki ułatwią udostępnianie aplikacji poza klastrem za pośrednictwem kontrolera transferu danych przychodzących. W tym momencie będziemy korzystać z przekazywania portów w celu uzyskania dostępu do aplikacji bookbuyer w klastrze AKS w celu sprawdzenia, czy są one książkami do kupowania z usługi księgarni.

Aby sprawdzić, czy aplikacja działa w ramach klastra, do wyświetlania interfejsu użytkownika składników bookbuyer i bookthief zostanie użyty port do przodu.

Najpierw Pobierz nazwę bookbuyer pod

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Twoje bookbuyer pod nazwą będzie mieć unikatową nazwę.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Po określeniu nazwy tego elementu możemy teraz użyć polecenia do przesyłania dalej portów, aby skonfigurować tunel z naszego systemu lokalnego do aplikacji wewnątrz klastra AKS. Uruchom następujące polecenie, aby skonfigurować port do przodu dla lokalnego portu systemowego 8080. Ponownie Użyj określonego bookbuyer pod nazwą.

> [!NOTE]
> W przypadku wszystkich poleceń przekazywania portów najlepiej używać dodatkowego terminalu, aby można było kontynuować pracę w tym instruktażu i nie rozłącza tunelu. Najlepszym rozwiązaniem jest również ustanowienie tunelu do przesyłania dalej portu poza Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Powinny pojawić się dane wyjściowe podobne do tego.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Podczas sesji przekazywania portów przejdź do następującego adresu URL w przeglądarce `http://localhost:8080` . Teraz będzie można zobaczyć interfejs użytkownika aplikacji bookbuyer w przeglądarce podobnej do poniższego obrazu.

![Obraz interfejsu użytkownika aplikacji OSM bookbuyer](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Należy również zauważyć, że łączna liczba kupowanych książek w dalszym ciągu jest zwiększana do usługi księgarni v1. Usługa księgarni v2 nie została jeszcze wdrożona. Usługa księgarni v2 zostanie wdrożona w przypadku zademonstrowania zasad podziału ruchu SMI.

Możesz również sprawdzić te same dla usługi bookthief.

```azurecli-interactive
kubectl get pod -n bookthief
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Twoje bookthief pod nazwą będzie mieć unikatową nazwę.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Port do przodu do bookthief pod.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Przejdź do następującego adresu URL w przeglądarce `http://localhost:8080` . Powinna zostać wyświetlona bookthiefa obecnie kradzież książek z usługi księgarni! W dalszej części zaimplementujmy zasady ruchu, aby zatrzymać bookthief.

![Obraz interfejsu użytkownika aplikacji OSM bookthief](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Wyłącz tryb ruchu ograniczającego OSM dla siatki

Jak wspomniano wcześniej podczas wyświetlania konfiguracji klastra OSM, konfiguracja OSM domyślnie umożliwia włączanie zasad trybu ruchu ograniczającego. W tym trybie wymuszanie zasad ruchu jest pomijane, a usługa OSM automatycznie odnajduje usługi, które są częścią reguł zasad ruchu w sieci i programu Service, na poszczególnych przyczepach z serwerem proxy wysłannika, aby mogły komunikować się z tymi usługami.

Spowoduje to wyłączenie zasad trybu ruchu i OSM, które będą wymagały jawnych zasad [SMI](https://smi-spec.io/) , wdrożonych w klastrze w celu zezwolenia na komunikację w sieci z każdej usługi. Aby wyłączyć tryb ruchu ograniczającego, uruchom następujące polecenie w celu zaktualizowania właściwości configmap zmiana wartości z `true` na `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Twoje bookthief pod nazwą będzie mieć unikatową nazwę.

```Output
configmap/osm-config patched
```

Aby upewnić się, że tryb ruchu ograniczającego został wyłączony, naprzódj do przodu lub do bookthief w celu wyświetlenia interfejsu użytkownika w przeglądarce i sprawdź, czy zakupione lub skradzione książki nie są już zwiększane. Upewnij się, że przeglądarka została odświeżona. Jeśli zwiększenie zostanie zatrzymane, zasady zostały zastosowane poprawnie. Pomyślnie zatrzymano bookthief z kradzieży książek, ale nie można kupić bookbuyer z księgarni ani księgarni mogą pobrać książki z bookwarehouse. Następnie zaimplementujemy zasady [SMI](https://smi-spec.io/) , aby zezwolić na dostęp do usług tylko w siatce, które chcesz skomunikować.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Zastosowanie zasad dostępu do ruchu sieciowego interfejsu sieci (SMI)

Teraz, gdy została wyłączona cała komunikacja w sieci, pozwól, aby nasza usługa bookbuyer mogła komunikować się z naszą usługą księgarni na potrzeby zakupów książek i umożliwić naszej usłudze księgarni komunikowanie się z naszą usługą bookwarehouse w celu pobierania książek do sprzedawania.

Wdróż następujące zasady [SMI](https://smi-spec.io/) .

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

Teraz można skonfigurować sesję przekazywania portów na bookbuyer lub księgarni, a także zobaczyć, że metryki z kupowanymi książkami i sprzedażmi są wycofywane. Możesz również wykonać te same czynności dla bookthief pod, aby upewnić się, że nadal nie można już wykraść książek.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Zastosuj zasady podziału ruchu interfejsu sieci (SMI) usługi

W naszej końcowej demonstracji utworzymy zasady podziału ruchu [SMI](https://smi-spec.io/) w celu skonfigurowania wagi komunikacji z jednej usługi do wielu usług jako zaplecza. Funkcja podziału ruchu pozwala na stopniowe przenoszenie połączeń do jednej usługi do innej przez ważenie ruchu w skali od 0 do 100.

Poniższa ilustracja przedstawia diagram zasad podziału ruchu [SMI](https://smi-spec.io/) do wdrożenia. Zostanie wdrożona dodatkowa księgarni wersja 2, a następnie zostanie podzielony ruch przychodzący z bookbuyer, co oznacza 25% ruchu do usługi księgarni V1 i 75% do usługi księgarni v2.

![Diagram podzielony ruchu OSM bookbuyer](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Wdróż usługę księgarni v2.

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

Teraz Wdróż zasady podziału ruchu w celu rozdzielenia ruchu bookbuyer między obiema usługami księgarni V1 i v2.

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

Skonfiguruj tunel do przesyłania dalej portów do bookbuyer pod, a następnie powinny być widoczne książki zakupione z usługi księgarni v2. W przypadku kontynuowania korzystania z większej liczby zakupów należy zauważyć szybszy przyrost zakupów w ramach usługi księgarni v2.

![Interfejs użytkownika OSM bookbuyer Books boough](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Zarządzanie istniejącymi wdrożonymi aplikacjami, które mają być zarządzane przez dodatek usługi Azure Kubernetes Service (AKS) w systemie OSM

### <a name="before-you-begin"></a>Zanim rozpoczniesz

Kroki opisane w tym instruktażu założono, że wcześniej włączono dodatek OSM AKS dla klastra AKS. W przeciwnym razie przed kontynuowaniem zapoznaj się z sekcją [Włączanie dodatku Open Service Grid (OSM) Azure Kubernetes Service (AKS) dla istniejącego klastra AKS](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) . Ponadto klaster AKS musi być w wersji Kubernetes `1.19+` lub nowszej, mieć włączoną funkcję RBAC Kubernetes i ustanowił `kubectl` połączenie z klastrem (Jeśli potrzebna jest pomoc z dowolnym z tych elementów, zobacz Przewodnik po usłudze [AKS](./kubernetes-walkthrough.md)i ZAinstalowano dodatek AKS OSM).

Wymagane są następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
- `aks-preview`Rozszerzenie w wersji 0.5.5 lub nowszej
- OSM w wersji v 0.8.0 lub nowszej
- apt — Pobierz JQ instalacji

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Weryfikowanie zasad trybu ruchu "OSM"

OSMy tryb zasad ruchu jest trybem, w którym jest pomijane wymuszanie zasad ruchu [SMI](https://smi-spec.io/) . W tym trybie program OSM automatycznie odnajduje usługi, które są częścią reguł zasad ruchu w sieci i narzędzi usług, na każdej przyczepce usługi wysłannika proxy, aby móc komunikować się z tymi usługami.

Aby sprawdzić bieżący tryb ruchu OSM dla klastra, uruchom następujące polecenie:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Dane wyjściowe OSM configmap powinny wyglądać następująco:

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

Jeśli **permissive_traffic_policy_mode** ma **wartość true**, można bezpiecznie dołączyć przestrzenie nazw bez zakłócania komunikacji między usługą a usługą. Jeśli **permissive_traffic_policy_mode** ma **wartość FAŁSZ**, należy upewnić się, że zostały wdrożone odpowiednie manifesty zasad dostępu do ruchu [SMI](https://smi-spec.io/) i upewnienia się, że masz konto usługi reprezentujące każdą usługę wdrożoną w przestrzeni nazw. Postępuj zgodnie ze wskazówkami dotyczącymi [dołączania istniejących wdrożonych aplikacji z zasadą ograniczania ruchu "Open Service oczka (OSM)" skonfigurowanym jako FAŁSZ](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Dołączanie istniejących wdrożonych aplikacji z zasadą "zasady ruchu" Open Service oczka (OSM) skonfigurowanych jako prawdziwe

Najpierw będziemy dodawać wdrożone przestrzenie nazw aplikacji do OSM w celu zarządzania nimi.

```azurecli-interactive
osm namespace add bookstore
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Następnie zajmiemy się bieżącym wdrożeniem pod w przestrzeni nazw. Uruchom następujące polecenie, aby wyświetlić w wyznaczeniu przestrzeni nazw.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Powinny zostać wyświetlone następujące podobne dane wyjściowe:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Zwróć uwagę na kolumnę **Ready** , która przedstawia **1/1**, co oznacza, że aplikacja pod ma tylko jeden kontener. Następnie będziemy musieli ponownie uruchomić wdrożenia aplikacji, aby OSM mógł wstrzyknąć kontener proxy przyczepki wysłannika z aplikacją pod. Pobierzmy listę wdrożeń w przestrzeni nazw.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Teraz będziemy ponownie uruchamiać wdrożenie, aby wstrzyknąć kontener proxy wysłannika przyczepki do aplikacji pod. Uruchom następujące polecenie.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```Output
deployment.apps/bookbuyer restarted
```

Jeśli ponownie Przyjrzyjmy się zasobnikom w przestrzeni nazw:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Teraz możesz zauważyć, że w kolumnie **gotowy** jest teraz wyświetlana **2/2** kontenery, które są gotowe do użytku w. Drugi kontener jest serwerem proxy przyczepki wysłannika.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Możemy przeanalizować pod kątem, aby wyświetlić serwer proxy wysłannika, uruchamiając polecenie opisz, aby wyświetlić konfigurację.

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

Upewnij się, że aplikacja nadal działa po iniekcji serwera proxy wysłannika przyczepki.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Dołączanie istniejących wdrożonych aplikacji z zasadą ograniczania ruchu "Open Service siatk (OSM)" skonfigurowanych jako fałszywe

W przypadku ustawienia konfiguracji OSM dla zasad ruchu przystosowanego do `false` usługi OSM będzie wymagała jawnych zasad dostępu do ruchu [SMI](https://smi-spec.io/) , wdrożonych na potrzeby komunikacji między usługami w ramach klastra. Obecnie OSM używa również kont usługi Kubernetes w ramach autoryzacji komunikacji między usługami. Aby upewnić się, że istniejące wdrożone aplikacje będą komunikować się, gdy są zarządzane przez siatkę OSM, konieczne będzie zweryfikowanie istnienia konta usługi do użycia, zaktualizowanie wdrożenia aplikacji przy użyciu informacji o koncie usługi, zastosowanie zasad dostępu do ruchu [SMI](https://smi-spec.io/) .

#### <a name="verify-kubernetes-service-accounts"></a>Sprawdź konta usług Kubernetes

Sprawdź, czy masz konto usługi Kubernetes w przestrzeni nazw, w której aplikacja jest wdrożona.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

W poniższej tabeli znajduje się konto usługi o nazwie `bookbuyer` w przestrzeni nazw bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Jeśli nie masz konta usługi wymienionego poza kontem domyślnym, musisz utworzyć je dla swojej aplikacji. Użyj poniższego polecenia, aby utworzyć konto usługi w wdrożonej przestrzeni nazw aplikacji.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Wyświetlanie bieżącej specyfikacji wdrożenia aplikacji

Jeśli użytkownik musiał utworzyć konto usługi z wcześniejszej sekcji, prawdopodobnie wdrożenie aplikacji nie jest skonfigurowane z określonym `serviceAccountName` w specyfikacji wdrożenia. Możemy wyświetlić specyfikację wdrożenia aplikacji za pomocą następujących poleceń:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Lista wdrożeń zostanie wyświetlona w danych wyjściowych.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Teraz opiszemy wdrożenie, aby sprawdzić, czy na liście znajduje się konto usługi wymienione w sekcji szablon pod.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

W tym konkretnym wdrożeniu można zobaczyć, że istnieje konto usługi skojarzone z wdrożeniem wymienionym w sekcji szablon pod. To wdrożenie korzysta z konta usługi bookbuyer. Jeśli nie widzisz właściwości **konto Service:** , wdrożenie nie jest skonfigurowane do korzystania z konta usługi.

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

Istnieje kilka technik aktualizowania wdrożenia, aby dodać konto usługi Kubernetes. Zapoznaj się z dokumentacją Kubernetes na temat aktualizacji wbudowanego [wdrożenia](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) lub [Skonfiguruj konta usług dla zasobników](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). Po zaktualizowaniu specyfikacji wdrożenia za pomocą konta usługi należy ponownie wdrożyć wdrożenie (polecenia kubectl Apply-f The-Deployment. YAML) wdrożenia w klastrze.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Wdróż wymagane zasady interfejsu sieci, SMI

Ostatnim krokiem w celu zezwolenia na ruch przychodzący do przepływu w sieci to wdrożenie niezbędnych zasad dostępu do ruchu [SMI](https://smi-spec.io/) dla aplikacji. Konfiguracja, którą można osiągnąć za pomocą zasad dostępu do ruchu [SMI](https://smi-spec.io/) , wykracza poza zakres tego instruktażu, ale podajemy szczegółowo niektóre ze wspólnych składników specyfikacji i pokazują, jak skonfigurować proste zasady TrafficTarget i HTTPRouteGroup, aby umożliwić komunikację między usługami dla aplikacji.

Specyfikacja [](https://smi-spec.io/) [**Access Control przesyłania danych**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) SMI umożliwia użytkownikom Definiowanie zasad kontroli dostępu dla aplikacji. Będziemy skupić się na zasobach interfejsu API **TrafficTarget** i **HTTPRoutGroup** .

Zasób TrafficTarget składa się z trzech głównych ustawień konfiguracji, zasad i źródeł. Poniżej przedstawiono przykład TrafficTarget.

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

W powyższej specyfikacji TrafficTarget oznacza to, `destination` że konto usługi skonfigurowane dla docelowej usługi źródłowej. Należy pamiętać, że konto usługi, które zostało dodane do wdrożenia wcześniej, będzie używane do autoryzacji dostępu do wdrożenia, do którego jest dołączone. `rules`Sekcja w tym konkretnym przykładzie definiuje typ ruchu HTTP, który jest dozwolony za pośrednictwem połączenia. Można skonfigurować dokładne wzorce wyrażeń regularnych dla nagłówków HTTP, aby określić, jaki ruch jest dozwolony za pośrednictwem protokołu HTTP. `sources`Sekcja to usługa, która pochodzi z komunikacji. Ta specyfikacja odczytuje bookbuyer musi komunikować się z księgarni.

Zasób HTTPRouteGroup składa się z jednej lub tablicy dopasowania informacji nagłówka HTTP i jest wymagana dla specyfikacji TrafficTarget. W poniższym przykładzie można zobaczyć, że HTTPRouteGroup ma autoryzację trzech akcji HTTP, dwa GET i jeden wpis.

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

Jeśli nie znasz typu ruchu HTTP, aplikacja frontonu wykonuje inne warstwy aplikacji, ponieważ Specyfikacja TrafficTarget wymaga reguły, można utworzyć odpowiednik reguły Zezwalaj na wszystkie przy użyciu poniższej specyfikacji dla HTTPRouteGroup.

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

Po skonfigurowaniu specyfikacji TrafficTarget i HTTPRouteGroup można umieścić je razem jako jeden YAML i wdrożyć. Poniżej znajduje się Przykładowa konfiguracja księgarni.

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

Więcej szczegółowych informacji na temat specyfikacji można znaleźć w witrynie [SMI](https://smi-spec.io/) .

### <a name="manage-the-applications-namespace-with-osm"></a>Zarządzanie przestrzenią nazw aplikacji za pomocą OSM

Następnie skonfigurujemy OSM do zarządzania przestrzenią nazw i ponownie uruchamiaj wdrożenia, aby uzyskać serwer proxy przyczepki wysłannika, który został dodany do aplikacji.

Uruchom następujące polecenie, aby skonfigurować `azure-vote` przestrzeń nazw, która ma być zarządzana przez moją OSM.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Następnie ponownie uruchom oba `azure-vote-front` `azure-vote-back` wdrożenia z następującymi poleceniami.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

W przypadku wyświetlenia zasobników dla `azure-vote` przestrzeni nazw zobaczymy **gotowy** etap obu `azure-vote-front` i `azure-vote-back` jako 2/2, co oznacza, że serwer proxy przyczepki wysłannika został dodany wraz z aplikacją.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Samouczek: wdrażanie aplikacji zarządzanej przez usługę Open Service siatk (OSM) przy użyciu usługi NGINX

Open Service siatk (OSM) to lekki, rozszerzalny, natywny, oparty na chmurze siatka usług, która umożliwia użytkownikom jednorodne zarządzanie, zabezpieczanie i uzyskiwanie wbudowanych funkcji związanych z obsługą w środowiskach wysoce dynamicznych.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> - Wyświetl bieżącą konfigurację klastra OSM
> - Utwórz przestrzenie nazw dla OSM, aby zarządzać wdrożonymi aplikacjami w przestrzeniach nazw
> - Dołącz przestrzenie nazw, które mają być zarządzane przez OSM
> - Wdrażanie aplikacji przykładowej
> - Weryfikowanie aplikacji uruchomionej w klastrze AKS
> - Tworzenie kontrolera NGINX ingresd używanego dla aplikacji
> - Uwidacznianie usługi za pośrednictwem platformy Azure Application Gateway dostęp do Internetu

### <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach przedstawionych w tym artykule przyjęto założenie, że został utworzony klaster AKS (Kubernetes `1.19+` lub nowszy z włączoną obsługą kontroli dostępu Kubernetes), nawiązać `kubectl` połączenie z klastrem (Jeśli potrzebna jest pomoc przy użyciu dowolnego z tych elementów, zobacz Przewodnik po programie [AKS](./kubernetes-walkthrough.md)i zainstalowano dodatek AKS OSM).

Wymagane są następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
- `aks-preview`Rozszerzenie w wersji 0.5.5 lub nowszej
- OSM w wersji v 0.8.0 lub nowszej
- apt — Pobierz JQ instalacji

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Wyświetl i sprawdź bieżącą konfigurację klastra OSM

Po włączeniu dodatku OSM dla AKS w klastrze AKS można wyświetlić bieżące parametry konfiguracji w OSM-config Kubernetes ConfigMap. Uruchom następujące polecenie, aby wyświetlić właściwości ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Dane wyjściowe przedstawiają bieżącą konfigurację OSM dla klastra.

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

Zauważ, że **permissive_traffic_policy_mode** jest skonfigurowany na **wartość true**. Tryb zasady ograniczania ruchu w OSM to tryb, w którym pominięto wymuszanie zasad ruchu [SMI](https://smi-spec.io/) . W tym trybie program OSM automatycznie odnajduje usługi, które są częścią reguł zasad ruchu w sieci i narzędzi usług, na każdej przyczepce usługi wysłannika proxy, aby móc komunikować się z tymi usługami.

### <a name="create-namespaces-for-the-application"></a>Tworzenie przestrzeni nazw dla aplikacji

W tym samouczku będziemy używać aplikacji OSM księgarni, która zawiera następujące składniki aplikacji:

- bookbuyer
- bookthief
- księgarni
- bookwarehouse

Utwórz obszary nazw dla każdego z tych składników aplikacji.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Dołącz przestrzenie nazw, które mają być zarządzane przez OSM

Dodanie przestrzeni nazw do siatki OSM umożliwi kontrolerowi OSM automatyczne wprowadzanie kontenerów proxy przyczepki wysłannika do aplikacji. Uruchom następujące polecenie, aby dołączyć przestrzenie nazw aplikacji OSM księgarni.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Wdrażanie aplikacji księgarni w klastrze AKS

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

Wszystkie dane wyjściowe wdrożenia są zestawione poniżej.

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

Zaktualizuj usługę bookbuyer do odpowiedniej konfiguracji portów przychodzących przy użyciu następującego manifestu usługi.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Weryfikowanie aplikacji księgarni działającej w klastrze AKS

Obecnie wdrożono aplikację kontenera księgarni Mulit, ale jest ona dostępna tylko z poziomu klastra AKS. Później dodamy kontroler usługi Azure Application Gateway w celu udostępnienia aplikacji poza klastrem AKS. Aby sprawdzić, czy aplikacja działa w ramach klastra, zostanie użyty port do przodu w celu wyświetlenia interfejsu użytkownika składnika bookbuyer.

Najpierw Pobierz nazwę bookbuyer pod

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Twoje bookbuyer pod nazwą będzie mieć unikatową nazwę.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Po określeniu nazwy tego elementu możemy teraz użyć polecenia do przesyłania dalej portów, aby skonfigurować tunel z naszego systemu lokalnego do aplikacji wewnątrz klastra AKS. Uruchom następujące polecenie, aby skonfigurować port do przodu dla lokalnego portu systemowego 8080. Ponownie Użyj określonego bookbuyer pod nazwą.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Powinny pojawić się dane wyjściowe podobne do tego.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Podczas sesji przekazywania portów przejdź do następującego adresu URL w przeglądarce `http://localhost:8080` . Teraz będzie można zobaczyć interfejs użytkownika aplikacji bookbuyer w przeglądarce podobnej do poniższego obrazu.

![OSM bookbuyer aplikacji dla interfejsu użytkownika NGINX](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Tworzenie kontrolera NGINX Ingress w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

Będziemy używać kontrolera transferu danych przychodzących, aby uwidocznić aplikację zarządzaną przez OSM w Internecie. Aby utworzyć kontroler transferu danych przychodzących, należy użyć Helm do zainstalowania Nginx — ruch przychodzący. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Nie należy go uruchamiać w węzłach z systemem Windows Server. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o nazwie transfery _-Basic_. W razie potrzeby określ przestrzeń nazw dla własnego środowiska.

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

Gdy usługa równoważenia obciążenia Kubernetes jest tworzona dla kontrolera NGINX, zostanie przypisany dynamiczny publiczny adres IP, jak pokazano w następujących przykładowych danych wyjściowych:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Nie utworzono jeszcze żadnych reguł dotyczących ruchu przychodzącego, więc w przypadku przechodzenia do wewnętrznego adresu IP zostanie wyświetlona domyślna strona 404 kontrolera NGINX. Reguły dotyczące transferu danych przychodzących są konfigurowane w poniższych krokach.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Uwidacznianie usługi bookbuyer w Internecie

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

### <a name="view-the-nginx-logs"></a>Wyświetlanie dzienników NGINX

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

Po pomyślnym zastosowaniu reguły transferu danych wyjściowych zostanie wyświetlony stan kontrolera NGINX Ingress:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Wyświetlanie usług NGINX Services i bookbuyer z zewnątrz

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Ponieważ nazwa hosta w manifeście danych przychodzących jest nazwą psuedo służącą do testowania, nazwa DNS nie będzie dostępna w Internecie. Możemy Alternatywnie użyć programu zwinięcie i wkleić nagłówek hostname do publicznego adresu IP NGINX i odebrać kod 200, który pomyślnie nawiąże połączenie z usługą bookbuyer.

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

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Samouczek: wdrażanie aplikacji zarządzanej przez usługę Open Service siatk (OSM) za pomocą dodatku Azure Application Gateway transferu danych przychodzących AKS

Open Service siatk (OSM) to lekki, rozszerzalny, natywny, oparty na chmurze siatka usług, która umożliwia użytkownikom jednorodne zarządzanie, zabezpieczanie i uzyskiwanie wbudowanych funkcji związanych z obsługą w środowiskach wysoce dynamicznych.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> - Wyświetl bieżącą konfigurację klastra OSM
> - Utwórz przestrzenie nazw dla OSM, aby zarządzać wdrożonymi aplikacjami w przestrzeniach nazw
> - Dołącz przestrzenie nazw, które mają być zarządzane przez OSM
> - Wdrażanie aplikacji przykładowej
> - Weryfikowanie aplikacji uruchomionej w klastrze AKS
> - Utwórz Application Gateway platformy Azure, który będzie używany jako kontroler transferu danych przychodzących dla aplikacji
> - Uwidacznianie usługi za pośrednictwem platformy Azure Application Gateway dostęp do Internetu

### <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach przedstawionych w tym artykule przyjęto założenie, że został utworzony klaster AKS (Kubernetes `1.19+` lub nowszy z włączoną obsługą kontroli dostępu Kubernetes), nawiązać `kubectl` połączenie z klastrem (Jeśli potrzebujesz pomocy dotyczącej któregokolwiek z tych elementów, zobacz Przewodnik po usłudze [AKS](./kubernetes-walkthrough.md), ZAinstalowano dodatek AKS OSM) i zostanie utworzony nowy Application Gateway platformy Azure na potrzeby transferu danych przychodzących.

Wymagane są następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
- `aks-preview`Rozszerzenie w wersji 0.5.5 lub nowszej
- AKS w wersji 1.19 + przy użyciu usługi Azure CNI Network (dołączona do sieci wirtualnej platformy Azure)
- OSM w wersji v 0.8.0 lub nowszej
- apt — Pobierz JQ instalacji

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Wyświetl i sprawdź bieżącą konfigurację klastra OSM

Po włączeniu dodatku OSM dla AKS w klastrze AKS można wyświetlić bieżące parametry konfiguracji w OSM-config Kubernetes ConfigMap. Uruchom następujące polecenie, aby wyświetlić właściwości ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Dane wyjściowe przedstawiają bieżącą konfigurację OSM dla klastra.

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

Zauważ, że **permissive_traffic_policy_mode** jest skonfigurowany na **wartość true**. Tryb zasady ograniczania ruchu w OSM to tryb, w którym pominięto wymuszanie zasad ruchu [SMI](https://smi-spec.io/) . W tym trybie program OSM automatycznie odnajduje usługi, które są częścią reguł zasad ruchu w sieci i narzędzi usług, na każdej przyczepce usługi wysłannika proxy, aby móc komunikować się z tymi usługami.

### <a name="create-namespaces-for-the-application"></a>Tworzenie przestrzeni nazw dla aplikacji

W tym samouczku będziemy używać aplikacji OSM księgarni, która zawiera następujące składniki aplikacji:

- bookbuyer
- bookthief
- księgarni
- bookwarehouse

Utwórz obszary nazw dla każdego z tych składników aplikacji.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Dołącz przestrzenie nazw, które mają być zarządzane przez OSM

Po dodaniu przestrzeni nazw do siatki OSM umożliwia kontrolerowi OSM automatyczne wprowadzanie kontenerów proxy przyczepki wysłannika do aplikacji. Uruchom następujące polecenie, aby dołączyć przestrzenie nazw aplikacji OSM księgarni.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Wdrażanie aplikacji księgarni w klastrze AKS

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

Wszystkie dane wyjściowe wdrożenia są zestawione poniżej.

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

Zaktualizuj usługę bookbuyer do odpowiedniej konfiguracji portów przychodzących przy użyciu następującego manifestu usługi.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Weryfikowanie aplikacji księgarni działającej w klastrze AKS

Obecnie wdrożono aplikację księgarni z obsługą wielokontenera, ale jest ona dostępna tylko z poziomu klastra AKS. Później dodamy kontroler usługi Azure Application Gateway w celu udostępnienia aplikacji poza klastrem AKS. Aby sprawdzić, czy aplikacja działa w ramach klastra, zostanie użyty port do przodu w celu wyświetlenia interfejsu użytkownika składnika bookbuyer.

Najpierw Pobierz nazwę bookbuyer pod

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Powinny pojawić się dane wyjściowe podobne do poniższych. Twoje bookbuyer pod nazwą będzie mieć unikatową nazwę.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Po określeniu nazwy tego elementu możemy teraz użyć polecenia do przesyłania dalej portów, aby skonfigurować tunel z naszego systemu lokalnego do aplikacji wewnątrz klastra AKS. Uruchom następujące polecenie, aby skonfigurować port do przodu dla lokalnego portu systemowego 8080. Ponownie Użyj konkretnej nazwy bookbuyer pod.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Powinny pojawić się dane wyjściowe podobne do tego.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Podczas sesji przekazywania portów przejdź do następującego adresu URL w przeglądarce `http://localhost:8080` . Teraz będzie można zobaczyć interfejs użytkownika aplikacji bookbuyer w przeglądarce podobnej do poniższego obrazu.

![Aplikacja OSM bookbuyer dla obrazu interfejsu użytkownika usługi App Gateway](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Tworzenie Application Gateway platformy Azure w celu udostępnienia aplikacji bookbuyer poza klastrem AKS

> [!NOTE]
> Poniższe wskazówki spowodują utworzenie nowego wystąpienia Application Gateway platformy Azure, które będzie używane na potrzeby transferu danych przychodzących. Jeśli masz istniejący Application Gateway platformy Azure, którego chcesz użyć, przejdź do sekcji umożliwiającej włączenie dodatku Application Gateway transferu danych przychodzących.

#### <a name="deploy-a-new-application-gateway"></a>Wdróż nowy Application Gateway

> [!NOTE]
> Odwołujemy się do istniejącej dokumentacji umożliwiającej włączenie dodatku Application Gateway transferu danych przychodzących dla istniejącego klastra AKS. Wprowadzono pewne modyfikacje w celu uzyskania przydatności do materiałów OSM. Bardziej szczegółową dokumentację dotyczącą tematu można znaleźć [tutaj](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing).

Teraz zostanie wdrożony nowy Application Gateway, aby symulować posiadanie istniejącej _Application Gateway, która_ ma być używana do równoważenia obciążenia ruchu sieciowego do klastra AKS. Nazwa Application Gateway będzie _myApplicationGateway_, ale musisz najpierw utworzyć zasób publicznego adresu IP o nazwie _myPublicIp_ i nową sieć wirtualną o nazwie _myVnet_ z przestrzenią adresową 11.0.0.0/8 i podsieć z przestrzenią adresową 11.1.0.0/16 o nazwie Moja _podsieć_, a następnie wdrożyć Application Gateway w _podsieci_ przy użyciu _myPublicIp_.

W przypadku korzystania z klastra AKS i Application Gateway w oddzielnych sieciach wirtualnych, przestrzenie adresowe dwóch sieci wirtualnych nie mogą nakładać się na siebie. Domyślną przestrzenią adresową, w której wdrażany jest klaster AKS, jest 10.0.0.0/8, więc ustawimy prefiks adresu sieci wirtualnej Application Gateway na 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Dodatek Application Gateway Management Controller (AGIC) obsługuje **tylko** wersje SKU Application Gateway v2 (standard i WAF), a **nie** jednostki SKU Application Gateway v1.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Włączanie dodatku AGIC dla istniejącego klastra AKS za pomocą interfejsu wiersza polecenia platformy Azure

Jeśli chcesz kontynuować korzystanie z interfejsu wiersza polecenia platformy Azure, możesz w dalszym ciągu włączyć dodatek AGIC w utworzonym klastrze AKS, utworzyć _klaster_ i określić dodatek AGIC, aby korzystał z istniejących utworzonych Application Gateway, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Możesz sprawdzić, czy dodatek AKS platformy Application Gateway Azure został włączony przez następujące polecenie.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

To polecenie powinno wyświetlać dane wyjściowe jako `true` .

#### <a name="peer-the-two-virtual-networks-together"></a>Łączenie równorzędne dwóch sieci wirtualnych

Ze względu na to, że klaster AKS został wdrożony we własnej sieci wirtualnej, a Application Gateway w innej sieci wirtualnej, należy połączyć równorzędnie dwie sieci wirtualne w celu przepływu ruchu z Application Gateway do zasobników w klastrze. Komunikacja równorzędna między dwiema sieciami wirtualnymi wymaga ponownego uruchomienia polecenia platformy Azure w celu zapewnienia dwukierunkowego połączenia. Pierwsze polecenie spowoduje utworzenie połączenia komunikacji równorzędnej z Application Gateway sieci wirtualnej z siecią wirtualną AKS. Drugie polecenie spowoduje utworzenie połączenia komunikacji równorzędnej w innym kierunku.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Uwidacznianie usługi bookbuyer w Internecie

Zastosuj następujący manifest danych przychodzących do klastra AKS, aby uwidocznić usługę bookbuyer w Internecie za pośrednictwem Application Gateway platformy Azure.

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

Ponieważ nazwa hosta w manifeście danych wejściowych jest używana do testowania, nazwa DNS nie będzie dostępna w Internecie. Możemy Alternatywnie użyć programu zwinięcie i wkleić nagłówek hostname do publicznego adresu IP usługi Azure Application Gateway i odebrać kod 200, który pomyślnie nawiąże połączenie z usługą bookbuyer.

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

- [Dokumentacja dotycząca rozwiązywania problemów AGIC](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Dodatkowe narzędzia do rozwiązywania problemów są dostępne w repozytorium GitHub AGIC](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>OSM i możliwości monitorowania za pomocą usług Azure Monitor i Application Insights

Zarówno Azure Monitor, jak i Azure Application Insights ułatwiają zmaksymalizowanie dostępności i wydajności aplikacji i usług, udostępniając kompleksowe rozwiązanie do zbierania, analizowania i działania na telemetrii w środowiskach w chmurze i lokalnych.

Dodatek OSM AKS będzie miał ścisłą integrację z obiema usługami platformy Azure i oferuje niezawodne środowisko platformy Azure do wyświetlania i reagowania na krytyczne kluczowe wskaźniki wydajności udostępniane przez metryki OSM. Aby uzyskać więcej informacji na temat sposobu włączania i konfigurowania tych usług dla dodatku OSM AKS, odwiedź stronę [Azure monitor dla OSM](https://aka.ms/azmon/osmpreview) , aby uzyskać więcej informacji.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Samouczek: ręczne wdrażanie Prometheus, Grafana i Jaeger, aby wyświetlić metryki otwartej sieci usługi (OSM)

> [!WARNING]
> Instalacja elementów Prometheus, Grafana i Jaeger jest świadczona jako ogólne wskazówki, które pokazują, jak te narzędzia mogą być wykorzystywane do wyświetlania danych metryk OSM. Wskazówki dotyczące instalacji nie mają być wykorzystywane w przypadku konfiguracji produkcyjnej. Zapoznaj się z dokumentacją każdego narzędzia, jak najlepiej dostosować swoje instalacje do Twoich potrzeb. Najbardziej istotny jest brak trwałego magazynu, co oznacza, że wszystkie dane są tracone po Prometheus Grafana, i/lub Jaeger pod (s).

Usługa Open Service siatk (OSM) generuje szczegółowe metryki związane ze wszystkimi ruchem w sieci. Te metryki zapewniają wgląd w zachowanie aplikacji w sieci, ułatwiając użytkownikom rozwiązywanie problemów, konserwację i analizowanie aplikacji.

Od dzisiaj OSM zbiera metryki bezpośrednio z serwerów proxy przyczepki (wysłannika). OSM zapewnia zaawansowane metryki ruchu przychodzącego i wychodzącego dla wszystkich usług w sieci. Za pomocą tych metryk użytkownik może uzyskać informacje o całkowitym natężeniu ruchu, błędach ruchu oraz czasie odpowiedzi na żądania.

OSM używa Prometheus do zbierania i przechowywania spójnych metryk ruchu i statystyk dla wszystkich aplikacji działających w sieci. Prometheus to zestaw narzędzi do monitorowania i generowania alertów Open Source, który jest często używany w środowiskach (ale nie ograniczonych do) Kubernetes i usług sieci.

Każda aplikacja, która jest częścią siatki, działa w obszarze, który zawiera wysłannika przyczepki, która udostępnia metryki (metryki serwera proxy) w formacie Prometheus. Ponadto każdy element, który jest częścią siatki, zawiera adnotacje Prometheus, co pozwala serwerowi Prometheus na dynamiczne odróżnienie aplikacji. Ten mechanizm automatycznie włącza odróżnienie metryk przy każdym dodawaniu nowej przestrzeni nazw/pod/usługi do siatki.

Metryki OSM można wyświetlać przy użyciu Grafana, czyli oprogramowania do wizualizacji i analizy Open Source. Pozwala to na wykonywanie zapytań, wizualizowanie, tworzenie alertów i eksplorowanie metryk.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> - Tworzenie i wdrażanie wystąpienia Prometheus
> - Konfigurowanie OSM w celu zezwalania na Prometheus odpadków
> - Aktualizowanie Configmap Prometheus
> - Tworzenie i wdrażanie wystąpienia Grafana
> - Konfigurowanie Grafana za pomocą źródła danych Prometheus
> - Importuj pulpit nawigacyjny OSM dla Grafana
> - Tworzenie i wdrażanie wystąpienia Jaeger
> - Skonfiguruj śledzenie Jaeger dla OSM

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Wdróż i skonfiguruj wystąpienie Prometheus dla OSM

Użyjemy Helm do wdrożenia wystąpienia Prometheus. Uruchom następujące polecenia, aby zainstalować Prometheus za pośrednictwem Helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Jeśli instalacja zakończyła się pomyślnie, należy zobaczyć podobne dane wyjściowe poniżej. Zwróć uwagę na port serwera Prometheus i nazwę DNS klastra. Te informacje będą używane później do konfigurowania Prometheus jako źródła danych dla Grafana.

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

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Konfigurowanie OSM w celu zezwalania na Prometheus odpadków

Aby upewnić się, że składniki OSM są skonfigurowane pod kątem Prometheusch wycinków, należy sprawdzić konfigurację **prometheus_scraping** znajdującą się w pliku konfiguracji OSM-config. Wyświetl konfigurację przy użyciu następującego polecenia:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

Dane wyjściowe poprzedniego polecenia powinny zwrócić, `true` Jeśli OSM jest skonfigurowany do Prometheus odpadków. Jeśli zwrócona wartość to `false` , musimy zaktualizować konfigurację `true` . Uruchom następujące polecenie, **Aby włączyć OSM** Prometheus:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Powinny zostać wyświetlone następujące dane wyjściowe.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Aktualizowanie Configmap Prometheus

Domyślna instalacja programu Prometheus będzie zawierać dwie Kubernetes configmaps. Listę Prometheus configmaps można wyświetlić za pomocą następującego polecenia.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Musimy wymienić konfigurację Prometheus. yml znajdującą się w **Prometheus-Server** configmap z następującą konfiguracją OSM. Do wykonania tego zadania istnieje kilka technik edycji plików. Prostym i bezpiecznym sposobem jest wyeksportowanie configmap, utworzenie kopii dla kopii zapasowej, a następnie edytowanie jej przy użyciu edytora, takiego jak Visual Studio Code.

> [!NOTE]
> Jeśli nie masz zainstalowanego Visual Studio Code, możesz go pobrać i zainstalować w [tym miejscu](https://code.visualstudio.com/Download).

Najpierw wyeksportuj **stabilny Prometheus serwera** configmap, a następnie utwórz kopię zapasową.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Następnie otwórz plik przy użyciu Visual Studio Code do edycji.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Po otwarciu configmap w edytorze Visual Studio Code Zastąp plik Prometheus. yml plikiem z konfiguracją OSM poniżej i Zapisz plik.

> [!WARNING]
> Niezwykle ważne jest, aby zachować strukturę wcięcia pliku YAML. Wszelkie zmiany struktury pliku YAML mogą spowodować, że configmap nie można ponownie zastosować.

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

Zastosuj zaktualizowany plik YAML configmap za pomocą następującego polecenia.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Może zostać wyświetlony komunikat o braku potrzebnej adnotacji Kubernetes. Można to teraz zignorować.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Sprawdź, czy Prometheus jest skonfigurowany pod kątem wycinków siatki OSM i punktów końcowych interfejsu API

Aby sprawdzić, czy Prometheus jest prawidłowo skonfigurowany pod kątem wycinków siatki OSM i punktów końcowych interfejsu API, nastąpi przejście do usługi Prometheus pod i wyświetlenie konfiguracji docelowej. Uruchom następujące polecenia.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Otwórz przeglądarkę do `http://localhost:9090/targets`

Jeśli przewiniesz w dół, powinny być widoczne wszystkie Stany punktów końcowych metryk SMI **, jak również** inne metryki OSM zdefiniowane w poniższej ilustracji.

![Obraz interfejsu użytkownika metryk OSM Prometheus Target](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Wdróż i skonfiguruj wystąpienie Grafana dla OSM

Użyjemy Helm do wdrożenia wystąpienia Grafana. Uruchom następujące polecenia, aby zainstalować Grafana za pośrednictwem Helm:

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

Następnie będziemy pobierać Grafana na port do przodu do pulpitu nawigacyjnego Grafana w celu zalogowania się.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Otwórz przeglądarkę do `http://localhost:3000`

Na ekranie logowania poniżej wpisz **administrator** jako nazwę użytkownika i Użyj hasła Grafana przechwycone wcześniej.

![Obraz interfejsu użytkownika strony logowania OSM Grafana](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Konfigurowanie źródła danych Grafana Prometheus

Po pomyślnym zalogowaniu się do usługi Grafana następnym krokiem jest dodanie Prometheus jako źródeł danych dla Grafana. W tym celu przejdź do ikony konfiguracja w menu po lewej stronie i wybierz pozycję źródła danych, jak pokazano poniżej.

![Obraz interfejsu użytkownika strony OSM Grafana DataSources](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Kliknij przycisk **Dodaj źródło danych** i wybierz pozycję Prometheus w obszarze bazy danych szeregów czasowych.

![Obraz interfejsu użytkownika strony wyboru OSM Grafana](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Na stronie **Konfiguruj źródło danych Prometheus poniżej** wprowadź nazwę FQDN klastra Kubernetes dla usługi Prometheus dla ustawienia adresu URL protokołu HTTP. Domyślną nazwą FQDN powinna być `stable-prometheus-server.default.svc.cluster.local` . Po wprowadzeniu tego punktu końcowego usługi Prometheus przewiń w dół strony i wybierz pozycję **zapisz & test**. Powinno zostać wyświetlone zielony znacznik wyboru wskazujący, że źródło danych działa.

#### <a name="importing-osm-dashboards"></a>Importowanie pulpitów nawigacyjnych OSM

Pulpity nawigacyjne OSM są dostępne w programie:

- [Naszym repozytorium](/charts/osm/grafana)i są one nieportem obiektów BLOB w formacie JSON za pomocą portalu administratora sieci Web
- lub [online pod adresem Grafana.com](https://grafana.com/grafana/dashboards/14145)

Aby zaimportować pulpit nawigacyjny, poszukaj `+` w menu po lewej stronie i wybierz pozycję `import` .
Pulpit nawigacyjny można bezpośrednio zaimportować według identyfikatora `Grafana.com` . Na przykład nasz `OSM Mesh Details` pulpit nawigacyjny używa identyfikatora `14145` , można użyć identyfikatora bezpośrednio w formularzu i wybrać `import` :

![Obraz interfejsu użytkownika funkcji importowania strony pulpitu nawigacyjnego OSM Grafana](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Po wybraniu opcji Importuj spowoduje to automatyczne przełączenie do zaimportowanego pulpitu nawigacyjnego.

![Obraz interfejsu użytkownika strony szczegółów siatki pulpitu nawigacyjnego OSM Grafana](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Wdróż i skonfiguruj operator Jaeger na Kubernetes dla OSM

[Jaeger](https://www.jaegertracing.io/) to system śledzenia "open source" używany do monitorowania i rozwiązywania problemów z systemami rozproszonymi. Można ją wdrożyć za pomocą OSM jako nowego wystąpienia lub można przenieść własne wystąpienie. W poniższych instrukcjach wdrożono nowe wystąpienie Jaeger do `jaeger` przestrzeni nazw w KLASTRZE AKS.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Wdrażanie Jaeger w klastrze AKS

Zastosuj następujący manifest, aby zainstalować Jaeger:

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

#### <a name="enable-tracing-for-the-osm-add-on"></a>Włącz śledzenie dla dodatku OSM

Następnie będziemy musieli włączyć śledzenie dla dodatku OSM.

> [!NOTE]
> Obecnie właściwości śledzenia nie są widoczne w OSM-config configmap. Zostanie to widoczne w nowej wersji dodatku OSM AKS.

Uruchom następujące polecenie, aby włączyć śledzenie dla dodatku OSM:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Wyświetlanie interfejsu użytkownika Jaeger z przekazywaniem portów

Interfejs użytkownika Jaeger jest uruchomiony na porcie 16686. Aby wyświetlić interfejs użytkownika sieci Web, można użyć portu polecenia kubectl-Forward:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

W przeglądarce powinna zostać wyświetlona lista rozwijana usługi, która umożliwia wybór spośród różnych aplikacji wdrożonych przez demonstrację księgarni. Wybierz usługę, aby wyświetlić wszystkie jej zakresy. Na przykład w przypadku wybrania opcji bookbuyer z Lookbackem jednej godziny można zobaczyć swoje interakcje z księgarni-V1 i księgarni-v2 posortowane według czasu.

![Obraz interfejsu użytkownika strony śledzenia Jaeger OSM](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Wybierz dowolny element, aby wyświetlić go szczegółowo. Wybierz wiele elementów, aby porównać ślady. Można na przykład porównać interakcje bookbuyer z księgarni i księgarni-v2 w danym momencie.

Możesz również wybrać kartę architektura systemu, aby wyświetlić wykres dotyczący sposobu, w jaki różne aplikacje przepracowali/komunikują się. Jest to pomysł dotyczący przepływu ruchu między aplikacjami.

![Obraz interfejsu użytkownika architektury systemowej OSM Jaeger](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Przewodnik dotyczący rozwiązywania problemów z dodatkiem Service siatk (OSM) AKS

Po wdrożeniu dodatku OSM AKS może czasem wystąpić problem. Poniższe przewodniki ułatwiają rozwiązywanie problemów z błędami i rozwiązywanie typowych problemów.

### <a name="verifying-and-troubleshooting-osm-components"></a>Weryfikowanie i rozwiązywanie problemów ze składnikami OSM

#### <a name="check-osm-controller-deployment"></a>Sprawdź wdrożenie kontrolera OSM

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

W dobrej kondycji kontroler OSM będzie wyglądać następująco:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Sprawdź kontroler OSM pod

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Dobra OSM pod uwagę będzie wyglądać następująco:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Mimo że mamy jeden kontroler wykluczony w pewnym momencie, mamy inną, która jest gotowa 1/1 i uruchomiona z 0 ponownych uruchomień. Jeśli kolumna jest gotowa, inne niż 1/1, jest w stanie uszkodzenia.
Kolumna gotowa z 0/1 wskazuje, że kontener płaszczyzny kontroli ulega awarii — musimy uzyskać dzienniki. Zobacz sekcję Pobieranie dzienników kontrolera OSM z centrum pomocy technicznej platformy Azure poniżej. Kolumna jest gotowa z liczbą większą niż 1 po wskazaniu, że są zainstalowane przyczepki. Kontroler OSM prawdopodobnie nie będzie działał z dołączonymi do niej przyczepami.

> [!NOTE]
> Począwszy od wersji v 0.8.2 kontroler OSM nie jest w trybie HA i zostanie uruchomiony w wdrożonej z liczbą replik 1 — pojedyncza pod. Na stronie pod musi być sondy kondycji i zostanie on ponownie uruchomiony przez kubelet, jeśli będzie to konieczne.

#### <a name="check-osm-controller-service"></a>Sprawdź usługę kontrolera OSM

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Usługa OSM kontrolera w dobrej kondycji będzie wyglądać następująco:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> KLASTER-adres IP będzie inny. Nazwa i porty usługi muszą być takie same jak w powyższym przykładzie.

#### <a name="check-osm-controller-endpoints"></a>Sprawdź punkty końcowe kontrolera OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Prawidłowe punkty końcowe kontrolera OSM będą wyglądać następująco:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Sprawdź wdrożenie iniektora OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Prawidłowe wdrożenie iniektora OSM będzie wyglądać następująco:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Sprawdź OSM wtryskiwacz pod

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

W dobrej kondycji iniektora OSM pod uwagę będzie wyglądać:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Sprawdź usługę iniektora OSM

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

W dobrej kondycji usługa iniektora OSM będzie wyglądać następująco:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Sprawdź punkty końcowe OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Prawidłowy punkt końcowy OSM będzie wyglądać następująco:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Sprawdzanie poprawności i mutacja elementów webhook

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Element webhook OSM w dobrej kondycji będzie wyglądać następująco:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

W dobrej OSM mutacja elementu webhook będzie wyglądać następująco:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Sprawdź, czy usługa i pakiet urzędu certyfikacji sprawdzają poprawność elementu webhook

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Poprawnie skonfigurowana Walidacja konfiguracji elementu webhook będzie wyglądać dokładnie następująco:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Sprawdź, czy jest to usługa i pakiet urzędu certyfikacji przynoszący się do mutacji elementu webhook

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Dobrze skonfigurowana mutacja konfiguracji elementu webhook będzie wyglądać dokładnie tak jak w przypadku:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Sprawdź, czy kontroler OSM zakończył walidację (lub mutację) elementu webhook

> [!NOTE]
> Począwszy od wersji 0.8.2 należy wiedzieć, że AKS RP instaluje element webhook, AKS Reconcile, że istnieje, ale kontroler OSM jest tym, który wypełnił pakiet urzędu certyfikacji.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Ta liczba wskazuje liczbę bajtów lub rozmiar pakietu urzędu certyfikacji. Jeśli ta wartość jest pusta, 0 lub pewna liczba w obszarze 1000, wskazuje, że pakiet urzędu certyfikacji nie został poprawnie zainicjowany. Bez poprawnego zbioru urzędów certyfikacji Walidacja elementu webhook będzie zgłaszać błędy i uniemożliwiać użytkownikowi wprowadzanie zmian do OSM-config ConfigMap w przestrzeni nazw polecenia-system.

Przykład błędu, jeśli pakiet urzędu certyfikacji jest niepoprawny:

- Próba zmiany OSM-config ConfigMap:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Błąd:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Obejście tego problemu, gdy konfiguracja **weryfikacji** elementu webhook ma zły certyfikat:

- Opcja 1 — ponowne uruchomienie kontrolera OSM — spowoduje to ponowne uruchomienie kontrolera OSM. Po uruchomieniu spowoduje to zastąpienie pakietu urzędu certyfikacji zarówno w przypadku mutacji, jak i weryfikacji elementów webhook.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Opcja 2 — Opcja 2. Usunięcie walidacji elementu webhook — usunięcie elementu webhook powoduje, że mutacje `osm-config` ConfigMap nie są już weryfikowane. Dowolna poprawka zostanie przekazana. W pewnym momencie AKS uzgadnianie będzie mieć pewność, że istnieje Walidacja elementu webhook i zostanie utworzony ponownie. Aby można było szybko napisać pakiet urzędu certyfikacji, może być konieczne ponowne uruchomienie kontrolera OSM.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Opcja 3 — usunięcie i Poprawka: poniższe polecenie spowoduje usunięcie walidacji elementu webhook, co pozwoli na dodanie dowolnych wartości, a następnie natychmiast spróbuje zastosować poprawkę. Prawdopodobnie AKS uzgadniania nie będzie miał wystarczająco dużo czasu, aby uzgodnić i przywrócić weryfikację elementu webhook, dzięki czemu można zastosować zmianę jako ostatnią okazję:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Sprawdź `osm-config` **ConfigMap**

> [!NOTE]
> Kontroler OSM nie wymaga, aby `osm-config` ConfigMap był obecny w przestrzeni nazw polecenia-system. Kontroler ma uzasadnione wartości domyślne dla konfiguracji i może działać bez tego.

Sprawdź istnienie:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Sprawdź zawartość OSM-config ConfigMap

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

`osm-config` ConfigMap wartości:

| Klucz                              | Typ   | Dozwolone wartości                                          | Wartość domyślna                          | Funkcja                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ruch wychodzący                           | bool   | wartość true, false                                             | `"false"`                              | Włącza ruch wychodzący w sieci.                                                                                                                                                                                                             |
| enable_debug_server              | bool   | wartość true, false                                             | `"true"`                               | Włącza punkt końcowy debugowania na kontrolerze OSM pod kątem wyświetlania informacji dotyczących siatki, takich jak połączenia serwera proxy, certyfikaty i zasady SMI.                                                                                    |
| enable_privileged_init_container | bool   | wartość true, false                                             | `"false"`                              | Włącza uprzywilejowane kontenery init dla zasobników w sieci. W przypadku wartości false kontenery init mają tylko NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | ciąg | śledzenie, debugowanie, informacje, ostrzeżenie, ostrzeżenie, błąd, krytyczne, wyłączone | `"error"`                              | Ustawia Szczegółowość rejestrowania wysłannika na przyczepce serwera proxy, które mają zastosowanie tylko do nowo utworzonych zasobników łączących siatkę. Aby zaktualizować poziom dziennika dla istniejących zasobników, uruchom ponownie wdrożenie za pomocą programu `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | ciąg | rozdzielana przecinkami lista zakresów adresów IP w postaci a. b. c. d/x | `-`                                    | Globalna lista zakresów adresów IP, które mają zostać wykluczone z wychodzącego przechwycenia ruchu przez serwer proxy przyczepki.                                                                                                                                    |
| permissive_traffic_policy_mode   | bool   | wartość true, false                                             | `"false"`                              | Ustawienie na `true` , włącza tryb Zezwalaj-All na siatce, czyli brak wymuszania zasad ruchu w sieci. Jeśli jest ustawiona na `false` , włącza odmowa — wszystkie zasady ruchu w sieci, co `SMI Traffic Target` jest niezbędne do komunikacji usług. |
| prometheus_scraping              | bool   | wartość true, false                                             | `"true"`                               | Włącza metryki Prometheus dotyczące wycinków serwerów proxy.                                                                                                                                                                                 |
| service_cert_validity_duration   | ciąg | 24 godziny, 1h30m (czas trwania)                          | `"24h"`                                | Ustawia czas ważności certyfikatu usługi, reprezentowany jako sekwencja liczb dziesiętnych z opcjonalnym ułamkiem i sufiksem jednostki.                                                                                             |
| tracing_enable                   | bool   | wartość true, false                                             | `"false"`                              | Włącza śledzenie Jaeger dla siatki.                                                                                                                                                                                                    |
| tracing_address                  | ciąg | Jaeger. Mesh-Namespace. svc. Cluster. Local                 | `jaeger.kube-system.svc.cluster.local` | Adres wdrożenia Jaeger, jeśli śledzenie jest włączone.                                                                                                                                                                                |
| tracing_endpoint                 | ciąg | /api/v2/spans                                           | /api/v2/spans                          | Punkt końcowy dla danych śledzenia, jeśli włączono śledzenie.                                                                                                                                                                                          |
| tracing_port                     | int    | dowolna Niezerowa wartość całkowita                              | `"9411"`                               | Port, na którym włączone jest śledzenie.                                                                                                                                                                                                       |
| use_https_ingress                | bool   | wartość true, false                                             | `"false"`                              | Włącza ruch przychodzący HTTPS w sieci.                                                                                                                                                                                                      |
| config_resync_interval           | ciąg | w ciągu 1 minuty wyłączenie tej opcji                            | 0 (wyłączone)                           | Gdy podano wartość powyżej 1M (60s), kontroler OSM wyśle wszystkie dostępne konfiguracje do każdego połączonego wysłannika w danym interwale                                                                                                    |

#### <a name="check-namespaces"></a>Sprawdź przestrzenie nazw

> [!NOTE]
> Przestrzeń nazw polecenia-system nie będzie wchodzić w skład siatki usługi i nigdy nie będzie mieć etykiet i/lub nie ma adnotacji z kluczem/wartościami poniżej.

Używamy `osm namespace add` polecenia do przyłączania przestrzeni nazw do danej sieci usługi.
Gdy przestrzeń nazw k8s jest częścią siatki (lub aby była częścią siatki), muszą być spełnione następujące kwestie:

Wyświetl adnotacje z

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Musi być obecna następująca Adnotacja:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Wyświetlanie etykiet z

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Musi być obecna następująca etykieta:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Jeśli przestrzeń nazw nie ma adnotacji z `"openservicemesh.io/sidecar-injection": "enabled"` etykietą lub nie ma etykiety z `"openservicemesh.io/monitored-by": "osm"` OSM wtryskiwacz nie dodaje przyczepek wysłannika.

> Uwaga: po `osm namespace add` wywołaniu tylko **nowe** zasobniki zostaną dodane do wysłannika przyczepki. Istniejące wymagane są ponowne uruchomienie `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Sprawdź CRDs SMI:

Sprawdź, czy klaster ma wymagane CRDs:

```azurecli-interactive
kubectl get crds
```

W klastrze muszą być zainstalowane następujące elementy:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Pobierz wersje programu CRDs, które zostały zainstalowane przy użyciu tego polecenia:

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

OSM Controller v 0.8.2 wymaga następujących wersji:

- traffictargets.access.smi-spec.io — [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io — [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io — [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io — nieobsługiwane
- trafficsplits.split.smi-spec.io — [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*. metrics.smi-spec.io — [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Jeśli brakuje CRDs, użyj następujących poleceń, aby zainstalować je w klastrze:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Wyłącz dodatek Open Service siatk (OSM) dla klastra AKS

Aby wyłączyć dodatek OSM, uruchom następujące polecenie:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register