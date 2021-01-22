---
title: Przewodnik po migracji oparty na scenariuszu Microsoft Docs
description: Ten artykuł zawiera wskazówki dotyczące kodowania oparte na scenariuszu, które ułatwiają migrowanie z Azure Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 2d122bdeb98de624d9053852b9bee4595b0ef8c8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690590"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Wskazówki dotyczące migracji opartej na scenariuszu

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-4.svg)

Ten artykuł zawiera wskazówki dotyczące kodowania oparte na scenariuszu, które ułatwiają migrowanie z Azure Media Services V2 do wersji v3.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zmianą przepływu pracy kodowania zaleca się zrozumienie różnic w sposobie zarządzania magazynem.  W usłudze AMS v3 interfejs API usługi Azure Storage jest używany do zarządzania kontami magazynu skojarzonymi z Twoim kontem Media Services.

> [!NOTE]
> Zadania i zadania utworzone w wersji 2 nie są wyświetlane w programie v3, ponieważ nie są skojarzone z przekształceniem. Zaleca się przełączenie na transformacje v3 i zadania.

## <a name="encoding-workflow-comparison"></a>Porównanie przepływu pracy kodowania

Poświęć kilka minut, aby zapoznać się z poniższymi schematami, aby porównać wizualizację przepływów pracy kodowania dla wersji 2 i v3.

### <a name="v2-encoding-workflow"></a>Przepływ pracy kodowania w wersji 2

Kliknij poniższy obraz, aby wyświetlić większą wersję.

