---
title: Przekazywanie plików na konto Azure Media Services v3 przy użyciu usługi REST | Microsoft Docs
description: Dowiedz się, jak pobrać zawartość multimedialną do Media Services przez utworzenie i przekazanie zasobów.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76705772"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Przekazywanie plików na konto Media Services v3 przy użyciu usługi REST

W Media Services można przekazać pliki cyfrowe do kontenera obiektów BLOB skojarzonego z elementem zawartości. Jednostka [zasobu](https://docs.microsoft.com/rest/api/media/operations/asset) może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów (oraz metadane dotyczące tych plików). Gdy pliki zostaną przekazane do kontenera zasobów, zawartość jest bezpiecznie przechowywana w chmurze w celu dalszej przetwarzania i przesyłania strumieniowego.

W tym artykule przedstawiono sposób przekazywania pliku lokalnego przy użyciu usługi REST.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym temacie, należy:

- Przejrzyj [koncepcję zasobów](assets-concept.md).
- [Skonfiguruj funkcję publikowania dla Azure Media Services wywołań interfejsu API REST](media-rest-apis-with-postman.md).
    
    Pamiętaj, aby postępować zgodnie z ostatnim krokiem w temacie [pobieranie tokenu usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Tworzenie zasobu

W tej sekcji przedstawiono sposób tworzenia nowego zasobu.

1. Wybierz pozycję **zasoby**  ->  **Utwórz lub zaktualizuj element zawartości**.
2. Kliknij pozycję **Wyślij**.

    ![Tworzenie zasobu](./media/upload-files/postman-create-asset.png)

Zostanie wyświetlona **odpowiedź** wraz z informacjami dotyczącymi nowo utworzonego elementu zawartości.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Uzyskaj adres URL sygnatury dostępu współdzielonego z uprawnieniami do odczytu i zapisu 

W tej sekcji pokazano, jak uzyskać adres URL sygnatury dostępu współdzielonego, który został wygenerowany dla utworzonego elementu zawartości. Adres URL sygnatury dostępu współdzielonego został utworzony z uprawnieniami do odczytu i zapisu. może służyć do przekazywania plików cyfrowych do kontenera zasobów.

1. Wybierz pozycję **zasoby**  ->  **Lista adresów URL zasobów**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/upload-files/postman-create-sas-locator.png)

Zostanie wyświetlona **odpowiedź** z informacjami o adresach URL zasobów. Skopiuj pierwszy adres URL i użyj go do przekazania pliku.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Przekazywanie pliku do usługi BLOB Storage przy użyciu adresu URL przekazywania

Użyj interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md) lub [zestawu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Następne kroki

[Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — REST](stream-files-tutorial-with-rest.md)
