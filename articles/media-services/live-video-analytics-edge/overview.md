---
title: Co to jest analiza filmów wideo na żywo na IoT Edge — Azure
description: Ten temat zawiera omówienie analizy filmów wideo na żywo na IoT Edge. Platforma oferuje możliwości, których można użyć do ulepszania rozwiązań IoT. Na przykład Przechwytuj, Rejestruj, Analizuj wideo na żywo i publikuj wyniki (Analiza wideo i/lub wideo) w usługach platformy Azure.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: b9b8726c5e8ad6850e05aeee48fccabee703080e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90904358"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>Co to jest usługa Live Video Analytics na IoT Edge? (wersja zapoznawcza)

Usługa Live Video Analytics w ramach usługi IoT Edge udostępnia platformę do tworzenia inteligentnych aplikacji wideo, które rozciągają się na brzeg sieci i chmurę. Platforma oferuje możliwość przechwytywania, rejestrowania i analizowania wideo na żywo wraz z publikowaniem wyników (wideo i/lub analiz wideo) do usług platformy Azure (w chmurze i/lub sieci brzegowej). Platforma może służyć do ulepszania rozwiązań IoT dzięki analizie wideo. Funkcje analizy filmów wideo na żywo w witrynie IoT Edge mogą być łączone z innymi modułami Azure IoT Edge, takimi jak Stream Analytics na IoT Edge, Cognitive Services na IoT Edge, a także usług platformy Azure w chmurze, takich jak Media Services, centrum zdarzeń, Cognitive Services itd., aby tworzyć zaawansowane hybrydowe (na przykład aplikacje brzegowe i w chmurze).

Funkcja analizy filmów wideo na żywo na IoT Edge została zaprojektowana jako platforma rozszerzalna, umożliwiająca łączenie różnych modułów brzegowych analizy wideo (takich jak kontenery usług poznawczych, niestandardowych modułów brzegowych utworzonych przez użytkownika z modelami uczenia maszynowego typu open source lub modelami niestandardowymi przeszkolonymi przy użyciu własnych danych) i używanie ich do analizowania wideo na żywo, bez konieczności tworzenia i uruchamiania

## <a name="accelerate-iot-solutions-development"></a>Przyspiesz opracowywanie rozwiązań IoT 

Rozwiązania IoT, które łączą analizę wideo z sygnałami z innych czujników IoT i/lub danymi firmowymi mogą pomóc zautomatyzować lub częściowo zautomatyzować decyzje biznesowe, co prowadzi do wzrostu efektywności. Usługa Live Video Analytics w usłudze IoT Edge umożliwia szybsze tworzenie takich rozwiązań. Możesz skupić się na tworzeniu modułów do analizy wideo i logiki, które są specyficzne dla Twojej firmy, pozwalając platformie na ukrycie złożoności uruchamiania potoku wideo i zarządzania nim.

Korzystając z usługi Analiza filmów wideo na żywo na IoT Edge, możesz nadal korzystać z [kamer CCTV](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) z istniejącymi [systemami zarządzania filmami](https://en.wikipedia.org/wiki/Video_management_system) i tworzyć aplikacje do analizy wideo niezależnie od siebie. Usługi Live Video Analytics w usłudze IoT Edge można używać w połączeniu z zestawami SDK i zestawami narzędzi do przetwarzania obrazów w celu tworzenia innowacyjnych rozwiązań IoT. Przedstawiono to na poniższym diagramie.

![Opracowywanie rozwiązań IoT za pomocą analizy filmów wideo na żywo na IoT Edge](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Obsługiwane środowiska

Obsługiwane są środowiska Linux x86 — 64 i ARM64.
> [!NOTE]
> Obsługa urządzeń z ARM64 jest dostępna w kompilacjach `1.0.4` i nowszych.
> Obsługa środowiska uruchomieniowego Azure IoT Edge na urządzeniach ARM64 jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-started"></a>Rozpoczęcie pracy

Zapoznaj się z następującymi artykułami koncepcji, a następnie skorzystaj z przewodnika Szybki Start, aby uruchomić Wykrywanie ruchu na żywo kanału wideo.

### <a name="concepts"></a>Pojęcia

* [Graf multimedialny](media-graph-concept.md)
* [Nagrywanie wideo](video-recording-concept.md)
* [Odtwarzanie wideo](video-playback-concept.md)
* [Ciągłe nagrywanie wideo](continuous-video-recording-concept.md)
* [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)
* [Platforma Live Video Analytics bez nagrania wideo](analyze-live-video-concept.md)

## <a name="next-steps"></a>Następne kroki

* Postępuj zgodnie z [przewodnikiem Szybki Start: Uruchamianie analizy filmów wideo na żywo z własnym modelem](use-your-model-quickstart.md) , aby dowiedzieć się, jak można uruchomić Wykrywanie ruchu na żywo kanału informacyjnego wideo.
* Przegląd [terminologii](terminology.md)
* Eksplorowanie [materiału Open Source usługi Live Video Analytics](https://github.com/Azure/live-video-analytics)

