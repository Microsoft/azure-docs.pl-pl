---
title: Analizowanie multimediów za pomocą Azure Portal | Microsoft Docs
description: W tym temacie omówiono sposób przetwarzania multimediów za pomocą procesorów Media Analytics Medias (MPs) przy użyciu Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0e49e90209c7337081458b7c214d27b37d3b4da1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74462617"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analizowanie multimediów za pomocą Azure Portal 

> [!IMPORTANT]
> Zapoznaj się z [planami wycofania](media-services-analytics-overview.md#retirement-plans) niektórych procesorów multimedialnych.

## <a name="overview"></a>Omówienie
Azure Media Services Analytics to kolekcja składników mowy i obsługi (na skalę przedsiębiorstwa, zgodność, zabezpieczenia i globalny zasięg), które ułatwiają organizacjom i przedsiębiorstwom uzyskiwanie szczegółowych informacji z ich plików wideo. Więcej szczegółowych informacji na temat Azure Media Services Analytics można znaleźć w [tym](media-services-analytics-overview.md) temacie. 

W tym temacie omówiono sposób przetwarzania multimediów za pomocą procesorów Media Analytics Medias (MPs) przy użyciu Azure Portal. Media Analytics MPs tworzy pliki MP4 lub pliki JSON. Jeśli procesor multimediów wygenerował plik MP4, należy pobrać go stopniowo. Jeśli procesor multimediów wygenerował plik JSON, pobierany jest plik z magazynu obiektów blob platformy Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Wybierz element zawartości, który chcesz analizować
1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W oknie **Ustawienia** wybierz opcję **Elementy zawartości**.  
   
    ![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Wybierz element zawartości, który chcesz analizować, i naciśnij przycisk **Analizuj** .
   
    ![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. W oknie **Media Analytics przetwarzania zasobów nośnika z programem** wybierz procesor. 
   
    W pozostałej części artykułu wyjaśniono, dlaczego i jak należy używać poszczególnych procesorów. 
5. Naciśnij pozycję **Utwórz** , aby uruchomić zadanie.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Procesor multimediów **Azure Media Indexer** umożliwia przeszukiwanie plików multimedialnych i zawartości oraz generowanie ścieżek napisów. Ta sekcja zawiera szczegółowe informacje dotyczące opcji określonych dla tego punktu zarządzania.

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Język
Język naturalny, który ma zostać rozpoznany w pliku multimedialnym. Na przykład angielski lub hiszpański. 

### <a name="captions"></a>Transkrypcje
Można wybrać format podpisu, który zostanie wygenerowany na podstawie zawartości. Zadanie indeksowania może generować pliki napisów w następujących formatach:  
 
* **TTML**
* **Ścieżka**

Pliki napisów (DW) w tych formatach mogą służyć do udostępniania plików audio i wideo osobom niepełnosprawnym.

### <a name="keywords"></a>Słowa kluczowe
Wybierz tę opcję, jeśli chcesz wygenerować plik XML ze słowami kluczowymi. Ten plik zawiera słowa kluczowe wyodrębnione z zawartości mowy i informacje o częstotliwości i przesunięciu.

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, która umożliwia identyfikację zadania. W [tym](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa, która pozwala identyfikować zawartość wyjściową. 

### <a name="speed"></a>Szybkość
Określ szybkość, z którą chcesz przyspieszyć wejściowe wideo. Dane wyjściowe są ustabilizowane i czasochłonne w odróżnieniu od wejściowego wideo.

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, która umożliwia identyfikację zadania. W [tym](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa, która pozwala identyfikować zawartość wyjściową. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Procesor Media **Azure Media Face Detector** (MP) umożliwia zliczanie, śledzenie przesunięć, a nawet ocenia uczestnictwo i reagowanie odbiorców przy użyciu wyrażeń twarzy. Ta usługa zawiera dwie funkcje: 

* **Wykrywanie twarzy**
  
    Wykrywanie twarzy wykrywa i śledzi ludzkie twarze w filmie wideo. Można wykryć wiele twarzy, a następnie śledzić je w miarę poruszania się, z metadanymi czasu i lokalizacji zwracanymi w pliku JSON. Podczas śledzenia podejmie próbę nawiązania spójnego identyfikatora z tą samą stroną, gdy osoba jest przenoszona na ekran, nawet jeśli są one zakłócone lub krótko opuszczają ramkę.
  
  > [!NOTE]
  > Te usługi nie przeprowadzają rozpoznawania twarzy. Osoba, która opuści ramkę lub nie jest zbyt długa, otrzymuje nowy identyfikator, gdy zwróci.
  > 
  > 
* **Wykrywanie rozpoznawania emocji**
  
    Wykrywanie rozpoznawania emocji jest opcjonalnym składnikiem procesora multimediów wykrywanie twarzy, który zwraca analizę dla wielu atrybutów emocjonalnej z wykrytych twarzy, w tym szczęście, smutek, obaw, gniew i wiele innych. 

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Tryb wykrywania
Procesor może używać jednego z następujących trybów:

* Wykrywanie kroju
* Wykrywanie rozpoznawania emocji na głowie
* Agregowanie wykrywania rozpoznawania emocji

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, która umożliwia identyfikację zadania. W [tym](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa, która pozwala identyfikować zawartość wyjściową. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Procesor Media **Azure Media Motion Detector** (MP) pozwala wydajnie identyfikować interesujące Cię sekcje w innym pliku wideo długim i niezdarzeń. Wykrywanie ruchu może być używane na potrzeby statycznego filmu kamery do identyfikowania sekcji wideo, w których odbywa się ruch. Generuje plik JSON zawierający metadane z sygnaturami czasowymi i obszarem ograniczenia, w którym wystąpiło zdarzenie.

Technologia ta umożliwia kategoryzowanie ruchu na odpowiednich zdarzeniach i fałszywych wartościach, takich jak cieniowanie i oświetlenie. Dzięki temu można generować alerty zabezpieczeń z kanałów informacyjnych z aparatu bez spamu, pozostawiając nieodpowiednie zdarzenia, jednocześnie umożliwiając wyodrębnienie czasu zainteresowania z bardzo długich filmów wideo.

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Ten procesor może pomóc w tworzeniu podsumowań długich filmów wideo przez automatyczne wybieranie interesujących fragmentów kodu z obrazu źródłowego. Jest to przydatne, gdy chcesz szybko zapoznać się z oczekiwaniami w długim filmie wideo. Aby uzyskać szczegółowe informacje i przykłady, zobacz [używanie Azure Media Video thumbnails do tworzenia podsumowania wideo](media-services-video-summarization.md)

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, która umożliwia identyfikację zadania. W [tym](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa, która pozwala identyfikować zawartość wyjściową. 

## <a name="azure-media-content-moderator"></a>Usługa Azure Media Content Moderator
Ten procesor pomaga wykryć potencjalną zawartość dla dorosłych i erotycznej w filmach wideo. Procesor automatycznie wykrywa zrzuty i ramki kluczowe w filmie wideo. Ocenia on klatki kluczowe dla możliwej zawartości dla dorosłych lub erotycznej i sugeruje przeglądy na podstawie domyślnych progów. Aby uzyskać szczegółowe informacje i przykłady, zobacz [Korzystanie z usługi Azure Media Content moderator do umiarkowanego wideo](media-services-content-moderation.md)

![Średnie wideo](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Wersja 
Użyj "2,0".

### <a name="mode"></a>Tryb
Wersja 2,0 zignoruje `Mode` ustawienie.

## <a name="next-steps"></a>Następne kroki
Wyświetl ścieżki uczenia Media Servicesowego.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
