---
title: Konfigurowanie programu Poster do Azure Media Services wywołań interfejsu API REST
description: W tym artykule opisano sposób konfigurowania programu Poster dla Media Services wywołań interfejsu API REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ef92e772085b1b89388c3f85fb3fdb91df0f6a75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012211"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Konfigurowanie programu Poster dla wywołań interfejsu API REST Media Services V2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

W tym samouczku pokazano, jak skonfigurować program **pocztowy** , aby można było go użyć do wywołania interfejsów api REST Azure Media Services (AMS). W tym samouczku przedstawiono sposób importowania plików środowiska i kolekcji do programu **Poster**. Kolekcja zawiera zgrupowane definicje żądań HTTP, które wywołują Azure Media Services (AMS) interfejsy API REST. Plik środowiska zawiera zmienne, które są używane przez kolekcję.

To środowisko i kolekcja są używane w artykułach, które pokazują, jak osiągnąć różne zadania za pomocą interfejsów API REST Azure Media Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj klienta REST programu [Postman](https://www.getpostman.com/), aby uruchomić interfejsy API REST przedstawione w niektórych samouczkach dotyczących AMS REST. 

    W przykładzie użyto programu **Postman**, ale odpowiednie będzie każde narzędzie REST. Inne alternatywy to: **Visual Studio Code** z wtyczką REST lub **Telerik programu Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Utwórz plik JSON zawierający zmienne środowiskowe używane w samouczkach AMS. Nazwij plik (na przykład **AzureMediaServices.postman_environment.json**). Otwórz plik i wklej kod, który definiuje środowisko Poster na podstawie [tej listy kodu](postman-environment.md). 
2. Otwórz program **Postman**.
3. Po prawej stronie ekranu wybierz opcję **Zarządzaj środowiskiem**.

    ![Zrzut ekranu przedstawia wybraną opcję Zarządzaj środowiskiem.](./media/media-services-rest-upload-files/postman-create-env.png)
4. W oknie dialogowym **Zarządzaj środowiskiem** kliknij opcję **Importuj**.
5. Przeglądaj i wybierz **AzureMediaServices.postman_environment.jsw** pliku.
6. Środowisko **AzureMedia** jest dodawane.
7. Zamknij okno dialogowe.
8. Wybierz środowisko **AzureMedia** .

    ![Zrzut ekranu przedstawia wybrane środowisko AzureMedia.](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Utwórz plik JSON zawierający kolekcję programu **Poster** ze wszystkimi operacjami, które są konieczne do przekazania pliku do Media Services. Nazwij plik (na przykład **AzureMediaServicesOperations.postman_collection.json**). Otwórz plik i wklej kod, który definiuje kolekcję **Poster** z [tej listy kodu](postman-collection.md).
2. Kliknij przycisk **Importuj**, aby zaimportować plik kolekcji.
3. Wybierz **AzureMediaServicesOperations.postman_collection.jsna** pliku.

    ![Zrzut ekranu przedstawia okno dialogowe importowania z wybranym wybieraniem plików.](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [przekazywanie elementów zawartości](media-services-rest-upload-files.md) .  
