---
title: Eksportuj model do urządzeń przenośnych Custom Vision Service
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób eksportowania modelu do użycia podczas tworzenia aplikacji mobilnych lub uruchamiania lokalnego na potrzeby klasyfikacji w czasie rzeczywistym.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: be28a10f8a96d21d64d08cfacee4543c065168ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85391761"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Eksportowanie modelu do użytku z urządzeniami przenośnymi

Custom Vision Service zezwala na eksportowanie klasyfikatorów do trybu offline. Możesz osadzić wyeksportowany klasyfikator w aplikacji i uruchomić ją lokalnie na urządzeniu na potrzeby klasyfikacji w czasie rzeczywistym.

## <a name="export-options"></a>Opcje eksportu

Custom Vision Service obsługuje następujące eksporty:

* __Tensorflow__ dla __systemu Android__.
* __CoreML__ dla __iOS11__.
* __ONNX__ dla __systemu Windows ml__.
* __[Vision Developer Kit](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* __Kontener platformy Docker__ dla systemu Windows, Linux lub architektury ARM. Kontener zawiera model Tensorflow i kod usługi do korzystania z interfejsu API Custom Vision.

> [!IMPORTANT]
> Custom Vision Service eksportuje tylko domeny __kompaktowe__ . Modele generowane przez domeny kompaktowe są zoptymalizowane pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Klasyfikatory skompilowane z kompaktową domeną mogą być nieco mniej dokładne niż domena standardowa z taką samą ilością danych szkoleniowych.
>
> Aby uzyskać informacje na temat ulepszania klasyfikatorów, zobacz [ulepszanie dokumentu klasyfikatora](getting-started-improving-your-classifier.md) .

## <a name="convert-to-a-compact-domain"></a>Konwertuj na kompaktową domenę

> [!NOTE]
> Kroki opisane w tej sekcji mają zastosowanie tylko wtedy, gdy masz istniejący model, który nie jest ustawiony na kompaktową domenę.

Aby skonwertować domenę istniejącego modelu, wykonaj następujące czynności:

1. W [witrynie sieci Web programu Custom Vision](https://customvision.ai)wybierz ikonę __Home__ , aby wyświetlić listę projektów.

    ![Obraz przedstawiający ikonę główną i listę projektów](./media/export-your-model/projects-list.png)

1. Wybierz projekt, a następnie wybierz ikonę __koła zębatego__ w prawym górnym rogu strony.

    ![Obraz ikony koła zębatego](./media/export-your-model/gear-icon.png)

1. W sekcji __domeny__ wybierz jedną z domen __kompaktowych__ . Wybierz pozycję __Zapisz zmiany__ , aby zapisać zmiany. 

    > [!NOTE]
    > W przypadku zestawu SDK programu Vision AI należy utworzyć projekt z __ogólną (kompaktową)__ domeną i należy określić opcję " **Vision AI Kit** " w sekcji **możliwości eksportu** .

    ![Obraz wyboru domen](./media/export-your-model/domains.png)

1. W górnej części strony wybierz pozycję __uczenie__ , aby ponownie przeprowadzić szkolenie przy użyciu nowej domeny.

## <a name="export-your-model"></a>Eksportowanie modelu

Aby wyeksportować model po przekształceniu, wykonaj następujące czynności:

1. Przejdź do karty **wydajność** i wybierz pozycję __Eksportuj__. 

    ![Obraz ikony eksportu](./media/export-your-model/export.png)

    > [!TIP]
    > Jeśli wpis __eksportu__ nie jest dostępny, wybrana iteracja nie korzysta z domeny kompaktowej. Użyj sekcji __iteracje__ na tej stronie, aby wybrać iterację korzystającą z domeny kompaktowej, a następnie wybierz pozycję __Eksportuj__.

1. Wybierz żądany format eksportu, a następnie wybierz pozycję __Eksportuj__ , aby pobrać model.

## <a name="next-steps"></a>Następne kroki

Aby zintegrować wyeksportowany model z aplikacją, należy zapoznać się z jednym z następujących artykułów lub przykładów:

* [Korzystanie z modelu Tensorflow w języku Python](export-model-python.md)
* [Korzystanie z modelu ONNX z systemem Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Zobacz przykład for [CoreML model w aplikacji dla systemu iOS](https://go.microsoft.com/fwlink/?linkid=857726) dla klasyfikacji obrazów w czasie rzeczywistym przy użyciu Swift.
* Zobacz przykład for [Tensorflow model w aplikacji systemu Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) dla klasyfikacji obrazów w czasie rzeczywistym w systemie Android.
* Zobacz przykład for [CoreML model z](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) platformą Xamarin dla klasyfikacji obrazów w czasie rzeczywistym w aplikacji platformy Xamarin dla systemu iOS.
