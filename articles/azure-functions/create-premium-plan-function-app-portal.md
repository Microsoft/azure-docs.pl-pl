---
title: Tworzenie planu Azure Functions Premium w portalu
description: Dowiedz się, jak za pomocą Azure Portal utworzyć aplikację funkcji, która jest uruchamiana w planie Premium.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 20921423247dda3cbb39b58dcc805dac6d367390
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937776"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Tworzenie aplikacji funkcji planu Premium w Azure Portal

Azure Functions oferuje skalowalny plan Premium, który zapewnia łączność sieci wirtualnej, brak sprzętu zimnego startu i warstwy Premium. Aby dowiedzieć się więcej, zobacz [Azure Functions plan Premium](functions-premium-plan.md). 

W tym artykule dowiesz się, jak za pomocą Azure Portal utworzyć aplikację funkcji w planie Premium. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

W tym momencie można tworzyć funkcje w nowej aplikacji funkcji. Te funkcje mogą korzystać z zalet [planu Premium](functions-premium-plan.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodaj funkcję wyzwalaną przez protokół HTTP](functions-create-first-azure-function.md#create-function)
