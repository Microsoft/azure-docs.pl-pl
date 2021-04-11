---
title: Czym jest przetwarzanie obrazów?
titleSuffix: Azure Cognitive Services
description: Usługa przetwarzanie obrazów zapewnia dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: Obsługa komputerów, aplikacje komputerowe, usługa komputerowa
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286138"
---
# <a name="what-is-computer-vision"></a>Czym jest przetwarzanie obrazów?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Usługa przetwarzanie obrazów platformy Azure zapewnia dostęp do zaawansowanych algorytmów, które przetwarzają obrazy i zwracają informacje na podstawie interesujących Cię funkcji wizualizacji. 

| Usługa|Opis|
|---|---|
|[Optyczne rozpoznawanie znaków (OCR)](overview-ocr.md)|Usługa optycznego rozpoznawania znaków (OCR) wyodrębnia tekst z obrazów. Możesz użyć nowego interfejsu API odczytu do wyodrębnienia wydrukowanych i odręcznych tekstu z zdjęć i dokumentów. Korzysta ona z modeli opartych na głębokiej uczeniu i współpracuje z tekstem na różnych powierzchniach i w tle. Obejmują one dokumenty biznesowe, faktury, potwierdzenia, plakaty, wizytówki, litery i tablice. Interfejsy API OCR obsługują wyodrębnianie drukowanego tekstu w [kilku językach](./language-support.md). Aby rozpocząć, postępuj zgodnie z [przewodnikiem Szybki Start](quickstarts-sdk/client-library.md) .|
|[Analiza obrazu](overview-image-analysis.md)| Usługa Image Analysis Service wyodrębnia wiele funkcji wizualnych z obrazów, takich jak obiekty, powierzchnie, zawartość dla dorosłych i automatycznie generowane opisy tekstu. Aby rozpocząć, postępuj zgodnie z [przewodnikiem Szybki Start dotyczącego analizy obrazu](quickstarts-sdk/image-analysis-client-library.md) .|
| [Analiza przestrzenna](intro-to-spatial-analysis-public-preview.md)| Usługa analizy przestrzennej analizuje obecność i przenoszenie osób w kanale wideo i generuje zdarzenia, na które mogą reagować inne systemy. Zainstaluj [kontener analizy przestrzennej](spatial-analysis-container.md) , aby rozpocząć pracę.|

## <a name="computer-vision-for-digital-asset-management"></a>przetwarzanie obrazów do zarządzania cyfrowymi zasobami

Przetwarzanie obrazów może obsługiwać wiele scenariuszy zarządzania zasobami cyfrowymi. Ponosisz biznesową proces organizowania, przechowywania i pobierania rozbudowanych zasobów multimedialnych oraz zarządzania prawami i uprawnieniami cyfrowymi. Na przykład firma może chcieć grupować i identyfikować obrazy na podstawie widocznych logo, twarzy, obiektów, kolorów i tak dalej. Możesz też chcieć automatycznie [generować podpisy dla obrazów](./Tutorials/storage-lab-tutorial.md) i dołączać słowa kluczowe, aby umożliwić ich wyszukiwanie. Aby uzyskać informacje na temat całościowego rozwiązania z użyciem Cognitive Services, platformy Azure Wyszukiwanie poznawcze i inteligentnych raportów, zobacz [Przewodnik dotyczący akceleratora rozwiązań wyszukiwania](https://github.com/Azure-Samples/azure-search-knowledge-mining) w witrynie GitHub. Aby zapoznać się z innymi przykładami, zobacz repozytorium [szablonów rozwiązań przetwarzanie obrazów](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) .

## <a name="image-requirements"></a>Wymagania obrazu

Przetwarzanie obrazów umożliwia analizowanie obrazów, które spełniają następujące wymagania:

- Obraz musi być w formacie JPEG, PNG, GIF lub BMP
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB)
- Wymiary obrazu muszą być większe niż 50x50 pikseli
  - W przypadku interfejsu API odczytu wymiary obrazu muszą zawierać się w przedziale od 50 x 50 do 10000 x 10000 pikseli.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi przetwarzania obrazów powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z przewodnika Szybki Start, aby zaimplementować i uruchomić usługę w preferowanym języku deweloperskim.

* [Szybki Start: optyczne rozpoznawanie znaków (OCR)](quickstarts-sdk/client-library.md)
* [Szybki Start: Analiza obrazu](quickstarts-sdk/image-analysis-client-library.md)
* [Szybki Start: kontener analizy przestrzennej](spatial-analysis-container.md)
