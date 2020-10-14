---
title: Analizowanie i weryfikowanie modeli
titleSuffix: Azure Digital Twins
description: Dowiedz się, jak analizować modele DTDL przy użyciu biblioteki parserów.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2cc60af26754eddbe8699019ae8d906a4c1e9e62
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057692"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Analizowanie i weryfikowanie modeli przy użyciu biblioteki DTDL parser

[Modele](concepts-models.md) w usłudze Azure Digital bliźniaczych reprezentacji są zdefiniowane przy użyciu języka Digital bliźniaczych reprezentacji Definition Language (DTDL) w formacie JSON. **Zaleca się zweryfikowanie modeli w trybie offline przed przekazaniem ich do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.**

Aby to ułatwić, biblioteka DTDL po stronie klienta platformy .NET jest udostępniana na serwerze NuGet: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

Biblioteki parser można użyć bezpośrednio w kodzie C# lub użyć przykładowego projektu kodu niezależny od języka, który jest skompilowany w bibliotece analizatora składni: [**DTDL modułu sprawdzania poprawności**](/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>Użyj przykładowego modułu sprawdzania DTDL

[**Moduł sprawdzania poprawności DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator) jest przykładowym projektem, który może weryfikować dokumenty modelu, aby upewnić się, że DTDL jest prawidłowy. Jest on oparty na bibliotece analizatora .NET i jest niezależny od języka. Możesz uzyskać za pomocą przycisku *Pobierz kod pocztowy* na przykładowym linku.

Kod źródłowy pokazuje przykłady użycia biblioteki parsera. Aby sprawdzić poprawność drzewa katalogów plików DTDL, można użyć przykładowego modułu sprawdzania poprawności jako narzędzia wiersza polecenia. Udostępnia również tryb interaktywny.

W folderze dla przykładowego modułu sprawdzania poprawności DTDL zapoznaj się z plikiem *README.MD* , aby uzyskać instrukcje dotyczące pakowania przykładu do samodzielnego pliku wykonywalnego.

Po skompilowaniu samodzielnego pakietu i dodaniu pliku wykonywalnego do ścieżki można uruchomić moduł sprawdzania poprawności przy użyciu tego polecenia w konsoli programu na maszynie:

```cmd/sh
DTDLValidator
```

Z opcjami domyślnymi przykład wyszukuje `*.json` pliki w bieżącym katalogu i wszystkich podkatalogach. Możesz również dodać następującą opcję, aby znaleźć przykładowe wyszukiwanie we wskazanym katalogu i wszystkich podkatalogach dla plików z rozszerzeniem *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Możesz dodać `-i` opcję dla przykładu, aby wprowadzić tryb interaktywny:

```cmd/sh
DTDLValidator -i
```

Aby uzyskać więcej informacji na temat tego przykładu, zobacz kod źródłowy lub uruchom `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>Korzystanie z biblioteki analizatora .NET 

Biblioteka [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) zapewnia dostęp modelu do definicji DTDL, zasadniczo działa jako odpowiednik odbicia w języku C# dla DTDL. Ta biblioteka może być używana niezależnie od dowolnego [zestawu SDK usługi Azure Digital bliźniaczych reprezentacji](how-to-use-apis-sdks.md), szczególnie w przypadku weryfikacji DTDL w edytorze wizualizacji lub tekstu. Jest to przydatne w celu upewnienia się, że pliki definicji modelu są prawidłowe przed podjęciem próby przekazania ich do usługi.

Aby skorzystać z biblioteki parsera, należy podać zestaw dokumentów DTDL. Zazwyczaj można pobrać te dokumenty modelu z usługi, ale można je również udostępnić lokalnie, jeśli klient ponosi odpowiedzialność za przekazanie go do usługi w pierwszej kolejności. 

Oto ogólny przepływ pracy dotyczący używania analizatora:
1. Pobierz niektóre lub wszystkie dokumenty DTDL z usługi.
2. Przekaż zwrócone DTDL dokumenty w pamięci do analizatora.
3. Analizator sprawdzi, czy zestaw dokumentów przeszedł do niego i zwróci szczegółowe informacje o błędzie. Ta możliwość jest przydatna w scenariuszach edytora.
4. Użyj interfejsów API analizatora, aby kontynuować analizowanie modeli zawartych w zestawie dokumentów. 

Możliwości parsera obejmują:
* Pobierz wszystkie zaimplementowane interfejsy modelu (zawartość `extends` sekcji interfejsu).
* Pobierz wszystkie właściwości, dane telemetryczne, polecenia, składniki i relacje zadeklarowane w modelu. To polecenie pobiera również wszystkie metadane zawarte w tych definicjach i przejmuje dziedziczenie ( `extends` sekcje).
* Pobierz wszystkie złożone definicje modeli.
* Ustal, czy model można przypisać z innego modelu.

> [!NOTE]
> Urządzenia [IoT Plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) używają małych wariantów składni do opisywania ich funkcjonalności. Ta składnia jest semantyką zgodną z podzbiorem DTDL używanym w usłudze Azure Digital bliźniaczych reprezentacji. W przypadku korzystania z biblioteki parserów nie trzeba wiedzieć, która odmiana składni została użyta do utworzenia DTDL dla dwuosiowej sieci. Parser zawsze będzie domyślnie zwracać ten sam model dla składni Digital bliźniaczych reprezentacji (PnP) i platformy Azure.

### <a name="code-with-the-parser-library"></a>Kod z biblioteką parsera

Biblioteki parser można używać bezpośrednio dla elementów, takich jak Weryfikowanie modeli we własnej aplikacji lub generowanie dynamicznych, opartych na modelu interfejsów użytkownika, pulpitów nawigacyjnych i raportów.

Aby obsłużyć Poniższy przykład kodu parsera, należy wziąć pod uwagę kilka modeli zdefiniowanych w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji:

> [!TIP] 
> `dtmi:com:contoso:coffeeMaker`Model używa składni *modelu możliwości* , która oznacza, że została zainstalowana w usłudze przez połączenie urządzenia PNP, które uwidacznia ten model.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

Poniższy kod przedstawia przykład użycia biblioteki parsera do odzwierciedlenia w tych definicjach w języku C#:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Po zakończeniu pisania modeli zapoznaj się z tematem jak je przekazać (i wykonać inne operacje zarządzania) przy użyciu interfejsów API DigitalTwinsModels:
* [*Instrukcje: Zarządzanie modelami niestandardowymi*](how-to-manage-model.md)