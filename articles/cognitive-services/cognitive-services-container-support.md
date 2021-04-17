---
title: Korzystanie Azure Cognitive Services kontenerów lokalnych
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać kontenerów platformy Docker Cognitive Services w środowisku lokalnym.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2021
ms.author: aahi
keywords: on-premises, Docker, container, Kubernetes
ms.openlocfilehash: c40e91d81df448021be74af768bc9d5952b263dd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588228"
---
# <a name="azure-cognitive-services-containers"></a>Kontenery usługi Azure Cognitive Services

Azure Cognitive Services udostępnia kilka [kontenerów platformy Docker,](https://www.docker.com/what-container) które umożliwiają korzystanie z tych samych interfejsów API, które są dostępne lokalnie na platformie Azure. Użycie tych kontenerów zapewnia elastyczność przybliżania Cognitive Services danych ze względu na zgodność, zabezpieczenia lub inne przyczyny operacyjne. Obsługa kontenerów jest obecnie dostępna dla podzestawu Azure Cognitive Services.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Konteneryzacja to podejście do dystrybucji oprogramowania, w którym aplikacja lub usługa, w tym jej zależności & konfiguracji, jest spakowana razem jako obraz kontenera. Przy niewielkiej modyfikacji lub bez modyfikacji obraz kontenera można wdrożyć na hoście kontenera. Kontenery są odizolowane od siebie i bazowego systemu operacyjnego o mniejszym rozmiarze niż maszyna wirtualna. Kontenery mogą być wykonywane na obrazach kontenerów na potrzeby krótkoterminowych zadań i usuwane, gdy nie są już potrzebne.

## <a name="features-and-benefits"></a>Funkcje i korzyści

- **Infrastruktura niezmienna:** umożliwienie zespołom DevOps korzystania ze spójnego i niezawodnego zestawu znanych parametrów systemowych przy jednoczesnym dostosowywaniu się do zmian. Kontenery zapewniają elastyczność obrotu w przewidywalnym ekosystemie i unikają dryfu konfiguracji.
- **Kontrola nad danymi:** wybierz miejsce przetwarzania danych przez Cognitive Services. Może to być niezbędne, jeśli nie możesz wysłać danych do chmury, ale potrzebujesz dostępu do interfejsy API usług Cognitive Services. Obsługa spójności w środowiskach hybrydowych — danych, zarządzania, tożsamości i zabezpieczeń.
- **Kontrola nad aktualizacjami modelu:** Elastyczność w zakresie wersjonarowania i aktualizowania modeli wdrożonych w ich rozwiązaniach.
- **Architektura przenośna:** umożliwia utworzenie przenośnej architektury aplikacji, która może zostać wdrożona na platformie Azure, lokalnie i na brzegu sieci. Kontenery można wdrażać bezpośrednio w [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)lub w klastrze [Kubernetes](https://kubernetes.io/) wdrożonym w [Azure Stack](/azure-stack/operator). Aby uzyskać więcej informacji, zobacz Deploy Kubernetes to Azure Stack (Wdrażanie rozwiązania [Kubernetes na platformie Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)).
- **Wysoka przepływność/małe opóźnienie:** zapewnia klientom możliwość skalowania w celu zapewnienia wysokiej przepływności i wymagań dotyczących małych opóźnień, umożliwiając uruchamianie aplikacji Cognitive Services fizycznie blisko logiki i danych aplikacji. Kontenery nie mają limitu transakcji na sekundę (TPS) i mogą być skalowane zarówno w górę, jak i w celu obsługi zapotrzebowania w przypadku podania niezbędnych zasobów sprzętowych.
- **Skalowalność:** wraz ze stale rosnącą popularnością oprogramowania konteneryzacji i orkiestracji kontenerów, takiego jak Kubernetes; skalowalność jest na pierwszym planie rozwoju technologicznego. Tworzenie aplikacji na skalowalnych podstawach klastra pozwala na wysoką dostępność.

## <a name="containers-in-azure-cognitive-services"></a>Kontenery w Azure Cognitive Services

Azure Cognitive Services kontenery zawierają następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzbiór funkcji z usług w Azure Cognitive Services. Instrukcje i lokalizacje obrazów można znaleźć w poniższych tabelach. Dostępna jest [również lista obrazów](containers/container-image-tags.md) kontenerów.

### <a name="decision-containers"></a>Kontenery decyzji

| Usługa |  Kontener | Opis | Dostępność |
|--|--|--|--|
| [Detektor anomalii][ad-containers] | **Narzędzie do wykrywania anomalii** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Interfejs API Narzędzie do wykrywania anomalii umożliwia monitorowanie i wykrywanie nieprawidłowości w danych szeregów czasowych za pomocą uczenia maszynowego. | Ogólnie dostępne |

### <a name="language-containers"></a>Kontenery języka

| Usługa |  Kontener | Opis | Dostępność |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** [(obraz](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Ładuje wytrenowany lub opublikowany model Language Understanding, znany również jako aplikacja usługi LUIS, do kontenera platformy Docker i zapewnia dostęp do przewidywań zapytań z punktów końcowych interfejsu API kontenera. Możesz zbierać dzienniki zapytań z kontenera i przekazywać je z powrotem do portalu [usługi LUIS,](https://www.luis.ai) aby zwiększyć dokładność przewidywania aplikacji. | Ogólnie dostępne |
| [Analiza tekstu][ta-containers-keyphrase] | **wyodrębnianie kluczowych fraz** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Wyodrębnia kluczowe frazy w celu zidentyfikowania głównych punktów. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. | Wersja zapoznawcza |
| [Analiza tekstu][ta-containers-language] |  **Tekst wykrywanie języka** [(obraz](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | W przypadku maksymalnie 120 języków program wykrywa język, w którym jest napisany tekst wejściowy, i zgłasza pojedynczy kod języka dla każdego dokumentu przesłanego w żądaniu. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. | Ogólnie dostępne |
| [Analiza tekstu][ta-containers-sentiment] | **analiza tonacji v3** [(obraz)](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409) | Analizuje nieprzetworzone tekst pod poszukiwaniu wskazówek dotyczących pozytywnej lub negatywnej tonacji. Ta wersja analizy tonacji zwraca etykiety tonacji (na *przykład* pozytywne lub negatywne *)* dla każdego dokumentu i zdania w nim. |  Ogólnie dostępne |
| [Analiza tekstu][ta-containers-health] |  **Analiza tekstu dla opieki zdrowotnej** | Wyodrębnianie i etykietowanie informacji medycznych z nieustrukturyzowanego tekstu medycznego. | Bramowa wersja zapoznawcza. [Zażądaj dostępu.][request-access] |

### <a name="speech-containers"></a>Kontenery usługi Mowa

> [!NOTE]
> Aby korzystać z kontenerów usługi Mowa, należy wypełnić [formularz żądania online](https://aka.ms/csgate).

| Usługa |  Kontener | Opis | Dostępność |
|--|--|--|
| [Interfejs API usługi rozpoznawania mowy][sp-containers-stt] |  **Speech-to-text** [(obraz)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text) | Przekształca w czasie rzeczywistym ciągłą mowę na tekst. | Ogólnie dostępne |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-cstt] | **Niestandardowa mowa na tekst** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transkrybuje ciągłą mowę w czasie rzeczywistym na tekst przy użyciu modelu niestandardowego. | Ogólnie dostępne |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-tts] | **Tekst na mowę** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Konwertuje tekst na naturalnie brzmiącą mowę. | Ogólnie dostępne |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-ctts] | **Niestandardowy tekst na mowę** ([obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Konwertuje tekst na naturalnie brzmiącą mowę przy użyciu modelu niestandardowego. | Podgląd z bramą |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-ntts] | **Neuronowy tekst na mowę** [(obraz)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech) | Konwertuje tekst na naturalnie brzmiącą mowę przy użyciu technologii głębokiej sieci neuronowej, co pozwala na bardziej naturalne syntetyzowanie mowy. | Ogólnie dostępne |
| [Interfejs API usługi rozpoznawania mowy][sp-containers-lid] | **Wykrywanie języka mowy** [(obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Określa język mówionego dźwięku. | Podgląd z bramą |

### <a name="vision-containers"></a>Kontenery przetwarzania obrazów

> [!WARNING]
> 11 czerwca 2020 roku firma Microsoft ogłosiła, że nie będzie sprzedawać technologii rozpoznawania twarzy departamentom policji w Stanach Zjednoczonych, dopóki nie zostaną wdrożone ścisłe przepisy oparte na prawach człowieka. W związku z tym klienci nie mogą korzystać z funkcji ani funkcji rozpoznawania twarzy zawartych w usługach platformy Azure, takich jak twarz lub Video Indexer, jeśli klient jest lub zezwala na korzystanie z takich usług przez lub dla działu Stany Zjednoczone.

| Usługa |  Kontener | Opis | Dostępność |
|--|--|--|--|
| [Przetwarzanie obrazów][cv-containers] | **Odczyt OCR** [(obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Kontener odczytu OCR umożliwia wyodrębnianie tekstu drukowanego i odręcznego z obrazów i dokumentów z obsługą formatów plików JPEG, PNG, BMP, PDF i TIFF. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API read](./computer-vision/overview-ocr.md). | Bramowa wersja zapoznawcza. [Zażądaj dostępu.][request-access] |
| [Analiza przestrzenna][spa-containers] | **Analiza przestrzenna** [(obraz](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analizuje wideo przesyłane strumieniowo w czasie rzeczywistym, aby zrozumieć relacje przestrzenne między osobami, ich ruch i interakcje z obiektami w środowiskach fizycznych. | Bramowa wersja zapoznawcza. [Zażądaj dostępu.][request-access] |
| [Rozpoznawanie twarzy][fa-containers] | **Rozpoznawanie twarzy** | Wykrywa ludzkie twarze na obrazach i identyfikuje atrybuty, w tym punkty charakterystyczne twarzy (takie jak nosy i oczy), płeć, wiek i inne cechy twarzy przewidywane maszynowo. Oprócz wykrywania, twarz może sprawdzić, czy dwie twarze na tym samym obrazie lub na różnych obrazach są takie same, używając oceny ufności, lub porównać twarze z bazą danych, aby sprawdzić, czy podobna lub identyczna twarz już istnieje. Może również organizować podobne twarze w grupy przy użyciu wspólnych cech wizualnych. | Niedostępny |
| [Rozpoznawanie formularzy][fr-containers] | **Rozpoznawanie formularzy** | Omówienie formularzy stosuje technologię uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość i tabel z formularzy. | Niedostępny | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Ponadto niektóre kontenery są obsługiwane w ofercie Cognitive Services [wielu usług.](cognitive-services-apis-create-account.md) Możesz utworzyć jeden pojedynczy zasób Cognitive Services typu "wszystko w jednym" i użyć tego samego klucza rozliczeniowego dla obsługiwanych usług dla następujących usług:

* Przetwarzanie obrazów
* Rozpoznawanie twarzy
* LUIS
* Analiza tekstu

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów kontenerów należy spełnić Azure Cognitive Services wymagania wstępne:

**Aparat platformy Docker:** aparat platformy Docker musi być zainstalowany lokalnie. Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w [systemach macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)i [Windows.](https://docs.docker.com/docker-for-windows/) W systemie Windows należy skonfigurować usługę Docker do obsługi kontenerów systemu Linux. Kontenery platformy Docker można również wdrażać bezpośrednio w [Azure Kubernetes Service](../aks/index.yml) lub [Azure Container Instances](../container-instances/index.yml).

Platformę Docker należy skonfigurować tak, aby umożliwić kontenerom nawiązywanie połączeń z platformą Azure i wysyłanie danych rozliczeniowych na platformę Azure.

Znajomość platform Microsoft Container Registry i **Docker:** musisz mieć podstawową wiedzę na temat pojęć związanych z platformami Microsoft Container Registry i Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także wiedzę na temat podstawowych `docker` poleceń.

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).

Poszczególne kontenery mogą mieć również własne wymagania, w tym wymagania dotyczące przydzielania pamięci i serwera.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Przykłady dla deweloperów

Przykłady dla deweloperów są dostępne w naszym [repozytorium GitHub.](https://github.com/Azure-Samples/cognitive-services-containers-samples)

## <a name="next-steps"></a>Następne kroki

Dowiedz się [więcej na temat przepisów na kontener,](containers/container-reuse-recipe.md) których można używać z Cognitive Services.

Instalowanie i eksplorowanie funkcji dostarczanych przez kontenery w Azure Cognitive Services:

* [Narzędzie do wykrywania anomalii kontenerów][ad-containers]
* [przetwarzanie obrazów kontenerów][cv-containers]
* [Kontenery rozpoznawania twarzy][fa-containers]
* [Rozpoznawanie formularzy kontenerów][fr-containers]
* [Language Understanding (LUIS)][lu-containers]
* [Kontenery interfejsu API usługi Mowa][sp-containers]
* [analiza tekstu kontenerów][ta-containers]

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
[request-access]: https://aka.ms/csgate
