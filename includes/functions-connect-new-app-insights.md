---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121571"
---
Funkcje ułatwiają dodawanie Application Insights integracji z aplikacją funkcji z [Azure Portal].

1. W [Azure Portal][witrynie Azure Portal]Wyszukaj i wybierz pozycję **aplikacja funkcji**, a następnie wybierz aplikację funkcji. 

1. Wybierz pozycję **Application Insights nie jest skonfigurowany** transparent w górnej części okna. Jeśli nie widzisz tego transparentu, aplikacja ma już włączony Application Insights.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Włączanie Application Insights z poziomu portalu":::

1. Utwórz zasób Application Insights przy użyciu ustawień określonych w tabeli poniżej obrazu.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Tworzenie zasobu Application Insights":::

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Unikatowa nazwa aplikacji | Najłatwiej używać tej samej nazwy, co aplikacja funkcji, która musi być unikatowa w subskrypcji. | 
    | **Lokalizacja** | Europa Zachodnia | Jeśli to możliwe, użyj tego samego [regionu](https://azure.microsoft.com/regions/) , w którym znajduje się aplikacja funkcji lub która znajduje się blisko tego regionu. |

1. Wybierz przycisk **Zastosuj**. Zasób Application Insights jest tworzony w tej samej grupie zasobów i subskrypcji co aplikacja funkcji. Po utworzeniu zasobu Zamknij okno Application Insights.

1. Wróć do aplikacji funkcji, wybierz pozycję **Settings**  >  **Konfiguracja**ustawień, a następnie wybierz pozycję **Ustawienia aplikacji**. Jeśli zobaczysz ustawienie o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY` , Application Insights integracja jest włączona dla aplikacji funkcji działającej na platformie Azure.

[Witryna Azure Portal]: https://portal.azure.com
