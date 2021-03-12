---
title: Jak kodować element zawartości platformy Azure przy użyciu Media Encoder Standard | Microsoft Docs
description: Dowiedz się, jak zakodować zawartość multimedialną na Azure Media Services przy użyciu Media Encoder Standard. Przykłady kodu używają interfejsu API REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4b7a188a9509c3510de2843b232f57e3437177b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009831"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Jak kodować element zawartości przy użyciu Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Omówienie

Aby zapewnić cyfrowe wideo przez Internet, należy skompresować multimedia. Cyfrowe pliki wideo są duże i mogą być zbyt duże, aby można je było dostarczać przez Internet lub aby urządzenia klientów były wyświetlane prawidłowo. Kodowanie to proces kompresowania wideo i audio, dzięki czemu klienci mogą wyświetlać multimedia.

Zadania kodowania są jedną z najczęstszych operacji przetwarzania w Azure Media Services. Tworzysz zadania kodowania, aby przekonwertować pliki multimedialne z jednego kodowania na inne. Podczas kodowania można użyć wbudowanego kodera Media Services (Media Encoder Standard). Można również użyć kodera dostarczonego przez partnera Media Services. Kodery innych firm są dostępne w portalu Azure Marketplace. Można określić szczegóły zadań kodowania przy użyciu wstępnie ustawionych ciągów zdefiniowanych dla kodera lub przy użyciu wstępnie ustawionych plików konfiguracji. Aby wyświetlić dostępne typy ustawień wstępnych, zobacz [Ustawienia wstępne zadań dla Media Encoder Standard](./media-services-mes-presets-overview.md).

Każde zadanie może mieć jedno lub więcej zadań w zależności od typu przetwarzania, które chcesz wykonać. Za pomocą interfejsu API REST można tworzyć zadania i powiązane z nimi zadania na jeden z dwóch sposobów:

* Zadania można definiować w tekście za pomocą właściwości nawigacji zadania w jednostkach zadania.
* Użyj przetwarzania wsadowego OData.

