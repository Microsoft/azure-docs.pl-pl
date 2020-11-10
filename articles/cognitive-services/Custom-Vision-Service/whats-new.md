---
title: Co nowego w Custom Vision?
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 10/23/2020
ms.author: pafarley
ms.openlocfilehash: 030b09dae9db11fb14defecde3d14e949b9e6748
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412735"
---
# <a name="whats-new-in-custom-vision"></a>Co nowego w Custom Vision

Dowiedz się, co nowego w usłudze. Te elementy mogą być informacjami o wersji, klipami wideo, wpisami w blogu i innymi rodzajami informacji. Oznacz Tę stronę zakładką, aby zapewnić aktualność usługi.


## <a name="october-2020"></a>Październik 2020 r. 

### <a name="custom-base-model"></a>Niestandardowy model podstawowy

- Niektóre aplikacje mają dużą ilość wspólnych danych szkoleniowych, ale wymagają dokładnego dostosowania ich modeli. skutkuje to lepszą wydajnością obrazów z różnych źródeł z niewielkimi różnicami. W takim przypadku można nauczyć pierwszy model w zwykły sposób z dużą ilością danych szkoleniowych. Następnie Wywołaj **TrainProject** w publicznej wersji zapoznawczej interfejsu API 3,4 z _CustomBaseModelInfo_ w treści żądania, aby użyć modelu przeszkolonego pierwszego etapu jako modelu podstawowego dla projektów podrzędnych. Jeśli projekt źródłowy i docelowy projekt podrzędny mają podobne cechy obrazu, można oczekiwać lepszej wydajności. 

### <a name="new-domain-information"></a>Informacje o nowej domenie

- Informacje o domenie zwrócone z **getdomen** w Custom Vision 3,4 publicznej wersji zapoznawczej obejmują teraz obsługiwane platformy możliwe do eksportowania, Krótki opis architektury modelu oraz rozmiar modelu dla kompaktowych domen.

### <a name="training-divergence-feedback"></a>Opinie dotyczące rozbieżności szkoleniowej

- Interfejs API publicznej wersji zapoznawczej Custom Vision Custom Vision 3,4 teraz zwraca **TrainingErrorDetails** z wywołania **getiteracji** . W przypadku zakończonych niepowodzeniem iteracji pokazuje to, czy przyczyną błędu była rozbieżność szkoleniowa, którą można rozwiązać przy użyciu więcej i większej jakości danych szkoleniowych.

## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

* Custom Vision obsługuje kontrolę dostępu opartą na rolach (Azure RBAC) na platformie Azure, system autoryzacji służący do zarządzania indywidualnym dostępem do zasobów platformy Azure. Aby dowiedzieć się, jak zarządzać dostępem do projektów Custom Vision, zobacz [Kontrola dostępu oparta na rolach na platformie Azure](./role-based-access-control.md).

### <a name="subset-training"></a>Szkolenie dotyczące podzbioru

* Podczas uczenia projektu wykrywania obiektów można opcjonalnie nauczyć się tylko podzestawu zastosowanych tagów. Możesz to zrobić, jeśli jeszcze nie zastosowano wystarczającej liczby niektórych tagów, ale masz wystarczającą ilość innych. Aby dowiedzieć się więcej, postępuj zgodnie z [biblioteką klienta szybki start](./quickstarts/object-detection.md) dla języka C# lub Python.

### <a name="azure-storage-notifications"></a>Powiadomienia usługi Azure Storage

* Projekt Custom Vision można zintegrować z kolejką usługi Azure Blob Storage w celu uzyskania powiadomień wypychanych dotyczących działania szkolenia i eksportowania projektu oraz kopii zapasowych opublikowanych modeli. Ta funkcja jest przydatna, aby uniknąć ciągłego sondowania usługi pod kątem wyników, gdy długotrwałe operacje są uruchomione. Zamiast tego można zintegrować powiadomienia kolejki magazynu z przepływem pracy. Zobacz przewodnik po [integracji magazynu](./storage-integration.md) , aby dowiedzieć się więcej.

### <a name="copy-and-move-projects"></a>Kopiowanie i przenoszenie projektów

* Teraz można kopiować projekty z jednego konta Custom Vision do innych. Możesz chcieć przenieść projekt z programowania do środowiska produkcyjnego lub utworzyć kopię zapasową projektu na koncie w innym regionie świadczenia usługi Azure, aby zwiększyć bezpieczeństwo danych. Aby dowiedzieć się więcej, zobacz Przewodnik [kopiowania i przenoszenia projektów](./copy-move-projects.md) .

## <a name="september-2019"></a>Wrzesień 2019

### <a name="suggested-tags"></a>Sugerowane Tagi

* Inteligentne narzędzie Labeler w [witrynie sieci web Custom Vision](https://www.customvision.ai/) generuje sugerowane znaczniki dla obrazów szkoleniowych. Dzięki temu można szybciej oznaczyć dużą liczbę obrazów podczas uczenia modelu Custom Vision. Aby uzyskać instrukcje dotyczące korzystania z tej funkcji, zobacz [sugerowane Tagi](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>Aktualizacje usługi poznawczej

[Anonse aktualizacji platformy Azure dla Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)