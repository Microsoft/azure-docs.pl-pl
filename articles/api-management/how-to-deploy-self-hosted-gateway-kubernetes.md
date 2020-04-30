---
title: Wdrażanie bramy samohostowanej w usłudze Kubernetes | Microsoft Docs
description: Dowiedz się, jak wdrożyć samohostowany składnik bramy platformy Azure API Management do Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205107"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Wdróż bramę samoobsługową do Kubernetes

W tym artykule opisano kroki wdrażania składnika bramy samoobsługowego API Management platformy Azure w klastrze Kubernetes.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Utwórz klaster Kubernetes.
> [!TIP]
> [Klastry z jednym węzłem](https://kubernetes.io/docs/setup/#learning-environment) działają dobrze w celach deweloperskich i testowych. Korzystaj z [certyfikowanych](https://kubernetes.io/partners/#conformance) klastrów wielowęzłowych Kubernetes lokalnie lub w chmurze na potrzeby obciążeń produkcyjnych.
- [Zainicjuj obsługę zasobu bramy samoobsługowego w wystąpieniu API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Wdrażanie na platformie Kubernetes

1. Wybierz pozycję **bramy** z sekcji **wdrażanie i infrastruktura**.
2. Wybierz zasób bramy samoobsługowej, który ma zostać wdrożony.
3. Wybierz pozycję **wdrożenie**.
4. Należy zauważyć, że token dostępu w polu tekstowym **token** został wygenerowany automatycznie przy użyciu domyślnych wartości kluczy **wygaśnięcia** i **tajnych** . W razie potrzeby wybierz odpowiednie wartości w jednej lub obu kontrolkach, aby wygenerować nowy token.
5. Wybierz pozycję Karta **Kubernetes** w obszarze **Skrypty wdrażania**.
6. Kliknij link **<Gateway-name>. yml** , a następnie Pobierz plik YAML.
7. Wybierz ikonę **Kopiuj** znajdującą się w prawym dolnym rogu pola tekstowego **Wdróż** , aby zapisać `kubectl` polecenia do Schowka.
8. Wklej polecenia do okna terminalu (lub polecenia). Pierwsze polecenie tworzy wpis tajny Kubernetes zawierający token dostępu wygenerowany w kroku 4. Drugie polecenie stosuje plik konfiguracji pobrany w kroku 6 do klastra Kubernetes i oczekuje, że plik jest obecny w bieżącym katalogu.
9. Wykonaj polecenia, aby utworzyć niezbędne obiekty Kubernetes w [domyślnej przestrzeni nazw](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) i uruchom samodzielną bramę pod (s) z [obrazu kontenera](https://aka.ms/apim/sputnik/dhub) pobranego z Container Registry firmy Microsoft.
10. Wykonaj pokazane polecenie, aby sprawdzić, czy wdrożenie zakończyło się pomyślnie. Należy pamiętać, że może upłynąć trochę czasu, aby można było utworzyć wszystkie obiekty i dla na przykład (-y).
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. Wykonaj pokazane polecenie, aby sprawdzić, czy usługa została pomyślnie utworzona. Należy pamiętać, że adresy IP i porty usługi będą się różnić.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Wróć do Azure Portal i wybierz pozycję **Przegląd**.
13. **Stan** pokazujący zieloną ikonę znacznika wyboru, po której następuje liczba węzłów zgodna z liczbą replik określoną w pliku YAML, potwierdza, że wdrożone samoobsługowe bramy bram pomyślnie komunikują się z usługą API Management i mają regularny "Puls".

![stan bramy](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Wykonaj <code>kubectl logs deployment/<gateway-name></code> polecenie, aby wyświetlić dzienniki z losowo wybranej usługi, jeśli istnieje więcej niż jeden.
> Wykonaj <code>kubectl logs -h</code> , aby uzyskać pełny zestaw opcji poleceń, np. sposób wyświetlania dzienników dla określonego elementu lub kontenera.

## <a name="production-deployment-considerations"></a>Zagadnienia dotyczące wdrażania produkcyjnego

### <a name="access-token"></a>Token dostępu
Bez prawidłowego tokenu dostępu Brama samoobsługowa nie może uzyskać dostępu do konfiguracji i pobrać jej z punktu końcowego danych konfiguracji skojarzonej usługi API Management. Token dostępu może być ważny przez maksymalnie 30 dni. Należy go ponownie wygenerować i skonfigurować klaster ręcznie lub za pośrednictwem automatyzacji przed jego wygaśnięciem. Podczas automatyzowania odświeżania tokenu Użyj tej [operacji](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) zarządzania interfejsem API do wygenerowania nowego tokenu. Skorzystaj z tego [linku](https://kubernetes.io/docs/concepts/configuration/secret) , aby uzyskać informacje o zarządzaniu wpisami tajnymi Kubernetes.

### <a name="namespace"></a>Przestrzeń nazw
[Przestrzenie nazw](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) Kubernetes ułatwiają dzielenie pojedynczego klastra między wieloma zespołami, projektami lub aplikacjami. Przestrzenie nazw zapewniają zakres zasobów i nazw oraz mogą być skojarzone z zasadami przydziału zasobów i kontroli dostępu.
Polecenia dostępne w Azure Portal utworzyć własne zasoby bramy w **domyślnej** przestrzeni nazw, która jest tworzona automatycznie, istnieje w każdym klastrze i nie można jej usunąć.
Rozważ [utworzenie i wdrożenie](https://kubernetesbyexample.com/ns/) bramy samoobsługowej w oddzielnym obszarze nazw w środowisku produkcyjnym.

### <a name="number-of-replicas"></a>Liczba replik
Minimalna liczba replik odpowiednia w środowisku produkcyjnym to dwie.

Domyślnie Brama samoobsługowa jest wdrażana z użyciem [strategii](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)wdrażania **RollingUpdate** . Sprawdź wartości domyślne i rozważ jawne ustawienie pól [**maxUnavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) i [**maxSurge**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) , szczególnie w przypadku korzystania z dużej liczby replik.

### <a name="container-resources"></a>Zasoby kontenera
Domyślnie plik YAML podany w Azure Portal nie określa żądań zasobów kontenera.

Nie jest możliwe niezawodne przewidywalność i zalecana ilość zasobów procesora i pamięci dla kontenera oraz liczba replik wymaganych do obsługi określonego obciążenia ze względu na wiele czynników, na przykład:

- Konkretny sprzęt, na którym działa klaster
- Obecność i typ wirtualizacji
- Liczba i szybkość jednoczesnych połączeń klienta
- Częstotliwość żądań
- Rodzaj i liczba skonfigurowanych zasad
- Rozmiar ładunku i jeśli ładunki są buforowane lub przesyłane strumieniowo
- Opóźnienie usługi wewnętrznej bazy danych

Zalecamy ustawienie żądania zasobu na 2 rdzenie i 2 GiB jako punkt początkowy, wykonanie testu obciążenia i skalowanie w górę/w dół lub w dół/w oparciu o wyniki.

### <a name="container-image-tag"></a>Tag obrazu kontenera
Plik YAML podany w Azure Portal używa **najnowszego** tagu, który zawsze odwołuje się do najnowszej wersji obrazu kontenera bramy samohostowanej.

Rozważ użycie określonego znacznika Version w środowisku produkcyjnym, aby uniknąć przypadkowego uaktualnienia do nowszej wersji.

Skorzystaj z tego [linku](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) , aby wyświetlić pełną listę dostępnych tagów.

### <a name="dns-policy"></a>Zasady DNS
Funkcja rozpoznawania nazw DNS odgrywa kluczową rolę w programie do samodzielnej bramy możliwość łączenia się z zależnościami na platformie Azure i wysyłania wywołań interfejsu API do usług zaplecza.

Plik YAML podany w Azure Portal stosuje domyślne zasady [**ClusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) , które powodują, że żądania rozpoznawania nazw, które nie są rozpoznawane przez klaster DNS w celu przekazania do nadrzędnego serwera DNS dziedziczonego z węzła.

Skorzystaj z tego [linku](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) , aby dowiedzieć się więcej o rozpoznawaniu nazw w Kubernetes, i rozważ dostosowanie konfiguracji [zasad DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) lub D[NS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) zgodnie z potrzebami Instalatora.

### <a name="configuration-backup"></a>Kopia zapasowa konfiguracji
Skorzystaj z tego [linku](self-hosted-gateway-overview.md#connectivity-to-azure) , aby dowiedzieć się, jak działa samodzielna Brama w obecności tymczasowej awarii łączności platformy Azure.
Skonfiguruj lokalny wolumin magazynu dla kontenera bramy samoobsługowej, aby można było zachować kopię zapasową najnowszej pobranej konfiguracji i, jeśli łączność nie działa, użyj jej po ponownym uruchomieniu. Ścieżka instalacji woluminu musi mieć <code>/apim/config</code>wartość. Zobacz przykład [tutaj](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Aby dowiedzieć się więcej o magazynie w Kubernetes, użyj tego [linku](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Dzienniki i metryki lokalne
Brama samoobsługowa wysyła dane telemetryczne do [Azure monitor](api-management-howto-use-azure-monitor.md) i [Application Insights platformy Azure](api-management-howto-app-insights.md) na ustawienia konfiguracji w skojarzonej usłudze API Management.
Gdy [łączność z platformą Azure](self-hosted-gateway-overview.md#connectivity-to-azure) zostanie tymczasowo utracona, przepływ danych telemetrycznych do platformy Azure zostanie przerwany, a dane zostaną utracone przez czas przestoju.
Rozważ [skonfigurowanie lokalnego monitorowania](how-to-configure-local-metrics-logs.md) , aby zapewnić możliwość obserwowania ruchu interfejsu API i zapobiegania utracie danych telemetrycznych podczas awarii łączności z platformą Azure.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz temat [usługa Azure API Management](self-hosted-gateway-overview.md) samodzielna Brama — Omówienie