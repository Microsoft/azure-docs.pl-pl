---
title: Wdrażanie bramy samohostowanej w usłudze Kubernetes | Microsoft Docs
description: Dowiedz się, jak wdrożyć składnik bramy samoobsługowej platformy Azure API Management do Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89500134"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Wdrażanie własnej bramy w usłudze Kubernetes

W tym artykule opisano kroki wdrażania składnika bramy samoobsługowego API Management platformy Azure w klastrze Kubernetes.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Utwórz klaster Kubernetes.
   > [!TIP]
   > [Klastry z jednym węzłem](https://kubernetes.io/docs/setup/#learning-environment) działają dobrze w celach deweloperskich i testowych. Korzystaj z [certyfikowanych](https://kubernetes.io/partners/#conformance) klastrów wielowęzłowych Kubernetes lokalnie lub w chmurze na potrzeby obciążeń produkcyjnych.
- [Zainicjuj obsługę zasobu bramy samoobsługowego w wystąpieniu API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Wdrażanie na platformie Kubernetes

1. Wybierz pozycję **bramy** w obszarze **wdrażanie i infrastruktura**.
2. Wybierz zasób bramy samoobsługowej, który chcesz wdrożyć.
3. Wybierz pozycję **wdrożenie**.
4. Token dostępu w polu tekstowym **token** został automatycznie wygenerowany dla Ciebie, na podstawie domyślnych wartości kluczy **wygaśnięcia** i **tajnych** . W razie potrzeby wybierz wartości w jednej lub obu kontrolkach, aby wygenerować nowy token.
5. Wybierz kartę **Kubernetes** w obszarze **Skrypty wdrażania**.
6. Wybierz łącze plik **\<gateway-name\> . yml** i Pobierz plik YAML.
7. Wybierz ikonę **kopiowania** w prawym dolnym rogu pola tekstowego **Wdróż** , aby zapisać `kubectl` polecenia do Schowka.
8. Wklej polecenia do okna terminalu (lub polecenia). Pierwsze polecenie tworzy wpis tajny Kubernetes, który zawiera token dostępu wygenerowany w kroku 4. Drugie polecenie stosuje plik konfiguracji pobrany w kroku 6 do klastra Kubernetes i oczekuje, że plik znajduje się w bieżącym katalogu.
9. Uruchom polecenia, aby utworzyć niezbędne obiekty Kubernetes w [domyślnej przestrzeni nazw](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) i uruchomić własne witryny bramy z [obrazu kontenera](https://aka.ms/apim/sputnik/dhub) pobranego z Container Registry firmy Microsoft.
10. Uruchom następujące polecenie, aby sprawdzić, czy wdrożenie zakończyło się pomyślnie. Należy pamiętać, że może upłynąć trochę czasu, aby można było utworzyć wszystkie obiekty i inicjować ich inicjowanie.
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. Uruchom następujące polecenie, aby sprawdzić, czy usługa została pomyślnie utworzona. Należy pamiętać, że adresy IP i porty usługi będą się różnić.
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Wróć do Azure Portal i wybierz pozycję **Przegląd**.
13. Upewnij się, że **stan** zawiera zielony znacznik wyboru, a następnie liczbę węzłów, która jest zgodna z liczbą replik określoną w pliku YAML. Ten stan oznacza, że wdrożone samodzielne usługi bramy pomyślnie komunikują się z usługą API Management i mają regularny "Puls".

    ![Stan bramy](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Uruchom <code>kubectl logs deployment/<gateway-name></code> polecenie, aby wyświetlić dzienniki z losowo wybranej usługi pod warunkiem, że istnieje więcej niż jeden.
> Uruchom <code>kubectl logs -h</code> polecenie, aby uzyskać pełny zestaw opcji poleceń, takich jak wyświetlanie dzienników dla określonego elementu lub kontenera.

## <a name="production-deployment-considerations"></a>Zagadnienia dotyczące wdrażania produkcyjnego

### <a name="access-token"></a>Token dostępu
Bez prawidłowego tokenu dostępu Brama samoobsługowa nie może uzyskać dostępu do danych konfiguracyjnych z punktu końcowego skojarzonej usługi API Management i pobrać ich. Token dostępu może być ważny przez maksymalnie 30 dni. Należy go ponownie wygenerować i skonfigurować klaster ręcznie lub za pośrednictwem automatyzacji przed jego wygaśnięciem.

W przypadku automatyzowania odświeżania tokenu Użyj [tej operacji zarządzania interfejsem API](/rest/api/apimanagement/2019-12-01/gateway/generatetoken) , aby wygenerować nowy token. Aby uzyskać informacje o zarządzaniu wpisami tajnymi Kubernetes, zobacz [witrynę sieci Web Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret).

### <a name="namespace"></a>Przestrzeń nazw
[Przestrzenie nazw](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) Kubernetes ułatwiają dzielenie pojedynczego klastra między wieloma zespołami, projektami lub aplikacjami. Przestrzenie nazw zapewniają zakres dla zasobów i nazw. Można je kojarzyć z przydziałem zasobów i zasadami kontroli dostępu.

Azure Portal udostępnia polecenia do tworzenia zasobów bramy samoobsługowego w **domyślnej** przestrzeni nazw. Ta przestrzeń nazw jest tworzona automatycznie, istnieje w każdym klastrze i nie można jej usunąć.
Rozważ [utworzenie i wdrożenie](https://kubernetesbyexample.com/ns/) bramy samohostowanej w oddzielnym obszarze nazw w środowisku produkcyjnym.

### <a name="number-of-replicas"></a>Liczba replik
Minimalna liczba replik odpowiednia dla produkcji to dwie.

Domyślnie Brama samoobsługowa jest wdrażana z użyciem [strategii](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)wdrażania **RollingUpdate** . Sprawdź wartości domyślne i rozważ jawne ustawienie pól [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) i [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) , szczególnie w przypadku korzystania z dużej liczby replik.

### <a name="container-resources"></a>Zasoby kontenera
Domyślnie plik YAML podany w Azure Portal nie określa żądań zasobów kontenera.

Nie jest możliwe niezawodne przewidywalność i zalecana ilość zasobów procesora i pamięci dla kontenera oraz liczba replik wymaganych do obsługi określonego obciążenia. Wiele czynników jest odtwarzanych, takich jak:

- Określony sprzęt, na którym działa klaster.
- Obecność i typ wirtualizacji.
- Liczba i szybkość jednoczesnych połączeń klienta.
- Liczba żądań.
- Rodzaj i liczba skonfigurowanych zasad.
- Rozmiar ładunku oraz to, czy ładunki są buforowane, czy przesyłane strumieniowo.
- Opóźnienie usługi zaplecza.

Zalecamy ustawienie żądań zasobów na dwa rdzenie i 2 GiB jako punkt początkowy. Wykonaj test obciążenia i Skaluj w górę/w dół lub w dół na podstawie wyników.

### <a name="container-image-tag"></a>Tag obrazu kontenera
Plik YAML podany w Azure Portal używa **najnowszego** tagu. Ten tag zawsze odwołuje się do najnowszej wersji obrazu kontenera bramy samoobsługowej.

Rozważ użycie określonego znacznika Version w środowisku produkcyjnym, aby uniknąć przypadkowego uaktualnienia do nowszej wersji.

Możesz [pobrać pełną listę dostępnych tagów](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>Zasady DNS
Funkcja rozpoznawania nazw DNS odgrywa rolę krytyczną w programie, która umożliwia nawiązywanie połączeń z zależnościami na platformie Azure i wysyłanie wywołań interfejsu API do usług zaplecza.

Plik YAML podany w Azure Portal stosuje domyślne zasady [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) . Te zasady powodują, że żądania rozpoznawania nazw nie są rozpoznawane przez serwer DNS klastra do przekazania do nadrzędnego serwera DNS, który jest Dziedziczony z węzła.

Aby dowiedzieć się więcej na temat rozpoznawania nazw w programie Kubernetes, zobacz [witrynę sieci Web Kubernetes](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service). Należy rozważyć dostosowanie [zasad DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) lub [konfiguracji DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) zgodnie z potrzebami Instalatora.

### <a name="external-traffic-policy"></a>Zasady ruchu zewnętrznego
Plik YAML, który znajduje się w polu Azure Portal ustawia w `externalTrafficPolicy` obiekcie [usługi](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) `Local` . Zachowuje to adres IP wywołującego (dostępny w [kontekście żądania](api-management-policy-expressions.md#ContextVariables)) i wyłącza Równoważenie obciążenia między węzłami, eliminując przekroczenia przez niego przeskoki sieciowe. Należy pamiętać, że to ustawienie może spowodować asymetryczne rozpowszechnianie ruchu we wdrożeniach z nierówną liczbą numerów bram bramy na węzeł.

### <a name="custom-domain-names-and-ssl-certificates"></a>Niestandardowe nazwy domen i certyfikaty SSL

Jeśli używasz niestandardowych nazw domen dla punktów końcowych API Management, zwłaszcza jeśli używasz niestandardowej nazwy domeny dla punktu końcowego zarządzania, może być konieczne zaktualizowanie wartości `config.service.endpoint` w pliku **\<gateway-name\> . YAML** , aby zastąpić domyślną nazwę domeny nazwą domeny niestandardowej. Upewnij się, że punkt końcowy zarządzania jest dostępny z poziomu usługi bramy samoobsługowej w klastrze Kubernetes.

W tym scenariuszu, jeśli certyfikat SSL używany przez punkt końcowy zarządzania nie jest podpisany przez dobrze znany certyfikat urzędu certyfikacji, należy się upewnić, że certyfikat urzędu certyfikacji jest zaufany w ramach bramy samoobsługowej.

### <a name="configuration-backup"></a>Kopia zapasowa konfiguracji
Aby dowiedzieć się więcej na temat zachowania bramy samoobsługowej w obecności tymczasowej awarii łączności platformy Azure, zobacz [Omówienie bramy samohostowanej](self-hosted-gateway-overview.md#connectivity-to-azure).

Skonfiguruj lokalny wolumin magazynu dla kontenera bramy samoobsługowej, aby można było zachować kopię zapasową najnowszej pobranej konfiguracji. Jeśli łączność nie działa, wolumin magazynu może użyć kopii zapasowej po ponownym uruchomieniu. Ścieżka instalacji woluminu musi mieć wartość <code>/apim/config</code> . Zobacz przykład w witrynie [GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Aby dowiedzieć się więcej o magazynie w programie Kubernetes, zobacz [witrynę sieci Web Kubernetes](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Dzienniki i metryki lokalne
Brama samoobsługowa wysyła dane telemetryczne do [Azure monitor](api-management-howto-use-azure-monitor.md) i [Application Insights platformy Azure](api-management-howto-app-insights.md) zgodnie z ustawieniami konfiguracji w skojarzonej usłudze API Management.
Gdy [łączność z platformą Azure](self-hosted-gateway-overview.md#connectivity-to-azure) zostanie tymczasowo utracona, przepływ danych telemetrycznych do platformy Azure zostanie przerwany, a dane zostaną utracone przez czas przestoju.
Należy rozważyć [skonfigurowanie monitorowania lokalnego](how-to-configure-local-metrics-logs.md) , aby zapewnić możliwość obserwowania ruchu interfejsu API i zapobiec utracie danych telemetrycznych podczas awarii łączności z platformą Azure.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz temat [Omówienie bramy samohostowanej](self-hosted-gateway-overview.md).
