---
title: Wskazówki dotyczące migracji kodowania
description: Ten artykuł zawiera wskazówki dotyczące kodowania oparte na scenariuszu, które ułatwiają migrowanie z Azure Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: a01571f4a1f852deb84b7f20d61b8048e8000790
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490101"
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

1. Konfiguracja
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

[![Kodowanie przepływu pracy dla wersji v3](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

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
1. Użyj [Event Grid](monitoring/monitor-events-portal-how-to.md) do monitorowania zadania.
1. Przesyłanie zadania.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Niestandardowe ustawienia wstępne w kodowaniu od wersji 2 do v3

Jeśli masz kod v2 o nazwie kodera standardowego z niestandardowym ustawieniem wstępnym, najpierw musisz utworzyć nowe przekształcenie przy użyciu niestandardowego ustawienia wstępnego kodera standardowego przed przesłaniem zadania.

Niestandardowe ustawienia wstępne są teraz JSON i nie są już oparte na języku XML. Utwórz ponownie ustawienia wstępne w formacie JSON zgodnie z niestandardowym schematem predefiniowanym zdefiniowanym w dokumentacji usługi [Transform Open API (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) .

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Pliki metadanych danych wejściowych i wyjściowych z zadania kodowania

W wersji 2 pliki metadanych danych wejściowych i wyjściowych XML są generowane w wyniku zadania kodowania. W wersji 3 format metadanych został zmieniony z XML na JSON. Aby uzyskać więcej informacji na temat metadanych, zobacz [metadane wejściowe](input-metadata-schema.md) i [metadane wyjściowe](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Koder w warstwie Premium z koderem standardowym v3 lub rozwiązaniami opartymi na partnerze

Interfejs API v2 nie obsługuje już kodera Premium. Jeśli wcześniej użyto kodera Premium opartego na przepływie pracy dla kodowania HEVC, należy przeprowadzić migrację do nowego [kodera standardowego](encode-media-encoder-standard-formats-reference.md) v3 z obsługą kodowania HEVC.

Jeśli potrzebujesz zaawansowanych funkcji przepływu pracy kodera Premium, zachęcasz do rozpoczęcia korzystania z rozwiązania Azure Advanced Encoding partner z [Wyobraź sobie Communications](https://imaginecommunications.com), [Telestream](https://www.telestream.net)lub [Bitmovin](https://bitmovin.com).

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Zadania z danymi wejściowymi w adresach URL hostowanych przy użyciu protokołu HTTPS

Teraz można przesyłać zadania w wersji 3 z plików przechowywanych w usłudze Azure Storage, przechowywanych lokalnie lub zewnętrznych serwerów sieci Web przy użyciu [obsługi danych wejściowych zadań http (S)](job-input-from-http-how-to.md).

Jeśli wcześniej użyto przepływów pracy do kopiowania plików z plików obiektów blob platformy Azure do pustych zasobów przed przesłaniem zadań, można uprościć przepływ pracy, przekazując adres URL sygnatury dostępu współdzielonego do pliku w usłudze Azure Blob Storage bezpośrednio w ramach zadania.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Transkrypcja dźwiękowa indeksatora V1 do nowego AudioAnalyzer "Tryb podstawowy"

W przypadku klientów korzystających z procesora indeksatora v1 w interfejsie API v2 należy utworzyć transformację, która wywołuje nowe `AudioAnalyzer` w [trybie podstawowym](transform-create-basic-audio-how-to.md) przed przesłaniem zadania.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Kodowanie, przekształcenia i zadania, samouczki i przewodniki

### <a name="concepts"></a>Pojęcia

- [Kodowanie wideo i audio przy użyciu Media Services](encode-concept.md)
- [Standardowe formaty i kodeki kodera](encode-media-encoder-standard-formats-reference.md)
- [Koduj z automatycznie wygenerowaną drabiną szybkości transmisji bitów](encode-autogen-bitrate-ladder.md)
- [Użyj ustawienia wstępnego kodowania obsługującego zawartość, aby znaleźć optymalną szybkość transmisji bitów dla danego rozwiązania](encode-content-aware-concept.md)
- [Jednostki zarezerwowane multimediów](concept-media-reserved-units.md)
- [Metadane wejściowe](input-metadata-schema.md)
- [Metadane wyjściowe](output-metadata-schema.md)
- [Dynamiczne pakowanie w Media Services v3: kodery-dekoder audio](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Samouczki

- [Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — .NET](stream-files-dotnet-quickstart.md)
- [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo z Media Services v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

- [Utwórz dane wejściowe zadania na podstawie adresu URL HTTPS](job-input-from-http-how-to.md)
- [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md)
- [Tworzenie podstawowego przekształcenia audio](transform-create-basic-audio-how-to.md)
- Przy użyciu platformy .NET
  - [Jak kodować przy użyciu przekształcenia niestandardowego — .NET](transform-custom-presets-how-to.md)
  - [Jak utworzyć nakładkę z Media Encoder Standard](transform-create-overlay-how-to.md)
  - [Jak generować miniatury przy użyciu standardu Encoder w programie .NET](transform-generate-thumbnails-dotnet-how-to.md)
- Z interfejsem wiersza polecenia platformy Azure
  - [Jak kodować przy użyciu niestandardowej transformacji — interfejs wiersza polecenia platformy Azure](transform-custom-preset-cli-how-to.md)
- Przy użyciu architektury REST
  - [Jak kodować przy użyciu niestandardowej transformacji](transform-custom-preset-rest-how-to.md)
  - [Jak generować miniatury przy użyciu standardu Encoder w warstwie REST](transform-generate-thumbnails-rest-how-to.md)
- [Podcinanie wideo przy kodowaniu przy użyciu Media Services-.NET](transform-subclip-video-dotnet-how-to.md)
- [Podcinanie wideo przy kodowaniu przy użyciu Media Services-REST](transform-subclip-video-rest-how-to.md)

## <a name="samples"></a>Samples

Możesz również [porównać kod v2 i V3 w przykładach kodu](migrate-v-2-v-3-migration-samples.md).
