---
title: Aktualizacja dla IoT Hub manifestu aktualizacji | Microsoft Docs
description: Dowiedz się, w jaki sposób właściwości są wysyłane z usługi aktualizacji urządzenia do urządzenia podczas aktualizacji
author: andbrown
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ea438a4a8db8dce9cf2acb0b5b3ff4e250c3cf39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663283"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>Aktualizacja dla IoT Hub manifestu aktualizacji

## <a name="overview"></a>Omówienie

Aktualizacja urządzenia dla IoT Hub używa _manifestu aktualizacji_ do komunikowania się z akcjami, a także metadanych obsługujących te działania za pośrednictwem właściwości interfejsu [AzureDeviceUpdateCore. OrchestratorMetadata: 4](./device-update-plug-and-play.md).
W tym dokumencie opisano podstawowe informacje o tym `updateManifest` , jak właściwość w `AzureDeviceUpdateCore.OrchestratorMetadata:4` interfejsie jest używana przez agenta aktualizacji urządzenia. `AzureDeviceUpdateCore.OrchestratorMetadata:4`Właściwości interfejsu są wysyłane z aktualizacji urządzenia do usługi IoT Hub do agenta aktualizacji urządzenia. `updateManifest`Jest serializowanym obiektem JSON, który jest analizowany przez agenta aktualizacji urządzenia.

### <a name="an-example-update-manifest"></a>Przykładowy manifest aktualizacji

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

Manifest aktualizacji ma na celu opisanie zawartości aktualizacji, a mianowicie jej tożsamości, typu, zainstalowanych kryteriów i metadanych pliku aktualizacji. Ponadto manifest aktualizacji jest podpisany kryptograficznie, aby umożliwić agentowi aktualizacji urządzenia zweryfikowanie jego autentyczności. Zapoznaj się z dokumentacją dotyczącą [zabezpieczeń aktualizacji urządzeń](./device-update-security.md) , aby uzyskać więcej informacji na temat sposobu używania manifestu aktualizacji do bezpiecznego importowania zawartości.

## <a name="import-manifest-vs-update-manifest"></a>Importuj manifest manifestu aktualizacji

Ważne jest zrozumienie różnic między manifestem importu i pojęciami dotyczącymi manifestu aktualizacji w ramach aktualizacji urządzenia dla IoT Hub. 
* [Manifest importu](./import-concepts.md) jest tworzony przez utworzenie odpowiedniej aktualizacji. Opisano w nim zawartość aktualizacji, która zostanie zaimportowana do aktualizacji urządzenia dla IoT Hub. 
* Manifest aktualizacji jest generowany automatycznie przez aktualizację urządzenia dla usługi IoT Hub przy użyciu niektórych właściwości, które zostały zdefiniowane w manifeście importu. Jest on używany do przekazywania istotnych informacji do agenta aktualizacji urządzeń podczas procesu aktualizacji. 

Każdy typ manifestu ma własny schemat i wersję schematu.

## <a name="update-manifest-properties"></a>Aktualizowanie właściwości manifestu

Definicje wysokiego poziomu właściwości manifestu aktualizacji można znaleźć w definicjach interfejsów znalezionych w [tym miejscu](./device-update-plug-and-play.md). Aby zapewnić dokładniejszy kontekst, przyjrzyjmy się bliżej właściwości i sposobach ich użycia w systemie.

### <a name="updateid"></a>updateId

Zawiera `provider` , `name` i `version` , która reprezentuje dokładną aktualizację dla IoT Hub tożsamości aktualizacji używaną do określenia zgodnych urządzeń dla aktualizacji.

### <a name="updatetype"></a>Typ aktualizacji

Reprezentuje typ aktualizacji, która jest obsługiwana przez określony typ procedury obsługi aktualizacji. Jest ona zgodna z formą `microsoft/swupdate:1` dotyczącą aktualizacji opartej na obrazie i `microsoft/apt:1` aktualizacji opartej na pakiecie (patrz `Update Handler Types` sekcja poniżej).

### <a name="installedcriteria"></a>installedCriteria

Ciąg zawierający informacje wymagane przez program obsługi aktualizacji agenta aktualizacji urządzeń w celu ustalenia, czy aktualizacja została zainstalowana na urządzeniu. `Update Handler Types`Sekcja dokumentuje format programu `installedCriteria` dla każdego typu aktualizacji obsługiwanego przez aktualizację urządzenia dla IoT Hub.

### <a name="files"></a>files

Informuje agenta aktualizacji urządzeń, które pliki do pobrania, oraz skrót, który zostanie użyty do sprawdzenia, czy pliki zostały prawidłowo pobrane.
Poniżej przedstawiono bardziej szczegółowy opis `files` zawartości właściwości:

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

Poza zakresem `updateManifest` jest `fileUrls` Tablica obiektu JSON.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

Zarówno `FILE_ID_STRING` , w `fileUrls` , jak i `files` są takie same (na przykład "0000" w `files` adresie URL w "0000" w ciągu `fileUrls` ).

### <a name="manifestversion"></a>manifestVersion

Ciąg, który reprezentuje wersję schematu.

## <a name="update-handler-types"></a>Aktualizowanie typów programów obsługi

|Update — Metoda|Typ procedury obsługi aktualizacji|Typ aktualizacji|Zainstalowane kryteria|Oczekiwane pliki do opublikowania|
|-------------|-------------------|----------|-----------------|--------------|
|Na podstawie obrazu|SWUpdate|"Microsoft/SWUpdate: wersja"|Obraz referencyjny zapisuje wskazówkę dotyczącą jego wersji w pliku/etc/adu-Version.  |plik. SWU, który zawiera obraz SWUpdate|
|Oparte na pakiecie|APT|"Microsoft/APT: wersja"|`<name>` + "-" + `<version>` (zdefiniowane właściwości w pliku manifestu apt|`<APT Update Manifest>`. JSON, który zawiera konfigurację APT i listę pakietów|

