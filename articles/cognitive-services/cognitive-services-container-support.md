---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak kontenery platformy Docker mogą uzyskać Cognitive Services bliżej danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7a38ec47d416027e8ea3fa772ae01e4f6264197a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876837"
---
# <a name="container-support-in-azure-cognitive-services"></a>Obsługa kontenerów w usłudze Azure Cognitive Services

Obsługa kontenerów w usłudze Azure Cognitive Services pozwala deweloperom korzystać z tych samych bogatych interfejsów API, które są dostępne na platformie Azure, i umożliwia elastyczność w przypadku wdrażania i hostowania usług, które są dostarczane z [kontenerami platformy Docker](https://www.docker.com/what-container). Obsługa kontenerów jest obecnie dostępna dla podzestawu Cognitive Services platformy Azure, w tym części:

> [!div class="checklist"]
> * [Narzędzie do wykrywania anomalii][ad-containers]
> * [Przetwarzanie obrazów][cv-containers]
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
- **Kontrola nad danymi**: Zezwól klientom na wybór, gdzie te Cognitive Services przetwarzać dane. Jest to istotne w przypadku klientów, którzy nie mogą wysyłać danych do chmury, ale muszą mieć dostęp do technologii Cognitive Services. Obsługa spójności w środowiskach hybrydowych — między danymi, zarządzaniem, tożsamością i zabezpieczeniami.
- **Kontrolę nad aktualizacjami modelu**: zapewniają klientom elastyczność w zakresie przechowywania wersji i aktualizowania modeli wdrożonych w ich rozwiązaniach.
- **Architektura przenośna**: umożliwia tworzenie architektury aplikacji przenośnej, którą można wdrożyć na platformie Azure, lokalnie i na krawędzi. Kontenery można wdrażać bezpośrednio w [usłudze Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)lub do klastra [Kubernetes](https://kubernetes.io/) wdrożonego w [Azure Stack](/azure-stack/operator). Aby uzyskać więcej informacji, zobacz [wdrażanie Kubernetes do Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Wysoka przepływność/małe opóźnienia**: umożliwiają klientom skalowanie w celu uzyskania wysokiej przepływności i małych opóźnień przez umożliwienie Cognitive Services uruchamiania w pobliżu ich logiki i danych aplikacji. Kontenery nie przekraczają transakcji na sekundę (TPS) i można je skalować w górę i w dół, aby obsługiwać zapotrzebowanie w przypadku podania niezbędnych zasobów sprzętowych.
- **Skalowalność**: dzięki coraz większej popularności oprogramowania kontenerach i aranżacji kontenerów, takich jak Kubernetes; skalowalność jest w programie Forefront z góry postępów technologicznych. Tworzenie aplikacji na skalowalnym serwerze klastra zapewnia wysoką dostępność.

## <a name="containers-in-azure-cognitive-services"></a>Kontenery w usłudze Azure Cognitive Services

Kontenery usługi Azure Cognitive Services oferują następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzestaw funkcji usług na platformie Azure Cognitive Services:

| Usługa | Obsługiwana warstwa cenowa | Kontener | Opis |
|---------|----------|----------|-------------|
|[Wykrywacz anomalii][ad-containers] |F0, S0|**Anomalia — detektor** |Interfejs API wykrywania anomalii umożliwia monitorowanie i wykrywanie anomalii w danych szeregów czasowych przy użyciu uczenia maszynowego.<br>[Żądanie dostępu](https://aka.ms/adcontainer)|
|[Przetwarzanie obrazów][cv-containers] |F0, S1|**Odczyt** |Wyodrębnia drukowany tekst z obrazów różnych obiektów z różnymi powierzchniami i tłem, takimi jak paragony, plakaty i wizytówki. Kontener odczytu wykrywa także *Tekst odręczny* w obrazach i zapewnia obsługę formatu PDF/TIFF/wielostronicowej.<br/><br/>**Ważne:** Kontener odczytu jest obecnie stosowany tylko w języku angielskim.|
|[Rozpoznawanie twarzy][fa-containers] |F0, S0|**Rozpoznawanie twarzy** |Wykrywa ludzkie twarze w obrazach i identyfikuje atrybuty, w tym dzielnice twarzy (takie jak nos i oczy), płeć, wiek i inne funkcje twarzy przewidziane dla maszyn. Oprócz wykrywania, twarz może sprawdzić, czy dwie twarze w tym samym obrazie lub różnych obrazach są takie same, przy użyciu oceny pewności, czy też można porównać twarze z bazą danych, aby zobaczyć, czy podobna lub identyczna twarz już istnieje. Może również organizować podobne twarze w grupy przy użyciu współużytkowanych cech wizualnych.<br>[Żądanie dostępu](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Aparat rozpoznawania formularzy][fr-containers] |F0, S0|**Rozpoznawanie formularzy** |Zrozumienie formularza stosuje technologię uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość oraz tabel z formularzy.<br>[Żądanie dostępu](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**Luis** ([obraz](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Ładuje przeszkolony lub opublikowany model Language Understanding, znany również jako aplikacja LUIS, do kontenera Docker i zapewnia dostęp do prognoz zapytania z punktów końcowych interfejsu API kontenera. Można zebrać dzienniki zapytań z kontenera i przekazać je z powrotem do [portalu Luis](https://www.luis.ai) , aby zwiększyć dokładność przewidywania aplikacji.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-stt] |F0, S0|**Zamiana mowy na tekst** |Przekształca w czasie rzeczywistym ciągłą mowę na tekst.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-cstt] |F0, S0|**Custom Speech do tekstu** |Przekształca ciągłe używanie mowy w czasie rzeczywistym do tekstu przy użyciu modelu niestandardowego.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-tts] |F0, S0|**Zamiana tekstu na mowę** |Konwertuje tekst na naturalnie brzmiącą mowę.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-ctts] |F0, S0|**Niestandardowa Zamiana tekstu na mowę** |Konwertuje tekst na mowę dźwięku naturalnego przy użyciu modelu niestandardowego.|
|[Analiza tekstu][ta-containers-keyphrase] |F0, S|**Wyodrębnianie kluczowych fraz** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Wyodrębnia kluczowe frazy do identyfikowania głównych punktów. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. |
|[Analiza tekstu][ta-containers-language]|F0, S|**Wykrywanie języka** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |W przypadku do 120 języków program wykrywa język, w którym wprowadzany jest tekst wejściowy, i raportuje jeden kod języka dla każdego dokumentu przesłanego w żądaniu. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. |
|[Analiza tekstu][ta-containers-sentiment]|F0, S|**Analiza tonacji** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analizuje nieprzetworzony tekst, aby uzyskać wskazówki dotyczące tonacji pozytywnej lub ujemnej. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację. Modele analiz są wstępnie nauczone przy użyciu obszernej treści tekstu i technologii języka naturalnego od firmy Microsoft. W przypadku [wybranych języków](./text-analytics/language-support.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. |

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

> [!IMPORTANT]
> Obecnie należy zakończyć proces tworzenia konta, aby uzyskać dostęp do następujących kontenerów, w których wypełniasz i przesłać kwestionariusz z pytaniami dotyczącymi użytkownika, firmy i przypadku użycia, dla którego chcesz zaimplementować kontenery. Po uzyskaniu dostępu i uzyskaniu poświadczeń można ściągnąć obrazy kontenerów z prywatnego rejestru kontenerów hostowanych przez Azure Container Registry.
> * [Wykrywacz anomalii](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Rozpoznawanie twarzy](Face/face-how-to-install-containers.md)
> * [Rozpoznawanie formularzy](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Odczyt](computer-vision/computer-vision-how-to-install-containers.md)
> * [Zamiana mowy na tekst i zamiany tekstu na mowę](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów Cognitive Services platformy Azure należy spełnić następujące wymagania wstępne:

**Aparat platformy Docker**: aparat platformy Docker musi być zainstalowany lokalnie. Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)i [Windows](https://docs.docker.com/docker-for-windows/). W systemie Windows program Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy Docker można także wdrażać bezpośrednio w [usłudze Azure Kubernetes Service](../aks/index.yml) lub [Azure Container Instances](../container-instances/index.yml).

Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure.

**Znajomość programu microsoft Container Registry i platformy Docker**: należy dysponować podstawową wiedzą na temat koncepcji firmy Microsoft Container Registry i platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość `docker` podstawowych poleceń.

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
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment