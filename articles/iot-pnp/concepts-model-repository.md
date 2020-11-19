---
title: Informacje o pojęciach repozytorium modelu urządzenia | Microsoft Docs
description: Jako deweloper rozwiązania lub Specjalista IT zapoznaj się z podstawowymi pojęciami dotyczącymi repozytorium modelu urządzeń.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b567efe2541bb33c905def73bb78398799b4ed69
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920546"
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

Użyj tego samego wzorca DMR, aby utworzyć niestandardowe DMR w dowolnym nośniku magazynującym, takim jak lokalny system plików lub niestandardowe serwery sieci Web HTTP. Modele urządzeń można pobrać z niestandardowego DMR w taki sam sposób jak w przypadku publicznego DMR, zmieniając podstawowy adres URL używany do uzyskiwania dostępu do DMR.

> [!NOTE]
> Firma Microsoft oferuje narzędzia do sprawdzania poprawności modeli urządzeń w DMR publicznym. Można ponownie użyć tych narzędzi w niestandardowych repozytoriach.

## <a name="public-models"></a>Modele publiczne

Modele urządzeń publicznych przechowywane w repozytorium modelu są dostępne dla wszystkich użytkowników w celu korzystania z nich i integrowania ich w aplikacjach. Modele urządzeń publicznych umożliwiają twórcom urządzeń, którzy mają możliwość udostępniania i ponownego wykorzystywania modeli urządzeń Plug and Play IoT w otwartym systemie ekonomicznym dla konstruktorów i deweloperów rozwiązań.

Zapoznaj się z sekcją [Publikowanie modelu](#publish-a-model) , aby uzyskać instrukcje dotyczące publikowania modelu w repozytorium modeli w celu udostępnienia go jako publicznego.

Użytkownicy mogą przeglądać, wyszukiwać i wyświetlać interfejsy publiczne z oficjalnego [repozytorium GitHub](https://github.com/Azure/iot-plugandplay-models).

Wszystkie interfejsy w `dtmi` folderach są również dostępne w publicznym punkcie końcowym [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Rozpoznaj modele

Aby programowo uzyskać dostęp do tych interfejsów, należy skonwertować DTMI na ścieżkę względną, której można użyć do wysyłania zapytań do publicznego punktu końcowego.

Aby skonwertować DTMI na ścieżkę bezwzględną, użyj `DtmiToPath` funkcji z `IsValidDtmi` :

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
1. Sklonuj repozytorium rozwidlenia. Opcjonalnie Utwórz nową gałąź, aby zachować zmiany odizolowane od `main` gałęzi.
1. Dodaj nowe interfejsy do `dtmi` folderu przy użyciu konwencji folderu/filename. Aby dowiedzieć się więcej, zobacz [Importowanie modelu do `dtmi/` folderu](#import-a-model-to-the-dtmi-folder).
1. Sprawdź poprawność modeli lokalnie przy użyciu `dmr-client` Narzędzia. Aby dowiedzieć się więcej, zobacz temat [Weryfikowanie modeli](#validate-models).
1. Zatwierdź zmiany lokalnie i wypchnij do Twojego rozwidlenia.
1. Z rozwidlenia Utwórz żądanie ściągnięcia, które odwołuje się do `main` gałęzi. Zobacz [tworzenie dokumentacji problemu lub żądania ściągnięcia](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Zapoznaj się z [wymaganiami dotyczącymi żądań ściągnięcia](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

Żądanie ściągnięcia wyzwala zestaw akcji usługi GitHub, które weryfikują przesłane interfejsy i sprawdza, czy żądanie ściągnięcia spełnia wszystkie wymagania.

Firma Microsoft odpowie na żądanie ściągnięcia ze wszystkimi kontrolami w ciągu trzech dni roboczych.

### <a name="dmr-client-tools"></a>`dmr-client` narzędzi

Narzędzia używane do sprawdzania poprawności modeli podczas sprawdzania żądań ściągnięcia mogą również służyć do lokalnego dodawania i weryfikowania interfejsów DTDL.

> [!NOTE]
> To narzędzie wymaga [zestawu .NET SDK](https://dotnet.microsoft.com/download) w wersji 3,1 lub nowszej.

### <a name="install-dmr-client"></a>Zainstaluj `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Importowanie modelu do `dtmi/` folderu

Jeśli model jest już przechowywany w plikach JSON, możesz użyć `dmr-client import` polecenia, aby dodać je do `dtmi/` folderu z prawidłowymi nazwami plików:

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> Możesz użyć argumentu, `--local-repo` Aby określić folder główny lokalnego repozytorium.

### <a name="validate-models"></a>Weryfikuj modele

Możesz sprawdzić poprawność modeli przy użyciu `dmr-client validate` polecenia:

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> Walidacja używa najnowszej wersji analizatora DTDL, aby upewnić się, że wszystkie interfejsy są zgodne ze specyfikacją języka DTDL.

Aby zweryfikować zależności zewnętrzne, muszą one znajdować się w repozytorium lokalnym. Aby sprawdzić poprawność modeli, użyj `--repo` opcji, aby określić `local` lub `remote` folder do rozpoznawania zależności:

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Ścisła weryfikacja

DMR zawiera dodatkowe [wymagania](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md), Użyj flagi, `stict` Aby sprawdzić poprawność modelu:

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Sprawdź dane wyjściowe konsoli dla wszystkich komunikatów o błędach.

### <a name="export-models"></a>Eksportowanie modeli

Modele można eksportować z danego repozytorium (lokalnego lub zdalnego) do pojedynczego pliku przy użyciu tablicy JSON:

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Następne kroki

Sugerowanym następnym krokiem jest zapoznanie się z [architekturą Plug and Play IoT](concepts-architecture.md).
