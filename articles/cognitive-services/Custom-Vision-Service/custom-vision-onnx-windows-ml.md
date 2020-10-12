---
title: używanie modelu ONNX z funkcją Windows ML — Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć aplikację Windows UWP używającą modelu ONNX wyeksportowanego z Azure Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: d6bcb5485833fbfaa3eb72191ee54b1ee69bab04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82594299"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>używanie modelu ONNX z usługi Custom Vision z funkcją Windows ML (wersja zapoznawcza)

Dowiedz się, jak używać modelu ONNX wyeksportowanego z usługi Custom Vision z funkcją Windows ML (wersja zapoznawcza).

W tym przewodniku dowiesz się, jak używać pliku ONNX wyeksportowanego z Custom Vision Service z systemem Windows ML. Będziesz używać przykładowej aplikacji platformy UWP z własnym przeszkolonym klasyfikatorem obrazu.

## <a name="prerequisites"></a>Wymagania wstępne

* Windows 10 w wersji 1809 lub nowszej
* Windows SDK dla kompilacji 17763 lub nowszej
* Visual Studio 2017 w wersji 15.7 lub nowszej z włączonym pakietem __Opracowywanie zawartości dla platformy uniwersalnej systemu Windows__.
* Tryb dewelopera jest włączony na komputerze. Aby uzyskać więcej informacji, zobacz [Włączanie urządzenia na potrzeby programowania](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Informacje o przykładowej aplikacji

Dołączona aplikacja to ogólna aplikacja platformy UWP systemu Windows. Umożliwia wybranie obrazu z komputera, który następnie jest przetwarzany przez lokalnie przechowywany model klasyfikacji. Tagi i wyniki zwrócone przez model są wyświetlane obok obrazu.

## <a name="get-the-example-code"></a>Pobieranie przykładowego kodu

Przykładowa aplikacja jest dostępna w [COGNITIVE Services ONNX Custom Vision przykładowego](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) repozytorium w serwisie GitHub. Sklonuj go na komputerze lokalnym i Otwórz plik *SampleOnnxEvaluationApp. sln* w programie Visual Studio.

## <a name="test-the-application"></a>Testowanie aplikacji

1. Naciśnij klawisz `F5`, aby uruchomić aplikację z poziomu programu Visual Studio. Może zostać wyświetlony monit o włączenie trybu dewelopera.
1. Po uruchomieniu aplikacji wybierz obraz do ocenienia przy użyciu przycisku. Domyślny model ONNX jest szkolony do klasyfikowania różnych typów planktonu.

## <a name="use-your-own-model"></a>Używanie własnego modelu

Aby użyć własnego modelu klasyfikatora obrazu, wykonaj następujące kroki:

1. Utwórz i naucz klasyfikator przy użyciu usługi Custom Vision Service. Aby uzyskać instrukcje, jak to zrobić, zobacz [Tworzenie i uczenie klasyfikatora](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier). Użyj jednej z domen **kompaktowych** , takich jak **General (Compact)**. 
   * Jeśli masz istniejący klasyfikator korzystający z innej domeny, możesz przekonwertować go na **kompaktowanie** w ustawieniach projektu. Następnie ponownie nauczenie projektu przed kontynuowaniem.
1. Eksportuj model. Przejdź do karty wydajność i wybierz iterację przeszkolonej przy użyciu domeny **kompaktowej** . Wybierz przycisk **Eksportuj** , który zostanie wyświetlony. Następnie wybierz pozycję **ONNX**, a następnie opcję **Eksportuj**. Gdy plik będzie gotowy, wybierz przycisk **Pobierz**. Aby uzyskać więcej informacji na temat opcji eksportowania, zobacz [eksportowanie modelu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).
1. Otwórz pobrany plik *zip* i Wyodrębnij z niego plik *model. Onnx* . Ten plik zawiera model klasyfikatora.
1. W Eksplorator rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy folder **Assets** , a następnie wybierz pozycję __Dodaj istniejący element__. Wybierz plik ONNX.
1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy plik ONNX i wybierz polecenie **Właściwości**. Zmień następujące właściwości pliku:
   * __Akcja kompilacji__  ->  __Zawartość__
   * __Kopiuj do katalogu wyjściowego__  ->  __Kopiuj, jeśli nowszy__
1. Następnie otwórz _MainPage.XAML.cs_ i zmień wartość `_ourOnnxFileName` na nazwę pliku ONNX.
1. Użyj, `F5` Aby skompilować i uruchomić projekt.
1. Kliknij przycisk, aby wybrać obraz do oceny.

## <a name="next-steps"></a>Następne kroki

Aby poznać więcej sposobów eksportowania i używania modeli Custom Vision, przeczytaj następujące artykuły:

* [Eksportowanie modelu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Używanie wyeksportowanego modelu TensorFlow w aplikacji systemu Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Używanie wyeksportowanego modelu CoreML w aplikacji w języku Swift dla systemu iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Używanie wyeksportowanego modelu CoreML w aplikacji dla systemu iOS na platformie Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Aby uzyskać więcej informacji na temat używania modeli ONNX z systemem Windows ML, zobacz [integrowanie modelu z aplikacją za pomocą systemu Windows ml](/windows/ai/windows-ml/integrate-model).
