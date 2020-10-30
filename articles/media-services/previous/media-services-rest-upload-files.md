---
title: Przekazywanie plików na konto Azure Media Services przy użyciu usługi REST | Microsoft Docs
description: Dowiedz się, jak pobrać zawartość multimedialną do Media Services przez utworzenie i przekazanie zasobów przy użyciu interfejsu REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 49863bec4cbd367b6b309ef5a79e7287cb53ee5b
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042979"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Przekazywanie plików na konto usługi Media Services przy użyciu stylu REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Za pomocą usługi Media Services można przekazać pliki cyfrowe do elementu zawartości. Jednostka [zasobu](/rest/api/media/operations/asset) może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów (oraz metadane dotyczące tych plików).  Gdy pliki zostaną przekazane do elementu zawartości, zawartość jest bezpiecznie przechowywana w chmurze w celu dalszej przetwarzania i przesyłania strumieniowego. 

W tym samouczku dowiesz się, jak przekazać plik i inną skojarzoną z nim operację:

> [!div class="checklist"]
> * Skonfiguruj notkę dla wszystkich operacji przekazywania
> * Łączenie się z usługą Media Services 
> * Tworzenie zasad dostępu z uprawnieniami do zapisu
> * Tworzenie zasobu
> * Tworzenie lokalizatora SAS i tworzenie adresu URL przekazywania
> * Przekazywanie pliku do usługi BLOB Storage przy użyciu adresu URL przekazywania
> * Utwórz metadane w elemencie zawartości dla przekazanego pliku multimedialnego

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Utwórz konto Azure Media Services przy użyciu Azure Portal](media-services-portal-create-account.md).
- Zapoznaj się z artykułem [Omówienie uzyskiwania dostępu do interfejsu API Azure Media Services przy użyciu usługi AAD Authentication](media-services-use-aad-auth-to-access-ams-api.md) .
- Aby uzyskać więcej informacji, zobacz artykuł [używanie uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API Media Services przy użyciu artykułu REST](./media-services-rest-connect-with-aad.md) .
- Skonfiguruj **notkę** zgodnie z opisem w artykule [Konfigurowanie programu poster dla Media Services wywołań interfejsu API REST](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

W przypadku korzystania z interfejsu API REST Media Services są stosowane następujące zagadnienia:
 
* Podczas uzyskiwania dostępu do jednostek przy użyciu interfejsu API REST Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [konfigurowanie Media Services tworzenia interfejsu API REST](media-services-rest-how-to-use.md). <br/>Kolekcja ogłoszeń użyta w tym samouczku obejmuje ustawienie wszystkich niezbędnych nagłówków.
* Media Services używa wartości właściwości IAssetFile.Name podczas kompilowania adresów URL dla zawartości przesyłania strumieniowego (na przykład http://{AMSAccount}. Origin. MediaServices. Windows. NET/{GUID}/{IAssetFile. Name}/streamingParameters). Z tego powodu nie jest dozwolone kodowanie procentowo. Wartość właściwości **name** nie może zawierać żadnego z następujących [znaków:%-Encoding](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters), które są zastrzeżone.! * ' ();: @ &= + $,/?% # [] ". Ponadto dla rozszerzenia nazwy pliku może istnieć tylko jeden ".".
* Długość nazwy nie może przekraczać 260 znaków.
* Istnieje limit maksymalnego rozmiaru pliku do przetwarzania w usłudze Media Services. Zobacz [ten](media-services-quotas-and-limitations.md) artykuł, aby uzyskać szczegółowe informacje na temat ograniczeń rozmiarów plików.

## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Aby uzyskać instrukcje dotyczące sposobu konfigurowania programu Poster dla tego samouczka, zobacz [Konfigurowanie programu Poster](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

1. Dodaj wartości połączeń do środowiska. 

    Niektóre zmienne, które są częścią **MediaServices** [środowiska](postman-environment.md) MediaServices, muszą zostać ustawione ręcznie przed rozpoczęciem wykonywania operacji zdefiniowanych w [kolekcji](postman-collection.md).

    Aby uzyskać wartości dla pierwszych pięciu zmiennych, zobacz [dostęp do interfejsu API Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Zrzut ekranu pokazujący ikonę "koło zębate" wybraną w prawym górnym rogu i pięć pierwszych zmiennych wybranych z karty "środowiska zarządzania".](./media/media-services-rest-upload-files/postman-import-env.png)
2. Określ wartość dla zmiennej środowiskowej **MediaFileName** .

    Określ nazwę pliku nośnika, który ma zostać przekazany. W tym przykładzie będziemy przekazywać BigBuckBunny.mp4. 
3. Przejrzyj **AzureMediaServices.postman_environment.js** pliku. Zobaczysz, że prawie wszystkie operacje w kolekcji wykonują skrypt "test". Skrypty pobierają pewne wartości zwracane przez odpowiedź i ustawiają odpowiednie zmienne środowiskowe.

    Na przykład Pierwsza operacja pobiera token dostępu i ustawia go w zmiennej środowiskowej **AccessToken** , która jest używana we wszystkich innych operacjach.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. Po lewej stronie okna programu **Poster** kliknij pozycję **1. Pobierz token uwierzytelniania usługi AAD**  ->  **Pobierz token usługi Azure AD dla jednostki usług** .

    Część adresu URL jest wypełniona zmienną środowiskową **AzureADSTSEndpoint** (wcześniej w samouczku, ustawiasz wartości zmiennych środowiskowych, które obsługują kolekcję).

    ![Zrzut ekranu pokazujący wartość "1". Uzyskaj token uwierzytelniania D — Pobierz token usługi Azure A D dla jednostki usług "wybrane z okna" Poster "i wybierz przycisk" Wyślij ".](./media/media-services-rest-upload-files/postment-get-token.png)

5. Kliknij pozycję **Wyślij** .

    Można zobaczyć odpowiedź zawierającą "access_token". Skrypt "test" pobiera tę wartość i ustawia zmienną środowiskową **AccessToken** (zgodnie z powyższym opisem). Jeśli sprawdzisz zmienne środowiskowe, zobaczysz, że ta zmienna zawiera teraz wartość tokenu dostępu (token okaziciela), która jest używana w pozostałej części operacji. 

    Jeśli token zostanie wystawiony ponownie, należy wykonać krok "Pobierz token usługi Azure AD w celu uzyskania głównej nazwy". 

## <a name="create-an-access-policy-with-write-permission"></a>Tworzenie zasad dostępu z uprawnieniami do zapisu

### <a name="overview"></a>Omówienie 

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) artykuł.

Przed przekazaniem plików do usługi BLOB Storage Ustaw uprawnienia dostępu do zapisu w elemencie zawartości. W tym celu Opublikuj żądanie HTTP w zestawie jednostek AccessPolicies. Zdefiniuj wartość DurationInMinutes podczas tworzenia lub w odpowiedzi zostanie wyświetlony komunikat o błędzie wewnętrznego serwera 500. Aby uzyskać więcej informacji na temat AccessPolicies, zobacz [AccessPolicy](/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Tworzenie zasad dostępu

1. Wybierz pozycję **AccessPolicy**  ->  **Utwórz AccessPolicy do przekazania** .
2. Kliknij pozycję **Wyślij** .

    ![Zrzut ekranu pokazujący opcję "AccessPolicy-Create AccessPolicy for upload" wybraną z menu po lewej stronie i wybranego przycisku "Wyślij".](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skrypt "test" Pobiera identyfikator AccessPolicy i ustawia odpowiednią zmienną środowiskową.

## <a name="create-an-asset"></a>Tworzenie zasobu

### <a name="overview"></a>Omówienie

Element [zawartości](/rest/api/media/operations/asset) to kontener dla wielu typów lub zestawów obiektów w Media Services, w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów. W interfejsie API REST Tworzenie elementu zawartości wymaga wysłania żądania POST do Media Services i umieszczenia wszelkich informacji o właściwościach zasobu w treści żądania.

Jedna z właściwości, które można dodać podczas tworzenia elementu zawartości, to **Opcje** . Można określić jedną z następujących opcji szyfrowania: **Brak** (domyślnie nie jest używane szyfrowanie), **StorageEncrypted** (dla zawartości, która została wstępnie zaszyfrowana przy użyciu szyfrowania magazynu po stronie klienta), **CommonEncryptionProtected** lub **EnvelopeEncryptionProtected** . W przypadku zaszyfrowanego zasobu należy skonfigurować zasady dostarczania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md).

Jeśli zasób jest szyfrowany, należy utworzyć **ContentKey** i połączyć go z zasobem, zgodnie z opisem w następującym artykule: [jak utworzyć ContentKey](media-services-rest-create-contentkey.md). Po przekazaniu plików do zasobu należy zaktualizować właściwości szyfrowania w jednostce **AssetFile** o wartości, które zostały uzyskane podczas szyfrowania **zasobów** . Zrób to za pomocą żądania **scalania** http. 

W tym przykładzie tworzymy niezaszyfrowany element zawartości. 

### <a name="create-an-asset"></a>Tworzenie zasobu

1. Wybierz pozycję **zasoby**  ->  **Utwórz element zawartości** .
2. Kliknij pozycję **Wyślij** .

    ![Zrzut ekranu pokazujący "zasoby — tworzenie zasobu" wybrany z menu "kolekcje" i wybrany przycisk "Wyślij".](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skrypt "test" Pobiera identyfikator zasobu i ustawia odpowiednią zmienną środowiskową.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Tworzenie lokalizatora SAS i tworzenie adresu URL przekazywania

### <a name="overview"></a>Omówienie

Po ustawieniu AccessPolicy i lokalizatora rzeczywisty plik zostanie przekazany do kontenera Blob Storage platformy Azure przy użyciu interfejsów API REST usługi Azure Storage. Pliki muszą być przekazywane jako blokowe obiekty blob. Stronicowe obiekty blob nie są obsługiwane przez Azure Media Services.  

Aby uzyskać więcej informacji na temat pracy z obiektami BLOB usługi Azure Storage, zobacz [API REST usługa BLOB Service](/rest/api/storageservices/blob-service-rest-api).

Aby otrzymać rzeczywisty adres URL przekazywania, Utwórz lokalizator sygnatury dostępu współdzielonego (pokazany poniżej). Lokalizatory definiują czas rozpoczęcia i typ punktu końcowego połączenia dla klientów, którzy chcą uzyskać dostęp do plików w elemencie zawartości. Można utworzyć wiele jednostek lokalizatora dla danej AccessPolicy i pary zasobów, aby obsługiwać różne żądania klientów i ich potrzeby. Każdy z tych lokalizatorów używa wartości StartTime oraz wartości DurationInMinutes AccessPolicy, aby określić długość czasu, przez jaki można użyć adresu URL. Aby uzyskać więcej informacji, zobacz [lokalizator](/rest/api/media/operations/locator).

Adres URL sygnatury dostępu współdzielonego ma następujący format:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>Zagadnienia do rozważenia

Zagadnienia do rozważenia:

* W tym samym czasie nie można mieć więcej niż pięciu unikatowych lokalizatorów skojarzonych z danym elementem zawartości. Aby uzyskać więcej informacji, zobacz lokalizator.
* Jeśli zachodzi potrzeba natychmiastowego przekazania plików, należy ustawić wartość StartTime na pięć minut przed bieżącą godziną. Wynika to z faktu, że nastąpi przechylenie zegara między komputerem klienckim a Media Services. Ponadto wartość StartTime musi mieć następujący format: RRRR-MM-DDTgg: mm: SSS (na przykład "2014-05-23T17:53:50Z").    
* Po utworzeniu lokalizatora do użycia może wystąpić 30-40 sekund.

### <a name="create-a-sas-locator"></a>Tworzenie lokalizatora SAS

1. Wybierz pozycję **lokalizator**  ->  **Utwórz lokalizator sygnatury dostępu współdzielonego** .
2. Kliknij pozycję **Wyślij** .

    Skrypt "test" tworzy "adres URL przekazywania" na podstawie określonej nazwy pliku nośnika oraz informacji o lokalizatorze SAS i ustawia odpowiednią zmienną środowiskową.

    ![Zrzut ekranu, na którym jest zaznaczona wartość "lokalizator-Create-A S Locator" z menu "kolekcje" i wybrano przycisk "Wyślij".](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Przekazywanie pliku do usługi BLOB Storage przy użyciu adresu URL przekazywania

### <a name="overview"></a>Omówienie

Teraz, gdy masz adres URL przekazywania, musisz napisać kod przy użyciu interfejsów API usługi Azure Blob bezpośrednio, aby przekazać plik do kontenera SAS. Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Korzystanie z interfejsu API REST usługi Azure Storage](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Umieść obiekt BLOB](/rest/api/storageservices/put-blob)
- [Przekazywanie obiektów BLOB do magazynu obiektów BLOB](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Przekaż plik za pomocą programu Poster

Załóżmy na przykład, że wyślemy mały plik MP4 przy użyciu programu. Rozmiar pliku binarnego można przekazać za pomocą programu Poster.

Żądanie przekazywania nie jest częścią kolekcji **AzureMedia** . 

Utwórz i skonfiguruj nowe żądanie:
1. Naciśnij klawisz **+** , aby utworzyć nową kartę żądania.
2. Wybierz pozycję **Umieść** operację i wklej **{{UploadURL}}** w adresie URL.
2. Pozostaw kartę **autoryzacja** jako is (nie ustawiaj jej na **token okaziciela** ).
3. Na karcie **nagłówki** Określ: **klucz** : "x-MS-BLOB-Type" i **Value** : "BlockBlob".
2. Na karcie **treść** kliknij pozycję dane **binarne** .
4. Wybierz plik o nazwie określonej w zmiennej środowiskowej **MediaFileName** .
5. Kliknij pozycję **Wyślij** .

    ![Zrzut ekranu przedstawiający wybraną kartę (UploadU R L).](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Tworzenie metadanych w elemencie zawartości

Po przekazaniu pliku należy utworzyć metadane w elemencie zawartości dla pliku nośnika, który został przekazany do magazynu obiektów BLOB skojarzonego z Twoim zasobem.

1. Wybierz pozycję **AssetFiles**  ->  **CreateFileInfos** .
2. Kliknij pozycję **Wyślij** .

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-file-info.png)

Plik powinien zostać przekazany i jego zestaw metadanych.

## <a name="validate"></a>Walidacja

Aby sprawdzić, czy plik został pomyślnie przekazany, można wykonać zapytanie dotyczące [AssetFile](/rest/api/media/operations/assetfile) i porównać **ContentFileSize** (lub inne szczegóły) z oczekiwaniami, które mają być widoczne w nowym elemencie zawartości. 

Na przykład następująca operacja **Get** umożliwia przeniesienie danych pliku do pliku zasobów (w przypadku pliku BigBuckBunny.mp4). Zapytanie korzysta ze zdefiniowanych wcześniej [zmiennych środowiskowych](postman-environment.md) .

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

Odpowiedź będzie zawierać rozmiar, nazwę i inne informacje.

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>Następne kroki

Teraz możesz zakodować przekazane elementy zawartości. Więcej informacji znajduje się na stronie [Kodowanie elementów zawartości](media-services-portal-encode.md).

Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania na podstawie pliku trafiającego do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz [ten przykład](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).
