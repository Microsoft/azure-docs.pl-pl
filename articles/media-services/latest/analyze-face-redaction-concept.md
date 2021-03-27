---
title: Redagowanie twarzy w interfejsie API Azure Media Services v3 | Microsoft Docs
description: Azure Media Services v3 zapewnia ustawienia wstępnego wykrywania i preprezentacji, które umożliwiają przesłanie pliku wideo, wykrywanie twarzy i stosowanie ich do rozmycia w pojedynczym połączonym przebiegu lub przez dwuetapową operację umożliwiającą edycję. W tym artykule przedstawiono sposób redagowania twarzy przy użyciu ustawienia wstępnego detektora powierzchni w interfejsie API v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 6db93aa369366936c90446c41406eafe9ee6e414
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630490"
---
# <a name="redact-faces-with-the-face-detector-preset"></a>Redagowanie twarzy przy użyciu ustawień domyślnych czujnika

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Interfejs API programu Azure Media Services v3 zawiera ustawienie wstępne czujnika, które oferuje skalowalną funkcję wykrywania i redakcyjną (rozmycie) w chmurze. Redakcja twarzy umożliwia modyfikowanie wideo w celu rozmycia powierzchni wybranych osób. Możesz chcieć użyć usługi redakcyjnej ze stroną w scenariuszach bezpieczeństwa publicznego i mediów informacyjnych. Kilka minut filmu, które zawiera wiele twarzy, może trwać kilka godzin do ręcznego zredagowania, ale z tym ustawieniem wstępnym proces redakcyjny twarzy będzie wymagał zaledwie kilku prostych kroków.

W tym artykule przedstawiono szczegółowe informacje dotyczące **ustawienia wstępnego detektora** i pokazano, jak używać go z zestawem SDK Azure Media Services dla platformy .NET.

## <a name="compliance-privacy-and-security"></a>Zgodność, prywatność i zabezpieczenia
 
