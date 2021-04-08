---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e5cfc9beb5473917a76f822862ce3d61675d6493
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93406638"
---
1. Aby utworzyć zasób usługi Azure sygnalizujący, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com). W lewym górnym rogu strony wybierz pozycję **+ Utwórz zasób**. W polu tekstowym **Wyszukaj w witrynie Marketplace** wprowadź wartość **Usługa sygnalizująca**.

2. Wybierz pozycję **Usługa sygnalizująca** w wynikach i wybierz pozycję **Utwórz**.

3. Na stronie nowe ustawienia **sygnalizującego** Dodaj następujące ustawienia dla nowego zasobu sygnalizującego:

    | Nazwa | Zalecana wartość | Opis |
    | ---- | ----------------- | ----------- |
    | Nazwa zasobu | *testsignalr* | Podaj unikatową nazwę zasobu do użycia dla zasobu usługi SignalR. Nazwa musi być ciągiem od 1 do 63 znaków i zawierać tylko cyfry, litery i znak łącznika ( `-` ). Nazwa nie może rozpoczynać się ani kończyć znakiem łącznika, a kolejne znaki łącznika są nieprawidłowe.|
    | Subskrypcja | Wybierz subskrypcję |  Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi SignalR. Jeśli konto ma tylko jedną subskrypcję, zostanie automatycznie wybrane i lista rozwijana **subskrypcji** nie zostanie wyświetlona.|
    | Grupa zasobów | Tworzenie grupy zasobów o nazwie *SignalRTestResources*| Wybierz lub utwórz grupę zasobów dla zasobu usługi SignalR. Ta grupa jest przydatna do organizowania wielu zasobów, które mogą zostać usunięte w tym samym czasie przez usunięcie grupy zasobów. Więcej informacji można znaleźć w temacie [Using resource groups to manage your Azure resources](../articles/azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure). |
    | Lokalizacja | *Wschodnie stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany zasób usługi SignalR. Aby uzyskać najlepszą wydajność, zalecamy utworzenie zasobu w tym samym regionie, co inne składniki aplikacji. |
    | Warstwa cenowa | *Bezpłatna* | Obecnie dostępne są opcje **bezpłatne** i **standardowe** . |
    | Przypnij do pulpitu nawigacyjnego | ✔ | Zaznacz to pole, aby zasób został przypięty do pulpitu nawigacyjnego, co ułatwia jego znalezienie. |

4. Wybierz pozycję **Przejrzyj i utwórz**. Poczekaj na zakończenie walidacji. 

5. Wybierz przycisk **Utwórz**. Wdrożenie może potrwać kilka minut.

6. Po zakończeniu wdrażania wybierz pozycję **klucze** w obszarze **Ustawienia**. Skopiuj parametry połączenia klucza podstawowego. Ten ciąg zostanie użyty później w celu skonfigurowania aplikacji do korzystania z zasobu usługi Azure Signal Service.

    Parametry połączenia mają następującą postać:
    
    `Endpoint=<service_endpoint>;AccessKey=<access_key>;`
