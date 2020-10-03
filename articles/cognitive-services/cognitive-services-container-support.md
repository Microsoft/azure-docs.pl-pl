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
ms.date: 09/28/2020
ms.author: aahi
keywords: lokalna, Docker, kontener, Kubernetes
ms.openlocfilehash: ed61760312ad8bada0241b0338c36ab3557e2caa
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665416"
---
# <a name="azure-cognitive-services-containers"></a>Kontenery usługi Azure Cognitive Services

> [!WARNING]
> 11 czerwca 2020 roku firma Microsoft ogłosiła, że nie będzie sprzedawać technologii rozpoznawania twarzy departamentom policji w Stanach Zjednoczonych, dopóki nie zostaną wdrożone ścisłe przepisy oparte na prawach człowieka. W związku z tym klienci nie mogą korzystać z funkcji rozpoznawania twarzy ani funkcji dostępnych w ramach usług platformy Azure, takich jak twarzy lub Video Indexer, jeśli klient jest lub zezwala na korzystanie z takich usług przez lub w odniesieniu do działu policji w Stany Zjednoczone.

Usługa Azure Cognitive Services udostępnia kilka [kontenerów platformy Docker](https://www.docker.com/what-container) , które umożliwiają korzystanie z tych samych interfejsów API, które są dostępne na platformie Azure lokalnie. Użycie tych kontenerów zapewnia elastyczność Cognitive Services bliżej danych pod kątem zgodności, bezpieczeństwa lub innych przyczyn operacyjnych. 

Obsługa kontenerów jest obecnie dostępna dla podzestawu Cognitive Services platformy Azure, w tym części:

> [!div class="checklist"]
> * [Narzędzie do wykrywania anomalii][ad-containers]
> * [Odczytaj OCR (optyczne rozpoznawanie znaków) ][cv-containers]
> * [Analiza przestrzenna][spa-containers]
> * [Rozpoznawanie twarzy][fa-containers]
> * [Rozpoznawanie formularzy][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Interfejs API usługi rozpoznawania mowy][sp-containers]
> * [Analiza tekstu][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Kontenerach to podejście do dystrybucji oprogramowania, w której aplikacje lub usługi, w tym jej zależności & konfiguracji, są pakowane razem jako obraz kontenera. W przypadku niewielkiej lub żadnej modyfikacji obraz kontenera można wdrożyć na hoście kontenera. Kontenery są odizolowane od siebie i w podstawowym systemie operacyjnym, z mniejszą wielkością niż maszyna wirtualna. Kontenery mogą być tworzone na podstawie obrazów kontenerów dla zadań krótkoterminowych i usuwane, gdy nie są już potrzebne.

Zasoby Cognitive Services są dostępne na [Microsoft Azure](https://azure.microsoft.com). Zaloguj się do [Azure Portal](https://portal.azure.com/) , aby utworzyć i poznać zasoby platformy Azure dla tych usług.

## <a name="features-and-benefits"></a>Funkcje i korzyści

- **Niezmienna infrastruktura**: Włącz zespoły DevOps ", aby korzystały z spójnego i niezawodnego zestawu znanych parametrów systemowych, jednocześnie umożliwiając dostosowanie się do zmian. Kontenery zapewniają elastyczność przestawiania w przewidywalnym ekosystemie i unikają dryfowania konfiguracji.
- **Kontrola nad danymi**: Wybierz miejsce, w którym dane są przetwarzane przez Cognitive Services. Może to być konieczne, jeśli nie można wysłać danych do chmury, ale wymagany jest dostęp do interfejsy API usług Cognitive Services. Obsługa spójności w środowiskach hybrydowych — między danymi, zarządzaniem, tożsamością i zabezpieczeniami.
- **Kontrola nad aktualizacjami modelu**: elastyczność w zakresie przechowywania wersji i aktualizowania modeli wdrożonych w ich rozwiązaniach.
- **Architektura przenośna**: umożliwia tworzenie architektury aplikacji przenośnej, którą można wdrożyć na platformie Azure, lokalnie i na krawędzi. Kontenery można wdrażać bezpośrednio w [usłudze Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)lub do klastra [Kubernetes](https://kubernetes.io/) wdrożonego w [Azure Stack](/azure-stack/operator). Aby uzyskać więcej informacji, zobacz [wdrażanie Kubernetes do Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Wysoka przepływność/małe opóźnienia**: umożliwiają klientom skalowanie w celu uzyskania wysokiej przepływności i małych opóźnień przez umożliwienie Cognitive Services uruchamiania w pobliżu ich logiki i danych aplikacji. Kontenery nie przekraczają transakcji na sekundę (TPS) i można je skalować w górę i w dół, aby obsługiwać zapotrzebowanie w przypadku podania niezbędnych zasobów sprzętowych.
- **Skalowalność**: dzięki coraz większej popularności oprogramowania kontenerach i aranżacji kontenerów, takich jak Kubernetes; skalowalność jest w programie Forefront z góry postępów technologicznych. Tworzenie aplikacji na skalowalnym serwerze klastra zapewnia wysoką dostępność.

## <a name="containers-in-azure-cognitive-services"></a>Kontenery w usłudze Azure Cognitive Services

Kontenery usługi Azure Cognitive Services oferują następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzestaw funkcji usług na platformie Azure Cognitive Services:

| Usługa | Obsługiwana warstwa cenowa | Kontener | Opis |
|--|--|--|--|
| [Wykrywacz anomalii][ad-containers] | F0, S0 | **Anomalia — detektor** ([obraz](https://hub.docker.com/_/azure-cognitive-services-decision-anomaly-detector))  | Interfejs API wykrywania anomalii umożliwia monitorowanie i wykrywanie anomalii w danych szeregów czasowych przy użyciu uczenia maszynowego.<br>[Żądanie dostępu][request-access] |
| [Przetwarzanie obrazów][cv-containers] | F0, S1 | **Przeczytaj** OCR ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Kontener OCR do odczytu umożliwia wyodrębnienie tekstu napisanego i odręcznego z obrazów i dokumentów z obsługą formatów plików JPEG, PNG, BMP, PDF i TIFF. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API odczytu](./computer-vision/concept-recognizing-text.md).<br>[Żądanie dostępu][request-access] |
| [Rozpoznawanie twarzy][fa-containers] | F0, S0 | **Rozpoznawanie twarzy** | Wykrywa ludzkie twarze w obrazach i identyfikuje atrybuty, w tym dzielnice twarzy (takie jak nos i oczy), płeć, wiek i inne funkcje twarzy przewidziane dla maszyn. Oprócz wykrywania, twarz może sprawdzić, czy dwie twarze w tym samym obrazie lub różnych obrazach są takie same, przy użyciu oceny pewności, czy też można porównać twarze z bazą danych, aby zobaczyć, czy podobna lub identyczna twarz już istnieje. Może również organizować podobne twarze w grupy przy użyciu współużytkowanych cech wizualnych. |
| [Aparat rozpoznawania formularzy][fr-containers] | F0, S0 | **Rozpoznawanie formularzy** | Zrozumienie formularza stosuje technologię uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość oraz tabel z formularzy. |
| [LUIS][lu-containers] | F0, S0 | **Luis** ([obraz](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Ładuje przeszkolony lub opublikowany model Language Understanding, znany również jako aplikacja LUIS, do kontenera Docker i zapewnia dostęp do prognoz zapytania z punktów końcowych interfejsu API kontenera. Można zebrać dzienniki zapytań z kontenera i przekazać je z powrotem do [portalu Luis](https://www.luis.ai) , aby zwiększyć dokładność przewidywania aplikacji. |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-stt] | F0, S0 | **Zamiana mowy na tekst** ([obraz](https://hub.docker.com/_/azure-cognitive-services-speechservices-speech-to-text)) | Przekształca w czasie rzeczywistym ciągłą mowę na tekst. |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-cstt] | F0, S0 | **Custom Speech do tekstu** ([obraz](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-speech-to-text)) | Przekształca ciągłe używanie mowy w czasie rzeczywistym do tekstu przy użyciu modelu niestandardowego. |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-tts] | F0, S0 | **Zamiana tekstu na mowę** ([obraz](https://hub.docker.com/_/azure-cognitive-services-speechservices-text-to-speech)) | Konwertuje tekst na naturalnie brzmiącą mowę. |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-ctts] | F0, S0 | **Niestandardowa Zamiana tekstu na mowę** ([obraz](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-text-to-speech)) | Konwertuje tekst na mowę dźwięku naturalnego przy użyciu modelu niestandardowego. |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-ntts] | F0, S0 | **Neuronowych Zamiana tekstu na mowę** ([obraz](https://hub.docker.com/_/azure-cognitive-services-speechservices-neural-text-to-speech)) | Konwertuje tekst na naturalną dźwiękową mowę przy użyciu technologii sieci głębokiej neuronowych, co pozwala na bardziej naturalną syntezę mowy. |
| [Analiza tekstu][ta-containers-keyphrase] | F0, S | **Wyodrębnianie kluczowych fraz** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Wyodrębnia kluczowe frazy do identyfikowania głównych punktów. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. |
| [Analiza tekstu][ta-containers-language] | F0, S | **Wykrywanie języka** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | W przypadku do 120 języków program wykrywa język, w którym wprowadzany jest tekst wejściowy, i raportuje jeden kod języka dla każdego dokumentu przesłanego w żądaniu. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. |
| [Analiza tekstu][ta-containers-sentiment] | F0, S | **Analiza tonacji v3** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analizuje nieprzetworzony tekst, aby uzyskać wskazówki dotyczące tonacji pozytywnej lub ujemnej. Ta wersja analizy tonacji zwraca etykiety tonacji (na przykład *dodatnie* lub *ujemne*) dla każdego dokumentu i zdania w nim. |
| [Analiza tekstu][ta-containers-health] | F0, S | **analiza tekstu dla kondycji** | Wyodrębnij i Oznacz informacje medyczne z niestrukturalnego tekstu klinicznego. |
| [Analiza przestrzenna][spa-containers] | S0 | **Analiza przestrzenna** | Wyodrębnij i Oznacz informacje medyczne z niestrukturalnego tekstu klinicznego. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Ponadto niektóre kontenery są obsługiwane w Cognitive Services wszystkich kluczy zasobów dostępnych [**w ramach jednej oferty**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) . Można utworzyć jeden Cognitive Services pojedynczym zasobem i korzystać z tego samego klucza rozliczania dla następujących usług:

* Przetwarzanie obrazów
* Rozpoznawanie twarzy
* LUIS
* Analiza tekstu

## <a name="container-availability-in-azure-cognitive-services"></a>Dostępność kontenera na platformie Azure Cognitive Services

Kontenery usługi Azure Cognitive Services są publicznie dostępne za pośrednictwem subskrypcji platformy Azure, a obrazy kontenerów platformy Docker można ściągnąć z programu Microsoft Container Registry lub Docker Hub. Za pomocą polecenia [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) można pobrać obraz kontenera z odpowiedniego rejestru.

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów Cognitive Services platformy Azure należy spełnić następujące wymagania wstępne:

**Aparat platformy Docker**: aparat platformy Docker musi być zainstalowany lokalnie. Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)i [Windows](https://docs.docker.com/docker-for-windows/). W systemie Windows program Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy Docker można także wdrażać bezpośrednio w [usłudze Azure Kubernetes Service](../aks/index.yml) lub [Azure Container Instances](../container-instances/index.yml).

Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure.

**Znajomość programu microsoft Container Registry i platformy Docker**: należy dysponować podstawową wiedzą na temat koncepcji firmy Microsoft Container Registry i platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).

Poszczególne kontenery mogą mieć własne wymagania, a także wymagania dotyczące alokacji serwera i pamięci.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

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
[spa-containers]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container
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
