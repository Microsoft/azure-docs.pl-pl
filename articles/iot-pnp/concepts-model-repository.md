---
title: Opis pojęć repozytorium modeli urządzeń | Microsoft Docs
description: Jako deweloper rozwiązania lub Specjalista IT zapoznaj się z podstawowymi pojęciami repozytorium modeli urządzeń.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33ff96b4e51dbf80bfdb924bc37786a344cdfdc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582650"
---
# <a name="device-models-repository"></a>Repozytorium modeli urządzeń

Repozytorium modele urządzeń (DMR) umożliwia konstruktorom urządzeń zarządzanie i udostępnianie modeli urządzeń Plug and Play IoT. Modele urządzeń to JSON LD dokumenty zdefiniowane przy użyciu [języka Digital bliźniaczych reprezentacji Modeling Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

DMR definiuje wzorzec do przechowywania interfejsów DTDL w strukturze folderów na podstawie identyfikatora modelu sznurka urządzenia (DTMI). Interfejs można zlokalizować w DMR, konwertując DTMI na ścieżkę względną. Na przykład `dtmi:com:example:Thermostat;1` DTMI tłumaczy na `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-models-repository"></a>Repozytorium modeli urządzeń publicznych

Firma Microsoft udostępnia publiczną DMR z następującymi cechami:

- Modele nadzorowane. Firma Microsoft przegląda i zatwierdza wszystkie dostępne interfejsy przy użyciu przepływu pracy weryfikacji żądania ściągnięcia w serwisie GitHub.
- Niezmienności.  Po opublikowaniu nie można zaktualizować interfejsu.
- Skalowanie w poziomie. Firma Microsoft zapewnia wymaganą infrastrukturę do tworzenia bezpiecznego, skalowalnego punktu końcowego, który umożliwia publikowanie i korzystanie z modeli urządzeń.

## <a name="custom-device-models-repository"></a>Repozytorium niestandardowych modeli urządzeń

Użyj tego samego wzorca DMR, aby utworzyć niestandardowe DMR w dowolnym nośniku magazynującym, takim jak lokalny system plików lub niestandardowe serwery sieci Web HTTP. Modele urządzeń można pobrać z niestandardowego DMR w taki sam sposób jak w przypadku publicznego DMR, zmieniając podstawowy adres URL używany do uzyskiwania dostępu do DMR.

> [!NOTE]
> Firma Microsoft oferuje narzędzia do sprawdzania poprawności modeli urządzeń w DMR publicznym. Można ponownie użyć tych narzędzi w niestandardowych repozytoriach.

## <a name="public-models"></a>Modele publiczne

Modele urządzeń publicznych przechowywane w repozytorium modele są dostępne dla wszystkich użytkowników w celu korzystania z nich i integrowania ich w aplikacjach. Modele urządzeń publicznych umożliwiają twórcom urządzeń, którzy mają możliwość udostępniania i ponownego wykorzystywania modeli urządzeń Plug and Play IoT w otwartym systemie ekonomicznym dla konstruktorów i deweloperów rozwiązań.

Zapoznaj się z sekcją [Publikowanie modelu](#publish-a-model) , aby uzyskać instrukcje dotyczące publikowania modelu w repozytorium modeli w celu udostępnienia go jako publicznego.

Użytkownicy mogą przeglądać, wyszukiwać i wyświetlać interfejsy publiczne z oficjalnego [repozytorium GitHub](https://github.com/Azure/iot-plugandplay-models).

Wszystkie interfejsy w `dtmi` folderach są również dostępne w publicznym punkcie końcowym [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Rozpoznaj modele

Aby programowo uzyskać dostęp do tych interfejsów, możesz użyć `ModelsRepositoryClient` dostępnego w pakiecie NuGet [platformy Azure. IoT. ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository). Ten klient jest domyślnie skonfigurowany do wysyłania zapytań do publicznej DMR dostępnej w usłudze [DeviceModels.Azure.com](https://devicemodels.azure.com/) i można go skonfigurować w dowolnym repozytorium niestandardowym.

Klient akceptuje `DTMI` jako dane wejściowe i zwraca słownik ze wszystkimi wymaganymi interfejsami:

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Oczekiwane dane wyjściowe powinny zawierać `DTMI` trzy interfejsy znajdujące się w łańcuchu zależności:

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

Program `ModelsRepositoryClient` można skonfigurować tak, aby wysyłał zapytania do niestandardowego repozytorium modelu — dostępne za pośrednictwem protokołu HTTP (s) — i określić rozpoznawanie zależności przy użyciu dowolnego dostępnego elementu `ModelDependencyResolution` :

- Wyłączona. Zwraca tylko określony interfejs bez żadnej zależności.
- Włączona. Zwraca wszystkie interfejsy w łańcuchu zależności
- TryFromExpanded. Użyj `.expanded.json` pliku, aby pobrać wstępnie obliczone zależności 

> [!Tip] 
> Niestandardowe repozytoria mogą nie ujawniać `.expanded.json` pliku, gdy nie jest dostępny, klient będzie przetwarzać każdą zależność lokalnie.

Następny przykładowy kod pokazuje, jak zainicjować `ModelsRepositoryClient` za pomocą niestandardowego podstawowego adresu URL repozytorium, w tym przypadku przy użyciu `raw` adresów URL z interfejsu API usługi GitHub bez korzystania z `expanded` formularza — ponieważ nie jest on dostępny w `raw` punkcie końcowym. `AzureEventSourceListener`Jest inicjowane w celu zbadania żądania HTTP wykonywanego przez klienta:

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

W ramach kodu źródłowego w repozytorium GitHub zestawu Azure SDK są dostępne więcej przykładów: [Azure. IoT. ModelsRepository/Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

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
