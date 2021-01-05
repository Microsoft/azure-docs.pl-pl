---
title: Kontenery Cognitive Services często zadawane pytania
titleSuffix: Azure Cognitive Services
description: Często zadawane pytania i odpowiedzi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 71b57eae1a66e6966f61123e638c4790410ef445
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862525"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Kontenery Cognitive Services platformy Azure — często zadawane pytania

## <a name="general-questions"></a>Pytania ogólne

**P: co jest dostępne?**

Odp **.:** Kontenery usługi Azure Cognitive Services umożliwiają deweloperom korzystanie z tych samych inteligentnych interfejsów API, które są dostępne na platformie Azure, ale z [korzyściami](../cognitive-services-container-support.md#features-and-benefits) z kontenerach. Niektóre kontenery są dostępne jako wersja zapoznawcza, co może wymagać, aby aplikacja mogła uzyskać dostęp. Inne kontenery są publicznie dostępne jako wersja zapoznawcza lub są ogólnie dostępne. Pełną listę kontenerów i ich dostępność można znaleźć w artykule [Obsługa kontenerów w usłudze Azure Cognitive Services](../cognitive-services-container-support.md) . Możesz również wyświetlić kontenery w usłudze [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**P: czy istnieje różnica między chmurą Cognitive Services i kontenerami?**

Odp **.:** Kontenery Cognitive Services są alternatywą dla chmury Cognitive Services. Kontenery oferują te same możliwości co odpowiednie usługi w chmurze. Klienci mogą wdrażać kontenery lokalnie lub na platformie Azure. Podstawowa technologia AI, warstwy cenowe, klucze interfejsu API i podpis interfejsu API są takie same, jak w przypadku kontenera i odpowiednich usług w chmurze. Poniżej przedstawiono [funkcje i korzyści wynikające](../cognitive-services-container-support.md#features-and-benefits) z wyboru kontenerów w ramach usługi w chmurze.

**P: Jak mogę dostępu i używania kontenera z bramą w wersji zapoznawczej?**

Odp **.:** Wcześniej w repozytorium były hostowane kontenery w wersji zapoznawczej `containerpreview.azurecr.io` . Od 22 września 2020 te kontenery są hostowane w Container Registry firmy Microsoft, a ich pobieranie nie wymaga użycia polecenia Docker login. Jeśli zasób platformy Azure został utworzony przy użyciu zatwierdzonego identyfikatora subskrypcji platformy Azure, będzie można uruchomić bramę z zapoznawczą. Nie będzie można uruchomić kontenera, jeśli subskrypcja platformy Azure nie została zatwierdzona po zakończeniu [formularza żądania](https://aka.ms/csgate).


**P: czy kontenery będą dostępne dla wszystkich Cognitive Services i jakie są następne zestawy kontenerów, których oczekujemy?**

Odp **.:** Chcemy, aby więcej Cognitive Services dostępnych jako oferty kontenerów. Skontaktuj się z lokalnym menedżerem konto Microsoft, aby uzyskać aktualizacje dotyczące nowych wydań kontenerów i innych anonsów Cognitive Services.

**P: co Service-Level umowa SLA dla Cognitive Services kontenerów?**

Odp **.:** Kontenery Cognitive Services nie mają umowy SLA.

Cognitive Services konfiguracjami kontenerów zasobów są kontrolowane przez klientów, dlatego firma Microsoft nie oferuje umowy SLA na potrzeby ogólnej dostępności. Klienci mogą bezpłatnie wdrażać kontenery w środowisku lokalnym, a tym samym definiować środowiska hosta.

> [!IMPORTANT]
> Aby dowiedzieć się więcej na temat umów Service-Level Cognitive Services, [odwiedź naszą stronę umów SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**P: czy te kontenery są dostępne w ramach suwerennych chmur?**

Odp **.:** Nie wszyscy znają termin "chmura suwerenna", więc zacznijmy od definicji:

> "Suwerenna chmura" składa się z [Azure Government](../../azure-government/documentation-government-welcome.md), [platformy Azure (Niemcy](../../germany/germany-welcome.md)) i [platformy Azure z Chin](/azure/china/overview-operations) .

Niestety kontenery Cognitive Services *nie* są natywnie obsługiwane w przypadku suwerennych chmur. Kontenery można uruchamiać w tych chmurach, ale zostaną one pobrane z chmury publicznej i muszą wysyłać dane użycia do publicznego punktu końcowego.

### <a name="versioning"></a>Przechowywanie wersji

**P: w jaki sposób kontenery są aktualizowane do najnowszej wersji?**

Odp **.:** Klienci mogą określić, kiedy mają być aktualizowane wdrożone kontenery. Kontenery zostaną oznaczone standardowymi [tagami platformy Docker](https://docs.docker.com/engine/reference/commandline/tag/) , `latest` na przykład w celu wskazania najnowszej wersji. Zachęcamy klientów do ściągania najnowszej wersji kontenerów po ich wydaniu, wyewidencjonowywania [Azure Container Registry elementów webhook](../../container-registry/container-registry-webhook.md) w celu uzyskania szczegółowych informacji o tym, jak uzyskać powiadamianie o zaktualizowaniu obrazu.
 
**P: Jakie wersje będą obsługiwane?**

Odp **.:** Bieżąca i Ostatnia wersja główna kontenera będą obsługiwane. Zachęcamy jednak, aby klienci mogli zachować aktualność w celu uzyskania najnowszej technologii.
 
**P: w jaki sposób są aktualizowane aktualizacje?**

Odp **.:** Główne zmiany wersji wskazują na nieprzerwaną zmianę sygnatury interfejsu API. Przewidujemy, że zwykle będzie to zbieżne ze zmianami wersji głównych do odpowiedniej oferty w chmurze usługi poznawczej. Zmiany wersji pomocniczej wskazują poprawki błędów, aktualizacje modelu lub nowe funkcje, które nie wprowadzają istotnej zmiany w sygnaturze interfejsu API.

## <a name="technical-questions"></a>Pytania techniczne

**P: jak uruchamiać kontenery Cognitive Services na urządzeniach IoT?**

Odp **.:** Bez względu na to, czy nie masz niezawodnego połączenia z Internetem, czy chcesz zaoszczędzić koszt przepustowości. Lub jeśli mają wymagania dotyczące małych opóźnień lub zawierają dane poufne, które należy analizować w witrynie, [Azure IoT Edge z kontenerami Cognitive Services](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) zapewnia spójność z chmurą.

**P: czy te kontenery są zgodne z OpenShift?** 

Kontenery nie są testowane z OpenShift, ale ogólnie kontenery Cognitive Services powinny działać na dowolnej platformie, która obsługuje obrazy platformy Docker. Jeśli używasz OpenShift, zalecamy uruchomienie kontenerów jako `root-user` .

**P: Jak mogę zapewniać opinie o produkcie i zalecenia dotyczące funkcji?**

Odp **.:** Zachęcamy klientów do [zagłosowania](https://cognitive.uservoice.com/) publicznie i popełnienia innych osób, które zrobiły to samo, gdzie potencjalne problemy nakładają się na siebie. Narzędzia głosowego użytkownika można używać zarówno w celu uzyskania opinii o produkcie, jak i zaleceń dotyczących funkcji.

**P: jakie komunikaty o stanie i błędy są zwracane przez Cognitive Services kontenery?**

Odp **.:** Listę komunikatów o stanie i błędów można znaleźć w poniższej tabeli.

|Stan  | Opis  |
|---------|---------|
| `Valid` | Klucz interfejsu API jest prawidłowy, nie jest wymagana żadna akcja. |
| `Invalid` |   Klucz interfejsu API jest nieprawidłowy. Musisz podać prawidłowy klucz interfejsu API, aby uruchomić kontener. Znajdź klucz interfejsu API i region usługi w sekcji **klucze i punkt końcowy** dla zasobu usługi Azure Cognitive Services w Azure Portal. |
| `Mismatch` | Podano klucz interfejsu API lub punkt końcowy dla innego rodzaju zasobu usług poznawczej. Znajdź klucz interfejsu API i region usługi w sekcji **klucze i punkt końcowy** dla zasobu Cognitive Services platformy Azure. |
| `CouldNotConnect` | Kontener nie może połączyć się z punktem końcowym rozliczeń. Sprawdź `Retry-After` wartość i poczekaj na zakończenie tego okresu przed wprowadzeniem dodatkowych żądań. |
| `OutOfQuota` | Brak limitu przydziału klucza interfejsu API. Możesz uaktualnić warstwę cenową lub poczekać na udostępnienie dodatkowego przydziału. Znajdź warstwę w sekcji **warstwa cenowa** zasobu usługi poznawczej platformy Azure, w Azure Portal. |
| `BillingEndpointBusy` | Punkt końcowy rozliczeniowy jest obecnie zajęty. Sprawdź `Retry-After` wartość i poczekaj na zakończenie tego okresu przed wprowadzeniem dodatkowych żądań. |
| `ContainerUseUnauthorized` | Podany klucz interfejsu API nie jest autoryzowany do użycia z tym kontenerem. Prawdopodobnie używasz kontenera warunkowego, więc upewnij się, że identyfikator subskrypcji platformy Azure został zatwierdzony przez przesłanie [żądania online](https://aka.ms/csgate). |
| `Unknown` | Serwer nie może obecnie przetwarzać żądań rozliczeń. |


**P: kogo mogę się skontaktować w celu uzyskania pomocy technicznej?**

Odp **.:** Kanały obsługi klienta są takie same, jak oferta Cognitive Services w chmurze. Wszystkie kontenery Cognitive Services obejmują funkcje rejestrowania, które ułatwią nam i klientom pomoc techniczną. Aby uzyskać dodatkową pomoc techniczną, zobacz poniższe opcje.

### <a name="customer-support-plan"></a>Plan pomocy technicznej klienta

Klienci powinni zapoznać się z [planem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/) , aby dowiedzieć się, komu skontaktować się z pomocą techniczną.

### <a name="azure-knowledge-center"></a>Centrum wiedzy Azure

Klient jest bezpłatny, aby poznać [usługę Azure Knowledge Center](https://azure.microsoft.com/resources/knowledge-center/) w celu uzyskania odpowiedzi na pytania i pomoc techniczną.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) jest witryną pytań i odpowiedzi dla programistów profesjonalnych i entuzjastą.

Zapoznaj się z następującymi tagami, aby poznać potencjalne pytania i odpowiedzi, które są dostosowane do Twoich potrzeb.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Poznawcze firmy Microsoft](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**P: jak działa rozliczenia?**

Odp **.:** Klienci są obciążani opłatami na podstawie zużycia, podobnie jak w przypadku chmury Cognitive Services. Kontenery muszą być skonfigurowane do wysyłania danych zliczania do platformy Azure, a transakcje będą rozliczane odpowiednio. Zasoby używane w ramach usług hostowanych i lokalnych zostaną dodane do pojedynczego przydziału przy użyciu cen warstwowych, licząc względem obu użycia. Aby uzyskać więcej informacji, zobacz stronę cennika odpowiedniej oferty.

* [Narzędzie do wykrywania anomalii][ad-containers-billing]
* [Przetwarzanie obrazów][cv-containers-billing]
* [Rozpoznawanie twarzy][fa-containers-billing]
* [Rozpoznawanie formularzy][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Interfejs API usługi rozpoznawania mowy][sp-containers-billing]
* [Analiza tekstu][ta-containers-billing]

> [!IMPORTANT]
> Kontenery Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą usługi pomiarowej przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta do firmy Microsoft.
 
**P: Jaka jest bieżąca gwarancja pomocy technicznej dla kontenerów?**

Odp **.:** Nie ma gwarancji dla wersji zapoznawczych. Standardowa gwarancja firmy Microsoft dla oprogramowania dla przedsiębiorstw zostanie zastosowana, gdy kontenery są ogłoszone jako ogólnie dostępne.
 
**P: co się dzieje z kontenerami Cognitive Services po utracie połączenia z Internetem?**

Odp **.:** Kontenery Cognitive Services *nie są licencjonowane* do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do komunikowania się z usługą pomiaru przez cały czas.

**P: jak długo może działać kontener bez połączenia z platformą Azure?**

Odp **.:** Kontenery Cognitive Services *nie są licencjonowane* do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do komunikowania się z usługą pomiaru przez cały czas.
 
**P: co to jest bieżący sprzęt wymagany do uruchomienia tych kontenerów?**

Odp **.:** Kontenery Cognitive Services są kontenerami opartymi na architekturze x64, które mogą uruchamiać dowolne zgodne maszyny wirtualne z systemem Linux, maszynę wirtualną i Urządzenie brzegowe obsługujące kontenery platformy Docker x64 Wszystkie wymagają procesorów CPU. Minimalne i zalecane konfiguracje dla każdej oferty kontenera są dostępne poniżej:

* [Narzędzie do wykrywania anomalii][ad-containers-recommendations]
* [Przetwarzanie obrazów][cv-containers-recommendations]
* [Rozpoznawanie twarzy][fa-containers-recommendations]
* [Rozpoznawanie formularzy][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Interfejs API usługi rozpoznawania mowy][sp-containers-recommendations]
* [Analiza tekstu][ta-containers-recommendations]
 
**P: czy te kontenery są obecnie obsługiwane w systemie Windows?**

Odp **.:** Kontenery Cognitive Services to kontenery systemu Linux, ale w systemie Windows istnieje pewna obsługa kontenerów systemu Linux. Aby uzyskać więcej informacji na temat kontenerów systemu Linux w systemie Windows, zobacz [dokumentację platformy Docker](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**P: Jak mogę odnajdywania kontenerów?**

Odp **.:** Kontenery Cognitive Services są dostępne w różnych lokalizacjach, takich jak Azure Portal, Hub Docker i rejestry kontenerów platformy Azure. W przypadku najnowszych lokalizacji kontenera zapoznaj się z tematem [obrazy kontenerów](container-image-tags.md).

**P: jak Cognitive Services kontenery są porównywane z ofertami usługi AWS i Google?**

Odp **.:** Firma Microsoft jest pierwszym dostawcą usług w chmurze, który umożliwia przenoszenie wstępnie przeszkolonych modeli AI do kontenerów z prostymi rozliczeniami na transakcję, tak jakby klienci korzystali z usługi w chmurze. Firma Microsoft uważa, że chmura hybrydowa zapewnia klientom większy wybór.

**P: jakie certyfikaty zgodności są kontenerami?**

Odp **.:** Kontenery usług poznawczych nie mają żadnych certyfikatów zgodności

**P: jakie regiony są dostępne Cognitive Services kontenerów?**

Odp **.:** Kontenery mogą być uruchamiane w dowolnym miejscu w dowolnym regionie, jednak potrzebują klucza i wywołania zwrotnego do platformy Azure w celu pomiaru. Wszystkie obsługiwane regiony dla usługi w chmurze są obsługiwane dla wywołania pomiaru kontenerów.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations