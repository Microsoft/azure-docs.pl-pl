---
title: Wyświetlanie danych na żywo (wersja zapoznawcza) za pomocą Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano widok Kubernetes dzienników, zdarzeń i metryk w czasie rzeczywistym bez używania polecenia kubectl w Azure Monitor for Containers.
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: references_regions
ms.openlocfilehash: 3655ff8e5879aa4113753b5529c1e484fb079401
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614323"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Jak wyświetlać dzienniki Kubernetes, zdarzenia i metryki pod względem czasu rzeczywistego

Azure Monitor dla kontenerów zawiera funkcję danych na żywo (wersja zapoznawcza), która jest zaawansowaną funkcją diagnostyczną umożliwiającą bezpośredni dostęp do dzienników kontenerów usługi Azure Kubernetes Service (AKS), zdarzeń i metryk pod. Udostępnia bezpośredni dostęp do `kubectl logs -c` , `kubectl get` zdarzeń i `kubectl top pods` . W okienku konsoli są wyświetlane dzienniki, zdarzenia i metryki wygenerowane przez aparat kontenera, aby dodatkowo pomóc w rozwiązywaniu problemów w czasie rzeczywistym.

Ten artykuł zawiera szczegółowe omówienie i pomaga zrozumieć, jak korzystać z tej funkcji.

