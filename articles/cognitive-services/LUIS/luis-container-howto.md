---
title: Instalowanie i uruchamianie kontenerów platformy Docker dla usługi LUIS
titleSuffix: Azure Cognitive Services
description: Użyj kontenera LUIS, aby załadować aplikację przeszkolony lub opublikowaną i uzyskać dostęp do jej prognoz w środowisku lokalnym.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: lokalna, Docker, kontener
ms.openlocfilehash: e157e976186f03aa984877435c42b996ce476740
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040196"
---
# <a name="install-and-run-docker-containers-for-luis"></a>Instalowanie i uruchamianie kontenerów platformy Docker dla usługi LUIS

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Kontenery umożliwiają korzystanie z LUIS w Twoim środowisku. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobrać, zainstalować i uruchomić kontener LUIS.

Kontener Language Understanding (LUIS) ładuje swój przeszkolony lub opublikowany Language Understanding model. Jako [aplikacja Luis](https://www.luis.ai), kontener platformy Docker zapewnia dostęp do prognoz zapytania z punktów końcowych interfejsu API kontenera. Można zebrać dzienniki zapytań z kontenera i przekazać je z powrotem do aplikacji Language Understanding, aby zwiększyć dokładność przewidywania aplikacji.

Poniższy film wideo demonstruje użycie tego kontenera.

[![Demonstracja kontenera dla Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić kontener LUIS, należy zwrócić uwagę na następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure. <br><br> **W systemie Windows** program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.|
|Zasób platformy Azure `Cognitive Services` i plik Luis [spakowanej aplikacji](luis-how-to-start-new-app.md) |Aby można było używać kontenera, musisz mieć:<br><br>* Zasób platformy Azure _Cognitive Services_ i skojarzony klucz rozliczeniowy identyfikator URI punktu końcowego rozliczenia. Obie wartości są dostępne na stronach przeglądów i kluczy dla zasobu i są wymagane do uruchomienia kontenera. <br>* Przeszkolone lub opublikowana aplikacja spakowana jako zainstalowano dane wejściowe do kontenera ze skojarzonym IDENTYFIKATORem aplikacji. Spakowany plik można pobrać z portalu LUIS lub interfejsów API tworzenia. Jeśli otrzymujesz spakowaną aplikację LUIS z [interfejsów API tworzenia](#authoring-apis-for-package-file), będziesz również potrzebować _klucza tworzenia_.<br><br>Te wymagania są używane do przekazywania argumentów wiersza polecenia do następujących zmiennych:<br><br>**{AUTHORING_KEY}**: ten klucz służy do uzyskiwania spakowanej aplikacji z usługi Luis w chmurze i przekazywania dzienników zapytań z powrotem do chmury. Format to `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .<br><br>**{App_id}**: ten identyfikator jest używany do wybierania aplikacji. Format to `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .<br><br>**{API_KEY}**: ten klucz jest używany do uruchamiania kontenera. Klucz punktu końcowego można znaleźć w dwóch miejscach. Pierwszy to Azure Portal na liście kluczy zasobu _Cognitive Services_ . Klucz punktu końcowego jest również dostępny w portalu LUIS na stronie ustawień klucze i punkt końcowy. Nie używaj klucza początkowego.<br><br>**{ENDPOINT_URI}**: punkt końcowy określony na stronie Przegląd.<br><br>[Klucz tworzenia i klucz punktu końcowego](luis-limits.md#key-limits) mają różne cele. Nie należy ich używać zamiennie. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Tworzenie interfejsów API dla pliku pakietu

Tworzenie interfejsów API dla spakowanych aplikacji:

* [Interfejs API opublikowanego pakietu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Interfejs API pakietów bez publikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

W poniższej tabeli wymieniono minimalne i zalecane wartości dla hosta kontenerów. Wymagania mogą ulec zmianie w zależności od ilości ruchu sieciowego.

|Kontener| Minimum | Zalecane | TPS<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
|LUIS|1 rdzeń, 2 GB pamięci|1 rdzeń, 4 GB pamięci|20, 40|

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.
* TPS — liczba transakcji na sekundę

Rdzeń i pamięć odpowiadają `--cpus` `--memory` ustawieniom i, które są używane jako część `docker run` polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera z `mcr.microsoft.com/azure-cognitive-services/language/luis` repozytorium:

```
docker pull mcr.microsoft.com/azure-cognitive-services/language/luis:latest
```

Pełny opis dostępnych tagów, takich jak `latest` użyty w poprzednim poleceniu, znajduje się w temacie [Luis](https://go.microsoft.com/fwlink/?linkid=2043204) on the Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

![Proces używania kontenera Language Understanding (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Pakiet eksportu](#export-packaged-app-from-luis) dla kontenera z Luis Portal lub interfejsów API Luis.
1. Przenieś plik pakietu do wymaganego katalogu **wejściowego** na [komputerze-hoście](#the-host-computer). Nie należy zmieniać nazwy, zmieniać, zastępować ani dekompresowanie pliku pakietu LUIS.
1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami _instalacji_ i rozliczeń. Więcej [przykładów](luis-container-configuration.md#example-docker-run-commands) `docker run` polecenia jest dostępnych.
1. [Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera](#query-the-containers-prediction-endpoint).
1. Po zakończeniu pracy z kontenerem [zaimportuj dzienniki punktów końcowych](#import-the-endpoint-logs-for-active-learning) z instalacji wyjściowej w portalu Luis i [Zatrzymaj](#stop-the-container) kontener.
1. Aby ulepszyć aplikację, użyj [aktywnej uczenia](luis-how-to-review-endpoint-utterances.md) w portalu Luis na stronie **Przegląd punktu końcowego wyrażenia długości** .

Nie można zmienić aplikacji działającej w kontenerze. Aby można było zmienić aplikację w kontenerze, należy zmienić aplikację w usłudze LUIS przy użyciu portalu [Luis](https://www.luis.ai) lub użyć [interfejsów API tworzenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)Luis. Następnie Wyszkol i/lub Opublikuj, a następnie Pobierz nowy pakiet i ponownie uruchom kontener.

Nie można wyeksportować aplikacji LUIS wewnątrz kontenera z powrotem do usługi LUIS. Można przekazać tylko dzienniki zapytań.

## <a name="export-packaged-app-from-luis"></a>Eksportuj spakowaną aplikację z LUIS

Kontener LUIS wymaga przeszkolonej lub opublikowanej aplikacji LUIS do odpowiedzi na zapytania predykcyjne wyrażenia długości użytkownika. Aby uzyskać aplikację LUIS, użyj interfejsu API pakietu przeszkolonego lub opublikowanego.

Domyślną lokalizacją jest `input` podkatalog w odniesieniu do miejsca, w którym jest uruchamiane `docker run` polecenie.

Umieść plik pakietu w katalogu i odwołuje się do tego katalogu jako instalacji wejściowej podczas uruchamiania kontenera Docker.

### <a name="package-types"></a>Typy pakietów

Wejściowy katalog instalacji może jednocześnie zawierać modele **produkcyjne**, **przejściowe** i w **wersji** aplikacji. Wszystkie pakiety są zainstalowane.

|Typ pakietu|Interfejs API punktu końcowego zapytania|Dostępność zapytania|Format nazwy pliku pakietu|
|--|--|--|--|
|Systemową|POBIERZ, OPUBLIKUJ|Tylko kontener|`{APP_ID}_v{APP_VERSION}.gz`|
|Przygotowanie|POBIERZ, OPUBLIKUJ|Platforma Azure i kontener|`{APP_ID}_STAGING.gz`|
|Produkcja|POBIERZ, OPUBLIKUJ|Platforma Azure i kontener|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Nie zmieniaj nazw, nie zmieniaj, zastępuj ani nie Kompresuj plików pakietu LUIS.

### <a name="packaging-prerequisites"></a>Wymagania wstępne dotyczące pakowania

Przed spakowaniem aplikacji LUIS należy wykonać następujące czynności:

|Wymagania dotyczące pakietów|Szczegóły|
|--|--|
|Wystąpienie zasobów usługi Azure _Cognitive Services_|Obsługiwane regiony obejmują<br><br>Zachodnie stany USA ( `westus` )<br>Europa Zachodnia ( `westeurope` )<br>Australia Wschodnia ( `australiaeast` )|
|Przeszkolone lub opublikowana aplikacja LUIS|Bez [nieobsługiwanych zależności][unsupported-dependencies]. |
|Dostęp do systemu plików [komputera hosta](#the-host-computer) |Komputer hosta musi zezwalać na [instalację wejściową](luis-container-configuration.md#mount-settings).|

### <a name="export-app-package-from-luis-portal"></a>Eksportuj pakiet aplikacji z portalu LUIS

[Portal](https://www.luis.ai) Luis umożliwia eksportowanie pakietu przeszkolonej lub opublikowanej aplikacji.

### <a name="export-published-apps-package-from-luis-portal"></a>Eksportuj pakiet opublikowanej aplikacji z portalu LUIS

Pakiet opublikowanej aplikacji jest dostępny na stronie listy **Moje aplikacje** .

1. Zaloguj się do [portalu](https://www.luis.ai)Luis.
1. Zaznacz pole wyboru po lewej stronie nazwy aplikacji na liście.
1. Wybierz element **Eksportuj** z kontekstowego paska narzędzi powyżej listy.
1. Wybierz pozycję **Eksportuj dla kontenera (gzip)**.
1. Wybierz środowisko **miejsca produkcyjnego** lub **miejsca przejściowego**.
1. Pakiet zostanie pobrany z przeglądarki.

![Wyeksportuj opublikowany pakiet dla kontenera z menu Eksportuj strony aplikacji](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Eksportuj pakiet aplikacji z wersji z portalu LUIS

Pakiet aplikacji z wersjami jest dostępny na stronie listy **wersji** .

1. Zaloguj się do [portalu](https://www.luis.ai)Luis.
1. Wybierz aplikację na liście.
1. Wybierz pozycję **Zarządzaj** na pasku nawigacyjnym aplikacji.
1. Wybierz pozycję **wersje** na lewym pasku nawigacyjnym.
1. Zaznacz pole wyboru po lewej stronie nazwy wersji na liście.
1. Wybierz element **Eksportuj** z kontekstowego paska narzędzi powyżej listy.
1. Wybierz pozycję **Eksportuj dla kontenera (gzip)**.
1. Pakiet zostanie pobrany z przeglądarki.

![Eksportuj przeszkolony pakiet dla kontenera z menu Eksportuj strony wersje](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Eksportuj pakiet opublikowanej aplikacji z interfejsu API

Użyj następującej metody interfejsu API REST, aby spakować aplikację LUIS, która została już [opublikowana](luis-how-to-publish-app.md). Podstawianie własnych odpowiednich wartości dla symboli zastępczych w wywołaniu interfejsu API przy użyciu tabeli poniżej specyfikacji protokołu HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Symbol zastępczy | Wartość |
|-------------|-------|
| **{APP_ID}** | Identyfikator aplikacji opublikowanej aplikacji LUIS. |
| **{SLOT_NAME}** | Środowisko opublikowanej aplikacji LUIS. Użyj jednej z następujących wartości:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Klucz tworzenia konta LUIS dla opublikowanej aplikacji LUIS.<br/>Swój klucz tworzenia można pobrać ze strony **ustawień użytkownika** w portalu Luis. |
| **{AZURE_REGION}** | Odpowiedni region świadczenia usługi Azure:<br/><br/>`westus` — Zachodnie stany USA<br/>`westeurope` — Europa Zachodnia<br/>`australiaeast` — Australia Wschodnia |

Aby pobrać opublikowany pakiet, zapoznaj się z [dokumentacją interfejsu API w tym miejscu][download-published-package]. Jeśli pobrano pomyślnie, odpowiedź to plik pakietu LUIS. Zapisz plik w lokalizacji magazynu określonej dla instalacji wejściowej kontenera.

### <a name="export-versioned-apps-package-from-api"></a>Eksportuj pakiet aplikacji z wersjami z interfejsu API

Użyj następującej metody interfejsu API REST, aby spakować aplikację LUIS, która została już [przeszkolone](luis-how-to-train.md). Podstawianie własnych odpowiednich wartości dla symboli zastępczych w wywołaniu interfejsu API przy użyciu tabeli poniżej specyfikacji protokołu HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Symbol zastępczy | Wartość |
|-------------|-------|
| **{APP_ID}** | Identyfikator aplikacji wyszkolonej aplikacji LUIS. |
| **{APP_VERSION}** | Wersja aplikacji z przeszkolonej aplikacji LUIS. |
| **{AUTHORING_KEY}** | Klucz tworzenia konta LUIS dla opublikowanej aplikacji LUIS.<br/>Swój klucz tworzenia można pobrać ze strony **ustawień użytkownika** w portalu Luis. |
| **{AZURE_REGION}** | Odpowiedni region świadczenia usługi Azure:<br/><br/>`westus` — Zachodnie stany USA<br/>`westeurope` — Europa Zachodnia<br/>`australiaeast` — Australia Wschodnia |

Aby pobrać pakiet z wersjami, zapoznaj się z [dokumentacją interfejsu API w tym miejscu][download-versioned-package]. Jeśli pobrano pomyślnie, odpowiedź to plik pakietu LUIS. Zapisz plik w lokalizacji magazynu określonej dla instalacji wejściowej kontenera.

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontener. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) , aby uzyskać szczegółowe informacje na temat sposobu pobierania `{ENDPOINT_URI}` `{API_KEY}` wartości i.

[Przykłady](luis-container-configuration.md#example-docker-run-commands) `docker run` polecenia są dostępne.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/language/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Ten przykład używa katalogu poza `C:` dyskiem, aby uniknąć konfliktów uprawnień w systemie Windows. Jeśli musisz użyć określonego katalogu jako katalogu wejściowego, może być konieczne przyznanie uprawnienia usługi Docker.
* Nie zmieniaj kolejności argumentów, chyba że znasz kontenery Docker.
* W przypadku korzystania z innego systemu operacyjnego należy użyć właściwej konsoli/terminalu, składni folderu dla instalacji i znaku kontynuacji wiersza dla systemu. W poniższych przykładach założono, że konsola systemu Windows ma znak kontynuacji wiersza `^` . Ponieważ kontener jest systemem operacyjnym Linux, instalacja docelowa używa składni folderu w stylu systemu Linux.

To polecenie:

* Uruchamia kontener z obrazu kontenera LUIS
* Ładuje aplikację LUIS z instalacji wejściowej w lokalizacji *C:\input*, która znajduje się na hoście kontenera
* Przydziela dwa rdzenie procesora CPU i 4 gigabajty (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Zapisuje dzienniki kontenerów i LUIS na potrzeby instalacji wyjściowej w *C:\output*, znajdującej się na hoście kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

Więcej [przykładów](luis-container-configuration.md#example-docker-run-commands) `docker run` polecenia jest dostępnych.

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby można było uruchomić kontener, należy określić opcje, i. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).
> Wartość ApiKey jest **kluczem** ze strony **zasobów platformy Azure** w portalu Luis i jest również dostępna na `Cognitive Services` stronie klucze zasobów platformy Azure.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Interfejsy API punktu końcowego obsługiwane przez kontener

W kontenerze są dostępne zarówno wersje 2, jak i [v3](luis-migration-api-v3.md) interfejsu API.

## <a name="query-the-containers-prediction-endpoint"></a>Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera

Kontener udostępnia oparte na interfejsie REST interfejsy API punktu końcowego przewidywania zapytań. Punkty końcowe dla opublikowanych aplikacji (przemieszczania lub produkcji) mają _różną_ trasę niż punkty końcowe dla aplikacji z wersjami.

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera.

# <a name="v3-prediction-endpoint"></a>[Punkt końcowy przewidywania v3](#tab/v3)

|Typ pakietu|Czasownik HTTP|Trasa|Parametry zapytania|
|--|--|--|--|
|Opublikowany|POBIERZ, OPUBLIKUJ|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Systemową|POBIERZ, OPUBLIKUJ|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Parametry zapytania konfigurują sposób i wartość zwracaną w odpowiedzi na zapytanie:

|Parametr zapytania|Typ|Przeznaczenie|
|--|--|--|
|`query`|ciąg|Wypowiedź użytkownika.|
|`verbose`|boolean|Wartość logiczna wskazująca, czy zwracać wszystkie metadane dla modeli predykcyjnych. Wartość domyślna to false.|
|`log`|boolean|Rejestruje zapytania, które mogą być używane później w przypadku [aktywnej nauki](luis-how-to-review-endpoint-utterances.md). Wartość domyślna to false.|
|`show-all-intents`|boolean|Wartość logiczna wskazująca, czy zwrócić wszystkie intencje lub górne przeznaczenie tylko do oceny. Wartość domyślna to false.|

# <a name="v2-prediction-endpoint"></a>[Punkt końcowy przewidywania wersji 2](#tab/v2)

|Typ pakietu|Czasownik HTTP|Trasa|Parametry zapytania|
|--|--|--|--|
|Opublikowany|[Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Opublikuj](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Systemową|POBIERZ, OPUBLIKUJ|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Parametry zapytania konfigurują sposób i wartość zwracaną w odpowiedzi na zapytanie:

|Parametr zapytania|Typ|Przeznaczenie|
|--|--|--|
|`q`|ciąg|Wypowiedź użytkownika.|
|`timezoneOffset`|liczba|TimezoneOffset umożliwia [zmianę strefy czasowej](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) używanej przez wstępnie utworzoną jednostkę datetimeV2.|
|`verbose`|boolean|Zwraca wszystkie intencje i ich wyniki po ustawieniu na wartość true. Wartość domyślna to false, która zwraca tylko najwyższy cel.|
|`staging`|boolean|Zwraca zapytanie z wyników środowiska przejściowego, jeśli ma wartość true. |
|`log`|boolean|Rejestruje zapytania, które mogą być używane później w przypadku [aktywnej nauki](luis-how-to-review-endpoint-utterances.md). Wartość domyślna to „true”.|

***

### <a name="query-the-luis-app"></a>Wysyłanie zapytań do aplikacji LUIS

Przykładowe polecenie ZWINIĘCIE dla kwerendy kontenera dla opublikowanej aplikacji to:

# <a name="v3-prediction-endpoint"></a>[Punkt końcowy przewidywania v3](#tab/v3)

Aby zbadać model w gnieździe, użyj następującego interfejsu API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Aby wykonać zapytania do środowiska **przejściowego** , Zastąp `production` w trasie `staging` :

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Aby zbadać model z wersjami, użyj następującego interfejsu API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Punkt końcowy przewidywania wersji 2](#tab/v2)

Aby zbadać model w gnieździe, użyj następującego interfejsu API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Aby wykonać zapytania w środowisku **przejściowym** , Zmień wartość parametru ciągu zapytania **przemieszczania** na true:

`staging=true`

Aby zbadać model z wersjami, użyj następującego interfejsu API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Nazwa wersji ma maksymalnie 10 znaków i zawiera tylko znaki dozwolone w adresie URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importowanie dzienników punktów końcowych dla usługi Active Learning

Jeśli dla kontenera LUIS określono instalację wyjściową, pliki dziennika zapytania aplikacji są zapisywane w katalogu wyjściowym, gdzie `{INSTANCE_ID}` jest identyfikatorem kontenera. Dziennik zapytań aplikacji zawiera zapytanie, odpowiedź i sygnatury czasowe dla każdego zapytania predykcyjnego przesłanego do kontenera LUIS.

W następującej lokalizacji przedstawiono zagnieżdżoną strukturę katalogów dla plików dziennika kontenera.
```
/output/luis/{INSTANCE_ID}/
```

W portalu LUIS wybierz aplikację, a następnie wybierz pozycję **Importuj dzienniki punktów końcowych** , aby przekazać te dzienniki.

![Importowanie plików dziennika kontenera do usługi Active Learning](./media/luis-container-how-to/upload-endpoint-log-files.png)

Po przekazaniu dziennika [zapoznaj się z punktem końcowym](./luis-concept-review-endpoint-utterances.md) wyrażenia długości w portalu Luis.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

Aby zamknąć kontener, w środowisku wiersza polecenia, w którym jest uruchomiony kontener, naciśnij **klawisze CTRL + C**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](luis-container-configuration.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontener LUIS wysyła informacje o rozliczeniach do platformy Azure przy użyciu zasobu _Cognitive Services_ na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](luis-container-configuration.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów Language Understanding (LUIS). Podsumowanie:

* Language Understanding (LUIS) udostępnia jeden kontener systemu Linux dla platformy Docker, który udostępnia przewidywania zapytań punktu końcowego wyrażenia długości.
* Obrazy kontenerów są pobierane z Container Registry firmy Microsoft (MCR).
* Obrazy kontenerów są uruchamiane w platformie Docker.
* Za pomocą interfejsu API REST można badać punkty końcowe kontenera, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje o rozliczeniach.

> [!IMPORTANT]
> Kontenery Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą usługi pomiarowej przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z tematem [Konfigurowanie kontenerów](luis-container-configuration.md) dla ustawień konfiguracji.
* Zobacz [ograniczenia dotyczące Luis kontenerów](luis-container-limitations.md) dla znanych ograniczeń możliwości.
* [Rozwiązywanie](troubleshooting.md) problemów związanych z działaniem funkcji Luis można znaleźć w rozwiązaniu.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container