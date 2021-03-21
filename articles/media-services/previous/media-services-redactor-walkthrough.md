---
title: Redagowanie twarzy przy użyciu przewodnika Azure Media Analytics | Microsoft Docs
description: W tym temacie przedstawiono instrukcje krok po kroku dotyczące uruchamiania pełnego przepływu pracy redakcyjnego za pomocą Eksploratora Azure Media Services (AMSE) i Azure Media Redactor wizualizatora (narzędzia typu "open source").
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: 1a7e20681dfa7da7ce30f46a7c4b0b6df6f78916
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009565"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redagowanie twarzy przy użyciu przewodnika Azure Media Analytics

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie

**Azure Media redactor** to procesor Media [Azure Media Analytics](./legacy-components.md) (MP), który oferuje skalowalne możliwości redakcyjne w chmurze. Redakcja twarzy umożliwia modyfikowanie wideo w celu rozmycia powierzchni wybranych osób. Możesz chcieć użyć usługi redakcyjnej ze stroną w scenariuszach bezpieczeństwa publicznego i mediów informacyjnych. Kilka minut filmu, które zawiera wiele twarzy, może zająć więcej czasu, ale w przypadku tej usługi proces redakcyjny twarzy będzie wymagał zaledwie kilku prostych kroków. Aby uzyskać więcej informacji, zobacz [ten](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Aby uzyskać szczegółowe informacje na temat  **Azure Media redactor**, zobacz temat [Omówienie redakcji czołowej](media-services-face-redaction.md) .

W tym temacie przedstawiono instrukcje krok po kroku dotyczące uruchamiania pełnego przepływu pracy redakcyjnego za pomocą Eksploratora Azure Media Services (AMSE) i Azure Media Redactor wizualizatora (narzędzia typu "open source").

Aby uzyskać więcej informacji, zobacz [ten](https://azure.microsoft.com/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Przepływ pracy Eksploratora Azure Media Services

Najprostszym sposobem na rozpoczęcie pracy z programem redactor jest użycie narzędzia Open Source AMSE w witrynie GitHub. Uproszczony przepływ pracy można uruchomić w trybie **połączonym** , jeśli nie potrzebujesz dostępu do pliku JSON adnotacji lub obrazów w formacie jpg.

### <a name="download-and-setup"></a>Pobierz i skonfiguruj

1. Pobierz narzędzie AMSE for AMS v2 w [tym miejscu](https://aka.ms/amseforv2).
1. Zaloguj się do swojego konta Media Services przy użyciu klucza usługi.

    Aby uzyskać informacje o kluczu i nazwie konta, przejdź do witryny [Azure Portal](https://portal.azure.com/) i wybierz swoje konto AMS. Następnie wybierz pozycję Ustawienia > klucze. W oknie Zarządzanie kluczami widoczna jest nazwa konta oraz wyświetlane są klucze podstawowe i pomocnicze. Skopiuj wartości nazwy konta i klucza podstawowego.

![Zrzut ekranu przedstawia Microsoft Azure Media Services, w którym można wprowadzić nazwę i klucz konta.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Tryb pierwszego przebiegu — analiza

1. Przekaż plik multimedialny do elementu zawartości — > przekazywanie lub za pomocą przeciągania i upuszczania. 
1. Kliknij prawym przyciskiem myszy i przetwórz plik multimedialny przy użyciu Media Analytics – > Azure Media Redactor — > Przeanalizuj tryb. 


![Zrzut ekranu przedstawia menu zawierające elementy zawartości procesu z Azure Media Redactor.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Zrzut ekranu przedstawia Azure Media Redactor z pierwszym przebiegiem: Przeanalizuj wybrany tryb.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Dane wyjściowe będą zawierać plik JSON adnotacji z danymi lokalizacji kroju, a także kombinację jpg każdej wykrytej funkcji. 

![Zrzut ekranu przedstawia dane wyjściowe analizy.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Drugi przebieg — tryb redagowania

1. Przekaż oryginalny zasób wideo do danych wyjściowych z pierwszego przebiegu i Ustaw jako zasób podstawowy. 

    ![Zrzut ekranu przedstawia przyciski przekazywania i ustawiania jako podstawowe.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. Obowiązkowe Przekaż plik "Dance_idlist.txt" zawierający listę identyfikatorów, które chcesz wypróbować. 

    ![Zrzut ekranu przedstawia opcję przekazania pliku tekstowego.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. Obowiązkowe Wprowadź wszelkie edycje annotations.jsw pliku, takie jak zwiększenie granic pola ograniczenia. 
4. Kliknij prawym przyciskiem myszy element zawartości wyjściowej z pierwszego przebiegu, wybierz redactor, a następnie uruchom polecenie z trybem **redagowania** . 

    ![Zrzut ekranu przedstawia Azure Media Redactor z drugim przebiegiem: zaznaczono tryb redagowania.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Pobieranie lub udostępnianie końcowego elementu wyjściowego redagowane. 

    ![Zrzut ekranu przedstawia przycisk Pobierz.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Narzędzie Azure Media Redactor wizualizatora Open Source

[Narzędzie wizualizatora](https://github.com/Microsoft/azure-media-redactor-visualizer) Open Source zostało zaprojektowane, aby pomóc deweloperom w rozpoczęciu pracy z formatem adnotacji z analizą i użyciem danych wyjściowych.

Po sklonowaniu repozytorium, aby uruchomić projekt, konieczne będzie pobranie narzędzia FFmpeg z ich [oficjalnej lokacji](https://ffmpeg.org/download.html).

Jeśli jesteś deweloperem próbującym analizować dane adnotacji JSON, zapoznaj się z tematem modele. metadane dotyczące przykładowych przykładów kodu.

### <a name="set-up-the-tool"></a>Konfigurowanie narzędzia

1.  Pobierz i skompiluj całe rozwiązanie. 

    ![Zrzut ekranu przedstawia rozwiązanie kompilacji wybrane z menu.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Pobierz narzędzia FFmpeg z tego [miejsca](https://ffmpeg.org/download.html). Ten projekt został pierwotnie opracowany przy użyciu wersji be1d324 (2016-10-04) z konsolidacją statyczną. 
3.  Skopiuj ffmpeg.exe i ffprobe.exe do tego samego folderu wyjściowego co AzureMediaRedactor.exe. 

    ![Zrzut ekranu przedstawia zawartość folderu, w tym narzędzia FFmpeg i ffprobe.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Uruchom AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Korzystanie z narzędzia

1. Przetwórz wideo na koncie Azure Media Services przy użyciu MP redactor w trybie analizy. 
2. Pobierz zarówno oryginalny plik wideo, jak i dane wyjściowe zadania redakcyjnego analizy. 
3. Uruchom aplikację wizualizatora i wybierz powyższe pliki. 

    ![Zrzut ekranu przedstawia Azure Media Redactor przekazywania plików.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Wyświetl podgląd pliku. Wybierz, które powierzchnie chcesz rozmycie za pośrednictwem paska bocznego po prawej stronie. 
    
    ![Zrzut ekranu przedstawia Azure Media Redactor, w którym można wyświetlać podgląd i wybierać twarzy do rozmycia.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Dolne pole tekstowe zostanie zaktualizowane o identyfikatorach. Utwórz plik o nazwie "idlist.txt" z tymi identyfikatorami jako listę rozdzielaną znakami nowego wiersza. 

    >[!NOTE]
    > idlist.txt należy zapisać w formacie ANSI. Aby zapisać w ANSI, możesz użyć notatnika.
    
6.  Przekaż ten plik do wyjściowego elementu zawartości z kroku 1. Przekaż oryginalne wideo do tego zasobu, a także Ustaw jako podstawowy element zawartości. 
7.  Uruchom zadanie redakcyjne dla tego elementu zawartości z trybem "Zredaguj", aby uzyskać finalne wideo redagowane. 

## <a name="next-steps"></a>Następne kroki 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Linki pokrewne
[Omówienie Azure Media Services Analytics](./legacy-components.md)

[Demonstracje Azure Media Analytics](http://amslabs.azurewebsites.net/demos/Analytics.html)

[Zapowiedź redakcyjną dla Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)