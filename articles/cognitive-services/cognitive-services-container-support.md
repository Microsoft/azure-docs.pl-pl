---
title: Korzystanie z usługi Azure Cognitive Services Containers lokalnie
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać kontenerów platformy Docker do korzystania z Cognitive Services lokalnie.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 12/16/2020
ms.author: aahi
keywords: lokalna, Docker, kontener, Kubernetes
ms.openlocfilehash: 007dfe6d67d504286b9546fe0139055b58dc700f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285607"
---
# <a name="azure-cognitive-services-containers"></a>Kontenery usługi Azure Cognitive Services

Usługa Azure Cognitive Services udostępnia kilka [kontenerów platformy Docker](https://www.docker.com/what-container) , które umożliwiają korzystanie z tych samych interfejsów API, które są dostępne na platformie Azure lokalnie. Użycie tych kontenerów zapewnia elastyczność Cognitive Services bliżej danych pod kątem zgodności, bezpieczeństwa lub innych przyczyn operacyjnych. Obsługa kontenerów jest obecnie dostępna dla podzestawu Cognitive Services platformy Azure.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Kontenerach to podejście do dystrybucji oprogramowania, w której aplikacje lub usługi, w tym jej zależności & konfiguracji, są pakowane razem jako obraz kontenera. W przypadku niewielkiej lub żadnej modyfikacji obraz kontenera można wdrożyć na hoście kontenera. Kontenery są odizolowane od siebie i w podstawowym systemie operacyjnym, z mniejszą wielkością niż maszyna wirtualna. Kontenery mogą być tworzone na podstawie obrazów kontenerów dla zadań krótkoterminowych i usuwane, gdy nie są już potrzebne.

## <a name="features-and-benefits"></a>Funkcje i korzyści

- **Niezmienna infrastruktura**: Włącz zespoły DevOps ", aby korzystały z spójnego i niezawodnego zestawu znanych parametrów systemowych, jednocześnie umożliwiając dostosowanie się do zmian. Kontenery zapewniają elastyczność przestawiania w przewidywalnym ekosystemie i unikają dryfowania konfiguracji.
- **Kontrola nad danymi**: Wybierz miejsce, w którym dane są przetwarzane przez Cognitive Services. Może to być konieczne, jeśli nie można wysłać danych do chmury, ale wymagany jest dostęp do interfejsy API usług Cognitive Services. Obsługa spójności w środowiskach hybrydowych — między danymi, zarządzaniem, tożsamością i zabezpieczeniami.
- **Kontrola nad aktualizacjami modelu**: elastyczność w zakresie przechowywania wersji i aktualizowania modeli wdrożonych w ich rozwiązaniach.
- **Architektura przenośna**: umożliwia tworzenie architektury aplikacji przenośnej, którą można wdrożyć na platformie Azure, lokalnie i na krawędzi. Kontenery można wdrażać bezpośrednio w [usłudze Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)lub do klastra [Kubernetes](https://kubernetes.io/) wdrożonego w [Azure Stack](/azure-stack/operator). Aby uzyskać więcej informacji, zobacz [wdrażanie Kubernetes do Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Wysoka przepływność/małe opóźnienia**: umożliwiają klientom skalowanie w celu uzyskania wysokiej przepływności i małych opóźnień przez umożliwienie Cognitive Services uruchamiania w pobliżu ich logiki i danych aplikacji. Kontenery nie przekraczają transakcji na sekundę (TPS) i można je skalować w górę i w dół, aby obsługiwać zapotrzebowanie w przypadku podania niezbędnych zasobów sprzętowych.
- **Skalowalność**: dzięki coraz większej popularności oprogramowania kontenerach i aranżacji kontenerów, takich jak Kubernetes; skalowalność jest w programie Forefront z góry postępów technologicznych. Tworzenie aplikacji na skalowalnym serwerze klastra zapewnia wysoką dostępność.

## <a name="containers-in-azure-cognitive-services"></a>Kontenery w usłudze Azure Cognitive Services

Kontenery usługi Azure Cognitive Services oferują następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzestaw funkcji usług w usłudze Azure Cognitive Services. Instrukcje i lokalizacje obrazów można znaleźć w poniższych tabelach. Dostępna jest również lista [obrazów kontenerów](containers/container-image-tags.md) .

### <a name="decision-containers"></a>Kontenery decyzyjne

| Usługa |  Kontener | Opis | Dostępność |
|--|--|--|--|
| [Wykrywacz anomalii][ad-containers] | **Detektor anomalii** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Interfejs API wykrywania anomalii umożliwia monitorowanie i wykrywanie anomalii w danych szeregów czasowych przy użyciu uczenia maszynowego. | Ogólnie dostępne |

### <a name="language-containers"></a>Kontenery języka

| Usługa |  Kontener | Opis | Dostępność |
|--|--|--|--|
| [LUIS][lu-containers] |  **Luis** ([obraz](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Ładuje przeszkolony lub opublikowany model Language Understanding, znany również jako aplikacja LUIS, do kontenera Docker i zapewnia dostęp do prognoz zapytania z punktów końcowych interfejsu API kontenera. Można zebrać dzienniki zapytań z kontenera i przekazać je z powrotem do [portalu Luis](https://www.luis.ai) , aby zwiększyć dokładność przewidywania aplikacji. | Ogólnie dostępne |
| [Analiza tekstu][ta-containers-keyphrase] | **Wyodrębnianie kluczowych fraz** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Wyodrębnia kluczowe frazy do identyfikowania głównych punktów. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. | Wersja zapoznawcza |
| [Analiza tekstu][ta-containers-language] |  **Wykrywanie języka tekstowe** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | W przypadku do 120 języków program wykrywa język, w którym wprowadzany jest tekst wejściowy, i raportuje jeden kod języka dla każdego dokumentu przesłanego w żądaniu. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. | Wersja zapoznawcza |
| [Analiza tekstu][ta-containers-sentiment] | **Analiza tonacji v3** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analizuje nieprzetworzony tekst, aby uzyskać wskazówki dotyczące tonacji pozytywnej lub ujemnej. Ta wersja analizy tonacji zwraca etykiety tonacji (na przykład *dodatnie* lub *ujemne*) dla każdego dokumentu i zdania w nim. |  Ogólnie dostępne |
| [Analiza tekstu][ta-containers-health] |  **Analiza tekstu dla opieki zdrowotnej** | Wyodrębnij i Oznacz informacje medyczne z niestrukturalnego tekstu klinicznego. | Wersja zapoznawcza. [Zażądaj dostępu][request-access]. |

### <a name="speech-containers"></a>Kontenery usługi Mowa

> [!NOTE]
> Aby korzystać z kontenerów mowy, należy wykonać [formularz żądania online](https://aka.ms/csgate).

| Usługa |  Kontener | Opis | Dostępność |
|--|--|--|
| [Interfejs API usługi rozpoznawania mowy][sp-containers-stt] |  **Zamiana mowy na tekst** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Przekształca w czasie rzeczywistym ciągłą mowę na tekst. | Ogólnie dostępne |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-cstt] | **Custom Speech do tekstu** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Przekształca ciągłe używanie mowy w czasie rzeczywistym do tekstu przy użyciu modelu niestandardowego. | Ogólnie dostępne |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-tts] | **Zamiana tekstu na mowę** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Konwertuje tekst na naturalnie brzmiącą mowę. | Ogólnie dostępne |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-ctts] | **Niestandardowa Zamiana tekstu na mowę** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Konwertuje tekst na mowę dźwięku naturalnego przy użyciu modelu niestandardowego. | Wersja zapoznawcza |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-ntts] | **Neuronowych Zamiana tekstu na mowę** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Konwertuje tekst na naturalną dźwiękową mowę przy użyciu technologii sieci głębokiej neuronowych, co pozwala na bardziej naturalną syntezę mowy. | Ogólnie dostępne |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-lid] | **Wykrywanie języka mowy** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Określa język dźwiękowy. | Wersja zapoznawcza |

### <a name="vision-containers"></a>Kontenery przetwarzania obrazów

> [!WARNING]
> 11 czerwca 2020 roku firma Microsoft ogłosiła, że nie będzie sprzedawać technologii rozpoznawania twarzy departamentom policji w Stanach Zjednoczonych, dopóki nie zostaną wdrożone ścisłe przepisy oparte na prawach człowieka. W związku z tym klienci nie mogą korzystać z funkcji rozpoznawania twarzy ani funkcji dostępnych w ramach usług platformy Azure, takich jak twarzy lub Video Indexer, jeśli klient jest lub zezwala na korzystanie z takich usług przez lub w odniesieniu do działu policji w Stany Zjednoczone.

| Usługa |  Kontener | Opis | Dostępność |
|--|--|--|--|
| [Przetwarzanie obrazów][cv-containers] | **Odczytaj OCR** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Kontener OCR do odczytu umożliwia wyodrębnienie tekstu napisanego i odręcznego z obrazów i dokumentów z obsługą formatów plików JPEG, PNG, BMP, PDF i TIFF. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API odczytu](./computer-vision/overview-ocr.md). | Wersja zapoznawcza. [Zażądaj dostępu][request-access]. |
| [Analiza przestrzenna][spa-containers] | **Analiza przestrzenna** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analizuje wideo w czasie rzeczywistym, aby zrozumieć relacje przestrzenne między osobami, ich przenoszeniem i interakcją z obiektami w środowiskach fizycznych. | Wersja zapoznawcza. [Zażądaj dostępu][request-access]. |
| [Rozpoznawanie twarzy][fa-containers] | **Rozpoznawanie twarzy** | Wykrywa ludzkie twarze w obrazach i identyfikuje atrybuty, w tym dzielnice twarzy (takie jak nos i oczy), płeć, wiek i inne funkcje twarzy przewidziane dla maszyn. Oprócz wykrywania, twarz może sprawdzić, czy dwie twarze w tym samym obrazie lub różnych obrazach są takie same, przy użyciu oceny pewności, czy też można porównać twarze z bazą danych, aby zobaczyć, czy podobna lub identyczna twarz już istnieje. Może również organizować podobne twarze w grupy przy użyciu współużytkowanych cech wizualnych. | Niedostępny |
| [Aparat rozpoznawania formularzy][fr-containers] | **Rozpoznawanie formularzy** | Zrozumienie formularza stosuje technologię uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość oraz tabel z formularzy. | Niedostępny | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Ponadto niektóre kontenery są obsługiwane w ramach oferty [zasobów](cognitive-services-apis-create-account.md) w ramach usługi Cognitive Services. Można utworzyć jeden Cognitive Services pojedynczym zasobem i korzystać z tego samego klucza rozliczania dla następujących usług:

