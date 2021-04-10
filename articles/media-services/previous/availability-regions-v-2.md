---
title: Azure Media Services regionalnej dostępności | Microsoft Docs
description: Ten artykuł zawiera omówienie funkcji Microsoft Azure Media Services i regionalnej dostępności usługi.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 125f2be205760618ab6ffa3175f719f5e91e8c05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012262"
---
# <a name="media-services-regional-availability"></a>Media Services dostępność regionalna

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/media-services-overview.md). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Usługa Microsoft Azure Media Services (AMS) umożliwia bezpieczne przekazywanie, przechowywanie, kodowanie i tworzenie pakietów zawartości wideo lub audio na potrzeby transmisji strumieniowej na żądanie i na żywo do różnych klientów (np. odbiorników TV, komputerów i urządzeń przenośnych).

AMS działa w wielu regionach na całym świecie, dzięki czemu można elastycznie wybierać miejsce kompilowania aplikacji. Ten artykuł zawiera omówienie funkcji Microsoft Azure Media Services i regionalnej dostępności usługi.

Aby uzyskać więcej informacji na temat całej infrastruktury globalnej platformy Azure, zobacz [Azure lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="ams-accounts"></a>Konta usługi AMS

Użyj [produktów platformy Azure według regionów,](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) aby określić, czy Media Services jest dostępna w określonym regionie.

## <a name="streaming-endpoints"></a>Punkty końcowe przesyłania strumieniowego

Klienci usługi Media Services mogą wybrać **Standardowy** punkt końcowy przesyłania strumieniowego lub punkt końcowy przesyłania strumieniowego **Premium**.

|Nazwa|Stan|Region (Region)
|---|---|---|
|Standardowa (Standard)|Ogólna dostępność|Wszystko|
|Premium|Ogólna dostępność|Wszystko|

## <a name="live-encoding"></a>Kodowanie na żywo

Dostępne we wszystkich regionach z wyjątkiem: Niemcy, Brazylia Południowa, Indie Zachodnie, Indie Południowe i Indie Środkowe.

## <a name="encoding-media-processors"></a>Kodowanie procesorów multimediów

Usługa AMS oferuje dwa kodery na żądanie: **Media Encoder Standard** i **Media Encoder Premium Workflow**. Aby uzyskać więcej informacji, zobacz temat [Przegląd i porównanie koderów multimediów na żądanie na platformie Azure ](media-services-encode-asset.md).

|Nazwa procesora multimediów|Stan|Regiony
|---|---|---|
|Usługa Media Encoder Standard|Ogólna dostępność|Wszystko|
|Przepływ pracy usługi Media Encoder w warstwie Premium|Ogólna dostępność|Wszystkie z wyjątkiem Chin|

## <a name="analytics-media-processors"></a>Procesory multimediów usługi analizy

Analiza multimediów to kolekcja składników mowy i obrazu, które ułatwiają organizacjom i przedsiębiorstwom pozyskiwanie przydatnych informacji z posiadanych plików wideo. Aby uzyskać więcej informacji, zobacz temat [Przegląd analiz usługi Azure Media Services](./legacy-components.md).

> [!NOTE]
> Niektóre procesory multimediów analitycznych zostaną wycofane. Aby uzyskać daty wycofania, zobacz temat [starsze składniki](legacy-components.md) .

|Nazwa procesora multimediów|Stan|Region (Region)
|---|---|---|
|Azure Media Face Detector|Wersja zapoznawcza|Wszystko|
|Azure Media Indexer|Ogólna dostępność|Wszystko|
|Azure Media Motion Detector|Wersja zapoznawcza|Wszystko|
|Azure Media OCR|Wersja zapoznawcza|Wszystko|
|Azure Media Redactor|Ogólna dostępność|Wszystko|
|Azure Media Video Thumbnails|Wersja zapoznawcza|Wszystko|

## <a name="protection"></a>Protection

Usługi Microsoft Azure Media Services umożliwiają zabezpieczenie multimediów od momentu wysłania danych z komputera w ramach procesów przechowywania, przetwarzania i dostarczania. Aby uzyskać więcej informacji, zobacz temat [Ochrona zawartości usługi AMS](media-services-content-protection-overview.md).

|Szyfrowanie|Stan|Regiony|
|---|---|---| 
|Storage|Ogólna dostępność|Wszystko|
|Klucze AES-128|Ogólna dostępność|Wszystko|
|FairPlay|Ogólna dostępność|Wszystko|
|PlayReady|Ogólna dostępność|Wszystko|
|Widevine|Ogólna dostępność|Wszystkie z wyjątkiem Niemiec, instytucji rządowych i Chin.

> [!NOTE]
> Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="reserved-units-rus"></a>Jednostki zarezerwowane (RU)

Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta.

Dostępne we wszystkich regionach.

## <a name="reserved-unit-ru-type"></a>Typ jednostki zarezerwowanej (RU)

Konto Media Services jest skojarzone z typem jednostki zarezerwowanej, który określa szybkość, z jaką zadania przetwarzania multimediów są wykonywane. Można wybrać jeden z następujących typów jednostek zarezerwowanych: S1, S2 lub S3.

|Nazwa typu jednostki zarezerwowanej|Stan|Regiony
|---|---|---|
|S1|Ogólna dostępność|Wszystko|
|S2|Ogólna dostępność|Wszystkie regiony z wyjątkiem Brazylii Południowej i Indii Zachodnich|
|S3|Ogólna dostępność|Wszystkie regiony z wyjątkiem Indii Zachodnich|

## <a name="next-steps"></a>Następne kroki

[Migrowanie do usługi Media Services w wersji 3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Wyraź opinię

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]