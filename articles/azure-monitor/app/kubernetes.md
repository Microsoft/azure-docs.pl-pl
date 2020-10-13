---
title: Użyj Application Insights do monitorowania usługi Azure Kubernetes Service (AKS) lub innych aplikacji hostowanych Kubernetes — Azure Monitor | Microsoft Docs
description: Azure Monitor korzysta z technologii siatki usługi Istio w klastrze Kubernetes w celu zapewnienia monitorowania aplikacji dla dowolnej aplikacji hostowanej Kubernetes. Dzięki temu można zbierać dane telemetryczne Application Insights związane z żądaniami przychodzących i wychodzących do i z serwerów w klastrze.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 3cd43963175594fcdc1c3c67d6b2493ce1ccd313
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321925"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Zero monitorowanie aplikacji Instrumentacji dla aplikacji hostowanych Kubernetes z Istio — PRZESTARZAŁe

> [!IMPORTANT]
> Ta funkcja jest obecnie przestarzała i nie będzie już obsługiwana po 1 sierpnia 2020.
> Obecnie monitorowanie bezkodowe można włączyć tylko dla [środowiska Java za pomocą autonomicznego agenta](./java-in-process-agent.md). W przypadku innych języków Użyj zestawów SDK do monitorowania aplikacji w AKS: [ASP.NET Core](./asp-net-core.md), [ASP.NET](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md)i [Python](./opencensus-python.md).

Azure Monitor teraz korzysta z usługi Service siatk Tech w klastrze Kubernetes, aby umożliwić monitorowanie aplikacji w usłudze Box dla dowolnej hostowanej aplikacji Kubernetes. Za pomocą domyślnych funkcji usługi Application Insight, takich jak [Mapa aplikacji](./app-map.md) , aby modelować zależności, [Live Metrics Stream](./live-stream.md) do monitorowania w czasie rzeczywistym, zaawansowanych wizualizacji z [domyślnym pulpitem nawigacyjnym](./overview-dashboard.md), [Eksploratorem metryk](../platform/metrics-getting-started.md)i [skoroszytami](../platform/workbooks-overview.md). Ta funkcja ułatwi użytkownikom dostęp do wąskich gardeł wydajności i hotspotów niepowodzeń we wszystkich obciążeniach Kubernetes w ramach wybranej przestrzeni nazw Kubernetes. Wielką literą dla istniejących inwestycji w siatkę usług przy użyciu technologii, takich jak Istio, Azure Monitor umożliwia monitorowanie aplikacji z autoinstrumentacją bez żadnych modyfikacji w kodzie aplikacji.

> [!NOTE]
> Jest to jeden z wielu sposobów przeprowadzania monitorowania aplikacji na Kubernetes.Możesz również instrumentować każdą aplikację hostowaną w Kubernetes za pomocą [zestawu SDK Application Insights](../azure-monitor-app-hub.yml) bez potrzeby używania siatki usług. Aby monitorować Kubernetes bez Instrumentacji aplikacji za pomocą zestawu SDK, można użyć poniższej metody.

## <a name="prerequisites"></a>Wymagania wstępne

- [Klaster Kubernetes](../../aks/concepts-clusters-workloads.md).
- Dostęp za pomocą konsoli do klastra w celu uruchomienia *polecenia kubectl*.
- [Zasób usługi Application Insights](create-new-resource.md)
- Mieć siatkę usługi. Jeśli klaster nie ma wdrożonego Istio, możesz dowiedzieć się, jak [zainstalować usługę Istio w usłudze Azure Kubernetes i korzystać](../../aks/servicemesh-istio-install.md)z niej.

## <a name="capabilities"></a>Możliwości

Korzystając z funkcji monitorowania aplikacji Instrumentacji dla aplikacji hostowanych w usłudze Kubernetes, będzie można użyć:

