---
title: Ręczne dodawanie interfejsu API przy użyciu witryny Azure Portal | Microsoft Docs
description: Ten samouczek przedstawia sposób użycia usługi API Management (APIM) do ręcznego dodawania interfejsu API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183861"
---
# <a name="add-an-api-manually"></a>Ręczne dodawanie interfejsu API

W tym artykule opisano kroki ręcznego dodawania interfejsu API do wystąpienia usługi API Management (APIM). Typowy scenariusz, w przypadku którego tworzysz pusty interfejs API i definiujesz go ręcznie, to pozorowanie interfejsu API. Aby uzyskać szczegółowe informacje na interfejsu API na temat pozorowania interfejsu API, zobacz [Pozorowanie odpowiedzi interfejsu API](mock-api-responses.md).

Jeśli chcesz zaimportować istniejący interfejs API, zobacz sekcję [tematów pokrewnych](#related-topics).

W tym artykule tworzymy pusty interfejs API i określamy usługę [httpbin.org](https://httpbin.org) (publiczną usługę testowania) jako interfejs API zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Tworzenie interfejsu API

1. Przejdź do usługi API Management w Azure Portal i wybierz opcję **interfejsy API** z menu.
2. Z menu po lewej stronie wybierz pozycję **+ Dodaj interfejs API**.
3. Z listy wybierz pozycję **Pusty interfejs API**.  
    ![Pusty interfejs API](media/add-api-manually/blank-api.png)  
4. Wprowadź ustawienia interfejsu API. Ustawienia są wyjaśnione w samouczku [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md#import-and-publish-a-backend-api) .
5. Wybierz przycisk **Utwórz**.

W tym momencie nie masz żadnych operacji w API Management, które mapują operacje w interfejsie API zaplecza. Jeśli wywołasz operację, która jest dostępna za pomocą zaplecza, ale nie za pomocą API Management, uzyskasz **404**.

>[!NOTE] 
> Domyślnie po dodaniu interfejsu API, nawet jeśli jest on podłączony do pewnej usługi zaplecza, APIM nie będzie uwidaczniać żadnych operacji, dopóki ich nie zezwolisz. Aby zezwolić na działanie usługi zaplecza, Utwórz operację APIM, która jest mapowana na operację zaplecza.

## <a name="add-and-test-an-operation"></a>Dodawanie i testowanie operacji

W tej sekcji przedstawiono sposób dodawania operacji „/ get” w celu mapowania jej na operację „http://httpbin.org/get” zaplecza.

### <a name="add-an-operation"></a>Dodawanie operacji

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Kliknij przycisk **+ Dodaj operację**.
3. W polu **adresu URL** wybierz pozycję **GET**, a następnie wprowadź ciąg „*/get*” w zasobie.
4. Wprowadź ciąg „*FetchData*” w polu **Nazwa wyświetlana**.
5. Wybierz pozycję **Zapisz**.

### <a name="test-an-operation"></a>Testowanie operacji

Przetestuj operację w witrynie Azure Portal. Możesz również przetestować ją w **portalu deweloperów**.

1. Wybierz kartę **Test**.
2. Wybierz pozycję **FetchData**.
3. Kliknij pozycję **Wyślij**.

Zostanie wyświetlona odpowiedź generowana przez operację „http://httpbin.org/get”. Jeśli chcesz przekształcać operacje, zobacz temat [Przekształcanie i ochrona interfejsu API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Dodawanie i testowanie operacji sparametryzowanej

W tej sekcji przedstawiono sposób dodawania operacji przyjmującej parametr. W tym przypadku mapujemy operację na adres „http://httpbin.org/status/200”.

### <a name="add-the-operation"></a>Dodawanie operacji

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Kliknij przycisk **+ Dodaj operację**.
3. W polu **adresu URL** wybierz pozycję **GET**, a następnie wprowadź ciąg „*/status/{code}*” w zasobie. Opcjonalnie możesz podać niektóre informacje skojarzone z tym parametrem. Na przykład wprowadź wartość „*Numer*” w obszarze **TYP** i „*200*” (wartość domyślna) w obszarze **WARTOŚCI**.
4. Wprowadź ciąg „GetStatus” w polu **Nazwa wyświetlana**.
5. Wybierz pozycję **Zapisz**.

### <a name="test-the-operation"></a>Testowanie operacji 

Przetestuj operację w witrynie Azure Portal.  Możesz również przetestować ją w **portalu deweloperów**.

1. Wybierz kartę **Test**.
2. Wybierz pozycję **GetStatus**. Domyślnie wartość kodu jest ustawiana na „*200*”. Można ją zmienić, aby testować inne wartości. Na przykład wpisz wartość „*418*”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wyświetlona odpowiedź generowana przez operację „http://httpbin.org/status/200”. Jeśli chcesz przekształcać operacje, zobacz temat [Przekształcanie i ochrona interfejsu API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
