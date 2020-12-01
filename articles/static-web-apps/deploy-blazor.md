---
title: 'Samouczek: Tworzenie statycznej aplikacji internetowej z Blazor w usłudze Azure static Web Apps'
description: Dowiedz się, jak utworzyć witrynę sieci Web usługi Azure static Web Apps za pomocą Blazor.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 0086f7f68fd05d6925d19c7ab457fbc125e36be4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350232"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Samouczek: Tworzenie statycznej aplikacji internetowej z Blazor w usłudze Azure static Web Apps

Usługa Azure static Web Apps publikuje witrynę sieci Web w środowisku produkcyjnym, tworząc aplikacje z repozytorium GitHub. W tym samouczku wdrażasz aplikację sieci Web w usłudze Azure static Web Apps przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi [GitHub](https://github.com)
- Konto [platformy Azure](https://portal.azure.com)

## <a name="application-overview"></a>Omówienie aplikacji

Usługa Azure static Web Apps umożliwia tworzenie statycznych aplikacji sieci Web obsługiwanych przez bezserwerowe zaplecza. W poniższym samouczku przedstawiono sposób wdrażania aplikacji sieci Web w języku C# Blazor, która zwraca dane pogodowe.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="Ukończ aplikację Blazor":::

Aplikacja proponowana w tym samouczku składa się z trzech różnych projektów programu Visual Studio:

- **Interfejs API**: aplikacja C# Azure Functions, która implementuje punkt końcowy interfejsu API, który zapewnia informacje o pogodzie dla aplikacji statycznej. **WeatherForecastFunction** zwraca tablicę `WeatherForecast` obiektów.

- **Klient**: projekt zestawu sieci Web frontonu Blazor. Zaimplementowano [trasę rezerwową](#fallback-route) , aby zapewnić, że wszystkie trasy są obsługiwane w pliku _index.html_ .

- **Udostępnione**: zawiera wspólne klasy, do których odwołują się zarówno projekty API, jak i klienckie, które umożliwiają przepływ danych z punktu końcowego interfejsu API do aplikacji frontonu sieci Web. [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs)Klasa jest udostępniana między obiema aplikacjami.

Razem te projekty składają się z wymaganych części Utwórz aplikację zestawu sieci Web Blazor działającą w przeglądarce obsługiwanej przez zaplecze interfejsu API.

## <a name="fallback-route"></a>Trasa rezerwowa

Aplikacja uwidacznia adresy URL, takie jak _/Counter_ i _/fetchdata_ , które mapują do określonych tras aplikacji. Ponieważ ta aplikacja jest zaimplementowana jako aplikacja jednostronicowa, każda trasa jest obsługiwana w pliku _index.html_ . Aby upewnić się, że żądanie dotyczące dowolnej ścieżki zwróci _index.html_ , zaimplementowana jest [trasa rezerwowa](./routes.md#fallback-routes) w _routes.js_ pliku znalezionym w folderze _wwwroot_ projektu klienta.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Powyższa konfiguracja gwarantuje, że żądania kierowane do dowolnej trasy w aplikacji zwracają stronę _index.html_ .

## <a name="create-a-repository"></a>Tworzenie repozytorium

W tym artykule wykorzystano repozytorium szablonów usługi GitHub, aby ułatwić rozpoczęcie pracy. Szablon zawiera aplikacje Starter wdrożone w usłudze Azure static Web Apps.

1. Upewnij się, że zalogowano się do usługi GitHub, i przejdź do następującej lokalizacji, aby utworzyć nowe repozytorium:
    - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Nazwij repozytorium **My-First-static-blazor-App**

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

Po utworzeniu repozytorium Utwórz statyczną aplikację sieci Web na podstawie Azure Portal.

1. Przejdź do [Azure Portal](https://portal.azure.com)
1. Wybierz pozycję **Utwórz zasób**
1. Wyszukaj usługę **Static Web Apps**
1. Wybierz **Web Apps statyczny (wersja zapoznawcza)**
1. Wybierz pozycję **Utwórz**

W sekcji _podstawowe_ Zacznij od skonfigurowania nowej aplikacji i powiązania jej z repozytorium GitHub.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Karta Podstawowe":::

1. Wybierz swoją _subskrypcję platformy Azure_
1. Wybierz lub Utwórz nową _grupę zasobów_
1. Nazwij aplikację **My-First-static-blazor-App**
    - Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`.
1. Wybierz _region_ znajdujący się najbliżej siebie
1. Wybierz **bezpłatną** _jednostkę SKU_
1. Wybierz przycisk **Zaloguj się przy użyciu usługi GitHub** i Uwierzytelnij się przy użyciu usługi GitHub

Po zalogowaniu się za pomocą usługi GitHub wprowadź informacje o repozytorium.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Szczegóły repozytorium":::

1. Wybierz preferowaną _organizację_
1. Wybierz pozycję **My-First-static-blazor-App** z listy rozwijanej _repozytorium_
1. Wybierz pozycję **główna** z listy rozwijanej _rozgałęzienie_

    Jeśli nie widzisz żadnych repozytoriów, może być konieczne autoryzowanie Web Apps statycznej platformy Azure w usłudze GitHub. Przejdź do repozytorium GitHub i przejdź do pozycji **ustawienia > aplikacje > autoryzowane aplikacje OAuth**, wybierz pozycję **statyczne Web Apps platformy Azure**, a następnie wybierz pozycję **Udziel**. W przypadku repozytoriów organizacji musisz być właścicielem organizacji, aby przyznać uprawnienia.

1. W sekcji _szczegóły kompilacji_ Dodaj szczegóły konfiguracji specyficzne dla Blazor.

    - Wybierz pozycję **Blazor** z listy rozwijanej _kompilacje predefiniowane_ i Zachowaj wszystkie wartości domyślne.

1. Wybierz pozycję **Przeglądanie + tworzenie**.

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="Przycisk tworzenia przeglądu":::

1. Wybierz pozycję **Utwórz**.

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="Przycisk Utwórz":::

1. Wybierz pozycję **Przejdź do zasobu**.

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Przycisk Przejdź do zasobu":::

## <a name="view-the-website"></a>Wyświetlanie witryny sieci Web

Istnieją dwa aspekty wdrażania aplikacji statycznej. Pierwszy ustanawia podstawowe zasoby platformy Azure tworzące aplikację. Drugi to przepływ pracy funkcji GitHub Actions, który kompiluje i publikuje aplikację.

Aby można było przejść do nowej lokacji statycznej, kompilacja wdrożenia musi być najpierw zakończona.

W oknie przeglądu Web Apps statycznego zostanie wyświetlona seria linków, które pomagają w współpracy z aplikacją sieci Web.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Okno przegląd":::

1. Kliknięcie transparentu, który brzmi, _kliknij tutaj, aby sprawdzić stan uruchomionych akcji usługi GitHub_ , spowoduje przejście do akcji GitHub uruchomionych względem repozytorium. Po sprawdzeniu, czy zadanie wdrożenia zostało zakończone, możesz przejść do witryny sieci Web za pośrednictwem wygenerowanego adresu URL.

2. Po zakończeniu przepływu pracy akcji usługi GitHub możesz wybrać link _adresu URL_ , aby otworzyć witrynę sieci Web na nowej karcie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, możesz usunąć wystąpienie usługi Azure static Web Apps, wykonując następujące czynności:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Wyszukaj **aplikację My-First-static-blazor-App** na górnym pasku wyszukiwania
1. Wybierz nazwę aplikacji
1. Wybierz przycisk **Usuń**
1. Wybierz pozycję **tak** , aby potwierdzić akcję usuwania

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uwierzytelnianie i autoryzacja](./authentication-authorization.md)