- [Mapa aplikacji](./app-map.md)
- [Metryki Live Stream](./live-stream.md)
- [Pulpity nawigacyjne](./overview-dashboard.md)
- [Eksplorator metryk](../platform/metrics-getting-started.md)
- [Rozproszone — śledzenie](./distributed-tracing.md)
- [Kompleksowe monitorowanie transakcji](../learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Kroki instalacji

Aby włączyć rozwiązanie, wykonaj następujące czynności:
- Wdróż aplikację (jeśli nie została jeszcze wdrożona).
- Upewnij się, że aplikacja jest częścią sieci usługi.
- Obserwuj zebrane dane telemetryczne.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurowanie aplikacji do pracy z siatką usługi

Istio obsługuje dwa sposoby [Instrumentacji pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
W większości przypadków najłatwiej oznaczyć przestrzeń nazw Kubernetes zawierającą aplikację przy użyciu etykiety *iniekcji istio* :

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Ze względu na to, że usługa sieci szkieletowej nie może przechwytywać szyfrowanych danych. W przypadku ruchu, który nie opuszcza klastra, należy użyć nieszyfrowanego protokołu (na przykład HTTP). W przypadku ruchu zewnętrznego, który musi być zaszyfrowany, należy rozważyć [skonfigurowanie zakończenia protokołu TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) na kontrolerze transferu danych przychodzących.

Aplikacje działające poza siatką usługi nie podlegają usterce.

### <a name="deploy-your-application"></a>Wdrażanie aplikacji

- Wdróż aplikację w przestrzeni nazw *My-App-przestrzeń nazw* . Jeśli aplikacja została już wdrożona i została zastosowana opisana powyżej Metoda automatycznego wstrzykiwania przyczepki, należy ponownie utworzyć te, aby upewnić się, że Istio wprowadza swoją przyczepkę. Zainicjuj aktualizację kroczącą lub Usuń poszczególne zasobniki i poczekaj na ich odtworzenie.
- Upewnij się, że Twoja aplikacja spełnia [wymagania Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Wdróż zero monitorowania aplikacji Instrumentacji dla hostowanych aplikacji Kubernetes

1. Pobierz i Wyodrębnij [wersję *adaptera Application Insights* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Przejdź do */src/Kubernetes/* wewnątrz folderu Release.
3. Edycja *Application-Insights-istio-mikser-adapter-Deployment. YAML*
    - Edytuj wartość zmiennej środowiskowej *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* , aby zawierała klucz Instrumentacji zasobu Application Insights w Azure Portal, aby zawierała dane telemetryczne.
    - W razie potrzeby Edytuj wartość zmiennej środowiskowej *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* , aby zawierała rozdzieloną przecinkami listę przestrzeni nazw, dla których chcesz włączyć monitorowanie. Pozostaw to pole puste, aby monitorować wszystkie przestrzenie nazw.
4. Zastosuj *każdy* plik YAML znaleziony w obszarze *src/Kubernetes/* , wykonując następujące czynności (nadal musisz być wewnątrz */src/Kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Weryfikowanie wdrożenia

- Upewnij się, że karta Application Insights została wdrożona:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> W niektórych przypadkach wymagana jest dostrajanie precyzyjne. Aby dołączyć lub wykluczyć dane telemetryczne dla osoby, która nie jest zbierana, użyj *appinsights/monitoring. Enabled* na tym komputerze. Będzie to miało pierwszeństwo przed całą konfiguracją opartą na przestrzeni nazw. Ustaw *appinsights/monitoring.* *wartość true powoduje włączenie wartości "pod"* i do *wartości false* , aby wykluczyć ją.

### <a name="view-application-insights-telemetry"></a>Wyświetl dane telemetryczne Application Insights

- Wygeneruj przykładowe żądanie dla aplikacji, aby upewnić się, że monitorowanie działa prawidłowo.
- W ciągu 3-5 minut należy zacząć wyświetlać dane telemetryczne wyświetlane w Azure Portal. Zapoznaj się z sekcją *Mapowanie aplikacji* Application Insights w portalu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej znajduje się przepływ rozwiązywania problemów, który ma być używany, gdy Telemetria nie pojawia się w Azure Portal zgodnie z oczekiwaniami.

1. Upewnij się, że aplikacja jest pod obciążeniem i wysyła/otrzymuje żądania w postaci zwykłego protokołu HTTP. Ze względu na to, że dane telemetryczne są zniesione, szyfrowany ruch nie jest obsługiwany. Jeśli nie ma żadnych żądań przychodzących lub wychodzących, nie będzie żadnych danych telemetrycznych.
2. Upewnij się, że prawidłowy klucz Instrumentacji jest podany w zmiennej środowiskowej *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* w temacie *Application-Insights-ISTIO-mikser-adapter-Deployment. YAML*. Klucz Instrumentacji znajduje się na karcie *Przegląd* zasobu Application Insights w Azure Portal.
3. Upewnij się, że poprawna przestrzeń nazw Kubernetes jest podana w zmiennej środowiskowej *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* w *aplikacji Application-Insights-ISTIO-mikser-adapter-Deployment. YAML*. Pozostaw to pole puste, aby monitorować wszystkie przestrzenie nazw.
4. Upewnij się, że Twoje zasobniki aplikacji zostały dodane do sieci przyczepki przez Istio. Upewnij się, że na każdym z nich znajduje się Przyczepka Istio.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Sprawdź, czy istnieje kontener o nazwie *istio-proxy* uruchomiony w ramach.

5. Wyświetl dane śledzenia karty Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Liczba odebranych elementów telemetrii jest aktualizowana raz na minutę. Jeśli nie zwiększy się minutę w ciągu minuty — żadne dane telemetryczne nie są wysyłane do karty przez Istio.
   Poszukaj błędów w dzienniku.
6. Jeśli została ustanowiona, że w usłudze *Application Insights dla karty Kubernetes* nie są przekazywane dane telemetryczne, Sprawdź dzienniki miksera Istio, aby dowiedzieć się, dlaczego nie wysyła on danych do karty:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Poszukaj błędów, szczególnie związanych z komunikacją z kartą *applicationinsightsadapter* .

## <a name="faq"></a>Najczęściej zadawane pytania

Aby uzyskać najnowsze informacje o postępie tego projektu, odwiedź [kartę Application Insightsową usługi GitHub projektu Istio](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Odinstalowanie

Aby odinstalować produkt, dla *każdego* pliku YAML znalezionego w ścieżce *src/Kubernetes/* Run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak Azure Monitor i kontenery współpracują ze sobą, odwiedź [Azure monitor Omówienie kontenerów](../insights/container-insights-overview.md)