Aby uzyskać pomoc w konfigurowaniu lub rozwiązywaniu problemów dotyczących aktywnych danych (wersja zapoznawcza), zapoznaj się z naszym [przewodnikiem Instalatora](container-insights-livedata-setup.md). Ta funkcja bezpośrednio uzyskuje dostęp do interfejsu API Kubernetes i dodatkowe informacje o modelu uwierzytelniania można znaleźć [tutaj](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

## <a name="view-deployment-live-logs-preview"></a>Wyświetlanie dzienników na żywo wdrożenia (wersja zapoznawcza)
Aby wyświetlić dzienniki na żywo wdrożeń, które są częścią klastrów AKS, które nie są monitorowane przez Azure Monitor dla kontenerów, należy wykonać poniższą procedurę. Jeśli klaster używa Azure Monitor dla kontenerów, użyj poniższego procesu, aby wyświetlić dane na żywo dla węzłów, kontrolerów, kontenerów i wdrożeń.

1. W Azure Portal przejdź do grupy zasobów klastra AKS i wybierz zasób AKS.

2. Wybierz pozycję **obciążenia** w sekcji **Kubernetes Resources (zasoby** ) w menu.

3. Wybierz wdrożenie na karcie **wdrożenia** .

4. Wybierz pozycję **dzienniki na żywo (wersja zapoznawcza)** z menu wdrożenia.

5. Wybierz pozycję pod, aby rozpocząć zbieranie danych na żywo.

    [![Wdrożenia dzienników na żywo](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>Wyświetlanie dzienników

Dane dzienników w czasie rzeczywistym można wyświetlić w miarę ich generowania przez aparat kontenera z **węzłów**, **kontrolerów** i widoku **kontenerów** . Aby wyświetlić dane dziennika, wykonaj następujące czynności.

1. W Azure Portal przejdź do grupy zasobów klastra AKS i wybierz zasób AKS.

2. Na pulpicie nawigacyjnym klastra AKS, w obszarze **monitorowanie** po lewej stronie, wybierz pozycję **szczegółowe informacje**.

3. Wybierz kartę **węzły**, **Kontrolery** lub **kontenery** .

4. Wybierz obiekt z siatki wydajności i w okienku właściwości, które znajdują się po prawej stronie, wybierz opcję **Wyświetl dane na żywo (wersja zapoznawcza)** . Jeśli klaster AKS jest skonfigurowany z logowaniem jednokrotnym przy użyciu usługi Azure AD, zostanie wyświetlony monit o uwierzytelnienie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i Ukończ uwierzytelnianie na platformie Azure.

    >[!NOTE]
    >Podczas przeglądania danych z obszaru roboczego Log Analytics, wybierając opcję **Wyświetl w analizie** z okienka właściwości, wyniki wyszukiwania dzienników będą potencjalnie wyświetlać **węzły**, **zestawy demonów**, **zestawy replik**, **zadania**, zadania firmy **cronus**, **zasobniki** i **kontenery** , które mogą już nie istnieć. Próba przeszukania dzienników dla kontenera, który nie jest dostępny w programie, `kubectl` również zakończy się niepowodzeniem. Przejrzyj [Widok w funkcji Analiza](container-insights-log-search.md#search-logs-to-analyze-data) , aby dowiedzieć się więcej na temat wyświetlania dzienników historycznych, zdarzeń i metryk.

Po pomyślnym uwierzytelnieniu okienko konsoli dane dynamiczne (wersja zapoznawcza) zostanie wyświetlone poniżej siatki danych wydajności, w której można wyświetlić dane dziennika w strumieniu ciągłym. Jeśli wskaźnik stanu pobierania zawiera zielony znacznik wyboru, który znajduje się po prawej stronie okienka, oznacza to, że dane mogą być pobierane i rozpoczyna przesyłanie strumieniowe do konsoli programu.

![Opcja danych widoku okienka właściwości węzła](./media/container-insights-livedata-overview/node-properties-pane.png)

W tytule okienka wyświetlana jest nazwa, pod którą jest zgrupowany kontener.

## <a name="view-events"></a>Wyświetlanie zdarzeń

Dane zdarzeń w czasie rzeczywistym można wyświetlać w miarę ich generowania przez aparat kontenera z **węzłów**, **kontrolerów**, **kontenerów** i **wdrożeń (wersja zapoznawcza)** , gdy jest zaznaczone kontenery, na przykład, ReplicaSet, elementu daemonset, zadanie, CronJob lub wdrożenie. Aby wyświetlić zdarzenia, wykonaj następujące czynności.

1. W Azure Portal przejdź do grupy zasobów klastra AKS i wybierz zasób AKS.

2. Na pulpicie nawigacyjnym klastra AKS, w obszarze **monitorowanie** po lewej stronie, wybierz pozycję **szczegółowe informacje**.

3. Wybierz kartę **węzły**, **Kontrolery**, **kontenery** lub **wdrożenia (wersja zapoznawcza)** .

4. Wybierz obiekt z siatki wydajności i w okienku właściwości, które znajdują się po prawej stronie, wybierz opcję **Wyświetl dane na żywo (wersja zapoznawcza)** . Jeśli klaster AKS jest skonfigurowany z logowaniem jednokrotnym przy użyciu usługi Azure AD, zostanie wyświetlony monit o uwierzytelnienie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i Ukończ uwierzytelnianie na platformie Azure.

    >[!NOTE]
    >Podczas przeglądania danych z obszaru roboczego Log Analytics, wybierając opcję **Wyświetl w analizie** z okienka właściwości, wyniki wyszukiwania dzienników będą potencjalnie wyświetlać **węzły**, **zestawy demonów**, **zestawy replik**, **zadania**, zadania firmy **cronus**, **zasobniki** i **kontenery** , które mogą już nie istnieć. Próba przeszukania dzienników dla kontenera, który nie jest dostępny w programie, `kubectl` również zakończy się niepowodzeniem. Przejrzyj [Widok w funkcji Analiza](container-insights-log-search.md#search-logs-to-analyze-data) , aby dowiedzieć się więcej na temat wyświetlania dzienników historycznych, zdarzeń i metryk.

Po pomyślnym uwierzytelnieniu okienko konsoli dane dynamiczne (wersja zapoznawcza) zostanie wyświetlone poniżej siatki danych wydajności. Jeśli wskaźnik stanu pobierania zawiera zielony znacznik wyboru, który znajduje się po prawej stronie okienka, oznacza to, że dane mogą być pobierane i rozpoczyna przesyłanie strumieniowe do konsoli programu.

Jeśli wybrany obiekt jest kontenerem, wybierz opcję **zdarzenia** w okienku. Jeśli wybrano węzeł, pod lub kontroler, Wyświetlanie zdarzeń jest automatycznie wybierane.

![Okienko właściwości kontrolera — zdarzenia](./media/container-insights-livedata-overview/controller-properties-live-event.png)

W tytule okienka wyświetlana jest nazwa, pod którą jest zgrupowany kontener.

### <a name="filter-events"></a>Filtrowanie zdarzeń

Podczas przeglądania zdarzeń można dodatkowo ograniczyć wyniki, używając **filtru** pill znalezionego po prawej stronie paska wyszukiwania. W zależności od wybranego zasobu pill wyświetla pozycję pod, przestrzeń nazw lub klaster do wybrania.

## <a name="view-metrics"></a>Wyświetlanie metryk

Dane metryki w czasie rzeczywistym można wyświetlić w miarę ich generowania przez aparat kontenera z **węzłów** lub **kontrolerów** , tylko **gdy jest zaznaczone** . Aby wyświetlić metryki, wykonaj następujące czynności.

1. W Azure Portal przejdź do grupy zasobów klastra AKS i wybierz zasób AKS.

2. Na pulpicie nawigacyjnym klastra AKS, w obszarze **monitorowanie** po lewej stronie, wybierz pozycję **szczegółowe informacje**.

3. Wybierz kartę **węzły** lub **Kontrolery** .

4. Wybierz obiekt **pod** z siatki wydajności i w okienku właściwości, które znajdują się po prawej stronie, wybierz opcję **Wyświetl dane dynamiczne (wersja zapoznawcza)** . Jeśli klaster AKS jest skonfigurowany z logowaniem jednokrotnym przy użyciu usługi Azure AD, zostanie wyświetlony monit o uwierzytelnienie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i Ukończ uwierzytelnianie na platformie Azure.

    >[!NOTE]
    >Podczas przeglądania danych z obszaru roboczego Log Analytics, wybierając opcję **Wyświetl w analizie** z okienka właściwości, wyniki wyszukiwania dzienników będą potencjalnie wyświetlać **węzły**, **zestawy demonów**, **zestawy replik**, **zadania**, zadania firmy **cronus**, **zasobniki** i **kontenery** , które mogą już nie istnieć. Próba przeszukania dzienników dla kontenera, który nie jest dostępny w programie, `kubectl` również zakończy się niepowodzeniem. Przejrzyj [Widok w funkcji Analiza](container-insights-log-search.md#search-logs-to-analyze-data) , aby dowiedzieć się więcej na temat wyświetlania dzienników historycznych, zdarzeń i metryk.

Po pomyślnym uwierzytelnieniu okienko konsoli dane dynamiczne (wersja zapoznawcza) zostanie wyświetlone poniżej siatki danych wydajności. Dane metryk są pobierane i rozpoczynają przesyłanie strumieniowe do konsoli programu w celu przedstawienia ich na dwóch wykresach. W tytule okienka wyświetlana jest nazwa, pod którą jest zgrupowany kontener.

![Przykład wyświetlania metryk pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>Korzystanie z widoków danych na żywo
W poniższych sekcjach opisano funkcje, których można użyć w różnych widokach danych na żywo.

### <a name="search"></a>Wyszukaj
Usługa Live Data (wersja zapoznawcza) zawiera funkcję wyszukiwania. W polu **wyszukiwania** można filtrować wyniki, wpisując słowo kluczowe lub termin, a wszystkie zgodne wyniki są wyróżniane w celu umożliwienia szybkiego przeglądu. Podczas przeglądania zdarzeń można dodatkowo ograniczyć wyniki, używając **filtru** pill znalezionego po prawej stronie paska wyszukiwania. W zależności od wybranego zasobu pill wyświetla pozycję pod, przestrzeń nazw lub klaster do wybrania.

![Przykład filtru okienka konsoli danych dynamicznych](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![Przykład filtru okienka konsoli danych na żywo dla wdrożenia](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>Scroll Lock i Pause

Aby zawiesić automatyczne przewijanie i kontrolować zachowanie okienka, umożliwiając ręczne przewinięcie nowych danych, można użyć opcji **przewijania** . Aby ponownie włączyć Autoprzewijanie, po prostu wybierz opcję **przewijania** ponownie. Możesz również wstrzymać pobieranie danych dziennika lub zdarzenia, wybierając opcję **Wstrzymaj** , a gdy wszystko będzie gotowe do wznowienia, wystarczy wybrać opcję **Odtwórz**.

![Okienko konsoli danych na żywo Wstrzymaj widok na żywo](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![Okienko konsoli danych na żywo Wstrzymaj widok na żywo na potrzeby wdrożenia](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>Zalecamy Wstrzymywanie lub wstrzymywanie autoprzewijania przez krótki okres czasu podczas rozwiązywania problemu. Te żądania mogą mieć wpływ na dostępność i ograniczenie interfejsu API Kubernetes w klastrze.

>[!IMPORTANT]
>Podczas wykonywania tej funkcji żadne dane nie są trwale przechowywane. Wszystkie informacje przechwycone podczas sesji są usuwane, gdy zamkniesz przeglądarkę lub opuścisz ją. Dane pozostają obecne tylko dla wizualizacji w oknie 5 minut funkcji metryki. wszystkie metryki starsze niż pięć minut również są usuwane. Bufory danych na żywo (wersja zapoznawcza) są wysyłane w ramach rozsądnych limitów użycia pamięci.

## <a name="next-steps"></a>Następne kroki

- Aby kontynuować uczenie się, jak używać Azure Monitor i monitorować inne aspekty klastra AKS, zobacz [Wyświetlanie usługi Azure Kubernetes Service Health](container-insights-analyze.md).

- Wyświetl [przykłady zapytań dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby wyświetlić wstępnie zdefiniowane zapytania i przykłady do tworzenia alertów, wizualizacji lub przeprowadzenia dalszej analizy klastrów.
