---
title: Włącz etapowe wdrażanie funkcji dla docelowych odbiorców
titleSuffix: Azure App Configuration
description: Dowiedz się, jak włączyć etapowe wdrażanie funkcji dla docelowych odbiorców
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929688"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>Włącz etapowe wdrażanie funkcji dla docelowych odbiorców

Flagi funkcji umożliwiają dynamiczne aktywowanie lub dezaktywowanie funkcji w aplikacji. Filtry funkcji określają stan flagi funkcji zawsze wtedy, gdy jest ona szacowana. `Microsoft.FeatureManagement`Biblioteka zawiera `TargetingFilter` , która umożliwia flagi funkcji dla określonej listy użytkowników i grup lub dla określonego procentu użytkowników. `TargetingFilter` jest "Stick." Oznacza to, że gdy pojedynczy użytkownik otrzyma funkcję, będzie nadal widział tę funkcję we wszystkich przyszłych żądaniach. Możesz użyć, `TargetingFilter` Aby włączyć funkcję dla konkretnego konta podczas pokazu, aby stopniowo wdrożyć nowe funkcje dla użytkowników w różnych grupach lub "pierścieniach" i wiele innych.

W tym artykule dowiesz się, jak wdrożyć nową funkcję w ASP.NET Core aplikacji sieci Web dla określonych użytkowników i grup za pomocą `TargetingFilter` usługi Azure App Configuration.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Tworzenie aplikacji sieci Web przy użyciu flag funkcji i uwierzytelniania

Aby wdrożyć funkcje w oparciu o użytkowników i grupy, potrzebna jest aplikacja sieci Web, która umożliwia użytkownikom logowanie się.

1. Utwórz aplikację sieci Web, która uwierzytelnia się w lokalnej bazie danych przy użyciu następującego polecenia:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Skompiluj i Uruchom, a następnie wybierz link **zarejestruj** w prawym górnym rogu, aby utworzyć nowe konto użytkownika. Użyj adresu e-mail `test@contoso.com` . Na ekranie **potwierdzenie rejestracji** wybierz **pozycję kliknij tutaj, aby potwierdzić swoje konto**.

1. Postępuj zgodnie z instrukcjami w [przewodniku szybki start: Dodawanie flag funkcji do aplikacji ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) , aby dodać flagę funkcji do nowej aplikacji sieci Web.

1. Przełącz flagę funkcji w obszarze Konfiguracja aplikacji. Sprawdź, czy ta akcja kontroluje widoczność elementu **beta** na pasku nawigacyjnym.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>Aktualizowanie kodu aplikacji sieci Web w celu użycia TargetingFilter

W tym momencie można użyć flagi funkcji, aby włączyć lub wyłączyć tę `Beta` funkcję dla wszystkich użytkowników. Aby włączyć flagę funkcji dla niektórych użytkowników podczas jej wyłączania dla innych osób, zaktualizuj kod do użycia `TargetingFilter` . W tym przykładzie adres e-mail zalogowanego użytkownika będzie używany jako identyfikator użytkownika, a nazwa domeny jest częścią adresu e-mail jako grupy. Dodasz użytkownika i grupę do `TargetingContext` . `TargetingFilter`Używa tego kontekstu do określenia stanu flagi funkcji dla każdego żądania.

1. Zaktualizuj do najnowszej wersji `Microsoft.FeatureManagement.AspNetCore` pakietu.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. Dodaj plik *TestTargetingContextAccessor.cs* :

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. W *Startup.cs* Dodaj odwołanie do przestrzeni nazw *Microsoft. FeatureManagement. FeatureFilters* :

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Zaktualizuj metodę *ConfigureServices* , aby zarejestrować się `TargetingFilter` , po wywołaniu `AddFeatureManagement()` :

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Zaktualizuj metodę *ConfigureServices* , aby dodać `TestTargetingContextAccessor` utworzony we wcześniejszej sekcji do kolekcji usług. *TargetingFilter* używa jej do określania kontekstu określania wartości docelowej przy każdej ocenie flagi funkcji.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

Cała Metoda *ConfigureServices* będzie wyglądać następująco:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>Zaktualizuj flagę funkcji, aby używać TargetingFilter

1. W Azure Portal przejdź do magazynu konfiguracji aplikacji i wybierz pozycję **Menedżer funkcji**.

1. Wybierz menu kontekstowe dla flagi funkcji *beta* , która została utworzona w przewodniku Szybki Start. Wybierz pozycję **Edit** (Edytuj).

    > [!div class="mx-imgBorder"]
    > ![Edytuj flagę funkcji beta](./media/edit-beta-feature-flag.png)

1. Na ekranie **Edycja** zaznacz pole wyboru **Włącz flagę funkcji** , jeśli nie zostało ono jeszcze wybrane. Następnie zaznacz pole wyboru **Użyj filtru funkcji** .

1. Wybierz przycisk radiowy **określania wartości docelowej** .

1. Wybierz następujące opcje:

    - **Wartość domyślna**: 0
    - **Grupy**: wprowadź **nazwę** _contoso.com_ i **procent** _50_
    - **Użytkownicy**: `test@contoso.com`

    Ekran filtru funkcji będzie wyglądać następująco:

    > [!div class="mx-imgBorder"]
    > ![Flaga funkcji warunkowej](./media/feature-flag-filter-enabled.png)

    Te ustawienia powodują następujące zachowanie:

    - Flaga funkcji jest zawsze włączona dla użytkownika `test@contoso.com` , ponieważ `test@contoso.com` jest wymieniona w sekcji _Użytkownicy_ .
    - Flaga funkcji jest włączona dla 50% innych użytkowników w grupie _contoso.com_ , ponieważ _contoso.com_ znajduje się w sekcji _grup_ z _wartością procentową_ _50_.
    - Funkcja jest zawsze wyłączona dla wszystkich innych użytkowników, ponieważ _Domyślna wartość procentowa_ jest ustawiona na _0_.

1. Wybierz pozycję **Zastosuj** , aby zapisać te ustawienia i powrócić do ekranu programu **Feature Manager** .

1. **Filtr funkcji** dla flagi funkcji jest teraz wyświetlany jako element *docelowy*. Ten stan wskazuje, że flaga funkcji zostanie włączona lub wyłączona dla poszczególnych żądań na podstawie kryteriów wymuszanych przez filtr funkcji *określania wartości docelowej* .

## <a name="targetingfilter-in-action"></a>TargetingFilter w działaniu

Aby zobaczyć efekty tej flagi funkcji, skompiluj i uruchom aplikację. Początkowo element *beta* nie jest wyświetlany na pasku narzędzi, ponieważ domyślna opcja _procentowa_ jest ustawiona na 0.

Teraz Zaloguj się jako `test@contoso.com` przy użyciu hasła ustawionego podczas rejestrowania. Element *beta* zostanie teraz wyświetlony na pasku narzędzi, ponieważ `test@contoso.com` jest określony jako użytkownik będący użytkownikiem.

Poniższe wideo pokazuje to zachowanie w działaniu.

> [!div class="mx-imgBorder"]
> ![TargetingFilter w działaniu](./media/feature-flags-targetingfilter.gif)

Możesz utworzyć dodatkowych użytkowników z `@contoso.com` adresami e-mail, aby zobaczyć zachowanie ustawień grupy. 50% tych użytkowników zobaczy element *beta* . Pozostałe 50% nie zobaczy elementu w *wersji beta* .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Omówienie zarządzania funkcjami](./concept-feature-management.md)