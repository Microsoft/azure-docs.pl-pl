---
title: Omówienie interfejsu API REST Media Services operacji | Microsoft Docs
description: Interfejs API REST usługi Media Services Operations jest używany do tworzenia zadań, zasobów, kanałów na żywo i innych zasobów na koncie Media Services. Ten artykuł zawiera omówienie interfejsu API REST Azure Media Services V2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.openlocfilehash: 9f147e333e4d1b95a14dd3121d7ab304b6166248
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010052"
---
# <a name="media-services-operations-rest-api-overview"></a>Omówienie interfejsu API REST usługi Media Services Operations

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Interfejs API **REST usługi Media Services Operations** jest używany do tworzenia zadań, zasobów, kanałów na żywo i innych zasobów na koncie Media Services. Aby uzyskać więcej informacji, zobacz [Media Services Dokumentacja interfejsu API REST usługi Operations](/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services udostępnia interfejs API REST, który akceptuje zarówno format XML JSON, jak i Atom + pub. Interfejs API REST Media Services wymaga określonych nagłówków HTTP, które każdy klient musi wysłać podczas nawiązywania połączenia z Media Services, jak również zestawu opcjonalnych nagłówków. W poniższych sekcjach opisano nagłówki i zlecenia HTTP, których można użyć podczas tworzenia żądań i odbierania odpowiedzi z Media Services.

Uwierzytelnianie w interfejsie API REST Media Services odbywa się przy użyciu uwierzytelniania Azure Active Directory, które opisano w artykule [używanie uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu api Azure Media Services przy użyciu usługi REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Zagadnienia do rozważenia

W przypadku korzystania z usługi REST obowiązują następujące zagadnienia.

* Podczas wykonywania zapytania o jednostki istnieje limit 1000 jednostek, które są zwracane w tym samym czasie, ponieważ Public REST v2 ogranicza wyniki zapytania do 1000 wyników. Należy użyć funkcji **Skip** i **Take** (.NET)/ **Top** (REST) zgodnie z opisem w [tym przykładzie platformy .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) i [przykładowym interfejsem API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* W przypadku używania kodu JSON i określania, aby użyć słowa kluczowego **__metadata** w żądaniu (na przykład w celu odwoływania się do obiektu połączonego), należy ustawić format **Zaakceptuj nagłówek akceptowania** [JSON](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (zobacz Poniższy przykład). Usługa OData nie rozpoznaje właściwości **__metadata** w żądaniu, chyba że ustawisz ją jako pełne.  

    ```console
    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    {
        "Name" : "NewTestJob", 
        "InputMediaAssets" : 
            [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
    . . . 
   ```

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standardowe nagłówki żądań HTTP obsługiwane przez Media Services
Dla każdego wywołania, które wprowadzasz do Media Services, istnieje zestaw wymaganych nagłówków, które należy uwzględnić w żądaniu, a także zestaw opcjonalnych nagłówków, które warto uwzględnić. W poniższej tabeli wymieniono wymagane nagłówki:

| Nagłówek | Typ | Wartość |
| --- | --- | --- |
| Autoryzacja |Bearer |Posiadacz jest jedynym zaakceptowanym mechanizmem autoryzacji. Wartość musi również zawierać token dostępu dostarczony przez Azure Active Directory. |
| x-MS-Version |Liczba dziesiętna |2,17 (lub Najnowsza wersja)|
| DataServiceVersion |Liczba dziesiętna |3.0 |
| MaxDataServiceVersion |Liczba dziesiętna |3.0 |

> [!NOTE]
> Ponieważ Media Services używa protokołu OData do uwidaczniania interfejsów API REST, nagłówki DataServiceVersion i MaxDataServiceVersion powinny być uwzględniane we wszystkich żądaniach. Jeśli jednak tak nie jest, obecnie Media Services zakłada, że wartość DataServiceVersion jest używana, to 3,0.
> 
> 

Poniżej znajduje się zestaw opcjonalnych nagłówków:

| Nagłówek | Typ | Wartość |
| --- | --- | --- |
| Date (Data) |Data 1123 |Sygnatura czasowa żądania |
| Zaakceptuj |Typ zawartości |Żądany typ zawartości dla odpowiedzi, na przykład następujące:<p> -Application/JSON; OData = verbose<p> -Application/Atom + XML<p> Odpowiedzi mogą mieć inny typ zawartości, taki jak pobieranie obiektów blob, gdzie pomyślnie odpowiedź zawiera strumień obiektów BLOB jako ładunek. |
| Accept-Encoding |Gzip, Wklęśnięcie |Kodowanie GZIP i WKLĘŚNIĘCIE, jeśli ma zastosowanie. Uwaga: w przypadku dużych zasobów Media Services może zignorować ten nagłówek i zwrócić nieskompresowane dane. |
| Accept-Language |"pl", "es" i tak dalej. |Określa preferowany język odpowiedzi. |
| Accept-Charset |Typ charset, taki jak "UTF-8" |Wartość domyślna to UTF-8. |
| X-HTTP-Metoda |Metoda HTTP |Zezwala klientom lub zaporom, które nie obsługują metod HTTP, takich jak PUT lub DELETE, do korzystania z tych metod, tunelowanie za pośrednictwem wywołania GET. |
| Content-Type |Typ zawartości |Typ zawartości treści żądania w żądaniu PUT lub POST. |
| Identyfikator żądania klienta |Ciąg |Wartość zdefiniowana przez obiekt wywołujący, która identyfikuje określone żądanie. Jeśli jest określony, ta wartość zostanie uwzględniona w komunikacie odpowiedzi jako sposób mapowania żądania. <p><p>**Ważne**<p>Wartości powinny być ograniczone w 2096b (2K). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standardowe nagłówki odpowiedzi HTTP obsługiwane przez Media Services
Poniżej znajduje się zestaw nagłówków, które mogą zostać zwrócone do użytkownika w zależności od zasobu, którego dotyczy żądanie, oraz akcji, która ma zostać wykonana.

| Nagłówek | Typ | Wartość |
| --- | --- | --- |
| Identyfikator żądania |Ciąg |Unikatowy identyfikator bieżącej operacji i wygenerowanej usługi. |
| Identyfikator żądania klienta |Ciąg |Identyfikator określony przez obiekt wywołujący w oryginalnym żądaniu, jeśli jest obecny. |
| Date (Data) |Data 1123 |Data/godzina przetworzenia żądania. |
| Content-Type |Różnie |Typ zawartości treści odpowiedzi. |
| Content-Encoding |Różnie |Odpowiednio, gzip lub Wklęśnięcie. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standardowe zlecenia HTTP obsługiwane przez Media Services
Poniżej znajduje się kompletna lista zleceń HTTP, które mogą być używane podczas wykonywania żądań HTTP:

| Czasownik | Opis |
| --- | --- |
| GET |Zwraca bieżącą wartość obiektu. |
| POST |Tworzy obiekt na podstawie dostarczonych danych lub przesyła polecenie. |
| PUT |Zastępuje obiekt lub tworzy nazwany obiekt (jeśli ma zastosowanie). |
| DELETE |Usuwa obiekt. |
| POŁĄCZENIE |Aktualizuje istniejący obiekt o nazwanych zmianach właściwości. |
| HEAD |Zwraca metadane obiektu dla odpowiedzi GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Odnajdywanie i przeglądanie modelu jednostki Media Services
Aby Media Services jednostki były bardziej wykrywalne, można użyć $metadata operacji. Umożliwia pobranie wszystkich prawidłowych typów jednostek, właściwości jednostek, skojarzeń, funkcji, akcji i tak dalej. Przez dodanie $metadata operacji do końca punktu końcowego interfejsu API REST Media Services, można uzyskać dostęp do tej usługi odnajdywania.

 /API/$metadata.

Należy dołączyć "? API-Version = 2. x" na końcu identyfikatora URI, jeśli chcesz wyświetlić metadane w przeglądarce lub nie dołączać nagłówka x-MS-Version w żądaniu.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Uwierzytelnianie za pomocą Media Services REST przy użyciu Azure Active Directory

Uwierzytelnianie w interfejsie API REST odbywa się za pomocą Azure Active Directory (AAD).
Aby uzyskać szczegółowe informacje na temat pobierania wymaganych szczegółów uwierzytelniania dla konta Media Services z witryny Azure Portal, zobacz [dostęp do interfejsu API Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Aby uzyskać szczegółowe informacje na temat pisania kodu, który nawiązuje połączenie z interfejsem API REST przy użyciu uwierzytelniania usługi Azure AD, zobacz artykuł [używanie uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu api Azure Media Services w usłudze REST](media-services-rest-connect-with-aad.md)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak używać uwierzytelniania usługi Azure AD za pomocą interfejsu API REST Media Services, zobacz [używanie uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu api Azure Media Services](media-services-rest-connect-with-aad.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