[![Kodowanie przepływu pracy dla wersji 2 ](./media/migration-guide/V2-pretty.svg)](./media/migration-guide/V2-pretty.svg#lightbox)

1. Konfigurowanie
    1. Utwórz element zawartości lub Użyj istniejącego elementu zawartości. W przypadku korzystania z nowego elementu zawartości Przekaż zawartość do tego zasobu. W przypadku korzystania z istniejącego elementu zawartości należy zakodować pliki, które już istnieją w elemencie zawartości.
    2. Pobierz wartości następujących elementów:
        - Identyfikator lub obiekt procesora multimediów
        - Ciąg kodera (nazwa) kodera, którego chcesz użyć
        - Identyfikator zasobu nowego zasobu lub identyfikator zasobu istniejącego elementu zawartości
    3. W celu monitorowania należy utworzyć subskrypcję powiadomienia na poziomie zadania lub zadania lub procedurę obsługi zdarzeń zestawu SDK
2. Utwórz zadanie, które zawiera zadanie lub zadania. Każde zadanie powinno obejmować powyższe elementy i:
    - Dyrektywa, którą należy utworzyć wyjściowy element zawartości.  Wyjściowy element zawartości jest tworzony przez system.
    - Opcjonalna nazwa dla wyjściowego elementu zawartości
3. Przesyłanie zadania.
4. Monitoruj zadanie.

### <a name="v3-encoding-workflow"></a>Przepływ pracy kodowania v3

[![Kodowanie przepływu pracy dla wersji v3 ](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Konfiguruj
    1. Utwórz element zawartości lub Użyj istniejącego elementu zawartości. W przypadku korzystania z nowego elementu zawartości Przekaż zawartość do tego zasobu. W przypadku korzystania z istniejącego elementu zawartości należy zakodować pliki, które już istnieją w elemencie zawartości. Nie należy *przekazywać większej zawartości do tego elementu zawartości.*
    1. Utwórz zasób wyjściowy.  Element zawartości wyjściowej to miejsce, w którym będą przechowywane zakodowane pliki i metadane wejściowe i wyjściowe.
    1. Pobierz wartości przekształcenia:
        - Standardowe ustawienie kodera
        - Grupa zasobów AMS
        - Nazwa konta AMS
    1. Utwórz transformację lub Użyj istniejącej transformacji.  Przekształcenia są wielokrotnego użytku. Nie jest konieczne tworzenie nowego przekształcenia za każdym razem, gdy chcesz przesłać zadanie.
1. Tworzenie zadania
    1. Dla zadania Pobierz wartości dla następujących elementów:
        - Nazwa przekształcenia
        - Podstawowy identyfikator URI dla adresu URL sygnatury dostępu współdzielonego dla elementu zawartości, ścieżki źródłowej HTTPs udziału plików lub ścieżki lokalnej plików. `JobInputAsset`Może również użyć nazwy zasobu jako dane wejściowe.
        - Nazwy plików
        - Wyjściowe elementy zawartości
        - Grupa zasobów
        - Nazwa konta AMS  
1. Użyj [Event Grid](monitor-events-portal-how-to.md) do monitorowania zadania.
1. Przesyłanie zadania.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Niestandardowe ustawienia wstępne w kodowaniu od wersji 2 do v3

Jeśli masz kod v2 o nazwie kodera standardowego z niestandardowym ustawieniem wstępnym, najpierw musisz utworzyć nowe przekształcenie przy użyciu niestandardowego ustawienia wstępnego kodera standardowego przed przesłaniem zadania.

Niestandardowe ustawienia wstępne są teraz JSON i nie są już oparte na języku XML. Utwórz ponownie ustawienia wstępne w formacie JSON zgodnie z niestandardowym schematem predefiniowanym zdefiniowanym w dokumentacji usługi [Transform Open API (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) .


<!-- removed because this is covered in the tutorials
Common custom [encoding](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json) scenarios:
        1. Create a custom Single Bitrate MP4 encode
        1. Create a custom [Adaptive Bitrate Encoding Ladder](autogen-bitrate-ladder.md)
        1. Creating Sprite Thumbnails
        1. Creating Thumbnails (see below for your preferred method)
        1. [Sub Clipping](subclip-video-rest-howto.md)
        1. Cropping
-->

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Pliki metadanych danych wejściowych i wyjściowych z zadania kodowania

W wersji 2 pliki metadanych danych wejściowych i wyjściowych XML są generowane w wyniku zadania kodowania. W wersji 3 format metadanych został zmieniony z XML na JSON. Aby uzyskać więcej informacji na temat metadanych, zobacz [metadane wejściowe](input-metadata-schema.md) i [metadane wyjściowe](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Koder w warstwie Premium z koderem standardowym v3 lub rozwiązaniami opartymi na partnerze

Interfejs API v2 nie obsługuje już kodera Premium. Jeśli wcześniej użyto kodera Premium opartego na przepływie pracy dla kodowania HEVC, należy przeprowadzić migrację do nowego [kodera standardowego](media-encoder-standard-formats.md) v3 z obsługą kodowania HEVC.

Jeśli potrzebujesz zaawansowanych funkcji przepływu pracy kodera Premium, zachęcasz do rozpoczęcia korzystania z rozwiązania Azure Advanced Encoding partner z [Wyobraź sobie Communications](https://imaginecommunications.com), [Telestream](https://www.telestream.net)lub [Bitmovin](https://bitmovin.com).

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Zadania z danymi wejściowymi w adresach URL hostowanych przy użyciu protokołu HTTPS

Teraz można przesyłać zadania w wersji 3 z plików przechowywanych w usłudze Azure Storage, przechowywanych lokalnie lub zewnętrznych serwerów sieci Web przy użyciu [obsługi danych wejściowych zadań http (S)](job-input-from-http-how-to.md).

Jeśli wcześniej użyto przepływów pracy do kopiowania plików z plików obiektów blob platformy Azure do pustych zasobów przed przesłaniem zadań, można uprościć przepływ pracy, przekazując adres URL sygnatury dostępu współdzielonego do pliku w usłudze Azure Blob Storage bezpośrednio w ramach zadania.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Transkrypcja dźwiękowa indeksatora V1 do nowego AudioAnalyzer "Tryb podstawowy"

W przypadku klientów korzystających z procesora indeksatora v1 w interfejsie API v2 należy utworzyć transformację, która wywołuje nowe `AudioAnalyzer` w [trybie podstawowym](how-to-create-basic-audio-transform.md) przed przesłaniem zadania.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Kodowanie, przekształcenia i zadania, samouczki i przewodniki

### <a name="concepts"></a>Pojęcia

- [Kodowanie wideo i audio przy użyciu Media Services](encoding-concept.md)
- [Standardowe formaty i kodeki kodera](media-encoder-standard-formats.md)
- [Koduj z automatycznie wygenerowaną drabiną szybkości transmisji bitów](autogen-bitrate-ladder.md)
- [Użyj ustawienia wstępnego kodowania obsługującego zawartość, aby znaleźć optymalną szybkość transmisji bitów dla danego rozwiązania](content-aware-encoding.md)
- [Jednostki zarezerwowane multimediów](concept-media-reserved-units.md)
- [Metadane wejściowe](input-metadata-schema.md)
- [Metadane wyjściowe](output-metadata-schema.md)
- [Dynamiczne pakowanie w Media Services v3: kodery-dekoder audio](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Samouczki

- [Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — .NET](stream-files-dotnet-quickstart.md)
- [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo z Media Services v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

- [Utwórz dane wejściowe zadania na podstawie adresu URL HTTPS](job-input-from-http-how-to.md)
- [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md)
- [Tworzenie podstawowego przekształcenia audio](how-to-create-basic-audio-transform.md)
- Przy użyciu platformy .NET
  - [Jak kodować przy użyciu przekształcenia niestandardowego — .NET](customize-encoder-presets-how-to.md)
  - [Jak utworzyć nakładkę z Media Encoder Standard](how-to-create-overlay.md)
  - [Jak generować miniatury przy użyciu standardu Encoder w programie .NET](media-services-generate-thumbnails-dotnet.md)
- Z interfejsem wiersza polecenia platformy Azure
  - [Jak kodować przy użyciu niestandardowej transformacji — interfejs wiersza polecenia platformy Azure](custom-preset-cli-howto.md)
- Przy użyciu architektury REST
  - [Jak kodować przy użyciu niestandardowej transformacji](custom-preset-rest-howto.md)
  - [Jak generować miniatury przy użyciu standardu Encoder w warstwie REST](media-services-generate-thumbnails-rest.md)
- [Podcinanie wideo przy kodowaniu przy użyciu Media Services-.NET](subclip-video-dotnet-howto.md)
- [Podcinanie wideo przy kodowaniu przy użyciu Media Services-REST](subclip-video-rest-howto.md)

## <a name="samples"></a>Samples

Możesz również [porównać kod v2 i V3 w przykładach kodu](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
