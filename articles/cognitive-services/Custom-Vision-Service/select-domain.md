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
ms.openlocfilehash: 87b9e4a3ca7151b3666928b00add175eddeea050
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409386"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Wybierz domenę dla projektu Custom Vision

W bloku ustawienia dla projektu Custom Vision możesz wybrać domenę dla projektu. Wybierz domenę znajdującą się najbliżej Twojego scenariusza.

## <a name="image-classification"></a>Klasyfikacja obrazów

|Domena|Przeznaczenie|
|---|---|
|__Ogólny__| Optymalizacja pod kątem szerokiego zakresu zadań klasyfikacji obrazów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę generyczną.|
|__Żywności__|Optymalizacja pod kątem zdjęć naczyń w postaci widocznej w menu restauracji. Jeśli chcesz sklasyfikować fotografie poszczególnych owoców lub warzyw, użyj domeny żywności.|
|__Punkty orientacyjne__|Optymalizacja pod kątem rozpoznawalnych terenów, zarówno naturalnych, jak i sztucznej. Ta domena działa najlepiej, gdy punkt orientacyjny jest jasno widoczny na zdjęciu. Ta domena działa nawet wtedy, gdy punkt orientacyjny jest nieco przesunięty przez osoby przed nim.|
|__Sprzedaż detaliczna__|Optymalizacja pod kątem obrazów znajdujących się w katalogu zakupów lub witrynie internetowej do kupowania. Jeśli potrzebujesz wysokiej dokładności klasyfikowania między Dresses, Pants i koszulami, Użyj tej domeny.|
|__Domeny kompaktowe__| Optymalizacja pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach brzegowych.|

## <a name="object-detection"></a>Wykrywanie obiektów

|Domena|Przeznaczenie|
|---|---|
|__Ogólne__| Optymalizacja pod kątem szerokiego zakresu zadań wykrywania obiektów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę generyczną.|
|__Logo__|Optymalizacja pod kątem znajdowania logo marki w obrazach.|
|__Produkty na półkach__|Optymalizacja pod kątem wykrywania i klasyfikowania produktów na półkach.|
|__Domeny kompaktowe__| Optymalizacja pod kątem ograniczeń wykrywania obiektów w czasie rzeczywistym na urządzeniach brzegowych.|

## <a name="compact-domains"></a>Domeny kompaktowe

Modele generowane przez domeny kompaktowe mogą być eksportowane do lokalnego uruchamiania. W interfejsie API publicznej wersji zapoznawczej Custom Vision 3,4 można uzyskać listę platform przeznaczonych do eksportu dla domen kompaktowych, wywołując interfejs API getdomen.

Wydajność modelu różni się w zależności od wybranej domeny. W poniższej tabeli zgłaszamy rozmiar modelu i czas wnioskowania w procesorach Intel Desktop CPU i NVidia GPU \[ 1 \] . Te numery nie obejmują przetwarzania wstępnego i czasu dostosujesz.

|Zadanie|Domena|Rozmiar modelu|Czas wnioskowania procesora CPU|Czas wnioskowania procesora GPU|
|---|---|---|---|---|
|Klasyfikacja|General (compact) (Ogólne (kompaktowe))|5 MB|13 MS|5 ms|
|Wykrywanie obiektów|General (compact) (Ogólne (kompaktowe))|45 MB|35 MS|5 ms|
|Wykrywanie obiektów|Ogólne (kompaktowe) [S1]|14 MB|27 MS|7 MS|

>[!NOTE]
>__Ogólna (kompaktowa)__ domena do wykrywania obiektów wymaga specjalnej logiki dostosujesz. Aby uzyskać szczegółowe informacje, zobacz przykładowy skrypt w wyeksportowanym pakiecie zip. Jeśli potrzebujesz modelu bez logiki dostosujesz, użyj __ogólnego (kompaktowego) [S1]__.

>[!IMPORTANT]
>Nie ma żadnej gwarancji, że eksportowane modele dają dokładnie ten sam wynik, co w przypadku interfejsu API przewidywania w chmurze. Niewielka różnica w działającej platformie lub implementacja przetwarzania wstępnego może spowodować większą różnicę w danych wyjściowych modelu. Szczegóły logiki przetwarzania wstępnego można znaleźć w [tym dokumencie](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU i NVIDIA Tesla M60
