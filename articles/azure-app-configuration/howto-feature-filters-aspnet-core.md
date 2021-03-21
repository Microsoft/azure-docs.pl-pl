---
title: Używanie filtrów funkcji do włączania flag funkcji warunkowej
titleSuffix: Azure App Configuration
description: Dowiedz się, jak używać filtrów funkcji do włączania flag funkcji warunkowej
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602251"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>Używanie filtrów funkcji do włączania flag funkcji warunkowej

Flagi funkcji umożliwiają aktywowanie lub dezaktywowanie funkcji w aplikacji. Prosta flaga funkcji jest włączona lub wyłączona. Aplikacja zawsze działa tak samo. Można na przykład wdrożyć nową funkcję za flagą funkcji. Po włączeniu flagi funkcji wszyscy użytkownicy widzą nową funkcję. Wyłączenie flagi funkcji powoduje ukrycie nowej funkcji.

W przeciwieństwie _Flaga funkcji warunkowej_ pozwala na dynamiczne Włączanie lub wyłączanie flagi funkcji. Aplikacja może zachowywać się inaczej, w zależności od kryteriów flagi funkcji. Załóżmy, że chcesz najpierw pokazać swoją nową funkcję w małym podzbiorze użytkowników. Flaga funkcji warunkowej umożliwia włączenie flagi funkcji dla niektórych użytkowników podczas jej wyłączania dla innych osób. _Filtry funkcji_ określają stan flagi funkcji zawsze wtedy, gdy jest ona szacowana.

`Microsoft.FeatureManagement`Biblioteka zawiera trzy filtry funkcji:

- `PercentageFilter` Włącza flagę funkcji na podstawie wartości procentowej.
- `TimeWindowFilter` Włącza flagę funkcji w określonym przedziale czasu.
- `TargetingFilter` Włącza flagę funkcji dla określonych użytkowników i grup.

Możesz również utworzyć własny filtr funkcji, który implementuje [interfejs Microsoft. FeatureManagement. IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Rejestrowanie filtru funkcji

Filtr funkcji można zarejestrować `AddFeatureFilter` , wywołując metodę, określając nazwę typu żądanego filtru funkcji. Na przykład następujące rejestry kodu `PercentageFilter` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Konfigurowanie filtru funkcji w konfiguracji aplikacji platformy Azure

Niektóre filtry funkcji mają dodatkowe ustawienia. Na przykład `PercentageFilter` uaktywnia funkcję na podstawie wartości procentowej. Ma ustawienie definiujące wartość procentową do użycia.

Te ustawienia można skonfigurować dla flag funkcji zdefiniowanych w obszarze Konfiguracja aplikacji platformy Azure. Na przykład wykonaj następujące kroki, aby `PercentageFilter` włączyć flagę funkcji dla 50% żądań do aplikacji sieci Web:

1. Postępuj zgodnie z instrukcjami w [przewodniku szybki start: Dodawanie flag funkcji do aplikacji ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) , aby utworzyć aplikację sieci Web przy użyciu flagi funkcji.

1. W Azure Portal przejdź do magazynu konfiguracji, a następnie kliknij pozycję **Menedżer funkcji**.

1. Kliknij menu kontekstowe dla flagi funkcji *beta* , która została utworzona w przewodniku Szybki Start. Kliknij pozycję **Edytuj**.

    > [!div class="mx-imgBorder"]
    > ![Edytuj flagę funkcji beta](./media/edit-beta-feature-flag.png)

1. Na ekranie **Edycja** zaznacz pole wyboru **Włącz flagę funkcji** , jeśli nie zostało jeszcze włączone. Następnie zaznacz pole wyboru **Użyj filtru funkcji** i wybierz pozycję **niestandardowe**. 

1. W polu **Nazwa** wybierz pozycję *Microsoft. PERCENTAGE*.

    > [!div class="mx-imgBorder"]
    > ![Dodaj filtr funkcji](./media/feature-flag-add-filter.png)

1. Kliknij menu kontekstowe obok nazwy filtru funkcji. Kliknij przycisk **Edytuj parametry filtru**.

    > [!div class="mx-imgBorder"]
    > ![Edytuj parametry filtru funkcji](./media/feature-flags-edit-filter-parameters.png)

1. Wprowadź **nazwę** *i* **wartość** 50. **Wartość** pola wskazuje procent żądań, dla których ma zostać włączony filtr funkcji.

    > [!div class="mx-imgBorder"]
    > ![Ustaw parametry filtru funkcji](./media/feature-flag-set-filter-parameters.png)

1. Kliknij przycisk **Zastosuj** , aby powrócić do ekranu **flagi edycji funkcji** . Następnie kliknij przycisk **Zastosuj** ponownie, aby zapisać ustawienia flagi funkcji.

1. Na stronie **Menedżer funkcji** flaga funkcji ma teraz wartość **filtru funkcji** *niestandardowa*. 

    > [!div class="mx-imgBorder"]
    > ![Flaga funkcji na liście z wartością filtru funkcji "Custom"](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>Filtry funkcji w akcji

Aby zobaczyć efekty tej flagi funkcji, uruchom aplikację i naciśnij wielokrotnie przycisk **odświeżania** w przeglądarce. Zobaczysz, że element *beta* pojawia się na pasku narzędzi około 50% czasu. Jest ona ukryta w pozostałej części czasu, ponieważ `PercentageFilter` dezaktywuje funkcję *beta* dla podzestawu żądań. Poniższe wideo pokazuje to zachowanie w działaniu.

> [!div class="mx-imgBorder"]
> ![TargetingFilter w działaniu](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Włącz etapowe wdrażanie funkcji dla docelowych odbiorców](./howto-targetingfilter-aspnet-core.md)
