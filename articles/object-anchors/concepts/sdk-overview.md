---
title: Omówienie zestawu SDK środowiska uruchomieniowego
description: Zapoznaj się z zestawem SDK środowiska uruchomieniowego kotwic obiektów.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 74663f05c5ff995a090c7cd35e4edf46a754da17
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034612"
---
# <a name="runtime-sdk-overview"></a>Omówienie zestawu SDK środowiska uruchomieniowego

W tej sekcji znajduje się ogólny opis zestawu SDK środowiska uruchomieniowego kotwic obiektów, który służy do wykrywania obiektów przy użyciu modelu kotwic obiektów. Zapoznaj się z informacjami o tym, jak obiekt jest reprezentowany i jakie są używane różne składniki.

Wszystkie typy opisane poniżej można znaleźć w przestrzeni nazw **Microsoft. MixedReality. ObjectAnchors** .

## <a name="types"></a>Typy

### <a name="objectmodel"></a>ObjectModel

[ObjectModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectmodel) reprezentuje geometrię obiektu fizycznego i koduje niezbędne parametry na potrzeby wykrywania i szacowania. Należy ją utworzyć za pomocą [usługi kotwice obiektów](../quickstarts/get-started-model-conversion.md). Następnie aplikacja może załadować wygenerowany plik modelu przy użyciu interfejsu API kotwic obiektów i zbadać siatkę osadzoną w tym modelu do wizualizacji.

### <a name="objectsearcharea"></a>ObjectSearchArea

[ObjectSearchArea](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) określa miejsce do odszukania jednego lub wielu obiektów. Jest on definiowany przez identyfikator węzła wykresu przestrzennego i granice przestrzenne w układzie współrzędnych reprezentowane przez identyfikator węzła wykresu przestrzennego. Zestawu SDK środowiska uruchomieniowego obiektów obsługuje cztery typy granic, czyli **pola widok**, **pole ograniczenia**, **sferę** i **lokalizację**.

### <a name="objectquery"></a>ObjectQuery

[ObjectQuery](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery) informuje **obiekt obserwatora** o sposobie znajdowania obiektów danego modelu. Udostępnia następujące parametry możliwość dostosowania, których wartości domyślne można pobrać z modelu obiektów.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

Właściwość [MinSurfaceCoverage](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) wskazuje wartość, aby wziąć pod uwagę wystąpienie wykryte.

Dla każdego obiektu kandydującego **obserwator** oblicza stosunek nakładających się powierzchni między przekształconym modelem obiektów a sceną, a następnie raportuje tego kandydata do aplikacji tylko wtedy, gdy wskaźnik pokrycia przekracza daną wartość progową.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

Właściwość [IsExpectedToBeStandingOnGroundPlane](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) wskazuje, czy obiekt docelowy ma być włączony na płaszczyźnie uziemienia.

Płaszczyzna uziemienia to najniższy poziom podłogi w obszarze wyszukiwania. Zapewnia dobre ograniczenie dla możliwych ułożenia obiektów. Włączenie tej flagi umożliwi **obserwatorowi** oszacowanie ułożenia w ograniczonym miejscu i może poprawić dokładność. Ten parametr będzie ignorowany, jeśli model nie powinien znajdować się na płaszczyźnie uziemienia.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

Właściwość [ExpectedMaxVerticalOrientationInDegrees](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) wskazuje oczekiwany maksymalny kąt w stopniach między kierunkiem obiektu a grawitacją.

Ten parametr zapewnia inne ograniczenie kierunku w górę szacowanego ułożenia. Na przykład jeśli obiekt jest w górę, ten parametr może mieć wartość 0. Kotwice obiektów nie powinny wykrywać obiektów, które różnią się od modelu. Jeśli model jest w stanie up, wówczas nie zostanie wykryty po stronie wystąpienia. Nowy model będzie używany do układu podwyższego poziomu. Ta sama reguła ma zastosowanie do przegubów.

#### <a name="maxscalechange"></a>MaxScaleChange

Właściwość [MaxScaleChange](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) wskazuje maksymalną zmianę skali obiektu (w zakresie 0 ~ 1) w odniesieniu do mapowania przestrzennego. Szacowana Skala jest stosowana do przekształconych wierzchołków obiektów wyśrodkowanych w miejscu źródłowym i wyrównanym do osi. Szacowane skale nie mogą być rzeczywistą skalą między modelem CAD i jego fizyczną reprezentacją, ale niektóre wartości zezwalające aplikacji na renderowanie modelu obiektów blisko mapowania przestrzennego w obiekcie fizycznym.

