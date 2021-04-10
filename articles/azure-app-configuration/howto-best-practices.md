---
title: Najlepsze rozwiązania dotyczące konfiguracji aplikacji platformy Azure | Microsoft Docs
description: Zapoznaj się z najlepszymi rozwiązaniami przy użyciu usługi Azure App Configuration. Omówione tematy obejmują grupy kluczy, kompozycje klucza i wartości, ładowania początkowego konfiguracji aplikacji.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 33661eafee6b180819b18d9a9a980eff1e2aeceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371553"
---
# <a name="azure-app-configuration-best-practices"></a>Najlepsze rozwiązania dotyczące konfiguracji aplikacji platformy Azure

W tym artykule omówiono typowe wzorce i najlepsze rozwiązania w przypadku korzystania z usługi Azure App Configuration.

## <a name="key-groupings"></a>Grupowanie kluczy

Konfiguracja aplikacji oferuje dwie opcje organizowania kluczy:

* Prefiksy kluczy
* Etykiety

Aby zgrupować klucze, można użyć jednej lub obu opcji.

*Prefiksy kluczy* są początkowymi częściami kluczy. Można logicznie grupować zestaw kluczy przy użyciu tego samego prefiksu w nazwach. Prefiksy mogą zawierać wiele składników połączonych przez ogranicznik, `/` na przykład podobne do ścieżki URL, w celu utworzenia przestrzeni nazw. Takie hierarchie są przydatne w przypadku przechowywania kluczy dla wielu aplikacji, usług składowych i środowisk w jednym magazynie konfiguracji aplikacji.

Ważne jest, aby pamiętać, że klucze są do których odwołuje się kod aplikacji w celu pobrania wartości odpowiednich ustawień. Klucze nie powinny się zmieniać lub w przeciwnym razie trzeba będzie zmodyfikować swój kod.

*Etykiety* są atrybutem kluczy. Są one używane do tworzenia wariantów klucza. Można na przykład przypisać etykiety do wielu wersji klucza. Wersja może być iteracją, środowiskiem lub innymi informacjami kontekstowymi. Aplikacja może zażądać całkowicie różnego zestawu wartości kluczowych, określając inną etykietę. W związku z tym wszystkie odwołania do kluczy pozostaną niezmienione w kodzie.

## <a name="key-value-compositions"></a>Kompozycje klucz-wartość

Konfiguracja aplikacji traktuje wszystkie klucze przechowywane z nią jako jednostki niezależne. W obszarze Konfiguracja aplikacji nie jest podejmowana próba wywnioskowania żadnej relacji między kluczami lub dziedziczenia wartości kluczy na podstawie ich hierarchii. Można jednak agregować wiele zestawów kluczy, używając etykiet sprzężonych z prawidłowym stosem konfiguracji w kodzie aplikacji.

Spójrzmy na przykład. Załóżmy, że masz ustawienie o nazwie **Asset1**, którego wartość może się różnić w zależności od środowiska deweloperskiego. Tworzysz klucz o nazwie "Asset1" z pustą etykietą i etykietą o nazwie "Development". W pierwszej etykiecie zostanie umieszczona wartość domyślna dla **Asset1** i zostanie umieszczona określona wartość "Programowanie" w tym drugim.

W kodzie należy najpierw pobrać wartości klucza bez żadnych etykiet, a następnie pobrać ten sam zestaw wartości klucza po raz drugi z etykietą "Programowanie". Po pobraniu wartości po raz drugi poprzednie wartości kluczy są zastępowane. System konfiguracji .NET Core umożliwia "stos" wielu zestawów danych konfiguracji na siebie nawzajem. Jeśli klucz istnieje w więcej niż jednym zestawie, używany jest ostatni zestaw, który zawiera. Dzięki nowoczesnej strukturze programistycznej, takiej jak .NET Core, można bezpłatnie skorzystać z tej możliwości tworzenia stosu, jeśli używasz natywnego dostawcy konfiguracji do uzyskiwania dostępu do konfiguracji aplikacji. Poniższy fragment kodu przedstawia sposób implementacji stosu w aplikacji .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Aby włączyć różne konfiguracje dla różnych środowisk, należy użyć etykiet](./howto-labels-aspnet-core.md) .

## <a name="app-configuration-bootstrap"></a>Konfiguracja aplikacji — Bootstrap

Aby uzyskać dostęp do magazynu konfiguracji aplikacji, można użyć jego parametrów połączenia, który jest dostępny w Azure Portal. Ponieważ parametry połączenia zawierają informacje o poświadczeniach, są one uznawane za wpisy tajne. Te klucze tajne muszą być przechowywane w Azure Key Vault, a kod musi być uwierzytelniany do Key Vault, aby je pobrać.

