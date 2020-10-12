---
title: plik dołączany
description: plik dołączany
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84731038"
---
Azure Functions ułatwia dodawanie Application Insights integracji do aplikacji funkcji z [Azure Portal].

1. W [Azure Portal][witrynie Azure Portal]Wyszukaj i wybierz pozycję **aplikacja funkcji**, a następnie wybierz aplikację funkcji. 

1. Wybierz pozycję **Application Insights nie jest skonfigurowany** transparent w górnej części okna. Jeśli nie widzisz tego transparentu, Twoja aplikacja może mieć już włączony Application Insights.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Włączanie Application Insights z poziomu portalu":::

1. Rozwiń węzeł **Zmień zasób** i utwórz zasób Application Insights przy użyciu ustawień określonych w poniższej tabeli.  

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nowa nazwa zasobu** | Unikatowa nazwa aplikacji | Najłatwiej używać tej samej nazwy, co aplikacja funkcji, która musi być unikatowa w subskrypcji. | 
    | **Lokalizacja** | West Europe | Jeśli to możliwe, użyj tego samego [regionu](https://azure.microsoft.com/regions/) , w którym znajduje się aplikacja funkcji lub która znajduje się blisko tego regionu. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Włączanie Application Insights z poziomu portalu":::

1. Wybierz przycisk **Zastosuj**. 

   Zasób Application Insights jest tworzony w tej samej grupie zasobów i subskrypcji co aplikacja funkcji. Po utworzeniu zasobu Zamknij okno Application Insights.

1. W aplikacji funkcji wybierz pozycję **Konfiguracja** w obszarze **Ustawienia**, a następnie wybierz pozycję **Ustawienia aplikacji**. Jeśli zobaczysz ustawienie o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY` , Application Insights integracja jest włączona dla aplikacji funkcji działającej na platformie Azure.

[Witryna Azure Portal]: https://portal.azure.com
