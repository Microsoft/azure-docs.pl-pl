---
title: Modele niestandardowe — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje dotyczące niestandardowych modeli interfejsu API rozpoznawania formularzy — użycie i limity.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 9d339d02310fddc3e5fd463f7d37e253614dba17
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067592"
---
# <a name="form-recognizer-custom-models"></a>Niestandardowe modele aparatu rozpoznawania formularzy

Aparat rozpoznawania formularzy używa zaawansowanej technologii uczenia maszynowego do analizowania i wyodrębniania danych z formularzy i dokumentów. Model aparatu rozpoznawania formularzy jest reprezentacją wyodrębnionych danych, które są używane jako odniesienia do analizowania określonej zawartości. Istnieją dwa typy modeli aparatu rozpoznawania formularzy:

* **Modele niestandardowe**. Modele niestandardowe aparatu rozpoznawania formularzy przedstawiają wyodrębnione dane z _formularzy_ specyficznych dla Twojej firmy. Modele niestandardowe muszą być przeszkolone, aby analizować dane odrębnych formularzy.

* **Wstępnie utworzone modele**. Aparat rozpoznawania formularzy obecnie obsługuje wstępnie utworzone modele dla _paragonów, kart służbowych, kart identyfikacyjnych_ i _faktur_. Wstępnie utworzone modele wykrywają i wyodrębniają informacje z obrazów dokumentów i zwracają wyodrębnione dane w strukturalnym danych wyjściowych JSON.

## <a name="what-does-a-custom-model-do"></a>Do czego służy model niestandardowy?

Za pomocą aparatu rozpoznawania formularzy można szkolić model, który będzie wyodrębniał informacje z formularzy, które są istotne dla przypadku użycia. Aby rozpocząć, wystarczy pięć przykładów tego samego typu formularza. Model niestandardowy może być szkolony z lub bez etykiet zestawów danych.

## <a name="create-use-and-manage-your-custom-model"></a>Tworzenie i Używanie modelu niestandardowego oraz zarządzanie nim

Na wysokim poziomie czynności do kompilowania, uczenia i korzystania z modelu niestandardowego są następujące:

> [!div class="nextstepaction"]
>[&#120783;. Złóż zestaw danych szkoleniowych](build-training-data-set.md#custom-model-input-requirements)

Tworzenie modelu niestandardowego zaczyna się od ustanowienia zestawu danych szkoleniowych. Wymagana jest co najmniej pięć ukończonych formularzy tego samego typu dla przykładowego zestawu danych. Mogą one mieć różne typy plików i zawierać zarówno tekst, jak i pismo ręczne. Formularze muszą być tego samego typu dokumentu i spełniać [wymagania wejściowe](build-training-data-set.md#custom-model-input-requirements) dla aparatu rozpoznawania formularzy.  

> [!div class="nextstepaction"]
> [&#120784;. Przekaż zestaw danych szkoleniowych](build-training-data-set.md#upload-your-training-data)

Musisz przekazać dane szkoleniowe do kontenera usługi Azure Blob Storage. Jeśli nie wiesz, jak utworzyć konto usługi Azure Storage za pomocą kontenera, *Zobacz* [Azure Storage — Szybki Start, aby uzyskać Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). Skorzystaj z warstwy cenowej bezpłatna (F0) w celu wypróbowania usługi i przeprowadź uaktualnienie później do warstwy płatnej dla środowiska produkcyjnego.  

> [!div class="nextstepaction"]
>[&#120785;. Uczenie modelu niestandardowego](quickstarts/client-library.md#train-a-custom-model)

Możesz nauczyć model [bez](quickstarts/client-library.md#train-a-model-without-labels) lub [z](quickstarts/client-library.md#train-a-model-with-labels) oznaczonymi zestawami danych. Zestawy danych bez etykiet opierają się wyłącznie na interfejsie API układu w celu wykrywania i identyfikowania informacji o kluczach, które nie zostały dodane przez człowieka. Zestawy danych z etykietami również polegają na interfejsie API układu, ale dodatkowe dane wejściowe są uwzględniane na przykład w określonych etykietach i lokalizacjach pól. Aby użyć danych z etykietami i bez etykiet, należy rozpocząć od co najmniej pięciu ukończonych formularzy tego samego typu dla danych szkoleń z etykietami, a następnie dodać dane nieoznaczone do wymaganego zestawu danych.  

>[!div class="nextstepaction"]
>[&#120786;. Analizowanie dokumentów przy użyciu modelu niestandardowego](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Przetestuj nowo szkolony model przy użyciu formularza, który nie jest częścią zestawu danych szkoleniowych. Możesz kontynuować dalsze szkolenia, aby zwiększyć wydajność modelu niestandardowego.  

> [!div class="nextstepaction"]
>[&#120787;. Zarządzanie modelami niestandardowymi](quickstarts/client-library.md#manage-custom-models)

W dowolnym momencie możesz wyświetlić listę wszystkich niestandardowych modeli w ramach subskrypcji, pobrać informacje o konkretnym modelu niestandardowym lub usunąć niestandardowy model z Twojego konta.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat biblioteki klienta aparatu rozpoznawania formularzy znajdziesz w dokumentacji dotyczącej interfejsu API.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API rozpoznawania formularzy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
>
