---
title: Co to jest usługa Azure App Configuration?
description: Zapoznaj się z omówieniem usługi Azure App Configuration. Informacje o tym, dlaczego warto korzystać z konfiguracji aplikacji, i dowiedzieć się, jak można jej używać.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: f38b8de37e84cd8a65c5a5caadf8d3b6b71ce455
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88587076"
---
# <a name="what-is-azure-app-configuration"></a>Co to jest usługa Azure App Configuration?

Konfiguracja aplikacji platformy Azure umożliwia centralne zarządzanie ustawieniami aplikacji i flagami funkcji. Nowoczesne programy, szczególnie programy działające w chmurze, zwykle mają wiele składników, które są rozpowszechniane. Posiadanie ustawień konfiguracji w ramach tych składników może powodować występowanie błędów podczas wdrażania aplikacji, których diagnozowanie będzie bardzo skomplikowane. Użyj konfiguracji aplikacji, aby przechowywać wszystkie ustawienia aplikacji i zabezpieczyć ich dostęp w jednym miejscu.

## <a name="why-use-app-configuration"></a>Dlaczego warto używać konfiguracji aplikacji?

Aplikacje oparte na chmurze są często uruchamiane na wielu maszynach wirtualnych lub kontenerach w wielu regionach i korzystają z wielu usług zewnętrznych. Tworzenie niezawodnej i skalowalnej aplikacji w środowisku rozproszonym daje znaczący problem.

Różne metody programowania pomagają deweloperom w coraz większym stopniu złożoności tworzenia aplikacji. Na przykład [aplikacja 12-czynnikowa](https://12factor.net/) opisuje wiele dobrze przetestowanych wzorców architektonicznych i najlepszych rozwiązań do użycia z aplikacjami w chmurze. Jednym z kluczowych zaleceń z tego przewodnika jest oddzielenia konfiguracji od kodu. Ustawienia konfiguracji aplikacji powinny być przechowywane poza plikiem wykonywalnym i odczytywane ze środowiska uruchomieniowego lub zewnętrznego źródła.

Podczas gdy dowolna aplikacja może korzystać z konfiguracji aplikacji, następujące przykłady są typami aplikacji, które korzystają z jej użycia:

* Mikrousługi oparte na usłudze Azure Kubernetes Service, platformie Azure Service Fabric lub innych aplikacjach kontenerowych wdrożonych w jednym lub większej liczbie lokalizacje geograficzne
* Aplikacje bezserwerowe, które obejmują Azure Functions lub inne bezstanowe aplikacje do obliczeń sterowane zdarzeniami
* Potok ciągłego wdrażania

Usługa App Configuration zapewnia następujące korzyści:

* W pełni zarządzana usługa, którą można skonfigurować w kilka minut
* Elastyczne reprezentacje i mapowania kluczy
* Tagowanie z etykietami
* Odtwarzanie ustawień do punktu w czasie
* Dedykowany interfejs użytkownika do zarządzania flagami funkcji
* Porównanie dwóch zestawów konfiguracji w wymiarach zdefiniowanych przez użytkownika
* Ulepszone zabezpieczenia za pomocą tożsamości zarządzanych przez platformę Azure
* Szyfrowanie poufnych informacji w czasie spoczynku i w tranzycie
* Natywna integracja z popularnymi strukturami

Konfiguracja aplikacji uzupełnia [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), które są używane do przechowywania wpisów tajnych aplikacji. Konfiguracja aplikacji ułatwia implementowanie następujących scenariuszy:

* Scentralizowanie zarządzania i dystrybucji hierarchicznych danych konfiguracyjnych dla różnych środowisk i lokalizacje geograficzne
* Dynamicznie Zmieniaj ustawienia aplikacji bez konieczności ponownego wdrażania lub ponownej instalacji aplikacji
* Dostępność funkcji kontroli w czasie rzeczywistym

## <a name="use-app-configuration"></a>Korzystanie z konfiguracji aplikacji

Najprostszym sposobem dodawania magazynu konfiguracji aplikacji do aplikacji jest użycie biblioteki klienckiej udostępnionej przez firmę Microsoft. Następujące metody są dostępne do łączenia się z aplikacją, w zależności od wybranego języka i struktury

| Język programowania i platforma | Jak nawiązać połączenie |
|---|---|
| .NET Core i ASP.NET Core | Dostawca konfiguracji aplikacji dla platformy .NET Core |
| .NET Framework i ASP.NET | Konstruktor konfiguracji aplikacji dla platformy .NET |
| Java Spring | Klient konfiguracji aplikacji dla chmury wiosennej |
| Inne | Interfejs API REST konfiguracji aplikacji |

## <a name="next-steps"></a>Następne kroki

* [ASP.NET Core — Szybki Start](./quickstart-aspnet-core-app.md)
* [.NET Core — Szybki Start](./quickstart-dotnet-core-app.md)
* [.NET Framework — Szybki Start](./quickstart-dotnet-app.md)
* [Azure Functions — Szybki Start](./quickstart-azure-functions-csharp.md)
* [Środowisko Java — Szybki Start](./quickstart-java-spring-app.md)
* [ASP.NET Core flagi funkcji — Szybki Start](./quickstart-feature-flag-aspnet-core.md)
* [Flaga funkcji rozruchu sprężynowego — Szybki Start](./quickstart-feature-flag-spring-boot.md)
