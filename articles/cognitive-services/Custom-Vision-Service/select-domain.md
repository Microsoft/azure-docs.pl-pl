---
title: Wybierz domenę dla Custom Vision projektu — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób wybierania domeny dla projektu w Custom Vision Service.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 8aba6f13957d37f843114572f001029baf41ded6
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889352"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Wybierz domenę dla projektu Custom Vision

Na karcie Ustawienia projektu Custom Vision możesz wybrać domenę dla projektu. Wybierz domenę znajdującą się najbliżej Twojego scenariusza. Jeśli uzyskujesz dostęp do Custom Vision za pomocą biblioteki klienta lub interfejsu API REST, należy określić identyfikator domeny podczas tworzenia projektu. Listę identyfikatorów domen można uzyskać przy użyciu okna [pobieranie domen](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)lub skorzystać z poniższej tabeli.

## <a name="image-classification"></a>Klasyfikacja obrazów

|Domena|Przeznaczenie|
|---|---|
|__Ogólne__| Optymalizacja pod kątem szerokiego zakresu zadań klasyfikacji obrazów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub jeśli nie masz pewności, którą domenę wybrać, wybierz jedną z głównych domen. #C1 `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__Ogólne [a1]__| Optymalizacja pod kątem lepszej dokładności dzięki porównywalnemu czasowi wnioskowania jako domeny ogólnej. Zalecane w przypadku większych zestawów danych lub trudniejszych scenariuszy użytkownika. Ta domena wymaga więcej czasu szkoleniowego. #C1 `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__Ogólne [a2]__| Zoptymalizowane pod kątem lepszej dokładności z szybszym czasem wnioskowania niż ogólne domeny [a1] i ogólne. Zalecane w przypadku większości zestawów danych. Ta domena wymaga mniej czasu szkolenia niż ogólne i ogólne domeny [a1]. #C1 `2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__Żywności__|Optymalizacja pod kątem zdjęć naczyń w postaci widocznej w menu restauracji. Jeśli chcesz sklasyfikować fotografie poszczególnych owoców lub warzyw, użyj domeny żywności. #C1 `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Punkty orientacyjne__|Optymalizacja pod kątem rozpoznawalnych terenów, zarówno naturalnych, jak i sztucznej. Ta domena działa najlepiej, gdy punkt orientacyjny jest jasno widoczny na zdjęciu. Ta domena działa nawet wtedy, gdy punkt orientacyjny jest nieco przesunięty przez osoby przed nim. #C1 `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Sprzedaż detaliczna__|Optymalizacja pod kątem obrazów znajdujących się w katalogu zakupów lub witrynie internetowej do kupowania. Jeśli potrzebujesz klasyfikacji o wysokiej precyzji między Dresses, Pants i koszulami, Użyj tej domeny. #C1 `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Domeny kompaktowe__| Optymalizacja pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach brzegowych.|


> [!NOTE]
> W szerokim zestawie scenariuszy można używać domen ogólnych [a1] i ogólnych [a2], które są zoptymalizowane pod kątem dokładności. Użyj modelu ogólnego [a2], aby uzyskać lepszą szybkość wnioskowania i krótszy czas uczenia. W przypadku większych zestawów danych warto użyć ogólnego [a1] do renderowania lepszej dokładności niż ogólne [a2], chociaż wymaga to więcej szkoleń i czasu wnioskowania. Ogólny model wymaga więcej czasu wnioskowania niż ogólne [a1] i ogólne [a2].

## <a name="object-detection"></a>Wykrywanie obiektów

|Domena|Przeznaczenie|
|---|---|
|__Ogólne__| Optymalizacja pod kątem szerokiego zakresu zadań wykrywania obiektów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę ogólne. #C1 `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__Ogólne [a1]__| Optymalizacja pod kątem lepszej dokładności dzięki porównywalnemu czasowi wnioskowania jako domeny ogólnej. Zalecane w celu uzyskania bardziej dokładnej lokalizacji regionów, większych zestawów danych lub trudniejszych scenariuszy użytkowników. Ta domena wymaga więcej czasu uczenia się, a wyniki nie są deterministyczne: Oczekiwano +-1% średniej dokładności (mapy) z tymi samymi danymi szkoleniowymi. #C1 `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Logo__|Optymalizacja pod kątem znajdowania logo marki w obrazach. #C1 `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Produkty na półkach__|Optymalizacja pod kątem wykrywania i klasyfikowania produktów na półkach. #C1 `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Domeny kompaktowe__| Optymalizacja pod kątem ograniczeń wykrywania obiektów w czasie rzeczywistym na urządzeniach brzegowych.|

## <a name="compact-domains"></a>Domeny kompaktowe

Modele generowane przez domeny kompaktowe mogą być eksportowane do lokalnego uruchamiania. W interfejsie API publicznej wersji zapoznawczej Custom Vision 3,4 można uzyskać listę platform przeznaczonych do eksportu dla domen kompaktowych, wywołując interfejs API getdomen.

Wydajność modelu różni się w zależności od wybranej domeny. W poniższej tabeli zgłaszamy rozmiar modelu i czas wnioskowania w procesorach Intel Desktop CPU i NVidia GPU \[ 1 \] . Te numery nie obejmują przetwarzania wstępnego i czasu dostosujesz.

|Zadanie|Domena|ID (Identyfikator)|Rozmiar modelu|Czas wnioskowania procesora CPU|Czas wnioskowania procesora GPU|
|---|---|---|---|---|---|
|Klasyfikacja|General (compact) (Ogólne (kompaktowe))|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 ms|5 ms|
|Klasyfikacja|Ogólne (kompaktowe) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 MS|5 ms|
|Wykrywanie obiektów|General (compact) (Ogólne (kompaktowe))|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 MS|5 ms|
|Wykrywanie obiektów|Ogólne (kompaktowe) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 MS|7 MS|

>[!NOTE]
>__Ogólna (kompaktowa)__ domena do wykrywania obiektów wymaga specjalnej logiki dostosujesz. Aby uzyskać szczegółowe informacje, zobacz przykładowy skrypt w wyeksportowanym pakiecie zip. Jeśli potrzebujesz modelu bez logiki dostosujesz, użyj __ogólnego (kompaktowego) [S1]__.

>[!IMPORTANT]
>Nie ma żadnej gwarancji, że eksportowane modele dają dokładnie ten sam wynik, co w przypadku interfejsu API przewidywania w chmurze. Niewielka różnica w działającej platformie lub implementacja przetwarzania wstępnego może spowodować większą różnicę w danych wyjściowych modelu. Szczegóły logiki przetwarzania wstępnego można znaleźć w [tym dokumencie](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU i NVIDIA Tesla M60