* Przetwarzanie obrazów
* Rozpoznawanie twarzy
* LUIS
* Analiza tekstu

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów Cognitive Services platformy Azure należy spełnić następujące wymagania wstępne:

**Aparat platformy Docker**: aparat platformy Docker musi być zainstalowany lokalnie. Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)i [Windows](https://docs.docker.com/docker-for-windows/). W systemie Windows program Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy Docker można także wdrażać bezpośrednio w [usłudze Azure Kubernetes Service](../aks/index.yml) lub [Azure Container Instances](../container-instances/index.yml).

Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure.

**Znajomość programu microsoft Container Registry i platformy Docker**: należy dysponować podstawową wiedzą na temat koncepcji firmy Microsoft Container Registry i platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).

Poszczególne kontenery mogą mieć własne wymagania, a także wymagania dotyczące alokacji serwera i pamięci.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Przykłady dla deweloperów

Przykłady dla deweloperów są dostępne w naszym [repozytorium GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [przepisami kontenera](containers/container-reuse-recipe.md) , których można używać z Cognitive Services.

Zainstaluj i Eksploruj funkcje udostępniane przez kontenery w usłudze Azure Cognitive Services:

* [Kontenery detektorów anomalii][ad-containers]
* [Kontenery przetwarzanie obrazów][cv-containers]
* [Kontenery do rozpoznawania][fa-containers]
* [Kontenery aparatu rozpoznawania formularzy][fr-containers]
* [Kontenery Language Understanding (LUIS)][lu-containers]
* [Kontenery interfejsu API usługi Speech Service][sp-containers]
* [Kontenery analiza tekstu][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u