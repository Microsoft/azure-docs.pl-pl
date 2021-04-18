---
title: Co to jest interfejs API narzędzia do wykrywania anomalii?
titleSuffix: Azure Cognitive Services
description: Użyj algorytmów Narzędzie do wykrywania anomalii API, aby zastosować wykrywanie anomalii do danych szeregów czasu.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: wykrywanie anomalii, uczenie maszynowe, algorytmy
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d63399d0f492f85a4a2d57a595a6d8ef5b606d92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599523"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co to jest interfejs API narzędzia do wykrywania anomalii?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Interfejs API Narzędzie do wykrywania anomalii umożliwia monitorowanie i wykrywanie nieprawidłowości w danych szeregów czasowych bez konieczności poznania uczenia maszynowego. Algorytmy Narzędzie do wykrywania anomalii API są dostosowywane przez automatyczne identyfikowanie i stosowanie najlepiej dopasowanych modeli do danych, niezależnie od branży, scenariusza lub ilości danych. Korzystając z danych szeregów czasu, interfejs API określa granice wykrywania anomalii, oczekiwane wartości i punkty danych, które są anomaliami.

![Wykrywanie zmian wzorca w żądaniach obsługi](./media/anomaly_detection2.png)

Korzystanie z Narzędzie do wykrywania anomalii nie wymaga wcześniejszego doświadczenia w uczeniu maszynowym, a interfejs API RESTful umożliwia łatwe integrowanie usługi z aplikacjami i procesami.

Ta dokumentacja zawiera następujące typy artykułów:
* Przewodnik [Szybki start to](./Quickstarts/client-libraries.md) instrukcje krok po kroku, które pozwalają na wywołania usługi i uzyskiwanie wyników w krótkim czasie. 
* Przewodniki [z instrukcjami zawierają](./how-to/identify-anomalies.md) instrukcje dotyczące korzystania z usługi w bardziej szczegółowe lub dostosowane sposoby.
* Artykuły [koncepcyjne](./concepts/anomaly-detection-best-practices.md) zawierają szczegółowe wyjaśnienia dotyczące funkcjonalności i funkcji usługi.
* Samouczki [są](./tutorials/batch-anomaly-detection-powerbi.md) dłuższymi przewodnikami, które pokazują, jak używać tej usługi jako składnika w szerszych rozwiązaniach biznesowych.

## <a name="features"></a>Funkcje

Dzięki Narzędzie do wykrywania anomalii możesz automatycznie wykrywać anomalie w danych szeregów czasu lub w czasie rzeczywistym.

|Cecha  |Opis  |
|---------|---------|
|Wykrywanie anomalii w czasie rzeczywistym. | Wykrywanie anomalii w danych przesyłanych strumieniowo przy użyciu wcześniej widocznych punktów danych w celu określenia, czy najnowszy jest anomalią. Ta operacja generuje model przy użyciu przesyłanych punktów danych i określa, czy punkt docelowy jest anomalią. Wywołując interfejs API przy użyciu każdego wygenerowanego nowego punktu danych, możesz monitorować dane podczas ich tworzenia. |
|Wykrywanie anomalii w zestawie danych jako partii. | Używaj szeregów czasu do wykrywania wszelkich anomalii, które mogą istnieć w danych. Ta operacja generuje model przy użyciu całych danych szeregów czasowych, z których każdy punkt jest analizowany przy użyciu tego samego modelu.         |
|Wykrywanie punktów zmiany w całym zestawie danych jako partii. | Użyj szeregu czasowego, aby wykryć wszelkie punkty zmiany trendu, które istnieją w danych. Ta operacja generuje model przy użyciu całych danych szeregów czasowych, z których każdy punkt jest analizowany przy użyciu tego samego modelu.    |
| Uzyskaj dodatkowe informacje o danych. | Uzyskaj przydatne szczegóły dotyczące danych i wszelkich zaobserwowanych anomalii, w tym oczekiwane wartości, granice anomalii i pozycje. |
| Dostosowywanie granic wykrywania anomalii. | Interfejs API Narzędzie do wykrywania anomalii automatycznie tworzy granice wykrywania anomalii. Dostosuj te granice, aby zwiększyć lub zmniejszyć czułość interfejsu API na anomalie danych i lepiej dopasować je do danych. |

## <a name="demo"></a>Demonstracja