Lepszym rozwiązaniem jest użycie funkcji tożsamości zarządzane w programie Azure Active Directory. W przypadku tożsamości zarządzanych do magazynu konfiguracji aplikacji potrzebny jest tylko adres URL punktu końcowego konfiguracji aplikacji. Możesz osadzić adres URL w kodzie aplikacji (na przykład w *appsettings.js* w pliku). Szczegóły można znaleźć w temacie [integracja z tożsamościami zarządzanymi przez platformę Azure](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Dostęp do aplikacji lub funkcji do konfiguracji aplikacji

Możesz zapewnić dostęp do konfiguracji aplikacji dla aplikacji lub funkcji sieci Web przy użyciu dowolnej z następujących metod:

* Za pomocą Azure Portal wprowadź parametry połączenia do magazynu konfiguracji aplikacji w ustawieniach aplikacji App Service.
* Zapisz parametry połączenia z magazynem konfiguracji aplikacji w Key Vault i [odwołując się do niego z App Service](../app-service/app-service-key-vault-references.md).
* Użyj tożsamości zarządzanych przez platformę Azure, aby uzyskać dostęp do magazynu konfiguracji aplikacji. Aby uzyskać więcej informacji, zobacz [Integrowanie z tożsamościami zarządzanymi przez platformę Azure](howto-integrate-azure-managed-service-identity.md).
* Konfiguracja wypychana z konfiguracji aplikacji do App Service. Konfiguracja aplikacji udostępnia funkcję eksportu (w Azure Portal i interfejsu wiersza polecenia platformy Azure), która wysyła dane bezpośrednio do App Service. W przypadku tej metody nie trzeba zmieniać kodu aplikacji.

## <a name="reduce-requests-made-to-app-configuration"></a>Ogranicz żądania dotyczące konfiguracji aplikacji

Nadmierne żądania konfiguracji aplikacji mogą powodować naliczanie opłat lub za użycie nadwyżkowe. Aby zmniejszyć liczbę wykonanych żądań:

* Zwiększ limit czasu odświeżania, szczególnie w przypadku, gdy wartości konfiguracyjne nie ulegają częstym zmianom. Określ nowy limit czasu odświeżania przy użyciu [ `SetCacheExpiration` metody](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Obejrzyj pojedynczy *klucz wskaźnikowy*, zamiast oglądać poszczególne klucze. Odśwież całą konfigurację tylko w przypadku zmiany klucza wskaźnikowego. Zobacz [Używanie konfiguracji dynamicznej w aplikacji ASP.NET Core](enable-dynamic-configuration-aspnet-core.md) na przykład.

* Użyj Azure Event Grid, aby otrzymywać powiadomienia o zmianach konfiguracji, a nie stale sondować o wszelkie zmiany. Aby uzyskać więcej informacji, zobacz [kierowanie zdarzeń konfiguracji aplikacji platformy Azure do punktu końcowego sieci Web](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>Importowanie danych konfiguracji do konfiguracji aplikacji

Konfiguracja aplikacji oferuje opcję zbiorczego [importowania](./howto-import-export-data.md) ustawień konfiguracji z bieżących plików konfiguracji przy użyciu Azure Portal lub interfejsu wiersza polecenia. Możesz również użyć tych samych opcji, aby wyeksportować wartości z konfiguracji aplikacji, na przykład między powiązanymi magazynami. Jeśli chcesz skonfigurować trwającą synchronizację z repozytorium GitHub, możesz użyć naszej [akcji GitHub](./concept-github-action.md) , aby móc nadal korzystać z istniejących rozwiązań kontroli źródła podczas uzyskiwania korzyści z konfiguracji aplikacji.

## <a name="multi-region-deployment-in-app-configuration"></a>Wdrożenie w ramach wieloregionu w konfiguracji aplikacji

Konfiguracja aplikacji to usługa regionalna. W przypadku aplikacji z różnymi konfiguracjami na region przechowywanie tych konfiguracji w jednym wystąpieniu może stworzyć single point of failure. Wdrożenie jednego wystąpienia konfiguracji aplikacji na region w wielu regionach może być lepszym rozwiązaniem. Może być pomocna w przypadku regionalnego odzyskiwania po awarii, wydajności i silosu zabezpieczeń. Konfiguracja według regionów poprawia również opóźnienia i używa przydzielonych przydziałów ograniczania przepustowości, ponieważ ograniczanie przepustowości odbywa się na każdym wystąpieniu. Aby zastosować rozwiązanie do rozwiązywania problemów z odzyskiwaniem po awarii, można użyć [wielu magazynów konfiguracji](./concept-disaster-recovery.md). 

## <a name="client-applications-in-app-configuration"></a>Aplikacje klienckie w konfiguracji aplikacji 

Nadmierne żądania konfiguracji aplikacji mogą powodować naliczanie opłat lub za użycie nadwyżkowe. Aplikacje wykorzystują buforowanie i inteligentne odświeżanie obecnie dostępne w celu zoptymalizowania liczby wysyłanych żądań. Ten proces może być dublowany w aplikacjach klienckich o dużym nasileniu, unikając bezpośrednich połączeń z magazynem konfiguracji. Zamiast tego aplikacje klienckie nawiązują połączenie z usługą niestandardową, a ta usługa komunikuje się z magazynem konfiguracji. To rozwiązanie proxy może zapewnić, że aplikacje klienckie nie zbliżają się do limitu ograniczania w magazynie konfiguracji. Aby uzyskać więcej informacji na temat ograniczania przepustowości, zobacz [często zadawane pytania](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration).  

## <a name="next-steps"></a>Następne kroki

* [Klucze i wartości](./concept-key-value.md)