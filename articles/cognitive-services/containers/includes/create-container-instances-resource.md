---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876451"
---
## <a name="create-an-azure-container-instance-resource"></a>Tworzenie zasobu wystąpienia kontenera platformy Azure

1. Przejdź do strony [Tworzenie](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) dla Container Instances.

2. Na karcie **podstawowe** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Subskrypcja|Wybierz subskrypcję.|
    |Grupa zasobów|Wybierz dostępną grupę zasobów lub Utwórz nową, taką jak `cognitive-services` .|
    |Nazwa kontenera|Wprowadź nazwę, taką jak `cognitive-container-instance` . Nazwa musi być krótsza.|
    |Lokalizacja|Wybierz region do wdrożenia.|
    |Typ obrazu|Jeśli obraz kontenera jest przechowywany w rejestrze kontenerów, który nie wymaga poświadczeń, wybierz opcję `Public` . W przypadku uzyskiwania dostępu do obrazu kontenera wymagane są poświadczenia `Private` . Zapoznaj się z [repozytoriami kontenerów i obrazami](../../cognitive-services-container-support.md#container-repositories-and-images) , aby uzyskać szczegółowe informacje na temat tego, czy obraz kontenera jest `Public` lub `Private` ("publiczna wersja zapoznawcza"). |
    |Nazwa obrazu|Wprowadź Cognitive Services lokalizację kontenera. Lokalizacja jest używana jako argument `docker pull` polecenia. Zapoznaj się z [repozytoriami i obrazami kontenerów](../../cognitive-services-container-support.md#container-repositories-and-images) dla dostępnych nazw obrazów i ich odpowiedniego repozytorium.<br><br>Nazwa obrazu musi być w pełni kwalifikowana, określając trzy części. Najpierw należy nazwa obrazu rejestru kontenerów, a następnie repozytorium: `<container-registry>/<repository>/<image-name>` .<br><br>Oto przykład, który `mcr.microsoft.com/azure-cognitive-services/keyphrase` reprezentuje wyodrębnianie kluczowych fraz obraz w Container Registry Microsoft w ramach repozytorium Cognitive Services platformy Azure. Innym przykładem jest, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` który będzie reprezentować mowę do obrazu tekstu w repozytorium firmy Microsoft rejestru kontenerów w wersji zapoznawczej kontenera. |
    |Typ systemu operacyjnego|`Linux`|
    |Rozmiar|Zmień rozmiar sugerowanych zaleceń dla określonego kontenera usługi poznawczej:<br>2 rdzenie procesora CPU<br>4 GB

3. Na karcie **Sieć** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Porty|Ustaw port TCP na `5000` . Udostępnia kontener na porcie 5000.|

4. Na karcie **Zaawansowane** wprowadź wymagane **zmienne środowiskowe** dla ustawień rozliczania kontenerów zasobu wystąpienia kontenera platformy Azure:

    | Klucz | Wartość |
    |--|--|
    |`apikey`|Skopiowane ze strony **klucze** zasobu. Jest to 32 ciąg znaków alfanumerycznych bez spacji ani kresek `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
    |`billing`|Skopiowane ze strony **Przegląd** zasobu.|
    |`eula`|`accept`|

5. Kliknij przycisk **Przeglądaj i Utwórz**
6. Po zakończeniu walidacji kliknij przycisk **Utwórz** , aby zakończyć proces tworzenia
7. Po pomyślnym wdrożeniu zasobu jest gotowy
