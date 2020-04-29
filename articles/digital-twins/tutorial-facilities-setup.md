---
title: 'Samouczek: wdrażanie środowiska w wersji zapoznawczej i wykresu przestrzennego — usługa Azure Digital bliźniaczych reprezentacji | Microsoft Docs'
description: Dowiedz się, jak wdrożyć wystąpienie usługi Azure Digital Twins i skonfigurować zasoby przestrzenne przy użyciu kroków opisanych w tym samouczku.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 878b64fe6dd491adbb61c4c74cf4a5fc039858cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79371413"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Samouczek: wdrażanie usługi Azure Digital bliźniaczych reprezentacji Preview i Konfigurowanie wykresu przestrzennego

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Możesz użyć usługi Azure Digital bliźniaczych reprezentacji w wersji zapoznawczej, aby połączyć osoby, miejsca i urządzenia w spójny system przestrzenny. W tej serii samouczków przedstawiono sposób wykrywania zajętości pomieszczeń z optymalnymi warunkami temperatury i jakości powietrza za pomocą usługi Azure Digital Twins. 

Samouczki przeprowadzą Cię przez proces tworzenia w aplikacji konsoli .NET scenariusza budynku biurowego. Budynek składa się z wielu pięter i pomieszczeń na każdym piętrze. W pomieszczeniach znajdują się urządzenia z podłączonymi czujnikami, które wykrywają ruch, temperaturę otoczenia i jakość powietrza. 

Nauczysz się, jak za pomocą usługi Azure Digital Twins replikować obszary i jednostki fizyczne w budynku jako obiekty cyfrowe. Przeprowadzisz symulację zdarzeń urządzeń przy użyciu innej aplikacji konsoli. Następnie dowiesz się, jak monitorować zdarzenia pochodzące z tych obszarów i jednostek fizycznych w czasie zbliżonym do rzeczywistego. 

Korzystając z tych informacji, administrator biura może ułatwić pracownikom tego budynku rezerwowanie sal konferencyjnych z optymalnymi warunkami. Menedżer pomieszczeń biurowych może za pomocą Twojej konfiguracji poznać trendy dotyczące wykorzystania pomieszczeń oraz monitorować warunki dla celów konserwacji.

W pierwszym samouczku tej serii poznasz następujące zagadnienia:

> [!div class="checklist"]
> * Wdrażanie usługi Digital Twins.
> * Udzielanie uprawnień dla Twojej aplikacji.
> * Modyfikowanie przykładowej aplikacji usługi Digital Twins.
> * Aprowizowanie budynku.

W tym samouczkach są używane i modyfikowane te same przykłady co w [przewodniku Szybki start dotyczącym znajdowania dostępnych pomieszczeń](quickstart-view-occupancy-dotnet.md), co pozwala uzyskać bardziej szczegółową wiedzę na temat poszczególnych pojęć.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Zestaw SDK dla platformy .NET Core. Przykładowe aplikacje usługi Azure Digital Twins używane w tych samouczkach są napisane w języku C#. Zainstaluj [zestaw SDK .NET Core w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na maszynie deweloperskiej w celu skompilowania i uruchomienia przykładu. Sprawdź, czy na Twojej maszynie jest zainstalowana właściwa wersja, uruchamiając polecenie `dotnet --version` w oknie poleceń.