Zapoznaj się z tą [interaktywną demonstracją,](https://aka.ms/adDemo) aby dowiedzieć się, Narzędzie do wykrywania anomalii działa.
Aby uruchomić pokaz, musisz utworzyć zasób usługi Narzędzie do wykrywania anomalii i uzyskać klucz interfejsu API oraz punkt końcowy.

## <a name="notebook"></a>Notes

Aby dowiedzieć się, jak wywołać interfejs API Narzędzie do wykrywania anomalii, wypróbuj ten [notes.](https://aka.ms/adNotebook) W Jupyter Notebook przedstawiono, jak wysłać żądanie interfejsu API i zwizualizować wynik.

Aby uruchomić notes, wykonaj następujące kroki:

1. Uzyskaj prawidłowy klucz subskrypcji Narzędzie do wykrywania anomalii API i punkt końcowy interfejsu API. W poniższej sekcji znajdują się instrukcje dotyczące rejestracji.
1. Zaloguj się i wybierz pozycję Klonuj w prawym górnym rogu.
1. Usuń zaznaczenie opcji "public" w oknie dialogowym przed ukończeniem operacji klonowania. W przeciwnym razie notes, w tym wszystkie klucze subskrypcji, będzie publiczny.
1. Wybierz pozycję **Uruchom w bezpłatnych obliczeniach**
1. Wybierz jeden z notesów.
1. Dodaj prawidłowy klucz subskrypcji Narzędzie do wykrywania anomalii API do `subscription_key` zmiennej .
1. Zmień `endpoint` zmienną na punkt końcowy. Na przykład: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Na górnym pasku menu wybierz pozycję **Komórka,** a następnie **pozycję Uruchom wszystko.**

## <a name="workflow"></a>Przepływ pracy

Interfejs API Narzędzie do wykrywania anomalii to usługa internetowa RESTful, która ułatwia wywołanie z dowolnego języka programowania, który może żądania HTTP i analizowanie danych JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Po zarejestrowaniu się:

1. Weź dane szeregów czasu i przekonwertuj je na prawidłowy format JSON. Podczas [przygotowywania danych w](concepts/anomaly-detection-best-practices.md) celu uzyskania najlepszych wyników należy stosować najlepsze rozwiązania.
1. Wyślij żądanie do interfejsu API Narzędzie do wykrywania anomalii przy użyciu danych.
1. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="algorithms"></a>Algorytmy

* Aby uzyskać informacje o używanych algorytmach, zobacz następujące blogi techniczne:
    * [Wprowadzenie do interfejsu API narzędzia do wykrywania anomalii platformy Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Omówienie algorytmu SR-CNN w usłudze Azure Narzędzie do wykrywania anomalii](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Możesz przeczytać dokument [Time-Series Anomaly Detection Service at Microsoft](https://arxiv.org/abs/1906.03821) (zaakceptowany przez KDD 2019), aby dowiedzieć się więcej o algorytmach SR-CNN opracowanych przez firmę Microsoft.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>Dostępność i nadmiarowość usługi

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>Czy strefa Narzędzie do wykrywania anomalii jest odporna?

Tak. Usługa Narzędzie do wykrywania anomalii jest domyślnie odporna na strefy.

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>Jak mogę skonfigurować usługę Narzędzie do wykrywania anomalii, aby była odporna na strefy?

Do zapewnienia odporności strefy nie jest konieczna żadna konfiguracja klienta. Odporność strefowa dla Narzędzie do wykrywania anomalii zasobów jest domyślnie dostępna i zarządzana przez samą usługę.

## <a name="deploy-on-premises-using-docker-containers"></a>Wdrażanie lokalne przy użyciu kontenerów platformy Docker

[Użyj Narzędzie do wykrywania anomalii kontenerów do](anomaly-detector-container-howto.md) wdrażania funkcji interfejsu API w środowisku lokalnym. Kontenery platformy Docker umożliwiają przybliżanie usługi do danych ze względu na zgodność, zabezpieczenia lub z innych powodów operacyjnych.

## <a name="join-the-anomaly-detector-community"></a>Dołączanie do społeczności narzędzia do wykrywania anomalii

* Dołącz do [grupy Narzędzie do wykrywania anomalii Advisors w aplikacji Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Wyświetlanie zawartości [wygenerowanej przez wybranego użytkownika](user-generated-content.md)

## <a name="next-steps"></a>Następne kroki

* [Szybki start: wykrywanie anomalii w danych szeregów czasu przy użyciu Narzędzie do wykrywania anomalii](quickstarts/client-libraries.md)
* Pokaz online Narzędzie do wykrywania anomalii [API](https://github.com/Azure-Samples/AnomalyDetector/tree/master/ipython-notebook)
* Dokumentacja Narzędzie do wykrywania anomalii [API REST](https://aka.ms/anomaly-detector-rest-api-ref)
