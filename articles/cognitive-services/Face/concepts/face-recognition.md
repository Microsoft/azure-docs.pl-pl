---
title: Pojęcia dotyczące rozpoznawania
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono koncepcje weryfikacji, znajdowania podobnej, grupy i identyfikacji operacji rozpoznawania kroju oraz podstawowych struktur danych.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518810"
---
# <a name="face-recognition-concepts"></a>Pojęcia dotyczące rozpoznawania

W tym artykule wyjaśniono koncepcje weryfikacji, znajdowania podobnej, grupy i identyfikacji operacji rozpoznawania kroju oraz podstawowych struktur danych. W szerokim zakresie, rozpoznawanie zawiera opis pracy porównującej dwie różne powierzchnie, aby określić, czy są one podobne lub należą do tej samej osoby.

## <a name="recognition-related-data-structures"></a>Struktury danych dotyczące rozpoznawania

Operacje rozpoznawania używają głównie następujących struktur danych. Te obiekty są przechowywane w chmurze i mogą odwoływać się do nich ciągi identyfikatorów. Ciągi identyfikatorów są zawsze unikatowe w ramach subskrypcji. Pola nazw mogą być zduplikowane.

|Nazwa|Opis|
|:--|:--|
|DetectedFace| Ta reprezentacja pojedynczego kroju jest pobierana przez operację [wykrywania kroju](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) . Jego identyfikator wygasa po upływie 24 godzin od jego utworzenia.|
|PersistedFace| Gdy obiekty DetectedFace są dodawane do grupy, takiej jak FaceList lub osoba, stają się obiektami PersistedFace. Mogą być [pobierane](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) w dowolnym momencie i nie wygasają.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) lub [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Ta struktura danych jest asortymentową listą obiektów PersistedFace. FaceList ma unikatowy identyfikator, ciąg nazwy i opcjonalnie ciąg danych użytkownika.|
|[Osoba](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Ta struktura danych jest listą obiektów PersistedFace, które należą do tej samej osoby. Ma unikatowy identyfikator, ciąg nazwy i opcjonalnie ciąg danych użytkownika.|
|[Osoba](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) lub [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Ta struktura danych jest asortymentową listą obiektów osób. Ma unikatowy identyfikator, ciąg nazwy i opcjonalnie ciąg danych użytkownika. Aby można było użyć tej osoby, należy ją [przeszkolić](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) .|

## <a name="recognition-operations"></a>Operacje rozpoznawania

W tej sekcji szczegółowo opisano, jak cztery operacje rozpoznawania używają opisanych wcześniej struktur danych. Aby uzyskać obszerny opis każdej operacji rozpoznawania, zobacz [Omówienie](../Overview.md).

### <a name="verify"></a>Weryfikacja

Operacja [verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) przyjmuje identyfikator kroju z DetectedFace lub PersistedFace oraz inny identyfikator lub obiekt osoby i określa, czy należą do tej samej osoby. W przypadku przekazania obiektu osoby można opcjonalnie przekazać osobę, do której należy ta osoba, aby zwiększyć wydajność.

### <a name="find-similar"></a>Znajdź podobne

Operacja [Find podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) Pobiera identyfikator klasy z DetectedFace lub PersistedFace oraz FaceList lub tablicę innych identyfikatorów. Za pomocą FaceList zwraca mniejszy FaceList twarzy, które są podobne do danej powierzchni. Z tablicą identyfikatorów funkcji, podobnie zwraca mniejszą tablicę.

### <a name="group"></a>Group (Grupa)

Operacja [grupy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) pobiera tablicę identyfikatorów klasy z DetectedFace lub PersistedFace i zwraca te same identyfikatory pogrupowane w kilka mniejszych tablic. Każda tablica "Groups" zawiera identyfikatory czołowe, które wyglądają podobnie. Pojedyncza Tablica "messyGroup" zawiera identyfikatory czołowe, dla których nie znaleziono podobieństw.

### <a name="identify"></a>Ustalenia

Operacja [identyfikowania](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) przyjmuje jeden lub kilka identyfikatorów czołowych z DetectedFace lub PersistedFace, a także zwraca listę obiektów osób, do których może należeć każda z nich. Zwracane obiekty osób są opakowane jako obiekty kandydujące, które mają wartość ufności prognoz.

## <a name="input-data"></a>Dane wejściowe

Skorzystaj z poniższych wskazówek, aby upewnić się, że obrazy wejściowe zawierają najbardziej dokładne wyniki rozpoznawania:

* Obsługiwane formaty obrazu wejściowego to JPEG, PNG, GIF (pierwsza ramka), BMP.
* Rozmiar pliku obrazu nie może przekraczać 6 MB.
* Podczas tworzenia obiektów osób należy używać zdjęć, które mają różne rodzaje kątów i oświetlenia.
* Niektóre twarzy mogą nie zostać rozpoznane ze względu na wyzwania techniczne, takie jak:
  * Obrazy z ekstremalną oświetleniem, na przykład silnego oświetlenia.
  * Przeszkody blokujące jedną lub obie oczy.
  * Różnice w typach włosów lub włosie twarzy.
  * Zmiany w twarzy ze względu na wiek.
  * Ekstremalne wyrażenia twarzy.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już koncepcje rozpoznawania twarzy, napisz skrypt, który identyfikuje twarze w odniesieniu do przeszkolonej osoby.

* [Przewodnik Szybki Start dotyczący biblioteki klienta](../Quickstarts/client-libraries.md)