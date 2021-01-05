---
title: Przegląd wersji Durable Functions — Azure Functions
description: Dowiedz się więcej o wersjach Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: azfuncdf
ms.openlocfilehash: c4d10bab06428295bbc8c5319bd47787d7b1fb34
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763374"
---
# <a name="durable-functions-versions-overview"></a>Omówienie wersji Durable Functions

*Durable Functions* to rozszerzenie [Azure Functions](../functions-overview.md) i [Azure WebJobs](../../app-service/webjobs-create.md) , które pozwala pisać funkcje stanowe w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem. Jeśli nie znasz jeszcze Durable Functions, zapoznaj się z [dokumentacją dotyczącą przeglądu](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nowe funkcje w 2. x

W tej sekcji opisano funkcje Durable Functions, które zostały dodane w wersji 2. x.

### <a name="durable-entities"></a>Trwałe jednostki

W Durable Functions 2. x wprowadziliśmy nową koncepcję [funkcji Entity](durable-functions-entities.md) .

Funkcje Entity definiują operacje umożliwiające odczytywanie i aktualizowanie małych fragmentów stanu, znanych jako *jednostek trwałych*. Podobnie jak funkcje programu Orchestrator, funkcje jednostki są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczem jednostki*. W przeciwieństwie do funkcji programu Orchestrator, funkcje jednostek nie mają żadnych ograniczeń związanych z kodem. Funkcje jednostek również zarządzają stanem jawnie, a nie niejawnie reprezentującą stan za pośrednictwem przepływu sterowania.

Aby dowiedzieć się więcej, zobacz artykuł dotyczący [jednostek trwałych](durable-functions-entities.md) .

### <a name="durable-http"></a>Trwałe HTTP

W Durable Functions 2. x wprowadziliśmy nową, [trwałą funkcję http](durable-functions-http-features.md#consuming-http-apis) , która umożliwia:

* Wywołaj interfejsy API protokołu HTTP bezpośrednio z funkcji aranżacji (z pewnymi opisanymi ograniczeniami).
* Zaimplementuj automatyczne sondowanie stanu protokołu HTTP 202 po stronie klienta.
* Wbudowana obsługa [tożsamości zarządzanych przez platformę Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Aby dowiedzieć się więcej, zobacz artykuł dotyczący [funkcji protokołu HTTP](durable-functions-http-features.md#consuming-http-apis) .

## <a name="migrate-from-1x-to-2x"></a>Migrowanie z 1. x do 2. x

W tej sekcji opisano sposób migrowania istniejącej wersji 1. x Durable Functions do wersji 2. x w celu skorzystania z nowych funkcji.

### <a name="upgrade-the-extension"></a>Uaktualnij rozszerzenie

Zainstaluj najnowszą wersję 2. x rozszerzenia powiązań Durable Functions w projekcie.

#### <a name="javascript-python-and-powershell"></a>JavaScript, Python i PowerShell

Durable Functions 2. x jest dostępny w wersji 2. x [pakietu rozszerzenia Azure Functions](../functions-bindings-register.md#extension-bundles).

Obsługa języka Python w Durable Functions wymaga Durable Functions 2. x.

Aby zaktualizować wersję pakietu rozszerzenia w projekcie, Otwórz host.jsna i zaktualizuj `extensionBundle` sekcję do korzystania z wersji 2. x ( `[2.*, 3.0.0)` ).

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

> [!NOTE]
> Jeśli Visual Studio Code nie wyświetla poprawnych szablonów po zmianie wersji pakietu rozszerzenia, należy załadować ponownie okno, uruchamiając polecenie *Deweloper: Załaduj ponownie okno* (<kbd>Ctrl + R</kbd> w systemach Windows i Linux, <kbd>Command + R</kbd> on macOS).

#### <a name="net"></a>.NET

Zaktualizuj projekt .NET, aby użyć najnowszej wersji [rozszerzenia powiązania Durable Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask).

Aby uzyskać więcej informacji, zobacz [Rejestrowanie rozszerzeń powiązań Azure Functions](../functions-bindings-register.md#local-csharp) .

### <a name="update-your-code"></a>Aktualizowanie kodu

Durable Functions 2. x wprowadza kilka istotnych zmian. Aplikacje Durable Functions 1. x nie są zgodne z Durable Functions 2. x bez zmian w kodzie. W tej sekcji przedstawiono niektóre zmiany, które należy wykonać podczas uaktualniania funkcji w wersji 1. x do 2. x.

#### <a name="hostjson-schema"></a>Host.jsw schemacie

Durable Functions 2. x używa nowego host.jsna schemacie. Główne zmiany z 1. x obejmują:

* `"storageProvider"` (i `"azureStorage"` podsekcję) dla konfiguracji specyficznej dla magazynu.
* `"tracing"` dla konfiguracji śledzenia i rejestrowania.
* `"notifications"` (i `"eventGrid"` podsekcję) dla konfiguracji powiadomień usługi Event Grid.

Aby uzyskać szczegółowe informacje, zobacz [host.jsDurable Functions w dokumentacji referencyjnej](durable-functions-bindings.md#durable-functions-2-0-host-json) .

#### <a name="default-taskhub-name-changes"></a>Domyślne zmiany nazwy taskhub

W wersji 1. x, jeśli nazwa centrum zadań nie została określona w host.json, miało wartość domyślną "DurableFunctionsHub". W wersji 2. x domyślna nazwa centrum zadań jest teraz pochodną nazwy aplikacji funkcji. W związku z tym, jeśli nie określisz nazwy centrum zadań podczas uaktualniania do wersji 2. x, kod będzie działać z nowym centrum zadań, a wszystkie aranżacje w locie nie będą już miały przetwarzania aplikacji. Aby obejść ten krok, można jawnie ustawić nazwę centrum zadań jako wartość domyślną v1. x "DurableFunctionsHub" lub skorzystać z naszych [wskazówek dotyczących wdrażania bez przestojów](durable-functions-zero-downtime-deployment.md) , aby uzyskać szczegółowe informacje na temat sposobu obsługi istotnych zmian dla aranżacji w locie.

#### <a name="public-interface-changes-net-only"></a>Zmiany interfejsu publicznego (tylko platforma .NET)

W wersji 1. x różne obiekty _kontekstu_ obsługiwane przez Durable Functions mają abstrakcyjne klasy bazowe przeznaczone do użycia w testach jednostkowych. W ramach Durable Functions 2. x te abstrakcyjne klasy bazowe są zastępowane interfejsami.

Poniższa tabela przedstawia główne zmiany:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` lub `IDurableClient` |
| `DurableOrchestrationContext` lub `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` lub `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

W przypadku, gdy abstrakcyjna klasa bazowa zawiera metody wirtualne, te metody wirtualne zostały zastąpione metodami rozszerzenia zdefiniowanymi w `DurableContextExtensions` .

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.jszmian (skrypt JavaScript i C#)

W Durable Functions 1. x powiązanie klienta aranżacji używa programu `type` z `orchestrationClient` . Zamiast tego używa programu w wersji 2. x `durableClient` .

#### <a name="raise-event-changes"></a>Zgłoś zmiany zdarzenia

W Durable Functions 1. x wywołanie interfejsu API [zdarzenia](durable-functions-external-events.md#send-events) wywołania i określenie wystąpienia, które nie istniało w wyniku błędu dyskretnego. Począwszy od 2. x, podnoszenie zdarzenia do nieistniejącej aranżacji spowoduje wyjątek.