- Program [Visual Studio Code](https://code.visualstudio.com/) umożliwiający eksplorowanie przykładowego kodu. 

## <a name="deploy-digital-twins"></a>Wdrażanie usługi Digital Twins

Wykonaj kroki opisane w tej sekcji, aby utworzyć nowe wystąpienie usługi Azure Digital Twins. Można utworzyć tylko jedno wystąpienie na subskrypcję. Jeśli jedno wystąpienie jest już uruchomione, przejdź do następnej sekcji. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Udzielanie uprawnień dla Twojej aplikacji

Usługa Digital Twins steruje [dostępem do odczytu/zapisu](../active-directory/develop/v2-permissions-and-consent.md) w usłudze, korzystając z usługi [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Każda aplikacja, która wymaga połączenia z wystąpieniem usługi Digital Twins, musi być zarejestrowana w usłudze Azure AD. W tej sekcji opisano procedurę rejestrowania przykładowej aplikacji.

Jeśli już masz zarejestrowaną aplikację, możesz wykorzystać ją jako przykład. Jednak przejrzyj tę sekcję, aby upewnić się, że Twoja rejestracja aplikacji jest poprawnie skonfigurowana.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Konfigurowanie przykładowej aplikacji usługi Digital Twins

W tej sekcji przejdziesz przez aplikację usługi Azure Digital Twins, która komunikuje się z [interfejsami API REST usługi Digital Twins](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Pobierz przykład

Jeśli już masz przykłady pobrane na potrzeby [przewodnika Szybki start dotyczącego znajdowania dostępnych pomieszczeń](quickstart-view-occupancy-dotnet.md), możesz pominąć te kroki.

1. Pobierz [przykładowe aplikacje usługi Digital Twins dla platformy .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Wyodrębnij zawartość folderu zip na swojej maszynie.

### <a name="explore-the-sample"></a>Eksplorowanie przykładu

W folderze z wyodrębnionym przykładem otwórz plik **digital-twins-samples-csharp\digital-twins-samples.code-workspace** w programie Visual Studio Code. Zawiera on dwa projekty:

* Używając przykładu aprowizacji **occupancy-quickstart**, możesz skonfigurować i aprowizować [wykres analizy przestrzennej](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Ten wykres jest cyfrowym obrazem lokalizacji fizycznych i znajdujących się w nich zasobów. Używa [modelu obiektów](concepts-objectmodel-spatialgraph.md#digital-twins-object-models), który definiuje obiekty do inteligentnego kompilowania. Aby uzyskać pełną listę obiektów usługi Digital Twins i interfejsów API REST, odwiedź stronę [dokumentacji interfejsu API REST](https://docs.westcentralus.azuresmartspaces.net/management/swagger) lub skorzystaj z adresu URL interfejsu API zarządzania, który został utworzony na potrzeby [Twojego wystąpienia](#deploy-digital-twins).

   Aby zapoznać się z przykładem, aby zrozumieć, jak komunikuje się z wystąpieniem Digital bliźniaczych reprezentacji, możesz rozpocząć pracę z folderem **src\actions** . W plikach w tym folderze zaimplementowano polecenia, które będą używane w tych samouczkach:
    - Plik **provisionSample.cs** pokazuje, jak zainicjować obsługę wykresu przestrzennego.
    - Plik **getSpaces.cs** pobiera informacje o zainicjowanych miejscach.
    - Plik **getAvailableAndFreshSpaces.cs** pobiera wyniki funkcji niestandardowej nazywanej funkcją zdefiniowaną przez użytkownika.
    - Plik **createEndpoints.cs** tworzy punkty końcowe do interakcji z innymi usługami.

* Przykład symulacji **device-connectivity** symuluje dane z czujników i wysyła je do centrum IoT aprowizowanego na potrzeby Twojego wystąpienia usługi Digital Twins. Ten przykład zostanie wykorzystany w [kolejnym samouczku po przeprowadzeniu aprowizacji wykresu przestrzennego](tutorial-facilities-udf.md#simulate-sensor-data). Identyfikatory czujników i urządzeń, których używasz do skonfigurowania tego przykładu, powinny być takie same jak te, które będą używane do aprowizacji wykresu.

### <a name="configure-the-provisioning-sample"></a>Konfigurowanie przykładu aprowizacji

1. Otwórz okno polecenia i przejdź do pobranego przykładu. Uruchom następujące polecenie:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Przywróć zależności w przykładowym projekcie, uruchamiając następujące polecenie:

    ```cmd/sh
    dotnet restore
    ```

1. W programie Visual Studio Code otwórz plik [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) w projekcie **occupancy-quickstart**. Zaktualizuj następujące wartości:
   * **ClientId**: wprowadź identyfikator aplikacji Twojej rejestracji aplikacji w usłudze Azure AD. Ten identyfikator został zanotowany w sekcji, w której były [określane uprawnienia aplikacji](#grant-permissions-to-your-app).
   * **Dzierżawa**: wprowadź identyfikator katalogu swojej [dzierżawy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Ten identyfikator także został zanotowany w sekcji, w której były [określane uprawnienia aplikacji](#grant-permissions-to-your-app).
   * **BaseUrl**: wprowadź adres URL Twojego wystąpienia usługi Digital Twins. Aby uzyskać ten adres URL, zamień symbole zastępcze w tym adresie URL na wartości odpowiadające Twojemu wystąpieniu: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Możesz również uzyskać ten adres URL, modyfikując adres URL interfejsu API zarządzania z [sekcji poświęconej wdrażaniu](#deploy-digital-twins). Zamień ciąg **swagger/** na **api/v1.0/**.

1. Zapoznaj się z listą funkcji Digital bliźniaczych reprezentacji, które możesz eksplorować przy użyciu przykładu. Uruchom następujące polecenie:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Omówienie procesu aprowizacji

W tej sekcji pokazano, jak przykładowa aplikacja aprowizuje wykres przestrzenny budynku.

W programie Visual Studio Code przejdź do folderu **occupancy-quickstart\src\actions** i otwórz plik **provisionSample.cs**. Zwróć uwagę na następującą funkcję:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}
```

Ta funkcja używa pliku [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) z tego samego folderu. Otwórz ten plik i zanotuj hierarchię budynku biura: *Venue* (Miejsce), *Floor* (Piętro), *Area* (Obszar) i *Rooms* (Pomieszczenia). W każdym z tych miejsc fizycznych mogą znajdować się *urządzenia* i *czujniki*. Każdy wpis ma wstępnie zdefiniowany typ (`type`) &mdash; na przykład Floor (Piętro) lub Room (Pomieszczenie).

Przykładowy plik **yaml** przedstawia wykres przestrzenny korzystający z domyślnego (`Default`) modelu obiektów usługi Digital Twins. Ten model zawiera nazwy rodzajowe dla większości typów. Nazwy rodzajowe są wystarczające dla budynku. Przykłady takich nazw to: Temperature dla typu SensorDataType i Map dla typu SpaceBlobType. Przykładowym typem miejsca jest Room (Pomieszczenie) z podtypami FocusRoom (Pomieszczenie robocze), ConferenceRoom (Sala konferencyjna) itd. 

Jeśli musisz utworzyć wykres przestrzenny dla innego typu miejsca, na przykład dla fabryki, możesz potrzebować innego modelu obiektów. Uruchamiając polecenie `dotnet run GetOntologies` w wierszu polecenia dla przykładu aprowizacji, możesz dowiedzieć się, jakie modele są dostępne do użycia. 

Aby uzyskać więcej informacji na temat wykresów przestrzennych i modeli obiektów, przeczytaj [Omówienie modeli obiektów i wykresu analizy przestrzennej usługi Digital Twins](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Modyfikowanie przykładowego wykresu przestrzennego

Plik **provisionSample.yaml** zawiera następujące węzły:

- **resources**: węzeł `resources` tworzy w konfiguracji zasób usługi Azure IoT Hub do komunikacji z urządzeniami. Centrum IoT w węźle głównym wykresu może komunikować się ze wszystkimi urządzeniami i czujnikami na wykresie.  

- **spaces**: w modelu obiektów usługi Digital Twins węzeł `spaces` reprezentuje lokalizacje fizyczne. Każde miejsce ma typ (`Type`) &mdash; na przykład Region, Venue (Miejsce) lub Customer (Klient) &mdash; oraz przyjazną nazwę (`Name`). Miejsca mogą należeć do innych miejsc tworzących strukturę hierarchiczną. Plik provisionSample.yaml zawiera wykres przestrzenny wymyślonego budynku. Zwróć uwagę na logiczne zagnieżdżanie miejsc typu `Floor` w węźle `Venue`, typu `Area` na piętrze i węzłów `Room` w obszarze. 

- **devices**: miejsca mogą zawierać urządzenia (`devices`) będące jednostkami fizycznymi lub wirtualnymi, które zarządzają pewną liczbą czujników. Na przykład urządzenie może być telefonem użytkownika, Raspberry z czujnikiem pi lub bramą. W wymyślonym budynku z Twojego przykładu zwróć uwagę na sposób umieszczenia urządzenia **Raspberry Pi 3 A1** w pomieszczeniu **Focus Room**. Każdy węzeł urządzenia jest identyfikowany przez unikatowy identyfikator `hardwareId` trwale zakodowany w przykładzie. Aby skonfigurować ten przykład na potrzeby rzeczywistego środowiska produkcyjnego, zamień te wartości na wartości z Twojej konfiguracji.  

- **sensors**: urządzenie może zawierać wiele czujników (`sensors`). Umożliwiają one wykrywanie i rejestrowanie zmian fizycznych, takich jak temperatura, ruch i poziom naładowania baterii. Każdy węzeł czujnika jest unikatowo identyfikowany za pomocą identyfikatora `hardwareId` trwale zapisanego w tym miejscu. Na potrzeby rzeczywistej aplikacji zamień te identyfikatory, używając unikatowych identyfikatorów czujników z Twojej konfiguracji. Plik provisionSample.yaml ma dwa czujniki umożliwiające rejestrowanie ruchu (*Motion*) i dwutlenku węgla (*CarbonDioxide*). Dodaj kolejny czujnik, aby rejestrować temperaturę (*Temperature*), dodając poniższe wiersze pod wierszami dotyczącymi czujnika CarbonDioxide. Są one dostępne w provisionSample. YAML jako wiersze z komentarzem. Możesz przenieść je poza komentarze, usuwając znak `#` na początku wiersza. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Upewnij się, że klucze `dataType` i `hardwareId` pasują do instrukcji powyżej tego fragmentu kodu. Upewnij się również, że edytor nie zastępuje spacji tabulatorami. 

Zapisz i zamknij plik provisionSample.yaml. W następnym samouczku dodasz więcej informacji do tego pliku, a następnie przeprowadzisz aprowizację przykładowego budynku w usłudze Azure Digital Twins.

> [!TIP]
> Wykres przestrzenny można wyświetlać i modyfikować przy użyciu narzędzia [Azure Digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować pracy z usługą Azure Digital Twins, możesz usunąć zasoby utworzone w tym samouczku:

1. W menu po lewej stronie w witrynie [Azure Portal](https://portal.azure.com) wybierz przycisk **Wszystkie zasoby**, wybierz grupę zasobów usługi Digital Twins i wybierz polecenie **Usuń**.

    > [!TIP]
    > Jeśli podczas usuwania wystąpienia usługi Digital Twins wystąpił problem, została wdrożona aktualizacja usługi zawierająca poprawkę. Ponów próbę usunięcia wystąpienia.

1. Jeśli będzie to konieczne, możesz usunąć przykładową aplikację na komputerze służbowym.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zaimplementować logikę niestandardową w celu monitorowania warunków w przykładowym budynku, przejdź do kolejnego samouczka w serii: 
> [!div class="nextstepaction"]
> [Samouczek: aprowizowanie budynku i monitorowanie warunków pracy](tutorial-facilities-udf.md)