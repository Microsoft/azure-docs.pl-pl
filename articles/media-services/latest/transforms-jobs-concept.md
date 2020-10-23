---
title: Transformacje i zadania w Media Services
titleSuffix: Azure Media Services
description: Dowiedz się, jak utworzyć przekształcenia, aby opisać reguły przetwarzania filmów wideo w Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: inhenkel
ms.openlocfilehash: 4cb4bcf5eb205d27cbca764d5ec16890a23be5c6
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426790"
---
# <a name="transforms-and-jobs-in-media-services"></a>Transformacje i zadania w Media Services

Ten temat zawiera szczegółowe informacje o [transformacjech](/rest/api/media/transforms) i [zadaniach](/rest/api/media/jobs) oraz opis relacji między tymi jednostkami.

## <a name="overview"></a>Omówienie

### <a name="transformsjobs-workflow"></a>Przepływ pracy transformacji/zadań

Na poniższym diagramie przedstawiono przepływ pracy transformacji/zadań:

![Przepływy pracy transformacji i zadań w Azure Media Services](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Typowy przepływ pracy

1. Utwórz transformację.
2. Prześlij zadania w ramach tej transformacji.
3. Przekształceń list.
4. Usuń transformację, jeśli nie planujesz jej używania w przyszłości.

#### <a name="example"></a>Przykład

Załóżmy, że chcesz wyodrębnić pierwszą ramkę ze wszystkich filmów wideo jako obraz miniatury — kroki, które należy wykonać:

1. Zdefiniuj przepis lub regułę przetwarzania filmów wideo: "Użyj pierwszej ramki filmu wideo jako miniatury".
2. Dla każdego filmu wideo poinformuj usługę:
    1. Gdzie znaleźć ten film wideo.
    2. Miejsce zapisania obrazu miniatury wyjściowej.

**Transformacja** pomaga utworzyć przepis jednokrotnie (krok 1) i przesłać zadania przy użyciu tego przepisu (krok 2).

> [!NOTE]
> Właściwości **transformacji** i **zadania** typu datetime są zawsze w formacie UTC.

## <a name="transforms"></a>Przekształcenia

Za pomocą **transformacji** można skonfigurować typowe zadania związane z kodowaniem lub analizowaniem filmów wideo. Każde **przekształcenie** opisuje przepis lub przepływ pracy zadań do przetwarzania plików wideo lub audio. Pojedyncze przekształcenie może zastosować więcej niż jedną regułę. Na przykład transformacja może określić, że poszczególne wideo mają być zakodowane w pliku MP4 z określoną szybkością transmisji bitów, i że obraz miniatury jest generowany na podstawie pierwszej ramki filmu wideo. Należy dodać jeden wpis TransformOutput dla każdej reguły, która ma zostać dołączona do transformacji. Możesz użyć ustawień wstępnych, aby określić, jak powinny być przetwarzane wejściowe pliki multimedialne.

### <a name="viewing-schema"></a>Wyświetlanie schematu

W Media Services v3, ustawienia wstępne są jednoznacznie określonymi jednostkami w interfejsie API. Definicję "schemat" tych obiektów można znaleźć w temacie [Open API Specification (lub Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Możesz również wyświetlić wstępnie zdefiniowane definicje (takie jak **StandardEncoderPreset**) w [interfejsie API REST](/rest/api/media/transforms/createorupdate#standardencoderpreset), [zestawie .NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)lub Media Services innej dokumentacji dotyczącej zestawu SDK w wersji v3.

### <a name="creating-transforms"></a>Tworzenie transformacji

Przekształcenia można tworzyć przy użyciu REST, interfejsu wiersza polecenia lub dowolnego z opublikowanych zestawów SDK. Interfejs API programu Media Services V3 jest oparty na Azure Resource Manager, dzięki czemu można także tworzyć i wdrażać transformacje na koncie Media Services przy użyciu szablonów Menedżer zasobów. Za pomocą kontroli dostępu opartej na rolach na platformie Azure można blokować dostęp do transformacji.

### <a name="updating-transforms"></a>Aktualizowanie przekształceń

Jeśli musisz zaktualizować [transformację](/rest/api/media/transforms), użyj operacji **Update** . Jest ona przeznaczona do wprowadzania zmian w opisie lub priorytetów bazowego TransformOutputs. Zaleca się, aby takie aktualizacje zostały wykonane po zakończeniu wszystkich zadań w toku. Jeśli zamierzasz ponownie napisać przepis, musisz utworzyć nowe przekształcenie.

### <a name="transform-object-diagram"></a>Przekształć diagram obiektów

Na poniższym diagramie przedstawiono obiekt **przekształcenia** i obiekty, do których się odwołuje, łącznie z relacjami pochodnymi. Szare strzałki pokazują typ, do którego odwołuje się zadanie, a zielona strzałka Pokaż relacje klasy pochodnej.

Wybierz obraz, aby wyświetlić go w pełnym rozmiarze.  

[![Diagram przedstawiający obiekt przekształcenia i obiekty, do których się odwołuje, łącznie z relacjami pochodnymi klasy między obiektami.](./media/api-diagrams/transform-small.png)](./media/api-diagrams/transform-large.png#lightbox)

## <a name="jobs"></a>Stanowiska

**Zadanie** to rzeczywiste żądanie Media Services, aby zastosować **transformację** do danych wejściowych wideo lub audio. Po utworzeniu przekształcenia można przesłać zadania przy użyciu interfejsów API Media Services lub dowolnego z opublikowanych zestawów SDK. **Zadanie** określa informacje na przykład o lokalizacji wejściowego wideo i lokalizacji danych wyjściowych. Możesz określić lokalizację wejściowego wideo przy użyciu: adresy URL HTTPS, adresy URL SAS lub [zasoby](/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Dane wejściowe zadania z protokołu HTTPS

Użyj [danych wejściowych zadania z protokołu HTTPS](job-input-from-http-how-to.md) , jeśli zawartość jest już dostępna za pośrednictwem adresu URL i nie musisz przechowywać pliku źródłowego na platformie Azure (na przykład zaimportować z S3). Ta metoda jest również odpowiednia, jeśli masz zawartość w usłudze Azure Blob Storage, ale nie ma potrzeby, aby plik znajdował się w elemencie zawartości. Obecnie ta metoda obsługuje tylko jeden plik na potrzeby danych wejściowych.

### <a name="asset-as-job-input"></a>Zasób jako dane wejściowe zadania

Użyj [elementu zawartości jako danych wejściowych zadania](job-input-from-local-file-how-to.md) , jeśli zawartość wejściowa jest już w elemencie zawartości lub zawartość jest przechowywana w pliku lokalnym. Jest również dobrym rozwiązaniem, jeśli planujesz opublikowanie elementu zawartości wejściowej do przesyłania strumieniowego lub pobierania (Załóżmy, że chcesz opublikować plik MP4 do pobrania, ale również chcesz zrobić zamianę mowy na tekst lub wykrywanie kroju). Ta metoda obsługuje zasoby wieloplikowe (na przykład zestawy przesyłania strumieniowego MBR, które zostały zakodowane lokalnie).

### <a name="checking-job-progress"></a>Sprawdzanie postępu zadania

Postęp i stan zadań można uzyskać przez monitorowanie zdarzeń za pomocą Event Grid. Aby uzyskać więcej informacji, zobacz [monitorowanie zdarzeń przy użyciu EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Aktualizowanie zadań

Operacja Update w jednostce [zadania](/rest/api/media/jobs) może służyć do modyfikowania *opisu* i właściwości *priorytetu* po przesłaniu zadania. Zmiana właściwości *Priority* jest skuteczna tylko wtedy, gdy zadanie jest nadal w stanie w kolejce. Jeśli zadanie rozpoczęło przetwarzanie lub zostało zakończone, zmiana priorytetu nie ma żadnego wpływu.

### <a name="job-object-diagram"></a>Diagram obiektu zadania

Na poniższym diagramie przedstawiono obiekt **zadania** i obiekty, do których się odwołuje, w tym relacje wyznaczania.

Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

[![Diagram przedstawiający obiekt zadania i obiekty, do których się odwołuje, włącznie z relacjami pochodnymi klasy między obiektami.](./media/api-diagrams/job-small.png)](./media/api-diagrams/job-large.png#lightbox)

## <a name="configure-media-reserved-units"></a>Konfigurowanie jednostek zarezerwowanych multimediów

W przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez Media Services v3 lub Video Indexer, zdecydowanie zaleca się zainicjowanie obsługi konta przy użyciu 10 jednostek zarezerwowanych multimediów (MRUs). Jeśli potrzebujesz więcej niż 10 MRUs S3, Otwórz bilet pomocy technicznej przy użyciu [Azure Portal](https://portal.azure.com/).

Aby uzyskać szczegółowe informacje, zobacz [Skalowanie przetwarzania multimediów przy użyciu interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="see-also"></a>Zobacz też

* [Kody błędów](/rest/api/media/jobs/get#joberrorcode)
* [Filtrowanie, porządkowanie, stronicowanie jednostek Media Services](entities-overview.md)

## <a name="next-steps"></a>Następne kroki

- Przed rozpoczęciem opracowywania, zobacz [Tworzenie aplikacji przy użyciu interfejsów api Media Services v3](media-services-apis-overview.md) (w tym informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itp.).
- Zapoznaj się z tymi samouczkami:

    - [Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo](stream-files-tutorial-with-rest.md)
    - [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo](stream-files-tutorial-with-api.md)
    - [Samouczek: analizowanie filmów wideo z Media Services v3](analyze-videos-tutorial-with-api.md)
