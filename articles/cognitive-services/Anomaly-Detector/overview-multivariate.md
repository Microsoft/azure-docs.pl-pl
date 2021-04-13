---
title: Co to jest interfejs API Wieloczynnikowaego wykrywania anomalii?
titleSuffix: Azure Cognitive Services
description: Omówienie nowych interfejsów API wieloczynnikowa publicznej wersji zapoznawczej wykrywania anomalii.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: wykrywanie anomalii, uczenie maszynowe, algorytmy
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315571"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Wieloczynnikowa wykrywanie anomalii szeregów czasowych (publiczna wersja zapoznawcza)

Pierwsze wydanie wykrywania anomalii w usłudze Azure Cognitive Services umożliwiało tworzenie rozwiązań do monitorowania metryk przy użyciu łatwych w użyciu [Univariate interfejsów API wykrywania anomalii w serii czasowej](overview.md). Dzięki umożliwieniu analizy szeregów czasowych indywidualnie, detektor anomalii univariate zapewnia prostotę i skalowalność.

Nowe interfejsy API **wykrywania anomalii** w usłudze wieloczynnikowa umożliwiają deweloperom łatwe integrowanie zaawansowanego systemu AI do wykrywania anomalii z grup metryk, bez konieczności znajomości wiedzy uczenia maszynowego ani etykietowania danych. Zależności i wzajemnych korelacji między do 300 różnych sygnałów są teraz automatycznie zliczane jako kluczowe czynniki. Ta nowa funkcja pomaga aktywnie chronić złożone systemy, takie jak aplikacje, serwery, maszyny fabryki, statki kosmiczne lub nawet Twoje firmy, z błędów.

Wyobraź sobie 20 czujników z aparatu autogenerowanego 20 różnych sygnałów, takich jak drgania, temperatura, ciśnienie paliwa itd. Odczyty tych sygnałów indywidualnie mogą nie poinformowania o problemach na poziomie systemu, ale wspólnie mogą reprezentować kondycję aparatu. Gdy interakcje tych sygnałów są odbiegające poza normalnym zakresem, funkcja wykrywania anomalii wieloczynnikowa może wyróżnić anomalie, takie jak specjalista z sezonem. Bazowe modele AI są przeszkolone i dostosowywane przy użyciu danych, dzięki czemu rozumieją unikatowe potrzeby firmy. Dzięki nowym interfejsom API w detektorze anomalii deweloperzy mogą teraz łatwo zintegrować funkcje wykrywania anomalii wieloczynnikowa szeregów czasowych z rozwiązaniami do konserwacji predykcyjnej, AIOps rozwiązania do monitorowania na potrzeby złożonego oprogramowania dla przedsiębiorstw lub narzędzi analizy biznesowej.

## <a name="when-to-use-multivariate-versus-univariate"></a>Kiedy używać **wieloczynnikowa** a **univariate**

Użyj interfejsów API wykrywania anomalii univariate, jeśli celem jest wykrycie anomalii z normalnego wzorca na poszczególnych seriach czasowych w sposób czysty na podstawie własnych danych historycznych. Przykłady: chcesz wykrywać dzienne anomalie przychodu na podstawie danych dotyczących przychodów lub wykrycie procesora CPU w sposób całkowicie oparty na danych procesora CPU.
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![Wykres liniowy szeregów czasowych z wartościami wahań pojedynczej zmiennej przechwytywanymi przez niebieską linię z anomaliami identyfikowanymi przez pomarańczowe okręgi](./media/anomaly_detection2.png)

Użyj poniższych interfejsów API wykrywania anomalii w programie wieloczynnikowa, jeśli chcesz wykryć anomalie na poziomie systemu z grupy danych szeregów czasowych. Szczególnie w przypadku, gdy którakolwiek z szeregów czasowych nie powiedzie się, a wszystkie sygnały (grupy szeregów czasowych) można wyszukać w całości w celu określenia problemu dotyczącego poziomu systemu. Przykład: masz kosztowne zasoby fizyczne, takie jak samolot, sprzęt w urządzeniu naftowym lub satelity. Każdy z tych zasobów ma dziesiątki lub setki różnych typów czujników. Należy zwrócić uwagę na wszystkie te sygnały szeregów czasowych z tych czujników, aby zdecydować, czy występuje problem z poziomem systemu.

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![Wiele wykresów liniowych szeregów czasowych dla zmiennych: drgania, temperatura, ciśnienie, prędkość, prędkość rotacji z anomaliami wyróżnionymi w kolorze pomarańczowym](./media/multivariate-graph.png)

## <a name="region-support"></a>Obsługa regionów

Publiczna wersja zapoznawcza wykrywania anomalii w wieloczynnikowa jest obecnie dostępna w trzech regionach: zachodnie stany USA 2, wschód stany USA 2 i Europa Zachodnia.

## <a name="algorithms"></a>Algorytmy

- [Wieloczynnikowa wykrywanie anomalii szeregów czasowych za pośrednictwem sieci uwagi dotyczącej grafu](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Dołączanie do społeczności narzędzia do wykrywania anomalii

- Dołącz do [grupy doradcy wykrywania anomalii w usłudze Microsoft Teams](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>Następne kroki

- [Przewodniki Szybki Start](./quickstarts/client-libraries-multivariate.md).
- [Najlepsze rozwiązania](./concepts/best-practices-multivariate.md): w tym artykule zawarto informacje dotyczące zalecanych wzorców do użycia z interfejsami API wieloczynnikowa.
