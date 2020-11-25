---
title: Co to jest interfejs API narzędzia do wykrywania anomalii?
titleSuffix: Azure Cognitive Services
description: Użyj algorytmów API wykrywania anomalii, aby zastosować wykrywanie anomalii w danych szeregów czasowych.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 11/23/2020
ms.author: mbullwin
keywords: wykrywanie anomalii, uczenie maszynowe, algorytmy
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 1a13b962a000e6e3b2143dc23f94b63c122bd963
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999108"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co to jest interfejs API narzędzia do wykrywania anomalii?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Interfejs API wykrywania anomalii umożliwia monitorowanie i wykrywanie anomalii w danych szeregów czasowych bez konieczności znajomości uczenia maszynowego. Algorytmy interfejsu API wykrywania anomalii dostosowują się przez automatyczne identyfikowanie i stosowanie najlepszych modeli do danych, bez względu na wielkość branżową, scenariusz lub dane. Korzystając z danych szeregów czasowych, interfejs API określa granice wykrywania anomalii, oczekiwane wartości i które punkty danych są anomaliami.

![Wykrywanie zmian wzorca w żądaniach obsługi](./media/anomaly_detection2.png)

Korzystanie z detektora anomalii nie wymaga wcześniejszego doświadczenia w uczeniu maszynowym, a interfejs API RESTful umożliwia łatwą integrację usługi z aplikacjami i procesami.

## <a name="features"></a>Funkcje

Dzięki detektorowi anomalii można automatycznie wykrywać anomalie w danych szeregów czasowych lub w miarę ich występowania w czasie rzeczywistym.

|Cechy  |Opis  |
|---------|---------|
|Wykrywanie anomalii w czasie rzeczywistym. | Wykrywaj anomalie w danych przesyłanych strumieniowo, używając wcześniej zaobserwowanych punktów danych w celu ustalenia, czy Najnowsza z nich jest nieaktualna. Ta operacja generuje model przy użyciu wysyłanych punktów danych i określa, czy punkt docelowy jest anomalią. Wywołując interfejs API z każdym wygenerowanym nowym punktem danych, można monitorować dane w miarę ich tworzenia. |
|Wykrywaj anomalie w zestawie danych jako Partia zadań. | Skorzystaj z szeregów czasowych, aby wykryć ewentualne anomalie, które mogą istnieć w danych. Ta operacja generuje model przy użyciu wszystkich danych szeregów czasowych, przy czym każdy punkt jest analizowany z tym samym modelem.         |
|Wykrywaj punkty zmian w zestawie danych jako Partia zadań. | Skorzystaj z szeregów czasowych, aby wykryć wszystkie punkty zmian trendów, które istnieją w danych. Ta operacja generuje model przy użyciu wszystkich danych szeregów czasowych, przy czym każdy punkt jest analizowany z tym samym modelem.    |
| Uzyskaj dodatkowe informacje na temat danych. | Uzyskaj przydatne szczegóły dotyczące danych i wszelkich obserwowanych anomalii, w tym oczekiwanych wartości, granic anomalii i pozycji. |
| Dostosuj granice wykrywania anomalii. | Interfejs API wykrywania anomalii automatycznie tworzy granice na potrzeby wykrywania anomalii. Dostosuj te granice, aby zwiększyć lub zmniejszyć czułość interfejsu API na anomalie danych i lepiej dopasować dane. |

## <a name="demo"></a>Demonstracja

Zapoznaj się z tą [interaktywną prezentacją](https://aka.ms/adDemo) , aby dowiedzieć się, jak działa detektor anomalii.
Aby uruchomić demonstrację, należy utworzyć zasób wykrywania anomalii i uzyskać klucz interfejsu API i punkt końcowy.

## <a name="notebook"></a>Notes

Aby dowiedzieć się, jak wywołać interfejs API wykrywania anomalii, Wypróbuj ten [Notes](https://aka.ms/adNotebook). W tym Jupyter Notebook pokazano, jak wysłać żądanie interfejsu API i wizualizować wynik.

Aby uruchomić Notes, wykonaj następujące czynności:

1. Pobierz prawidłowy klucz subskrypcji interfejsu API wykrywania anomalii i punkt końcowy interfejsu API. W poniższej sekcji znajdują się instrukcje dotyczące rejestrowania się.
1. Zaloguj się, a następnie kliknij pozycję Klonuj w prawym górnym rogu.
1. Usuń zaznaczenie opcji "publiczny" w oknie dialogowym przed ukończeniem operacji klonowania, w przeciwnym razie Notes, w tym wszystkie klucze subskrypcji, będzie publiczny.
1. Kliknij pozycję **Uruchom przy bezpłatnej obliczeń**
1. Wybierz jeden z notesów.
1. Dodaj do zmiennej prawidłowy klucz subskrypcji interfejsu API wykrywania anomalii `subscription_key` .
1. Zmień `endpoint` zmienną na punkt końcowy. Na przykład: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Na górnym pasku menu kliknij **komórkę**, a następnie **Uruchom wszystkie**.

## <a name="workflow"></a>Przepływ pracy

Interfejs API wykrywania anomalii to usługa sieci Web RESTful, ułatwiająca wywoływanie z dowolnego języka programowania, który może wykonywać żądania HTTP i analizować dane JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Po zarejestrowaniu:

1. Zrób dane szeregów czasowych i Przekształć je w prawidłowy format JSON. Podczas przygotowywania danych należy stosować [najlepsze rozwiązania](concepts/anomaly-detection-best-practices.md) w celu uzyskania najlepszych wyników.
1. Wyślij żądanie do interfejsu API wykrywania anomalii z danymi.
1. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="algorithms"></a>Algorytmy

* Aby uzyskać informacje o używanych algorytmach, zobacz następujące Blogi techniczne:
    * [Wprowadzenie do interfejsu API narzędzia do wykrywania anomalii platformy Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Omówienie algorytmu SR-CNN w usłudze Azure anomalie wykrywania](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Aby dowiedzieć się więcej na temat algorytmów SR-CNN opracowanych przez firmę Microsoft, możesz przeczytać informacje na temat [usługi wykrywania anomalii w serii czasowej w firmie Microsoft](https://arxiv.org/abs/1906.03821) (zaakceptowanej przez KDD 2019).


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="deploy-on-premises-using-docker-containers"></a>Wdrażanie lokalnie przy użyciu kontenerów platformy Docker

[Używanie kontenerów wykrywania anomalii](anomaly-detector-container-howto.md) do wdrażania funkcji interfejsu API w środowisku lokalnym. Kontenery platformy Docker umożliwiają przełączenie usługi do danych pod kątem zgodności, bezpieczeństwa lub innych przyczyn operacyjnych.

## <a name="join-the-anomaly-detector-community"></a>Dołączanie do społeczności narzędzia do wykrywania anomalii

* Dołącz do [grupy doradcy wykrywania anomalii w usłudze Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Zobacz wybraną [zawartość wygenerowaną przez użytkownika](user-generated-content.md)

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii](quickstarts/detect-data-anomalies-csharp.md)
* [Demonstracja online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) interfejsu API wykrywania anomalii
* [Dokumentacja interfejsu API REST](https://aka.ms/anomaly-detector-rest-api-ref) usługi wykrywania anomalii
