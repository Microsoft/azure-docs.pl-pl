---
title: Informacje o pojęciach repozytorium modelu urządzenia | Microsoft Docs
description: Jako deweloper rozwiązania lub Specjalista IT zapoznaj się z podstawowymi pojęciami dotyczącymi repozytorium modelu urządzeń.
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4e15ef5256c1552fc8ab7fb9bd84f15bb3433834
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131364"
---
# <a name="device-model-repository"></a>Repozytorium modelu urządzenia

Repozytorium modeli urządzeń (DMR) umożliwia konstruktorom urządzeń zarządzanie i udostępnianie modeli urządzeń Plug and Play IoT. Modele urządzeń to JSON LD dokumenty zdefiniowane przy użyciu [języka Digital bliźniaczych reprezentacji Modeling Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

DMR definiuje wzorzec do przechowywania interfejsów DTDL w strukturze folderów na podstawie identyfikatora modelu sznurka urządzenia (DTMI). Interfejs można zlokalizować w DMR, konwertując DTMI na ścieżkę względną. Na przykład `dtmi:com:example:Thermostat;1` DTMI tłumaczy na `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Repozytorium modelu urządzenia publicznego

Firma Microsoft udostępnia publiczną DMR z następującymi cechami:

- Modele nadzorowane. Firma Microsoft przegląda i zatwierdza wszystkie dostępne interfejsy przy użyciu przepływu pracy weryfikacji żądania ściągnięcia w serwisie GitHub.
- Niezmienności.  Po opublikowaniu nie można zaktualizować interfejsu.
- Skalowanie w poziomie. Firma Microsoft zapewnia wymaganą infrastrukturę do tworzenia bezpiecznego, skalowalnego punktu końcowego, który umożliwia publikowanie i korzystanie z modeli urządzeń.

## <a name="custom-device-model-repository"></a>Niestandardowe repozytorium modeli urządzeń

Tego samego wzorca DMR można użyć w dowolnym nośniku magazynującym, takim jak lokalny system plików lub niestandardowe serwery sieci Web HTTP, aby utworzyć niestandardową DMR. Modele urządzeń można pobrać z niestandardowego DMR w taki sam sposób jak w przypadku publicznego DMR po prostu przez zmianę podstawowego adresu URL służącego do uzyskiwania dostępu do DMR.

> [!NOTE]
> Firma Microsoft oferuje narzędzia do sprawdzania poprawności modeli urządzeń w DMR publicznym. Można ponownie użyć tych narzędzi w niestandardowych repozytoriach.

## <a name="public-models"></a>Modele publiczne

Modele urządzeń publicznych przechowywane w repozytorium modelu są dostępne dla wszystkich użytkowników w celu korzystania z nich i integrowania ich w aplikacjach. Modele urządzeń publicznych umożliwiają twórcom urządzeń, którzy mają możliwość udostępniania i ponownego wykorzystywania modeli urządzeń Plug and Play IoT w otwartym systemie ekonomicznym dla konstruktorów i deweloperów rozwiązań.

Zapoznaj się z sekcją [Publikowanie modelu](#publish-a-model) , aby uzyskać instrukcje dotyczące publikowania modelu w repozytorium modeli w celu udostępnienia go jako publicznego.

Użytkownicy mogą przeglądać, wyszukiwać i wyświetlać interfejsy publiczne z oficjalnego [repozytorium GitHub](https://github.com/Azure/iot-plugandplay-models).

Wszystkie interfejsy w `dtmi` folderach są również dostępne w publicznym punkcie końcowym [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Rozpoznaj modele

Aby programowo uzyskać dostęp do tych interfejsów, należy skonwertować DTMI na ścieżkę względną, której można użyć do wysyłania zapytań do publicznego punktu końcowego. Poniższy przykład kodu pokazuje, jak to zrobić:

Aby skonwertować DTMI na ścieżkę bezwzględną, używamy `DtmiToPath` funkcji z `IsValidDtmi` :

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Mając ścieżkę i podstawowy adres URL repozytorium, możemy uzyskać interfejs:

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>Publikowanie modelu

> [!Important]
> Musisz mieć konto usługi GitHub, aby móc przesyłać modele do DMR publicznego.

1. Rozwidlenie publicznego repozytorium GitHub: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Klonuj repozytorium rozwidlenia. Opcjonalnie Utwórz nową gałąź, aby zachować zmiany odizolowane od `main` gałęzi.
1. Dodaj nowe interfejsy do `dtmi` folderu przy użyciu konwencji folderu/filename. Zobacz Narzędzie [Dodaj model](#add-model) .
1. Sprawdź poprawność modeli urządzeń lokalnie, używając [skryptów do walidacji zmian](#validate-files) .
1. Zatwierdź zmiany lokalnie i wypchnij do Twojego rozwidlenia.
1. Z rozwidlenia Utwórz żądanie ściągnięcia, które odwołuje się do `main` gałęzi. Zobacz [tworzenie dokumentacji problemu lub żądania ściągnięcia](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Zapoznaj się z [wymaganiami dotyczącymi żądań ściągnięcia](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

Żądanie ściągnięcia wyzwala serię akcji usługi GitHub, które weryfikują nowe przesłane interfejsy i upewnij się, że żądanie ściągnięcia spełni wszystkie testy.

Firma Microsoft odpowie na żądanie ściągnięcia ze wszystkimi kontrolami w ciągu trzech dni roboczych.

### <a name="add-model"></a>Dodaj model

Poniższe kroki pokazują, jak skrypt add-model.js ułatwia dodawanie nowego interfejsu. Ten skrypt wymaga Node.js do uruchomienia:

1. W wierszu polecenia przejdź do lokalnego repozytorium git
1. Uruchom polecenie `npm install`
1. Uruchom polecenie `npm run add-model <path-to-file-to-add>`

Obejrzyj dane wyjściowe konsoli dla wszystkich komunikatów o błędach.

### <a name="local-validation"></a>Weryfikacja lokalna

Te same sprawdzenia poprawności można uruchomić lokalnie przed przesłaniem żądania ściągnięcia, aby ułatwić zdiagnozowanie problemów z wyprzedzeniem.

#### <a name="validate-files"></a>Validate — pliki

`npm run validate-files <file1.json> <file2.json>` sprawdza, czy ścieżka pliku jest zgodna z oczekiwanymi nazwami folderów i plików.

#### <a name="validate-ids"></a>Weryfikuj — identyfikatory

`npm run validate-ids <file1.json> <file2.json>` sprawdza, czy wszystkie identyfikatory zdefiniowane w dokumencie używają tego samego katalogu głównego, który jest IDENTYFIKATORem głównym.

#### <a name="validate-deps"></a>Validate-deps

`npm run validate-deps <file1.json> <file2.json>` sprawdza, czy wszystkie zależności są dostępne w `dtmi` folderze.

#### <a name="validate-models"></a>Weryfikuj — modele

Możesz uruchomić [przykład weryfikacji DTDL](https://github.com/Azure-Samples/DTDL-Validator) , aby sprawdzić poprawność modeli urządzeń lokalnie.

## <a name="next-steps"></a>Następne kroki

Sugerowanym następnym krokiem jest zapoznanie się z [architekturą Plug and Play IoT](concepts-architecture.md).
