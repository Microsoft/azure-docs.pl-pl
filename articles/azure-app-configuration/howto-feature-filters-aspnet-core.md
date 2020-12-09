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
ms.openlocfilehash: 9a6ae1581b3958495010b032980b0de2e267088b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931881"
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

Filtr funkcji można zarejestrować `AddFeatureFilter` , wywołując metodę, określając nazwę filtru funkcji. Na przykład następujące rejestry kodu `PercentageFilter` :

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

1. Na ekranie **Edycja** wybierz **przycisk radiowy** , jeśli nie został jeszcze wybrany. Następnie kliknij przycisk **Dodaj filtr** . (Etykieta **przycisku radiowego zmieni się na odczyt** **warunkowy**).

1. W polu **klucz** wprowadź *wartość Microsoft. PERCENTAGE*.

    > [!div class="mx-imgBorder"]
    > ![Dodaj filtr funkcji](./media/feature-flag-add-filter.png)

1. Kliknij menu kontekstowe obok klucza filtru funkcji. Kliknij przycisk **Edytuj parametry**.

    > [!div class="mx-imgBorder"]
    > ![Edytuj parametry filtru funkcji](./media/feature-flag-edit-filter-parameters.png)

1. Umieść kursor pod nagłówkiem **Nazwa** , aby pola tekstowe pojawiały się w siatce. Wprowadź **nazwę** *i* **wartość** 50. **Wartość** pola wskazuje procent żądań, dla których ma zostać włączony filtr funkcji.

    > [!div class="mx-imgBorder"]
    > ![Ustaw parametry filtru funkcji](./media/feature-flag-set-filter-parameters.png)

1. Kliknij przycisk **Zastosuj** , aby powrócić do ekranu **flagi edycji funkcji** . Następnie kliknij przycisk **Zastosuj** ponownie, aby zapisać ustawienia flagi funkcji.

1. **Stan** flagi funkcji jest teraz wyświetlany jako *Conditional*. Ten stan wskazuje, że flaga funkcji zostanie włączona lub wyłączona dla poszczególnych żądań na podstawie kryteriów wymuszanych przez filtr funkcji.

    > [!div class="mx-imgBorder"]
    > ![Flaga funkcji warunkowej](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Filtry funkcji w akcji

Aby zobaczyć efekty tej flagi funkcji, uruchom aplikację i naciśnij wielokrotnie przycisk **odświeżania** w przeglądarce. Zobaczysz, że element *beta* pojawia się na pasku narzędzi około 50% czasu. Jest ona ukryta w pozostałej części czasu, ponieważ `PercentageFilter` dezaktywuje funkcję *beta* dla podzestawu żądań. Poniższe wideo pokazuje to zachowanie w działaniu.

> [!div class="mx-imgBorder"]
> ![TargetingFilter w działaniu](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Włącz etapowe wdrażanie funkcji dla docelowych odbiorców](./howto-targetingfilter-aspnet-core.md)