#### <a name="searchareas"></a>SearchAreas

Właściwość [SearchAreas](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) wskazuje tablicę granic przestrzennych, gdzie można znaleźć obiekty.

**Obserwator** będzie szukać obiektów w przestrzeni Unii dla wszystkich obszarów wyszukiwania określonych w zapytaniu. W tej wersji będziemy zwracać co najwyżej jeden obiekt o najwyższej pewności, aby zmniejszyć opóźnienie.

### <a name="objectinstance"></a>Obiektu ObjectInstance

[Obiektu ObjectInstance](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectinstance) reprezentuje hipotetyczną pozycję, w której wystąpienie danego modelu może znajdować się w systemie współrzędnych HoloLens. Każde wystąpienie jest dostarczane z `SurfaceCoverage` właściwością, aby wskazać, jak dobre ma być szacowane.

Wystąpienie jest tworzone przez wywołanie `ObjectObserver.DetectAsync` metody, a następnie automatycznie aktualizowane w tle podczas aktywności. Aplikacja może nasłuchiwać zdarzenia zmiany stanu w konkretnym wystąpieniu lub zmienić tryb śledzenia, aby wstrzymać/wznowić aktualizację. Wystąpienie zostanie automatycznie usunięte ze **obserwatora** po utracie śledzenia.

### <a name="objectobserver"></a>ObjectObserver

[ObjectObserver](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectobserver) ładuje modele obiektów, wykrywa ich wystąpienia i raportuje 6-DOF dla każdego wystąpienia w systemie współrzędnych HoloLens.

Chociaż każdy model obiektów lub wystąpienie jest tworzone ze **obserwatora**, ich okresy istnienia są niezależne. Aplikacja może usunąć obserwatora i nadal używać modelu obiektów lub wystąpienia.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[ObjectDiagnosticSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) rejestruje diagnostykę i zapisuje dane w archiwum.

Archiwum diagnostyki obejmuje chmurę punktu sceny, stan obserwatora oraz informacje o modelach. Te informacje są przydatne do identyfikowania możliwych problemów dotyczących środowiska uruchomieniowego. Aby uzyskać więcej informacji, zobacz [często zadawane pytania](../faq.md).

## <a name="runtime-sdk-usage-and-details"></a>Szczegóły użycia i szczegółów zestawu SDK środowiska uruchomieniowego

W tej sekcji należy udostępnić podstawowe informacje dotyczące korzystania z zestawu SDK środowiska uruchomieniowego. Należy zapewnić wystarczający kontekst do przeglądania przykładowych aplikacji, aby zobaczyć, jak kotwice obiektów są używane całościowo.

### <a name="initialization"></a>Inicjalizacja

Aplikacje muszą wywoływać `ObjectObserver.IsSupported()` interfejs API, aby określić, czy na urządzeniu jest obsługiwane kotwice obiektów przed jego użyciem. Jeśli `ObjectObserver.IsSupported()` interfejs API zwraca `false` , sprawdź, czy aplikacja włączyła funkcję **spatialPerception** and\or upgrade do najnowszego systemu operacyjnego HoloLens.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

Następnie aplikacja tworzy obiekt obserwatora i ładuje niezbędne modele wygenerowane przez [usługę konwersji modelu kotwic obiektów](../quickstarts/get-started-model-conversion.md).

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

Aplikacja tworzy zapytanie w celu wykrycia wystąpień tego modelu w obrębie obszaru.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Domyślnie każde wykryte wystąpienie będzie śledzone automatycznie przez **obserwatora**. Opcjonalnie można manipulować tymi wystąpieniami przez zmianę ich trybu śledzenia lub nasłuchiwanie zdarzenia zmiany stanu.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

W zdarzeniu zmiany stanu można wysłać zapytanie do najnowszego stanu lub usunąć wystąpienie, jeśli zostało utracone.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Ponadto aplikacja może opcjonalnie zarejestrować jedną lub wiele sesji diagnostycznych na potrzeby debugowania w trybie offline.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Wreszcie zwolnimy zasoby, usuwając wszystkie obiekty.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```