Zaleca się, aby zawsze kodować pliki źródłowe do zestawu MP4 z adaptacyjną szybkością transmisji bitów, a następnie skonwertować zestaw do odpowiedniego formatu przy użyciu funkcji [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

Jeśli zasoby wyjściowe są szyfrowane magazynem, należy skonfigurować zasady dostarczania zasobów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas uzyskiwania dostępu do jednostek w Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [konfigurowanie Media Services tworzenia interfejsu API REST](media-services-rest-how-to-use.md).

Przed rozpoczęciem odwoływania się do procesorów multimediów Sprawdź, czy masz prawidłowy identyfikator procesora multimediów. Aby uzyskać więcej informacji, zobacz [pobieranie procesorów multimediów](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsem API usługi AMS, zobacz [dostęp do interfejsu api Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Tworzenie zadania z pojedynczym zadaniem kodowania

> [!NOTE]
> Podczas pracy z interfejsem API REST Media Services są stosowane następujące zagadnienia:
>
> Podczas uzyskiwania dostępu do jednostek w Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [konfigurowanie Media Services tworzenia interfejsu API REST](media-services-rest-how-to-use.md).
>
> W przypadku używania kodu JSON i określania, aby użyć słowa kluczowego **__metadata** w żądaniu (na przykład w celu odwoływania się do obiektu połączonego), należy ustawić format **Zaakceptuj nagłówek akceptowania** [JSON](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Zaakceptuj: Application/JSON; OData = verbose.
>
>

Poniższy przykład pokazuje, jak utworzyć i ogłosić zadanie przy użyciu jednego zestawu zadań do kodowania wideo z określoną rozdzielczością i jakością. W przypadku kodowania przy użyciu Media Encoder Standard można użyć ustawień domyślnych konfiguracji zadania określonych w [tym miejscu](./media-services-mes-presets-overview.md).

Żądanie:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net

{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}
```

Odpowiedź:

```console
HTTP/1.1 201 Created

. . .
```

### <a name="set-the-output-assets-name"></a>Ustaw nazwę wyjściowego elementu zawartości
Poniższy przykład pokazuje, jak ustawić atrybut assetname:

```console
{ "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}`
```

### <a name="considerations"></a>Zagadnienia do rozważenia
* Właściwości TaskBody — muszą używać literału XML do definiowania liczby zasobów wejściowych lub wyjściowych, które są używane przez zadanie. Artykuł zadania zawiera definicję schematu XML dla kodu XML.
* W definicji TaskBody — każda wartość wewnętrzna dla `<inputAsset>` i `<outputAsset>` musi być ustawiona jako JobInputAsset (wartość) lub JobOutputAsset (wartość).
* Zadanie może mieć wiele elementów zawartości wyjściowej. Jeden JobOutputAsset (x) może być używany tylko raz jako dane wyjściowe zadania w zadaniu.
* Można określić JobInputAsset lub JobOutputAsset jako element zawartości wejściowej zadania.
* Zadania nie mogą tworzyć cyklu.
* Parametr value przekazany do JobInputAsset lub JobOutputAsset reprezentuje wartość indeksu dla elementu zawartości. Rzeczywiste elementy zawartości są zdefiniowane we właściwościach nawigacji InputMediaAssets i OutputMediaAssets w definicji jednostki zadania.
* Ponieważ Media Services jest oparty na protokole OData V3, poszczególne zasoby w kolekcjach właściwości nawigacji InputMediaAssets i OutputMediaAssets są przywoływane przez parę nazwa-wartość "__metadata: URI".
* InputMediaAssets mapuje do jednego lub większej liczby zasobów utworzonych w Media Services. OutputMediaAssets są tworzone przez system. Nie odwołują się do istniejącego elementu zawartości.
* OutputMediaAssets można nazwać przy użyciu atrybutu assetname. Jeśli ten atrybut nie jest obecny, nazwa OutputMediaAsset jest dowolną wartością tekstu wewnętrznego `<outputAsset>` elementu z sufiksem wartości nazwy zadania lub wartości identyfikatora zadania (w przypadku, gdy właściwość Name nie jest zdefiniowana). Na przykład jeśli ustawisz wartość dla elementu assetname na "sample", właściwość OutputMediaAsset name ma wartość "sample". Jeśli jednak wartość nie została ustawiona dla elementu assetname, ale ustawisz nazwę zadania na "NewJob", wówczas nazwą OutputMediaAsset byłaby "JobOutputAsset (wartość) _NewJob".

## <a name="create-a-job-with-chained-tasks"></a>Tworzenie zadania z łańcucha zadań
W wielu scenariuszach aplikacji deweloperzy chcą utworzyć szereg zadań przetwarzania. W Media Services można utworzyć serię zadań łańcucha. Każde zadanie wykonuje różne kroki przetwarzania i może korzystać z różnych procesorów multimediów. Zadania łańcucha mogą odłączać zasób od jednego zadania do drugiego, wykonując liniową sekwencję zadań w elemencie zawartości. Jednak zadania wykonywane w ramach zadania nie muszą być w kolejności. Podczas tworzenia połączonego zadania łańcuchowego obiekty **ITask** są tworzone w jednym obiekcie **IJob** .

> [!NOTE]
> Obecnie obowiązuje limit 30 zadań na zadanie. Jeśli konieczne jest utworzenie łańcucha więcej niż 30 zadań, należy utworzyć więcej niż jedno zadanie, które będzie zawierać zadania.
>
>

```console
POST https://media.windows.net/api/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      },
      {  
         "Configuration":"H264 Smooth Streaming 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
      }
   ]
}
```

### <a name="considerations"></a>Zagadnienia do rozważenia
Aby włączyć tworzenie łańcucha zadań:

* Zadanie musi mieć co najmniej dwa zadania podrzędne.
* Musi istnieć co najmniej jedno zadanie, którego dane wejściowe są danymi wyjściowymi innego zadania w zadaniu.

## <a name="use-odata-batch-processing"></a>Korzystanie z przetwarzania wsadowego OData
W poniższym przykładzie pokazano, jak za pomocą przetwarzania wsadowego OData utworzyć zadanie i zadania. Aby uzyskać informacje na temat przetwarzania wsadowego, zobacz artykuł [Przetwarzanie wsadowe protokołu Open Data Protocol (OData)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

```console
POST https://media.windows.net/api/$batch HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Accept: multipart/mixed
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
Host: media.windows.net


--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/Jobs HTTP/1.1
Content-ID: 1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/$1/Tasks HTTP/1.1
Content-ID: 2
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
   "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
```


## <a name="create-a-job-by-using-a-jobtemplate"></a>Tworzenie zadania przy użyciu JobTemplate
Podczas przetwarzania wielu zasobów przy użyciu wspólnego zestawu zadań, użyj JobTemplate, aby określić domyślne ustawienia wstępne zadania lub określić kolejność zadań.

Poniższy przykład pokazuje, jak utworzyć element JobTemplate z TaskTemplate, który jest zdefiniowany w tekście. TaskTemplate używa Media Encoder Standard jako MediaProcessor do kodowania pliku zasobów. Można jednak również użyć innych MediaProcessors.

```console
POST https://media.windows.net/API/JobTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
```

> [!NOTE]
> W przeciwieństwie do innych jednostek Media Services, należy zdefiniować nowy identyfikator GUID dla każdego TaskTemplateu i umieścić go we właściwości taskTemplateId i ID w treści żądania. Schemat identyfikacji zawartości musi następować po schemacie opisanym w temacie identyfikowanie Azure Media Services jednostek. Nie można również zaktualizować JobTemplates. Zamiast tego należy utworzyć nowy z zaktualizowanymi zmianami.
>
>

W przypadku powodzenia następuje zwrócenie następującej odpowiedzi:

```console
HTTP/1.1 201 Created

. . .
```

Poniższy przykład pokazuje, jak utworzyć zadanie odwołujące się do identyfikatora JobTemplate:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
```

W przypadku powodzenia następuje zwrócenie następującej odpowiedzi:

```console
HTTP/1.1 201 Created

. . .
```


## <a name="advanced-encoding-features-to-explore"></a>Zaawansowane funkcje kodowania do eksplorowania
* [Jak generować miniatury](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generowanie miniatur podczas kodowania](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Przytnij wideo podczas kodowania](media-services-crop-video.md)
* [Dostosowywanie ustawień wstępnych kodowania](media-services-custom-mes-presets-with-dotnet.md)
* [Nałożenie wideo z obrazem lub znak wodny](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, jak utworzyć zadanie kodowania elementu zawartości, zobacz [Jak sprawdzić postęp zadania w Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Zobacz też
[Pobierz procesory multimedialne](media-services-rest-get-media-processor.md)