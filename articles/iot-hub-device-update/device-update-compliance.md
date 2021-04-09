---
title: Informacje o aktualizacji urządzenia dla usługi Azure IoT Hub zgodności | Microsoft Docs
description: Dowiedz się, jak aktualizacja urządzenia dla usługi Azure IoT Hub mierzenia zgodności aktualizacji urządzeń.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663416"
---
# <a name="device-update-compliance"></a>Update Compliance urządzenia

W obszarze aktualizacja urządzenia dla IoT Hub jest określana liczba urządzeń, na których zainstalowano najwyższą aktualizację zgodną z wersją. Urządzenie jest zgodne, jeśli zainstalowano dla niego najwyższą dostępną aktualizację. 

Rozważmy na przykład wystąpienie aktualizacji urządzenia z następującymi aktualizacjami:

|Nazwa aktualizacji|Aktualizuj wersję|Zgodny model urządzenia|
|-----------|--------------|-----------------------|
|Update1    |1.0    |Model1|
|Update2    |1.0    |Model2|
|Update3    |2.0    |Model1|

Załóżmy, że utworzono następujące wdrożenia:

|Nazwa wdrożenia    |Nazwa aktualizacji    |Grupa docelowa|
|-----------|--------------|-------------------|
|Deployment1    |Update1    |Grupa1|
|Deployment2    |Update2    |Group2|
|Deployment3    |Update3    |Grupa3|

Teraz Rozważ następujące urządzenia wraz z ich członkostwem w grupie i zainstalowanymi wersjami:

|DeviceId   |Model urządzenia   |Zainstalowana wersja aktualizacji|Group (Grupa) |Zgodność|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Grupa1 |Dostępne są nowe aktualizacje</span>|
|Device2    |Model1 |2.0    |Grupa3 |W przypadku najnowszej aktualizacji|
|Device3    |Model2 |1.0    |Group2 |W przypadku najnowszej aktualizacji|
|Device4    |Model1 |1.0    |Grupa3 |Aktualizacja w toku|

Device1 i Device4 nie są zgodne, ponieważ mają zainstalowane wersje 1,0, nawet jeśli w wystąpieniu aktualizacji urządzenia jest dostępna nowsza wersja, Update3 zgodna z modelem. Device2 i Device3 są zgodne ze względu na to, że są one zgodne z najwyższymi aktualizacjami wersji dla ich zainstalowanych modeli.

Zgodność nie jest uwzględniana, czy aktualizacja została wdrożona w grupie urządzenia, czy nie; sprawdza on wszystkie aktualizacje opublikowane w ramach aktualizacji urządzenia. Tak więc w powyższym przykładzie, mimo że program Device1 zainstalował wdrożoną aktualizację, jest uznawany za niezgodny. Device1 będzie nadal uznawany za niezgodny do czasu, aż pomyślnie zainstaluje Update3. Stan zgodności może pomóc w ustaleniu, czy potrzebne są nowe wdrożenia. 

Jak pokazano powyżej, istnieją trzy stany zgodności w ramach aktualizacji urządzenia dla IoT Hub:

*   **W przypadku najnowszej aktualizacji** — na urządzeniu zainstalowano najnowszą aktualizację zgodną z wersją opublikowaną w ramach aktualizacji urządzenia.
*   **Aktualizacja w toku** — aktywne wdrożenie jest w trakcie dostarczania na urządzeniu najnowszej wersji aktualizacji zgodnej z wersją.
*   **Dostępne są nowe aktualizacje** — urządzenie nie zainstalowało jeszcze najnowszej wersji aktualizacji zgodnej z wersją i nie należy do aktywnego wdrożenia dla tej aktualizacji.
