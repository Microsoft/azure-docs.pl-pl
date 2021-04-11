---
title: Cykl życia modelu i punktu końcowego usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech udostępnia modele podstawowe do adaptacji i umożliwia tworzenie niestandardowych modeli na podstawie danych. W tym artykule opisano osie czasu dla modeli i punkty końcowe używające tych modeli.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: b82a732533c3d069b519b07c3209d4b96c472900
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385029"
---
# <a name="model-and-endpoint-lifecycle"></a>Cykl życia modelu i punktu końcowego

Nasz standard (niedostosowany) jest oparty na modelach AI, które wywołują modele podstawowe. W większości przypadków firma Microsoft szkoli inny model podstawowy dla każdego obsługiwanego języka.  Usługa Speech została zaktualizowana o nowe modele podstawowe co kilka miesięcy, aby poprawić dokładność i jakość.  
Za pomocą Custom Speech modele niestandardowe są tworzone przez dostosowanie wybranego modelu podstawowego z danymi z określonego scenariusza klienta. Po utworzeniu modelu niestandardowego ten model nie zostanie zaktualizowany ani zmieniony, nawet jeśli odpowiedni model podstawowy, z którego został dostosowany, zostanie zaktualizowany w usłudze Standard Speech.  
Te zasady umożliwiają utrzymywanie użycia określonego modelu niestandardowego przez dłuższy czas po utworzeniu modelu niestandardowego, który spełnia Twoje potrzeby.  Jednak zalecamy okresowe ponowne utworzenie modelu niestandardowego, aby można było dostosować go od najnowszego modelu podstawowego w celu wykorzystania ulepszonej dokładności i jakości.

Inne kluczowe terminy związane z cyklem życia modelu obejmują:

* **Adaptacja**: Tworzenie modelu podstawowego i dostosowywanie go do domeny/scenariusza przy użyciu danych tekstowych i/lub danych audio.
* **Dekodowanie**: korzystanie z modelu i wykonywanie rozpoznawania mowy (dekodowanie dźwięku na tekst).
* **Punkt końcowy**: specyficzne dla użytkownika wdrożenie modelu podstawowego lub modelu niestandardowego, który jest dostępny *tylko* dla danego użytkownika.

### <a name="expiration-timeline"></a>Oś czasu wygaśnięcia

W miarę jak nowe modele i nowe funkcje stają się dostępne i starsze, mniej dokładne modele są wycofywane, zobacz następujące osie czasu dla elementu model i wygaśnięcie punktu końcowego:

**Modele podstawowe** 

* Adaptacja: dostępne przez jeden rok. Po zaimportowaniu modelu jest on dostępny przez jeden rok do tworzenia modeli niestandardowych. Po jednym roku nowe modele niestandardowe muszą zostać utworzone przy użyciu nowszej wersji modelu bazowego.  
* Dekodowanie: dostępne przez dwa lata po zaimportowaniu. Można więc utworzyć punkt końcowy i użyć transkrypcji partii przez dwa lata z tym modelem. 
* Punkty końcowe: dostępne na tej samej osi czasu co dekodowanie.

**Modele niestandardowe**

* Dekodowanie: dostępne przez dwa lata po utworzeniu modelu. W związku z tym możesz użyć niestandardowego modelu przez dwa lata (Batch/czas rzeczywisty/test) po jego utworzeniu. Po upływie dwóch lat należy ponownie przeprowadzić *uczenie modelu* , ponieważ model podstawowy zazwyczaj jest przestarzały do adaptacji.  
* Punkty końcowe: dostępne na tej samej osi czasu co dekodowanie.

Gdy model podstawowy lub model niestandardowy wygaśnie, zawsze będzie powracać do *najnowszej wersji podstawowego modelu*. W związku z tym Twoja implementacja nigdy nie zostanie przerwana, ale może stać się mniej dokładne dla *konkretnych danych* , jeśli modele niestandardowe osiągnęły ważność. Możesz zobaczyć wygaśnięcie dla modelu w następujących miejscach w Custom Speechm obszarze programu Speech Studio:

* Podsumowanie szkolenia modelu
* Szczegóły szkolenia modelu
* Podsumowanie wdrożenia
* Szczegóły wdrożenia

Oto przykład formy podsumowania szkolenia modelu:

![Podsumowanie szkolenia modelu ](media/custom-speech/custom-speech-model-training-with-expiry.png) oraz na stronie szczegółów szkolenia modelu:

![Szczegóły szkolenia modelu](media/custom-speech/custom-speech-model-details-with-expiry.png)

Możesz również sprawdzić daty wygaśnięcia za pośrednictwem [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) i [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) niestandardowych interfejsów API mowy we `deprecationDates` właściwości w odpowiedzi JSON.

Oto przykład danych o wygasaniu z wywołania interfejsu API GetMode. **DEPRECATIONDATES** Pokaż po wygaśnięciu modelu: 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Należy pamiętać, że można uaktualnić model do niestandardowego punktu końcowego mowy bez przestoju, zmieniając model używany przez punkt końcowy w sekcji Wdrażanie w programie Speech Studio lub za pośrednictwem interfejsu API Custom Speech.

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>Co się stanie po wygaśnięciu modeli i sposobie ich aktualizacji
Co się stanie po wygaśnięciu modelu i sposobie aktualizowania modelu, zależy od tego, w jaki sposób jest używany.
### <a name="batch-transcription"></a>Transkrypcja wsadowa
Jeśli model wygaśnie, który jest używany z żądaniami transkrypcji [transkrypcji partii](batch-transcription.md) , zakończy się niepowodzeniem z powodu błędu 4xx. Aby zapobiec tej aktualizacji, `model` parametr w formacie JSON wysłanym w treści żądania **Create transkrypcji** wskazuje na nowszy model podstawowy lub nowy model niestandardowy. Możesz również usunąć `model` wpis z pliku JSON, aby zawsze używać najnowszego modelu podstawowego.
### <a name="custom-speech-endpoint"></a>Niestandardowy punkt końcowy mowy
Jeśli model wygaśnie, który jest używany przez [niestandardowy punkt końcowy mowy](how-to-custom-speech-train-model.md), usługa automatycznie powróci do korzystania z najnowszego modelu podstawowego dla używanego języka. Możesz wybrać pozycję **wdrożenie** w menu **Custom Speech** w górnej części strony, a następnie kliknąć nazwę punktu końcowego, aby wyświetlić jego szczegóły. W górnej części strony szczegółów zobaczysz przycisk **Aktualizuj model** , który pozwala bezproblemowo zaktualizować model używany przez ten punkt końcowy bez przestoju. Możesz również wprowadzić tę zmianę programowo przy użyciu interfejsu API REST [**modelu aktualizacji**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel) .

## <a name="next-steps"></a>Następne kroki

* [Trenowanie i wdrażanie modelu](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przygotowywanie i testowanie danych](./how-to-custom-speech-test-and-train.md)
* [Inspekcja danych](how-to-custom-speech-inspect-data.md)