Ważną kwestią jest przestrzeganie wszystkich obowiązujących przepisów dotyczących używania analiz w programie Azure Media Services. Nie należy używać Azure Media Services ani żadnej innej usługi platformy Azure w sposób naruszający prawa innych. Przed przekazaniem jakichkolwiek filmów wideo, w tym wszelkich danych biometrycznych, do usługi Azure Media Services na potrzeby przetwarzania i przechowywania, należy dysponować wszystkimi właściwymi prawami, w tym wszystkimi odpowiednimi komunikatami o zgodzie, od osób w filmie wideo. Aby dowiedzieć się więcej o zgodności, ochronie prywatności i bezpieczeństwie w Azure Media Services, [warunki dotyczące Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)platformy Azure. W przypadku obowiązków związanych z ochroną prywatności firmy Microsoft i korzystania z danych zapoznaj się z zasadami [zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, tematami dotyczącymi [usług online](https://www.microsoft.com/licensing/product-licensing/products) i [uzupełnieniem przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Więcej informacji o ochronie prywatności, takich jak przechowywanie danych, usuwanie/niszczenie, jest dostępna w pliku OST i w [tym miejscu](../video-indexer/faq.md). Korzystając z Azure Media Services, wyrażasz zgodę na związanie Cognitive Services warunkami, OST, DPA i zasad zachowania poufności informacji


## <a name="face-redaction-modes"></a>Tryby redakcyjne

Redakcja twarzy działa przez wykrywanie powierzchni w każdej klatce wideo i śledzenie obiektu twarzy zarówno do przodu, jak i do tyłu, tak aby ta sama osoba mogła być zamazana z innych kątów. Zautomatyzowany proces redakcyjny jest skomplikowany i nie zawsze jest rozmycia co czołowe 100%. Z tego powodu ustawienia wstępne można użyć trybu dwuprzebiegu, aby poprawić jakość i dokładność rozmycia przez etap edycji przed przesłaniem pliku do ostatniego przebiegu rozmycia. 

Oprócz w pełni zautomatyzowanego trybu **połączonego** , dwuprzebiegowy przepływ pracy umożliwia wybranie twarzy, które mają być rozmycia (lub nie rozmycia) za pomocą listy identyfikatorów. W celu dostrojenia dowolnej ramki ustawienia wstępne używa pliku metadanych w formacie JSON jako dane wejściowe do drugiego przebiegu. Ten przepływ pracy jest podzielony na tryby **analizowania** i **redagowania** .

Można również łatwo połączyć dwa tryby w jednym przebiegu, który uruchamia oba zadania w jednym zadaniu. Ten tryb jest wywoływany **razem**.  W tym artykule przykładowy kod pokazuje, jak użyć uproszczonego trybu **połączonego** pojedynczego przebiegu na przykładowym pliku źródłowym.

### <a name="combined-mode"></a>Tryb połączony

Spowoduje to utworzenie pliku wideo redagowane MP4 w jednym przebiegu bez konieczności ręcznej edycji pliku JSON. Dane wyjściowe w folderze zasobów dla zadania będą pojedynczym plikiem MP4, który zawiera rozmycie twarzy przy użyciu zaznaczonego efektu rozmycia. Użyj właściwości rozwiązanie ustawionej na **SourceResolution** , aby osiągnąć najlepsze wyniki dla redakcyjnej.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |"ignite-sample.mp4" |Wideo w formacie WMV, MOV lub MP4 |
| Konfiguracja wstępnie ustawiona |Konfiguracja detektora kroju | **mode**: FaceRedactorMode. kombinowane,  **blurtype**: blurtype. med, **Solution**: AnalysisResolution. SourceResolution |
| Wyjściowy element zawartości |"ignite-redacted.mp4 |Wideo z efektem rozmytym stosowanym do twarzy |

### <a name="analyze-mode"></a>Tryb analizy

**Przeanalizowanie** przebiegu przepływu pracy dwuprzebiegowej pobiera wideo i tworzy plik JSON z listą lokalizacji, identyfikatorami i obrazami jpg każdej wykrytej klasy. 

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |"ignite-sample.mp4" |Wideo w formacie WMV, MPV lub MP4 |
| Konfiguracja wstępnie ustawiona |Konfiguracja detektora kroju |**mode**: FaceRedactorMode. analizować, **rozwiązanie**: AnalysisResolution. SourceResolution|
| Wyjściowy element zawartości |ignite-sample_annotations.jsna |Dane adnotacji lokalizacji czołowych w formacie JSON. Może to być edytowane przez użytkownika w celu zmodyfikowania pól związanych z rozmyciem. Zobacz przykład poniżej. |
| Wyjściowy element zawartości |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Przycięta jpg każdej wykrytej klasy, gdzie liczba wskazuje labelId |

#### <a name="output-example"></a>Przykład danych wyjściowych

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Tryb redagowania

Drugi przebieg przepływu pracy pobiera większą liczbę danych wejściowych, które muszą być połączone w pojedynczy element zawartości.

Obejmuje to listę identyfikatorów rozmycia, oryginalnego wideo oraz JSON adnotacji. W tym trybie są stosowane adnotacje w celu zastosowania rozmycia danych wejściowych wideo.

Dane wyjściowe z przebiegu analizy nie obejmują oryginalnego wideo. Film wideo musi zostać przekazany do wejściowego zasobu dla zadania tryb Zredaguj i wybrany jako plik podstawowy.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |"ignite-sample.mp4" |Wideo w formacie WMV, MPV lub MP4. To samo wideo jak w kroku 1. |
| Zasób wejściowy |"ignite-sample_annotations.json" |plik metadanych adnotacji z fazy pierwszej, z opcjonalnymi modyfikacjami, jeśli chcesz zmienić rozmyte twarze. Musi to być edytowane w aplikacji zewnętrznej, w kodzie lub edytorze tekstu. |
| Zasób wejściowy | "ignite-sample_IDList.txt" (opcjonalnie) | Opcjonalna lista oddzielonych od nowa linia identyfikatorów kroju do redagowania. Jeśli pole pozostanie puste, zostaną zastosowane rozmycie wszystkich twarzy w źródle. Możesz użyć listy, aby selektywnie wybrać rozmycie określonych twarzy. |
| Ustawienie wstępne czujnika rozpoznawania  |Konfiguracja wstępnie ustawiona  | **mode**: FaceRedactorMode. Zredaguj, **blurtype**: blurtype. med |
| Wyjściowy element zawartości |"ignite-sample-redacted.mp4" |Wideo z rozmyciem stosowane na podstawie adnotacji |

#### <a name="example-output"></a>Przykładowe dane wyjściowe

Jest to wyjście z IDList z wybranym IDENTYFIKATORem.

Przykład foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Typy rozmycia

W trybie **połączonym** lub **Zredaguj** istnieje pięć różnych trybów rozmycia, z których można skorzystać za pośrednictwem konfiguracji danych wejściowych JSON: **Low**, **Med**, **High**, **Box** i **Black**. Domyślnie jest używana wartość **Med** .

Przykłady typów rozmycia można znaleźć poniżej.

### <a name="example-settings-for-face-detector-preset"></a>Przykładowe ustawienia dla wstępnie zdefiniowanego czujnika rozpoznawania
[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]


#### <a name="low"></a>Niski

![Przykład ustawienia rozmycia niskiej rozdzielczości.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Komitetem

![Przykład ustawienia rozmycia średniego rozdzielczości.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Wys.

![Przykład ustawienia rozmycia o wysokiej rozdzielczości.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Tryb Box do użycia w debugowaniu danych wyjściowych.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Czarnoskórzy

![Tryb czarno-Box obejmuje wszystkie twarze z czarnym pudełkami.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Elementy wyjściowego pliku JSON

Pakiet administracyjny redakcyjny zapewnia wykrywanie i śledzenie lokalizacji twarzy z wysoką dokładnością, które mogą wykrywać nawet 64 ludzkich powierzchni w ramce wideo. Twarze czołowe zapewniają najlepsze wyniki, a jednocześnie twarze i małe twarze (mniejsze niż lub równe 24x24 pikseli) są trudne.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

Poniższy program pokazuje, jak używać **połączonego** trybu redakcyjnego pojedynczego przebiegu:

- Utwórz element zawartości i Przekaż plik multimedialny do elementu zawartości.
- Skonfiguruj ustawienia wstępne detektora kroju, które używa ustawień tryb i rozmytytype.
- Utwórz nowe przekształcenie przy użyciu ustawienia wstępnego wykrywania kroju
- Pobierz wyjściowy plik wideo redagowane.

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład dla platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

Przykład znajduje się w folderze [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) . Otwórz [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) w pobranym projekcie. Zastąp wartości poświadczeniami uzyskanymi w celu [uzyskania dostępu do interfejsów API](./access-api-howto.md).

**Opcjonalnie** można skopiować plik **[Sample. env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** w katalogu głównym repozytorium i uzupełnić w nim szczegółowe informacje, a następnie zmienić jego nazwę na **. env** (należy zanotować kropkę w przód!), aby można było jej używać we wszystkich przykładowych projektach w repozytorium.  Eliminuje to konieczność wypełniania appsettings.jsdla plików w poszczególnych przykładach, a także chroni przed sprawdzaniem jakichkolwiek ustawień w ramach własnych repozytoriów usługi git.

#### <a name="examples"></a>Przykłady

Ten kod pokazuje, jak skonfigurować **FaceDetectorPreset** do rozmycia w trybie **połączonym** .

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Ten przykładowy kod pokazuje, jak ustawienie wstępne jest przesyłane do obiektu transformacji podczas jego tworzenia. Po utworzeniu przekształcenia zadania mogą być przesyłane bezpośrednio do niego.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

