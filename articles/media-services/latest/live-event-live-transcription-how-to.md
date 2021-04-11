---
Tytuł: transkrypcja dynamiczna: Opis Azure Media Services: informacje na temat Azure Media Services transkrypcji na żywo.  
usługi: Media-Services documentationcenter: "" Author: IngridAtMicrosoft Manager: femila Editor: "" MS. Service: Media-Services MS. obciążeni: Media ms.tgt_pltfrm: na MS. devlang: ne MS. temat: How-to MS. Date: 08/31/2020 MS. Author: inhenkel

---

# <a name="live-transcription-preview"></a>Transkrypcja dynamiczna (wersja zapoznawcza)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Usługa Azure Media Service oferuje wideo, audio i tekst w różnych protokołach. Gdy publikujesz strumień na żywo przy użyciu formatu MPEG-KRESKowego lub HLS/CMAF, a następnie wideo i audio, Nasza usługa dostarcza tekst uzyskanego w IMSC 1.1 zgodne TTML. Dostarczenie jest spakowane w fragmenty MPEG-4 część 30 (ISO/IEC 14496-30). W przypadku używania dostarczania za pośrednictwem HLS/TS, tekst jest dostarczany jako podzielony VTT.

Dodatkowe opłaty są naliczane po włączeniu transkrypcji na żywo. Zapoznaj się z informacjami o cenach w sekcji wideo na żywo na [stronie cennika Media Services](https://azure.microsoft.com/pricing/details/media-services/).

W tym artykule opisano sposób włączania transkrypcji na żywo podczas przesyłania strumieniowego zdarzenia na żywo przy użyciu Azure Media Services. Przed kontynuowaniem upewnij się, że znasz już korzystanie z interfejsów API REST usługi Media Services v3 (zobacz [ten samouczek](stream-files-tutorial-with-rest.md) , aby uzyskać szczegółowe informacje). Należy również zapoznać się z koncepcją [przesyłania strumieniowego na żywo](stream-live-streaming-concept.md) . Zaleca się ukończenie [przesyłania strumieniowego na żywo za pomocą](stream-live-tutorial-with-api.md) samouczka Media Services.

## <a name="live-transcription-preview-regions-and-languages"></a>Regiony i języki podglądu transkrypcji na żywo

Transkrypcja na żywo jest dostępna w następujących regionach:

- Southeast Asia
- West Europe
- Europa Północna
- East US
- Central US
- South Central US
- Zachodnie stany USA 2
- Brazylia Południowa

Jest to lista dostępnych języków, które mogą być uzyskanego, przy użyciu kodu języka w interfejsie API.

| Język | Kod języka |
| -------- | ------------- |
| Kataloński  | ca-ES |
| Duński (Dania) | da-DK |
| Niemiecki (Niemcy) | de-DE |
| Angielski (Australia) | en-AU |
| Angielski (Kanada) | EN-CA |
| Angielski (Zjednoczone Królestwo) | en-GB |
| Angielski (Indie) | dodatek EN-IN |
| Angielski (Nowa Zelandia) | EN-NZ |
| Angielski (Stany Zjednoczone) | en-US |
| hiszpański (Hiszpania) | es-ES |
| Hiszpański (Meksyk) | es — MX |
| Fiński (Finlandia) | fi-FI |
| francuski (Kanada) | fr — CA |
| Francuski (Francja) | fr-FR |
| Włoski (Włochy) | it-IT |
| Niderlandzki (Holandia) | nl-NL |
| Portugalski (Brazylia) | pt-BR |
| Portugalski (Portugalia) | pt-PT |
| Szwedzki (Szwecja) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>Utwórz wydarzenie na żywo z transkrypcją dynamiczną

Aby utworzyć wydarzenie na żywo z włączonym transkrypcją, Wyślij operację PUT z wersją interfejsu API 2019-05-01-Preview, na przykład:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Operacja ma następującą treść (w której jest tworzone zdarzenie typu pass-through na żywo z protokołem RTMP jako protokół pozyskiwania). Zwróć uwagę na dodanie właściwości transkrypcji.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Rozpocznij lub Zatrzymaj transkrypcję po rozpoczęciu zdarzenia na żywo

Można uruchomić i zatrzymać transkrypcję na żywo, gdy wydarzenie na żywo jest w stanie uruchomienia. Aby uzyskać więcej informacji na temat uruchamiania i zatrzymywania wydarzeń na żywo, przeczytaj sekcję długotrwałe operacje w temacie [Programowanie przy użyciu interfejsów api Media Services v3](media-services-apis-overview.md#long-running-operations).

Aby włączyć transkrypcje na żywo lub zaktualizować język transkrypcji, należy poprawić zdarzenie na żywo, aby zawierało Właściwość "transkrypcje". Aby wyłączyć transkrypcje na żywo, Usuń właściwość "transkrypcji" z obiektu zdarzenia na żywo.  

> [!NOTE]
> Włączenie lub wyłączenie transkrypcji **więcej niż raz** w ramach zdarzenia na żywo nie jest obsługiwanym scenariuszem.

Jest to przykładowe wywołanie umożliwiające włączenie transkrypcji na żywo.

WYSŁANA ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>Dostarczanie i odtwarzanie transkrypcji

Zapoznaj się z artykułem [Omówienie pakietów dynamicznych](encode-dynamic-packaging-concept.md#to-prepare-your-source-files-for-delivery) , w jaki sposób usługa używa pakietu dynamicznego do dostarczania wideo, audio i tekstu w różnych protokołach. Gdy publikujesz strumień na żywo przy użyciu formatu MPEG-KRESKowego lub HLS/CMAF, a następnie wideo i audio, Nasza usługa dostarcza tekst uzyskanego w IMSC 1.1 zgodne TTML. To dostarczanie jest opakowane na fragmenty w formacie MPEG-4 część 30 (ISO/IEC 14496-30). W przypadku używania dostarczania za pośrednictwem HLS/TS tekst jest dostarczany jako podzielony VTT. Aby odtworzyć strumień, można użyć odtwarzacza sieci Web, takiego jak [Azure Media Player](player-use-azure-media-player-how-to.md) .  

> [!NOTE]
> Jeśli używasz Azure Media Player, użyj wersji 2.3.3 lub nowszej.

## <a name="known-issues"></a>Znane problemy

W przypadku wersji zapoznawczej poniżej przedstawiono znane problemy związane z transkrypcją na żywo:

- Aplikacje muszą używać interfejsów API w wersji zapoznawczej, opisanych [Media Services w specyfikacji openapi v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
- Ochrona za pomocą funkcji Digital Rights Management (DRM) nie ma zastosowania do ścieżki tekstowej. możliwe jest tylko szyfrowanie kopert AES.

## <a name="next-steps"></a>Następne kroki

* [Przegląd Media Services](media-services-overview.md)
