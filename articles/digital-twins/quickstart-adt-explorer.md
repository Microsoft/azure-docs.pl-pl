---
title: Szybki Start — Eksplorowanie przykładowego scenariusza
titleSuffix: Azure Digital Twins
description: Szybki Start — Użyj przykładu ADT Explorer, aby wizualizować i eksplorować prekompilowany scenariusz.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: d42a32e236eb73f2aa9f2f61d9708314783564dd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187305"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Przewodnik Szybki Start — Eksplorowanie przykładowego scenariusza usługi Azure Digital bliźniaczych reprezentacji za pomocą Eksploratora ADT

Za pomocą usługi Azure Digital bliźniaczych reprezentacji można tworzyć i korzystać z modeli na żywo w rzeczywistych środowiskach. Najpierw należy modelować poszczególne elementy jako *bliźniaczych reprezentacji cyfrowe*. Następnie można połączyć je z *grafem* wiedzy, który może odpowiedzieć na zdarzenia na żywo i uzyskać zapytanie o informacje.

W tym przewodniku szybki start poznasz wstępnie utworzony Graf usługi Azure Digital bliźniaczych reprezentacji z pomocą przykładowej aplikacji o nazwie [Eksplorator usługi Azure Digital bliźniaczych reprezentacji (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Użyj Eksploratora ADT do:

- Przekaż reprezentację cyfrową środowiska.
- Wyświetl graficzne obrazy bliźniaczych reprezentacji i grafów, które są tworzone w celu reprezentowania środowiska w usłudze Azure Digital bliźniaczych reprezentacji.
- Wykonaj inne czynności związane z zarządzaniem za pośrednictwem przeglądarki i środowiska wizualnego.

Przewodnik Szybki Start zawiera następujące główne kroki:

1. Skonfiguruj wystąpienie usługi Azure Digital bliźniaczych reprezentacji i Eksploratora ADT.
1. Przekazuj wstępnie skompilowane modele i dane grafu w celu skonstruowania przykładowego scenariusza.
1. Zapoznaj się z wykresem scenariusza, który został utworzony.
1. Wprowadź zmiany w grafie.

Przykładowy wykres, z którym będziesz pracować, reprezentuje Kompilowanie z dwiema podłogami i dwoma pomieszczeń. Wykres będzie wyglądać podobnie do tego obrazu:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Widok grafu składający się z czterech węzłów cyklicznych połączonych za pomocą strzałek. Okrąg oznaczony etykietą &quot;Floor1&quot; jest połączony przez strzałkę o nazwie &quot;Contains&quot; z kółkiem o nazwie &quot;Room1&quot;. Okrąg oznaczony etykietą &quot;Floor0&quot; jest połączony przez strzałkę o nazwie &quot;Contains&quot; z kółkiem o nazwie &quot;Room0&quot;. &quot;Floor1&quot; i &quot;Floor0&quot; nie są połączone.":::

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz mieć subskrypcję platformy Azure. Jeśli jeszcze tego nie zrobiono, [Utwórz go bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Na maszynie będzie również potrzebne **Node.js** . Aby uzyskać najnowszą wersję, zobacz [Node.js](https://nodejs.org/).

Na koniec należy również pobrać przykład do użycia podczas korzystania z przewodnika Szybki Start. Przykładowa aplikacja to **ADT Explorer**. Ten przykład zawiera aplikację używaną w przewodniku szybki start do ładowania i eksplorowania scenariusza bliźniaczych reprezentacji Digital na platformie Azure. Zawiera również przykładowe pliki scenariusza. Aby uzyskać przykład, przejdź do [Eksploratora Digital bliźniaczych reprezentacji (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Wybierz przycisk **Pobierz kod pocztowy** , aby pobrać plik zip tego przykładowego kodu na swój komputer. Rozpakuj folder **Azure_Digital_Twins__ADT__explorer.zip** i Wyodrębnij pliki.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Konfigurowanie usługi Azure Digital bliźniaczych reprezentacji i Eksploratora ADT

Pierwszym krokiem w pracy z usługą Azure Digital bliźniaczych reprezentacji jest skonfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Po utworzeniu wystąpienia usługi i skonfigurowaniu poświadczeń do uwierzytelniania za pomocą Eksploratora ADT można nawiązać połączenie z wystąpieniem w Eksploratorze ADT i wypełnić je danymi przykładowymi w dalszej części przewodnika Szybki Start.

Pozostała część tej sekcji przeprowadzi Cię przez te kroki.

### <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Konfigurowanie lokalnych poświadczeń platformy Azure

Aplikacja Eksplorator ADT korzysta z [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (części `Azure.Identity` biblioteki) do uwierzytelniania użytkowników za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji, gdy zostanie on uruchomiony na komputerze lokalnym. Aby uzyskać więcej informacji na temat różnych sposobów uwierzytelniania aplikacji klienckiej za pomocą usługi Azure Digital bliźniaczych reprezentacji, zobacz [pisanie kodu uwierzytelniania aplikacji](how-to-authenticate-client.md).

W przypadku tego typu uwierzytelniania program ADT Explorer szuka poświadczeń w środowisku lokalnym, takich jak logowanie do platformy Azure w lokalnym [interfejsie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) lub w programie Visual Studio lub Visual Studio Code. Z tego powodu należy *zalogować się lokalnie na platformie Azure* za pomocą jednego z tych mechanizmów, aby skonfigurować poświadczenia dla aplikacji ADT Explorer.

Jeśli użytkownik jest już zalogowany do platformy Azure za pomocą jednego z tych sposobów, można przejść do [następnej sekcji](#run-and-configure-adt-explorer).

W przeciwnym razie możesz zainstalować lokalny interfejs wiersza polecenia platformy Azure, wykonując następujące czynności:

1. Wykonaj czynności opisane w [tym linku instalacji](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) , aby zakończyć instalację zgodną z systemem operacyjnym.
1. Otwórz okno konsoli na komputerze.
1. Uruchom `az login` polecenie i postępuj zgodnie z monitami o uwierzytelnienie, aby zalogować się do konta platformy Azure.
1. Możliwy ostatni krok: Jeśli używasz wielu subskrypcji platformy Azure w ramach tego konta, ustaw kontekst uwierzytelniania na subskrypcję platformy Azure zawierającą wystąpienie usługi Azure Digital bliźniaczych reprezentacji, uruchamiając polecenie `az account set --subscription "<your-subscription-name-or-ID>"` (nazwa lub wartość identyfikatora subskrypcji będzie działać).

Po zalogowaniu się program ADT Explorer powinien automatycznie pobrać poświadczenia platformy Azure po uruchomieniu w następnej sekcji.

Jeśli chcesz, możesz zamknąć okno konsoli uwierzytelniania. Można też pozostawić ją otwartą do użycia w następnym kroku.

### <a name="run-and-configure-adt-explorer"></a>Uruchom i skonfiguruj Eksploratora ADT

Następnie uruchom aplikację ADT Explorer i skonfiguruj ją dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

1. Przejdź do folderu pobrane i rozpakowane **Azure_Digital_Twins__ADT__explorer** .
Otwórz okno konsoli w lokalizacji folderu **Azure_Digital_Twins__ADT__explorer/Client/src**.

1. Uruchom `npm install` , aby pobrać wszystkie wymagane zależności.

1. Uruchom aplikację, uruchamiając `npm run start` .

   Po kilku sekundach zostanie otwarte okno przeglądarki, a aplikacja zostanie wyświetlona w przeglądarce.

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Okno przeglądarki pokazujące aplikację uruchomioną na hoście lokalnym: 3000. Aplikacja jest nazywana Eksploratorem ADT i zawiera pola Eksploratora zapytań, modelu, widoku wykresu i Eksploratora właściwości. Nie ma jeszcze danych na ekranie." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. Wybierz przycisk **Zaloguj** w prawym górnym rogu okna, jak pokazano na poniższej ilustracji, aby skonfigurować Eksploratora ADT do pracy z utworzonym wystąpieniem.

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="W Eksploratorze ADT Wyróżnij ikonę logowania w górnej części okna. Ikona pokazuje prostą Silhouette osoby nałożonej przy użyciu Silhouette klucza." lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. Wprowadź adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji, który został zebrany wcześniej w sekcji [Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji](#set-up-an-azure-digital-twins-instance) w formacie *https://{Nazwa hosta wystąpienia}*.

>[!NOTE]
> Te informacje można ponownie odwiedzać lub edytować w dowolnym momencie, wybierając tę samą ikonę w celu ponownego otwarcia pola **Zaloguj** . Dane zostaną zachowane.

> [!TIP]
> Jeśli `SignalRService.subscribe` podczas nawiązywania połączenia pojawi się komunikat o błędzie, upewnij się, że adres URL usługi Azure Digital bliźniaczych reprezentacji zaczyna się od *https://*.

Jeśli zobaczysz **wymagane uprawnienia** do okna podręcznego od firmy Microsoft, przyznaj zgodę na tę aplikację i zaakceptuj ją, aby kontynuować.

## <a name="add-the-sample-data"></a>Dodawanie przykładowych danych

Następnie zaimportujemy przykładowy scenariusz i wykres do Eksploratora ADT. Przykładowy scenariusz znajduje się również w folderze **Azure_Digital_Twins__ADT__explorer** pobranym wcześniej.

### <a name="models"></a>Modele

Pierwszym krokiem w rozwiązaniu Digital bliźniaczych reprezentacji na platformie Azure jest zdefiniowanie słownika dla danego środowiska. Utworzysz [modele](concepts-models.md) niestandardowe opisujące typy jednostek, które istnieją w danym środowisku.

Każdy model jest pisany w języku, takim jak JSON-LD o nazwie Digital bliźniaczy Definition Language (DTDL). Każdy model opisuje pojedynczy typ jednostki pod względem *Właściwości*, *telemetrii*, *relacji* i *składników*. Później te modele będą używane jako podstawa bliźniaczych reprezentacji cyfrowych, która reprezentuje określone wystąpienia tych typów.

Zazwyczaj podczas tworzenia modelu należy wykonać trzy czynności:

1. Napisz definicję modelu. W samouczku szybki start ten krok jest już realizowany jako część przykładowego rozwiązania.
1. Sprawdź poprawność, aby upewnić się, że składnia jest dokładna. W samouczku szybki start ten krok jest już realizowany jako część przykładowego rozwiązania.
1. Przekaż go do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
 
W tym przewodniku szybki start pliki modelu są już zapisywane i weryfikowane. Są one dołączone do pobranego rozwiązania. W tej sekcji przekażesz dwa gotowe modele do wystąpienia, aby zdefiniować te składniki środowiska budowania:

* Floor
* Zmieścić

#### <a name="upload-models"></a>Przekazywanie modeli

Wykonaj następujące kroki, aby przekazać modele.

1. W polu **Widok modelu** wybierz ikonę **Przekaż model** .

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="W polu widok modelu zostanie wyróżniona środkowa ikona. Pokazuje strzałkę wskazującą chmurę." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. W wyświetlonym oknie dialogowym Wybieranie pliku przejdź do folderu **Azure_Digital_Twins__ADT__explorer/Client/examples** w pobranym repozytorium.
1. Wybierz pozycję **Room.js** i **Floor.jsna**, a następnie wybierz **przycisk OK**. Jeśli chcesz, możesz przekazać dodatkowe modele, ale nie będą one używane w tym przewodniku Szybki Start.
1. Wykonaj podręczne okno dialogowe z prośbą o zalogowanie się do konta platformy Azure.

>[!NOTE]
>Jeśli zostanie wyświetlony następujący komunikat o błędzie: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="odczytywanie okna podręcznego &quot;błąd: błąd podczas pobierania modeli: ClientAuthError: błąd otwierania okienka podręcznego. Taka sytuacja może wystąpić, jeśli używasz programu IE lub w przeglądarce są blokowane okienka wyskakujące. w dolnej części przycisku Zamknij." border="false"::: 
> Spróbuj wyłączyć blokowanie wyskakujących okienek lub użyć innej przeglądarki.

Eksplorator ADT teraz przekazuje te pliki modelu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Powinny one być wyświetlane w polu **Widok modelu** i wyświetlać przyjazne nazwy oraz pełne identyfikatory modeli. Możesz wybrać ikony wyświetlania informacji o **modelu** , aby wyświetlić kod DTDL.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Widok pola widok modelu z dwoma definicjami modelu wymienionymi w programie, Floor (dtmi: example; 1) i pokoju (dtmi: przykład: Pokój; 1). Ikona Wyświetl informacje o modelu pokazująca literę &quot;i&quot; w okręgu jest wyróżniona dla każdego modelu." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Bliźniaczych reprezentacji i wykres Wieloosiowy

Teraz, gdy niektóre modele zostały przekazane do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, można dodać [bliźniaczych reprezentacji cyfrowe](concepts-twins-graph.md) , które są zgodne z definicjami modelu.

Bliźniaczych reprezentacji Digital reprezentuje rzeczywiste jednostki w środowisku biznesowym. Mogą to być takie jak czujniki w farmie, lampy w kabinie lub — w tym przewodniku szybki start — pokoje na piętro budynku. Można utworzyć wiele bliźniaczych reprezentacji danego typu modelu, takich jak wiele pokojów, które używają modelu *Room* . Można połączyć je z relacjami do *grafu dwuosiowego* , który reprezentuje pełne środowisko.

W tej sekcji przekażemy przedtworzony bliźniaczych reprezentacji, które są połączone ze schematem. Wykres zawiera dwie piętra i dwa pokoje, połączone w następującym układzie:

* Floor0
    - Zawiera Room0
* Floor1
    - Zawiera Room1

#### <a name="import-the-graph"></a>Importowanie wykresu

Wykonaj następujące kroki, aby zaimportować wykres.

1. W polu **Widok wykresu** wybierz ikonę **Importuj wykres** .

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="W polu widok wykresu zostanie wyróżniona ikona. Pokazuje strzałkę wskazującą chmurę." lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. W polu Selektor plików przejdź do folderu **Azure_Digital_Twins__ADT__explorer/Client/examples** i wybierz **buildingScenario.xlsx** plik arkusza kalkulacyjnego. Ten plik zawiera opis przykładowego wykresu. Wybierz przycisk **OK**.

   Po kilku sekundach Eksplorator ADT otwiera widok **importu** , który pokazuje podgląd grafu do załadowania.

3. Aby potwierdzić przekazywanie wykresu, wybierz ikonę **Zapisz** w prawym górnym rogu pola **Widok wykresu** .

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Wyróżnianie ikony Zapisz w okienku podglądu wykresu." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Eksplorator ADT używa teraz przekazanego pliku do utworzenia żądanego bliźniaczych reprezentacji i relacji między nimi. Po zakończeniu zostanie wyświetlone okno dialogowe. Wybierz pozycję **Close** (Zamknij).

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Okno dialogowe z informacją o powodzeniu importowania grafu. Plik &quot;Import&quot; został pomyślnie odczytany. 4 bliźniaczych reprezentacji zaimportowano. zaimportowano 2 relacje &quot;." lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Wykres został teraz przekazany do Eksploratora ADT. Aby wyświetlić wykres, wybierz przycisk **Uruchom zapytanie** w oknie **Eksplorator Graf** w górnej części okna Eksploratora ADT.

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Zostanie wyróżniony przycisk Uruchom zapytanie w prawym górnym rogu okna." lightbox="media/quickstart-adt-explorer/run-query.png":::

Ta akcja uruchamia domyślne zapytanie w celu wybrania i wyświetlenia wszystkich bliźniaczych reprezentacji cyfrowych. Eksplorator ADT pobiera wszystkie bliźniaczych reprezentacji i relacje z usługi. Rysuje wykres zdefiniowany przez siebie w polu **Widok wykresu** .

## <a name="explore-the-graph"></a>Eksplorowanie grafu

Teraz można zobaczyć przekazany wykres przykładowego scenariusza.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Widok wykresu z wykresem bliźniaczym w obrębie. Okrąg oznaczony etykietą &quot;floor1&quot; jest połączony przez strzałkę o nazwie &quot;Contains&quot; z kółkiem o nazwie &quot;room1&quot;. Okrąg oznaczony etykietą &quot;floor0&quot; jest połączony przez strzałkę o nazwie &quot;Contains&quot; z kółkiem o nazwie &quot;room0&quot;.":::

Okręgi ("węzły grafu") reprezentują cyfrowe bliźniaczych reprezentacji. Wiersze reprezentują relacje. **Floor0** bliźniaczy zawiera **Room0**, a **Floor1a** zawiera **Room1**.

Jeśli używasz myszy, możesz przeciągnąć fragmenty wykresu, aby przenieść je wokół siebie.

### <a name="view-twin-properties"></a>Wyświetl właściwości przędzy

Możesz wybrać sznurek, aby wyświetlić listę jego właściwości i ich wartości w oknie **Eksplorator właściwości** .

Oto właściwości Room0:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Zaznacz pole Eksplorator właściwości, w którym są wyświetlane właściwości Room0, które obejmują (między innymi) pole $dtId Room0, pole temperatury 70 i pole wilgotności 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 ma temperaturę 70.

Oto właściwości Room1:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Zaznacz pole Eksplorator właściwości, w którym są wyświetlane właściwości Room1, które obejmują (między innymi) pole $dtId Room1, pole temperatury 80 i pole wilgotności 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 ma temperaturę 80.

### <a name="query-the-graph"></a>Tworzenie zapytań względem grafu

Główną funkcją usługi Azure Digital bliźniaczych reprezentacji jest możliwość łatwego i wydajnego [wykonywania zapytań](concepts-query-language.md) na pytania dotyczące środowiska.

Jednym ze sposobów wykonywania zapytań dotyczących bliźniaczych reprezentacji w grafie jest ich *Właściwości*. Zapytania oparte na właściwościach mogą pomóc w udzieleniu odpowiedzi na różne pytania. Na przykład można znaleźć elementy odstające w środowisku, które mogą wymagać uwagi.

W tej sekcji zostanie uruchomione zapytanie, aby odpowiedzieć na pytanie, ile bliźniaczych reprezentacji w środowisku ma temperaturę powyżej 75.

Aby wyświetlić odpowiedź, uruchom następujące zapytanie w polu **Eksplorator zapytań** .

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Wycofaj z wyświetlania właściwości przędzy wcześniej, że Room0 ma temperaturę 70, a Room1 ma temperaturę 80. Z tego powodu tylko Room1 są wyświetlane w wynikach w tym miejscu.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Wyniki zapytania właściwości, które pokazują tylko Room1." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> W powyższym zapytaniu są również obsługiwane inne operatory porównania (<, >, = lub! =). Aby wypróbować Twoje pytania, możesz spróbować podłączyć te operatory, różne wartości lub różne właściwości sznurka do zapytania.

## <a name="edit-data-in-the-graph"></a>Edytowanie danych na grafie

Za pomocą Eksploratora ADT można edytować właściwości bliźniaczych reprezentacji reprezentowanych w grafie. W tej sekcji będziemy podnieść temperaturę Room0 do 76.

Aby rozpocząć, wybierz pozycję **Room0** , aby wyświetlić jej listę właściwości w polu **Eksplorator właściwości** .

Właściwości na tej liście są edytowalne. Wybierz wartość temperatury **70** , aby włączyć wprowadzanie nowej wartości. Wprowadź **76**, a następnie wybierz ikonę **Zapisz** , aby zaktualizować temperaturę do **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Pole Eksplorator właściwości, w którym są wyświetlane właściwości Room0. Wartość temperatury jest polem edytowalnym pokazującym 76 i wyróżnieniem wokół ikony Zapisz." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Teraz zobaczysz okno **informacji o poprawkach** , w której zostanie wyświetlony kod poprawki, który został użyty w tle za pomocą [interfejsu API](how-to-use-apis-sdks.md) Digital bliźniaczych reprezentacji platformy Azure, aby przeprowadzić aktualizację. Wybierz pozycję **Close** (Zamknij).

### <a name="query-to-see-the-result"></a>Zapytanie w celu wyświetlenia wyniku

Aby sprawdzić, czy wykres pomyślnie zarejestrował aktualizację do temperatury Room0, należy ponownie uruchomić zapytanie ze starszej wersji, aby uzyskać wszystkie bliźniaczych reprezentacji w środowisku z temperaturą powyżej 75.

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Teraz, gdy temperatura Room0 została zmieniona z 70 na 76, oba bliźniaczych reprezentacji powinny być widoczne w wyniku.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Wyniki zapytania właściwości, które pokazują zarówno Room0, jak i Room1." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Przegląd i contextualize nauk

W tym przewodniku szybki start utworzono wystąpienie usługi Azure Digital bliźniaczych reprezentacji, połączono je z Eksploratorem ADT i wypełniono z przykładowym scenariuszem.

Następnie zbadasz wykres, wykonując następujące informacje:

* Korzystanie z zapytania, aby odpowiedzieć na pytanie dotyczące scenariusza.
* Edytowanie właściwości w dwucyfrowej formie dwuosiowej.
* Ponownie uruchomić zapytanie, aby zobaczyć, jak zmieniono odpowiedź w wyniku aktualizacji.

Celem tego ćwiczenia jest zademonstrowanie, jak można użyć grafu Digital bliźniaczych reprezentacji na potrzeby odpowiedzi na pytania dotyczące środowiska, nawet w przypadku zmiany środowiska.

W tym przewodniku Szybki Start Aktualizacja temperatury została wprowadzona ręcznie. Jest ona często oparta na usłudze Azure Digital bliźniaczych reprezentacji, która umożliwia łączenie cyfrowych bliźniaczych reprezentacji z rzeczywistymi urządzeniami IoT w taki sposób, aby automatycznie otrzymywały aktualizacje na podstawie danych telemetrycznych. W ten sposób można utworzyć Graf na żywo, który zawsze odzwierciedla rzeczywiste Stany środowiska. Za pomocą zapytań można uzyskać informacje o tym, co dzieje się w danym środowisku w czasie rzeczywistym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby zagnieździć prace dla tego przewodnika Szybki Start, należy najpierw zakończyć uruchomioną aplikację konsolową. Ta akcja powoduje wyłączenie połączenia z aplikacją ADT Explorer w przeglądarce. Nie będzie już można wyświetlać danych na żywo w przeglądarce. Możesz zamknąć kartę przeglądarka.

Jeśli planujesz kontynuować korzystanie z samouczków Digital bliźniaczych reprezentacji platformy Azure, możesz ponownie użyć tego wystąpienia w tym przewodniku szybki start dla tych artykułów i nie musisz go usunąć.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Na koniec Usuń przykładowy folder projektu, **Azure_Digital_Twins__ADT__explorer** pobrany na komputer lokalny. Może być konieczne usunięcie zarówno wersji spakowanej, jak i rozpakowanej.

## <a name="next-steps"></a>Następne kroki

Następnie przejdź do samouczków Digital bliźniaczych reprezentacji systemu Azure, aby utworzyć własny scenariusz i narzędzia interakcji dotyczące platformy Azure Digital bliźniaczych reprezentacji.

> [!div class="nextstepaction"]
> [Samouczek: kod aplikacji klienckiej](tutorial-code.md)