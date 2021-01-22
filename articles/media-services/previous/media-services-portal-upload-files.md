---
title: Przekazywanie plików na konto usługi Media Services w witrynie Azure Portal | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki przekazywania plików na konto usługi Media Services w witrynie Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: fdb301cd719d98d806e2a9e539cd81e6778461bb
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695310"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Przekazywanie plików na konto usługi Media Services w witrynie Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. Aby uzyskać aktualne pliki przekazywania z portalu, zobacz [Korzystanie z portalu do przekazywania, kodowania i przesyłania strumieniowego zawartości](../latest/manage-assets-quickstart.md).<br/>Sprawdź również: [Media Services v3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Za pomocą usługi Azure Media Services można przekazać pliki cyfrowe do elementu zawartości. Element zawartości może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki transkrypcji (oraz metadane dla tych plików). Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego.

W usłudze Media Services obowiązuje limit maksymalnego rozmiaru przetwarzanych plików. Aby uzyskać szczegółowe informacje na temat limitów rozmiarów plików, zobacz [Media Services quotas and limitations](media-services-quotas-and-limitations.md) (Limity przydziału i ograniczenia w usłudze Media Services).

Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="upload-files"></a>Przekazywanie plików
1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz pozycję **Ustawienia**  >  **zasoby**. Następnie wybierz przycisk **Przekaż**.
   
    ![Przekazywanie plików](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Zostanie wyświetlone okno **Przekaż element zawartości wideo**.
   
   > [!NOTE]
   > W usłudze Media Services nie ma limitu rozmiaru pliku dla przekazywanych wideo.
 
3. Na swoim komputerze przejdź do wideo, które chcesz przekazać. Wybierz wideo, a następnie wybierz przycisk **OK**.  
   
    Rozpocznie się przekazywanie. Postęp będzie widoczny pod nazwą pliku.  

Po zakończeniu przekazywania nowy element zawartości będzie widoczny w okienku **Elementy zawartości**. 

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zakodować przekazane elementy zawartości](media-services-portal-encode.md).

* Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania, gdy plik trafi do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz przykład w temacie [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) (Media Services: Integrowanie usługi Azure Media Services z usługami Azure Functions i Logic Apps).
