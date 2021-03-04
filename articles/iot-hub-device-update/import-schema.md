---
title: Importowanie aktualizacji do aktualizacji urządzeń IoT Hub-Schema i innych informacji | Microsoft Docs
description: Schemat i inne powiązane informacje (w tym obiekty), które są używane podczas importowania aktualizacji do usługi IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054786"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Importowanie aktualizacji do aktualizacji urządzeń dla IoT Hub-Schema i innych informacji
Aby zaimportować aktualizację do usługi IoT Hub, należy zapoznać się z [pojęciami](import-concepts.md) i pierwszym [przewodnikiem](import-update.md) . Jeśli interesujesz się szczegółowymi informacjami o schemacie używanym podczas konstruowania manifestu importu, a także informacjami o powiązanych obiektach, zobacz poniżej.

## <a name="import-manifest-schema"></a>Importuj Schemat manifestu

| Nazwa | Typ | Opis | Ograniczenia |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId` Stream | Aktualizowanie tożsamości. |
| Typ aktualizacji | ciąg | Typ aktualizacji: <br/><br/> * Określ `microsoft/apt:1` , kiedy ma być wykonywana Aktualizacja oparta na pakiecie przy użyciu agenta odwołań.<br/> * Określ `microsoft/swupdate:1` podczas przeprowadzania aktualizacji opartej na obrazie przy użyciu agenta odwołań.<br/> * Określ, kiedy ma być `microsoft/simulator:1` używany przykładowy symulator agentów.<br/> * Określ typ niestandardowy w przypadku tworzenia niestandardowego agenta. | Format: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Łącznie z 32 znaków |
| InstalledCriteria | ciąg | Ciąg interpretowany przez agenta w celu ustalenia, czy aktualizacja została zastosowana pomyślnie:  <br/> * Określ **wartość** SWVersion dla typu aktualizacji `microsoft/swupdate:1` .<br/> * Określ `{name}-{version}` dla typu aktualizacji `microsoft/apt:1` , która nazwa i wersja są uzyskiwane z pliku apt.<br/> * Określ skrót pliku aktualizacji dla typu aktualizacji `microsoft/simulator:1` .<br/> * Określ niestandardowy ciąg w przypadku tworzenia niestandardowego agenta.<br/> | Maksymalnie 64 znaków |
| Zgodność | Tablica `CompatibilityInfo` obiektów | Informacje o zgodności urządzenia zgodne z tą aktualizacją. | Maksymalnie 10 elementów |
| CreatedDateTime | Data/godzina | Data i godzina utworzenia aktualizacji. | Rozdzielany format daty i godziny ISO 8601, w formacie UTC |
| ManifestVersion | ciąg | Importuj wersję schematu manifestu. Określ `2.0` , która będzie zgodna z `urn:azureiot:AzureDeviceUpdateCore:1` interfejsem i `urn:azureiot:AzureDeviceUpdateCore:4` interfejsem. | Musi być `2.0` |
| Pliki | Tablica `File` obiektów | Aktualizowanie plików ładunku | Maksymalnie 5 plików |

## <a name="updateid-object"></a>Obiekt UpdateId

| Nazwa | Typ | Opis | Ograniczenia |
| --------- | --------- | --------- | --------- |
| Dostawca | ciąg | Część dostawcy aktualizacji. | 1-64 znaków, alfanumeryczne, kropki i kreski. |
| Nazwa | ciąg | Nazwa części tożsamości aktualizacji. | 1-64 znaków, alfanumeryczne, kropki i kreski. |
| Wersja | Wersja | Część wersji aktualizacji tożsamości. | od 2 do 4 części, numer wersji oddzielonej kropką z przedziału od 0 do 2147483647. Zera wiodące zostaną usunięte. |

## <a name="file-object"></a>Obiekt pliku

| Nazwa | Typ | Opis | Ograniczenia |
| --------- | --------- | --------- | --------- |
| Nazwa pliku | ciąg | Nazwa pliku | Musi być unikatowa w ramach aktualizacji |
| SizeInBytes | Int64 | Rozmiar pliku w bajtach. | Maksymalnie 800 MB na pojedynczy plik lub 800 MB zbiorczo na aktualizację |
| Skróty | `Hashes` Stream | Obiekt JSON zawierający skróty dla pliku |

## <a name="compatibilityinfo-object"></a>Obiekt CompatibilityInfo

| Nazwa | Typ | Opis | Ograniczenia |
| --- | --- | --- | --- |
| DeviceManufacturer | ciąg | Producent urządzenia, z którym jest zgodna aktualizacja. | 1-64 znaków, alfanumeryczne, kropki i kreski. |
| DeviceModel | ciąg | Model urządzenia, z którym jest zgodna aktualizacja. | 1-64 znaków, alfanumeryczne, kropki i kreski. |

## <a name="hashes-object"></a>Hash — obiekt

| Nazwa | Wymagany | Typ | Opis |
| --------- | --------- | --------- | --------- |
| SHA256 | True | ciąg | Skrót z kodowaniem Base64 pliku przy użyciu algorytmu SHA-256. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zaimportowaniu koncepcji](./import-concepts.md).

Jeśli wszystko jest gotowe, wypróbuj [Przewodnik importowania How-To](./import-update.md), który przeprowadzi Cię przez krok proces importowania.
