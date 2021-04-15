---
title: Szybki start — eksplorowanie przykładowego scenariusza
titleSuffix: Azure Digital Twins
description: Szybki start — skorzystaj z Azure Digital Twins Explorer przykładu, aby zwizualizować i eksplorować wstępnie utworzony scenariusz.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: adafbdb903c9ba827e8ccf56890c69ee2ce4eaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495211"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>Szybki start — eksplorowanie przykładowego Azure Digital Twins scenariuszu przy użyciu Azure Digital Twins Explorer

Dzięki Azure Digital Twins możesz tworzyć modele rzeczywistych środowisk i wchodzić z nimi w interakcje. Najpierw należy modelować poszczególne elementy jako **cyfrowe bliźniacze reprezentacji.** Następnie połączysz je z **wykresem** wiedzy, który może reagować na wydarzenia na żywo i być pytany o informacje.

W tym przewodniku Szybki start poznasz wstępnie utworzony wykres Azure Digital Twins pomocą przykładowej aplikacji o [nazwie Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Za pomocą Azure Digital Twins Explorer:

- Przekaż cyfrową reprezentację środowiska.
- Wyświetlanie obrazów wizualnych bliźniaczych reprezentacji i grafu utworzonych w celu reprezentowania środowiska w Azure Digital Twins.
- Wykonywanie innych działań zarządzania za pośrednictwem opartego na przeglądarce, wizualnego środowisko.

Przewodnik Szybki start zawiera następujące główne kroki:

1. Skonfiguruj wystąpienie Azure Digital Twins i Azure Digital Twins Explorer.
1. Przekaż wstępnie utworzone modele i dane grafu w celu skonstruowania przykładowego scenariusza.
1. Zapoznaj się z utworzonym grafem scenariusza.
1. Wprowadzaj zmiany w grafie.

Przykładowy wykres, z który będziesz pracować, reprezentuje budynek z dwoma piętrami i dwoma pomieszczeniami. Wykres będzie wyglądać jak na poniższym obrazie:

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Widok grafu z czterech okrągłych węzłów połączonych strzałkami. Okrąg z etykietą &quot;Floor1&quot; jest połączony strzałką z etykietą &quot;zawiera&quot; z okręgiem oznaczonym jako &quot;Room1&quot;. Okrąg z etykietą &quot;Floor0&quot; jest połączony strzałką z etykietą &quot;zawiera&quot; z okręgiem oznaczonym jako &quot;Room0&quot;. &quot;Floor1&quot; i &quot;Floor0&quot; nie są połączone.":::

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego przewodnika Szybki start potrzebna jest subskrypcja platformy Azure. Jeśli jeszcze jej nie masz, utwórz [je bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

Konieczne będzie również **Node.js** na maszynie. Aby uzyskać najnowszą wersję, zobacz [Node.js](https://nodejs.org/).

Na koniec musisz również pobrać przykład, który będzie potrzebny podczas pracy z tym przewodnikem Szybki start. Przykładowa aplikacja to **Azure Digital Twins Explorer**. Ten przykład zawiera aplikację używaną w przewodniku Szybki start do ładowania i eksplorowania Azure Digital Twins scenariuszu. Zawiera również przykładowe pliki scenariuszy. Aby uzyskać przykład, przejdź do [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Wybierz przycisk **Przeglądaj kod** pod tytułem, aby przejść do repozytorium GitHub dla przykładu. Wybierz przycisk **Kod** i pobierz **plik ZIP,** aby pobrać przykład jako *plik . Plik ZIP.* 

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png" alt-text="Zrzut ekranu przedstawiający repozytorium digital-twins-explorer w witrynie GitHub. Po wybraniu przycisku Kod zostanie wyświetlone małe okno dialogowe, w którym wyróżniony jest przycisk Pobierz plik ZIP." lightbox="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png":::

Rozpakować **folderdigital-twins-explorer-main.zip** i wyodrębnić pliki.

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>Konfigurowanie Azure Digital Twins i Azure Digital Twins Explorer

Pierwszym krokiem podczas pracy z Azure Digital Twins jest skonfigurowanie Azure Digital Twins danych. Po utworzeniu wystąpienia usługi i skonfigurowaniu poświadczeń do uwierzytelniania w usłudze Azure Digital Twins Explorer możesz nawiązać połączenie z wystąpieniem w usłudze Azure Digital Twins Explorer i wypełnić je przykładami w dalszej części tego przewodnika Szybki start.

W pozostałej części tej sekcji oswajają cię te kroki.

### <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurowanie Azure Digital Twins danych

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Konfigurowanie lokalnych poświadczeń platformy Azure

Aplikacja Azure Digital Twins Explorer używa [obiektu DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (część biblioteki) do uwierzytelniania użytkowników w wystąpieniu Azure Digital Twins po uruchomieniu go na `Azure.Identity` komputerze lokalnym. Aby uzyskać więcej informacji na temat różnych sposobów uwierzytelniania aplikacji klienckiej za Azure Digital Twins, [zobacz Pisanie kodu uwierzytelniania aplikacji.](how-to-authenticate-client.md)

W przypadku tego typu uwierzytelniania usługa Azure Digital Twins Explorer wyszukiwać poświadczenia w środowisku lokalnym, takie jak logowanie do platformy Azure w lokalnym interfejsie wiersza polecenia platformy [Azure](/cli/azure/install-azure-cli) lub w usłudze Visual Studio lub Visual Studio Code. Z tego powodu należy zalogować **się do** platformy Azure lokalnie za pomocą jednego z tych mechanizmów, aby skonfigurować poświadczenia dla Azure Digital Twins Explorer aplikacji.

Jeśli już zalogowano się na platformie Azure za pomocą jednego z tych sposobów, możesz przejść do [następnej sekcji](#run-and-configure-azure-digital-twins-explorer).

W przeciwnym razie możesz zainstalować lokalny interfejs wiersza polecenia platformy Azure, wykonać następujące kroki:

1. Postępuj zgodnie z procesem [pod tym linkiem instalacyjnym,](/cli/azure/install-azure-cli) aby ukończyć instalację, która jest taka jak w Twoim systemu operacyjnego.
1. Otwórz okno konsoli na maszynie.
1. Uruchom `az login` narzędzie i postępuj zgodnie z monitami uwierzytelniania, aby zalogować się do konta platformy Azure.
1. Możliwy ostatni krok: Jeśli korzystasz z wielu subskrypcji platformy Azure w ramach tego konta, ustaw kontekst uwierzytelniania na subskrypcję platformy Azure zawierającą wystąpienie usługi Azure Digital Twins, uruchamiając program (będzie działać nazwa lub wartość identyfikatora `az account set --subscription "<your-subscription-name-or-ID>"` subskrypcji).

Po zalogowaniu się Azure Digital Twins Explorer automatycznie pobrać poświadczenia platformy Azure po uruchomieniu go w następnej sekcji.

Jeśli chcesz, możesz zamknąć okno konsoli uwierzytelniania. Możesz też zachować ją otwartą do użycia w następnym kroku.

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Uruchamianie i konfigurowanie Azure Digital Twins Explorer

Następnie uruchom aplikację Azure Digital Twins Explorer i skonfiguruj ją dla swojego Azure Digital Twins wystąpienia.

1. Przejdź do pobranego i rozpakowanego **folderu digital-twins-explorer-main.**
Otwórz okno konsoli w lokalizacji folderu **digital-twins-explorer-main/client/src.**

1. Uruchom `npm install` , aby pobrać wszystkie wymagane zależności.

1. Uruchom aplikację, `npm run start` uruchamiając .

   Po kilku sekundach zostanie otwarte okno przeglądarki, a aplikacja pojawi się w przeglądarce.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Okno przeglądarki z wyświetloną aplikacją uruchamianą pod adresem localhost:3000. Aplikacja nosi nazwę Azure Digital Twins Explorer i zawiera pola Eksplorator zapytań, Widok modelu, Widok grafu i Eksplorator właściwości. Nie ma jeszcze żadnych danych na ekranie." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

1. Wybierz przycisk **Zaloguj** się w prawym górnym rogu okna, jak pokazano na poniższej ilustracji, aby skonfigurować Azure Digital Twins Explorer do pracy ze skonfigurowanym wystąpieniem.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/sign-in.png" alt-text="Azure Digital Twins Explorer wyróżnić ikonę Zaloguj w górnej części okna. Ikona przedstawia prosty sposób nało że osoba jest nałogowana za pomocą ikony klucza." lightbox="media/quickstart-azure-digital-twins-explorer/sign-in.png":::

1. Wprowadź adres URL wystąpienia Azure Digital Twins, który został zebrany wcześniej w sekcji Konfigurowanie wystąpienia usługi [Azure Digital Twins,](#set-up-an-azure-digital-twins-instance) w formacie *https://<nazwa hosta-wystąpienia>*.

> [!TIP]
> Jeśli podczas nawiązywania połączenia zostanie wyświetlony komunikat o błędzie, upewnij się, że adres URL Azure Digital Twins zaczyna się `SignalRService.subscribe` *od https://*.
>
> Jeśli zostanie wyświetlony błąd uwierzytelniania,  możesz sprawdzić zmienne środowiskowe, aby upewnić się, że wszystkie zawarte w nich poświadczenia są prawidłowe dla Azure Digital Twins. Próby `DefaultAzureCredential` uwierzytelnienia względem typów poświadczeń w [określonej](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)kolejności , a zmienne środowiskowe są oceniane jako pierwsze.

Jeśli zostanie wyświetlony wyskakujące **okno z żądaniem** uprawnień od firmy Microsoft, przyznaj zgodę dla tej aplikacji i zaakceptuj dalsze.

>[!NOTE]
> Możesz ponownie sprawdzić lub edytować te informacje w dowolnym momencie, wybierając tę samą ikonę, aby ponownie **otworzyć pole** Zaloguj. Spowoduje to zachowanie przekazanych wartości.

## <a name="add-the-sample-data"></a>Dodawanie przykładowych danych

Następnie zaimportujesz przykładowy scenariusz i graf do Azure Digital Twins Explorer. Przykładowy scenariusz znajduje się również w pobranym wcześniej folderze **digital-twins-explorer-main.**

### <a name="models"></a>Modele

Pierwszym krokiem w Azure Digital Twins jest zdefiniowanie słownictwa dla twojego środowiska. Utworzysz modele niestandardowe [opisujące](concepts-models.md) typy jednostek, które istnieją w Twoim środowisku.

Każdy model jest napisany w języku, np. JSON-LD o nazwie Digital Twin Definition Language (DTDL). Każdy model opisuje jeden typ jednostki pod względem **właściwości,** telemetrii,  **relacji** i **składników**. Później użyjesz tych modeli jako podstawy dla cyfrowych bliźniaczych reprezentacji reprezentujących określone wystąpienia tych typów.

Zazwyczaj podczas tworzenia modelu należy wykonać trzy kroki:

1. Napisz definicję modelu. W tym przewodniku Szybki start ten krok jest już wykonywane w ramach przykładowego rozwiązania.
1. Zweryfikuj ją, aby upewnić się, że składnia jest dokładna. W tym przewodniku Szybki start ten krok jest już wykonywane w ramach przykładowego rozwiązania.
1. Przekaż go do swojego Azure Digital Twins wystąpienia.
 
W tym przewodniku Szybki start pliki modelu są już zapisywane i weryfikowane. Są one dołączone do pobranego rozwiązania. W tej sekcji przekażesz dwa wstępnie napisane modele do wystąpienia, aby zdefiniować następujące składniki środowiska budynku:

* Floor
* Pokojach

#### <a name="upload-models"></a>Przekazywanie modeli

Wykonaj następujące kroki, aby przekazać modele.

1. W **polu MODEL VIEW (WIDOK MODELU)** wybierz **ikonę Upload a Model (Przekaż** model).

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="W polu Widok modelu wyróżniona jest środkowa ikona. Pokazuje strzałkę wskazującą chmurę." lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. W wyświetlonym polu selektora plików przejdź do folderu **digital-twins-explorer-main/client/examples** w pobranym repozytorium.
1. Wybierz **Room.jsi wybierz** Floor.js **na** stronie , a następnie wybierz przycisk **OK.** Jeśli chcesz, możesz przekazać dodatkowe modele, ale nie będą one używane w tym przewodniku Szybki start.
1. Postępuj zgodnie z oknie podręcznym z prośbą o zalogowanie się do konta platformy Azure.

>[!NOTE]
>Jeśli zostanie wyświetlony następujący komunikat o błędzie: Okno podręczne z komunikatem "Błąd: Błąd podczas pobierania :::image type="content" source="media/quickstart-azure-digital-twins-explorer/error-models-popup.png" alt-text="modeli: ClientAuthError: Błąd podczas otwierania okna podręcznego. Może się to zdarzyć, jeśli" border="false"::: używasz programu IE lub jeśli okna podręczne są blokowane w przeglądarce", korzystając z przycisku Zamknij u dołu. 
> Spróbuj wyłączyć blokowanie wyskakujących okienek lub użyć innej przeglądarki.

Azure Digital Twins Explorer teraz przekaże te pliki modelu do Azure Digital Twins wystąpienia. Powinny one być wyświetlane w **polu MODEL VIEW (WIDOK MODELU)** i powinny wyświetlać przyjazne nazwy oraz pełne identyfikatory modeli. Możesz wybrać ikony **Wyświetl informacje o modelu,** aby wyświetlić ich kod DTDL.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="Widok pola Widok modelu z dwiema definicjami modelu wymienionymi wewnątrz: Floor (dtmi:example:Floor;1) i Room (dtmi:example:Room;1). Ikona informacji o modelu widoku przedstawiająca literę &quot;i&quot; w okręgu jest wyróżniona dla każdego modelu." lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Bliźniacze reprezentacji i graf bliźniaczych reprezentacji

Teraz, gdy niektóre modele zostały przekazane do Twojego Azure Digital Twins, możesz dodać bliźniacze bliźniacze reprezentacji, które są zgodne z definicjami modelu. [](concepts-twins-graph.md)

Bliźniacze reprezentacje cyfrowe reprezentują rzeczywiste jednostki w środowisku biznesowym. Mogą to być czujniki na farmie, światła w samochodzie lub — w tym przewodniku Szybki start — pomieszczenia na budynku. Można utworzyć wiele bliźniaczych reprezentacji dowolnego typu modelu, takich jak wiele pomieszczeń, które korzystają z *modelu Room* (Pomieszczenie). Możesz połączyć je z relacjami z grafem **bliźniaczych reprezentacji,** który reprezentuje pełne środowisko.

W tej sekcji przekażesz wstępnietworzone bliźniacze bliźniacze reprezentacji, które są połączone ze wstępnie sprekrementowany graf. Wykres zawiera dwie podłogi i dwa pomieszczenia połączone w następującym układzie:

* Floor0
    - Zawiera room0
* 1 łódka
    - Zawiera pomieszczenie 1

#### <a name="import-the-graph"></a>Importowanie grafu

Wykonaj następujące kroki, aby zaimportować graf.

1. W polu **WIDOK GRAFU** wybierz **ikonę Importuj graf.**

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="W polu Widok grafu wyróżniona jest ikona. Pokazuje strzałkę wskazującą chmurę." lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. W polu selektora plików przejdź do folderu **digital-twins-explorer-main/client/examples** i wybierz **buildingScenario.xlsx** arkusza kalkulacyjnego. Ten plik zawiera opis przykładowego grafu. Wybierz przycisk **OK**.

   Po kilku sekundach program Azure Digital Twins Explorer widok **importu,** który pokazuje podgląd grafu do załadowania.

3. Aby potwierdzić przekazanie grafu, wybierz ikonę **Zapisz** w prawym górnym rogu pola **WIDOK GRAFU.**

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="Wyróżnienie ikony Zapisz w okienku Podgląd programu Graph." lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Azure Digital Twins Explorer teraz używa przekazanego pliku do utworzenia żądanych bliźniaczych reprezentacji i relacji między nimi. Po zakończeniu zostanie wyświetlone okno dialogowe. Wybierz pozycję **Zamknij**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="Okno dialogowe wskazujące powodzenie importowania grafu. Jest w nim odczytywany &quot;Importowanie powiodło się. Zaimportowano 4 bliźniacze reprezentacji. 2 zaimportowane relacje&quot;." lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Graf został teraz przekazany do Azure Digital Twins Explorer. Aby wyświetlić wykres, wybierz przycisk **Uruchom zapytanie** w polu EKSPLORATOR PROGRAMU **GRAPH** w górnej Azure Digital Twins Explorer okna.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="Przycisk Uruchom zapytanie w prawym górnym rogu okna został wyróżniony." lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

Ta akcja uruchamia domyślne zapytanie w celu wybrania i wyświetlenia wszystkich bliźniaczych reprezentacji cyfrowych. Azure Digital Twins Explorer pobiera wszystkie bliźniacze i relacje z usługi. Rysuje on wykres zdefiniowany przez nie w polu **WIDOK GRAFU.**

## <a name="explore-the-graph"></a>Eksplorowanie grafu

Teraz możesz zobaczyć przekazany wykres przykładowego scenariusza.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Widok pola Widok grafu z grafem bliźniaczym. Okrąg z etykietą &quot;floor1&quot; jest połączony strzałką z etykietą &quot;zawiera&quot; z okręgiem oznaczonym jako &quot;room1&quot;. Okrąg z etykietą &quot;floor0&quot; jest połączony strzałką z etykietą &quot;zawiera&quot; z okręgiem oznaczonym jako &quot;room0&quot;.":::

Okręgi (grafowe "węzły") reprezentują bliźniacze reprezentacje cyfrowe. Linie reprezentują relacje. Bliźniak **Floor0** zawiera **room0,** a **bliźniak Floor1** **zawiera room1**.

Jeśli używasz myszy, możesz przeciągać fragmenty grafu, aby je poruszać.

### <a name="view-twin-properties"></a>Wyświetlanie właściwości bliźniaczych reprezentacji

Możesz wybrać bliźniacze reprezentacji, aby wyświetlić listę jej właściwości i ich wartości w polu **EKSPLORATOR** WŁAŚCIWOŚCI.

Oto właściwości obiektu Room0:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="Wyróżnij wokół pola Eksplorator właściwości właściwości, pokazując właściwości room0, w tym (między innymi) pole $dtId Room0, pole Temperature o wartości 70 i pole Humidity o wartości 30." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Temperatura pomieszczenia Room0 wynosi 70.

Oto właściwości pomieszczenia Room1:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="Wyróżnij wokół pola Eksplorator właściwości właściwości, pokazując właściwości Dla pomieszczenia Room1, które obejmują (między innymi) pole $dtId z pola Room1, pole Temperature o wartości 80 i pole Humidity o wartości 60." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Temperatura pomieszczenia Room1 wynosi 80.

### <a name="query-the-graph"></a>Tworzenie zapytań względem grafu

Główną funkcją Azure Digital Twins jest możliwość łatwego i wydajnego wykonywania zapytań dotyczących grafu bliźniaczej reprezentacji w celu odpowiadania na pytania dotyczące środowiska. [](concepts-query-language.md)

Jednym ze sposobów wykonywania zapytań o bliźniacze reprezentacji na grafie jest ich **właściwości**. Wykonywanie zapytań na podstawie właściwości może ułatwić udzielenie odpowiedzi na różne pytania. Na przykład możesz znaleźć w swoim środowisku odejścia, które mogą wymagać uwagi.

W tej sekcji zostanie uruchomione zapytanie, aby odpowiedzieć na pytanie, ile bliźniaczych reprezentacji w twoim środowisku ma temperaturę powyżej 75.

Aby wyświetlić odpowiedź, uruchom następujące zapytanie w polu **EKSPLORATOR ZAPYTAŃ.**

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Przypomnij sobie z wcześniejszych informacji o właściwościach bliźniaczych reprezentacji, że w pomieszczeniu Room0 temperatura wynosi 70, a w pomieszczeniu Room1 temperatura wynosi 80. Z tego powodu w wynikach jest w tym miejscu pojawia się tylko pomieszczenie Room1.
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="Wyniki zapytania właściwości pokazującego tylko room1." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> Inne operatory porównania (<, >, =, lub !=) są również obsługiwane w poprzednim zapytaniu. Możesz spróbować podłączyć te operatory, różne wartości lub różne właściwości bliźniaczych reprezentacji do zapytania, aby spróbować odpowiedzieć na własne pytania.

## <a name="edit-data-in-the-graph"></a>Edytowanie danych na grafie

Możesz użyć Azure Digital Twins Explorer, aby edytować właściwości bliźniaczych reprezentacji reprezentowanych na grafie. W tej sekcji podniesiemy temperaturę room0 do 76.

Aby rozpocząć, wybierz **pozycję Room0,** aby wyświetlić listę właściwości w polu **EKSPLORATOR WŁAŚCIWOŚCI.**

Właściwości na tej liście można edytować. Wybierz wartość temperatury **70,** aby włączyć wprowadzanie nowej wartości. Wprowadź **wartość 76** i wybierz **ikonę** Zapisz, aby zaktualizować temperaturę do **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Okno Eksploratora właściwości z właściwościami dla room0. Wartość temperatury to edytowalne pole z wartością 76. Wokół ikony Zapisz znajduje się wyróżnienie." lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Teraz zobaczysz okno Informacje  o poprawce, w którym pojawia się kod poprawki, który został użyty w tle wraz z Azure Digital Twins [API](how-to-use-apis-sdks.md) w celu aktualizacji. Wybierz pozycję **Zamknij**.

### <a name="query-to-see-the-result"></a>Zapytanie w celu zobaczenia wyniku

Aby sprawdzić, czy graf pomyślnie zarejestrował aktualizację temperatury dla pomieszczenia Room0, ponownie wytliczenie zapytania z wcześniejszej wersji w celu uzyskania wszystkich bliźniaczych reprezentacji w środowisku o temperaturze powyżej 75.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Teraz, gdy temperatura pomieszczenia Room0 została zmieniona z 70 na 76, obie bliźniacze reprezentacji powinny być wyświetlane w wyniku.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="Wyniki zapytania właściwości przedstawiające zarówno room0, jak i room1." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Przeglądanie i kontekstowe uczenie

W tym przewodniku Szybki start utworzono wystąpienie Azure Digital Twins, połączono je z Azure Digital Twins Explorer i wypełniono je przykładowym scenariuszem.

Następnie zbadano wykres, korzystając z:

* Używanie zapytania do odpowiadania na pytanie dotyczące scenariusza.
* Edytowanie właściwości w bliźniaczej reprezentacji cyfrowej.
* Ponownie uruchamia zapytanie, aby zobaczyć, jak zmieniła się odpowiedź w wyniku aktualizacji.

Celem tego ćwiczenia jest zademonstrować, jak za pomocą wykresu Azure Digital Twins odpowiedzieć na pytania dotyczące środowiska, nawet jeśli środowisko nadal się zmienia.

W tym przewodniku Szybki start ręcznie dokonano aktualizacji temperatury. Często łączy się Azure Digital Twins cyfrowymi z rzeczywistymi urządzeniami IoT, aby automatycznie otrzymywać aktualizacje na podstawie danych telemetrycznych. W ten sposób można utworzyć wykres na żywo, który zawsze odzwierciedla rzeczywisty stan środowiska. Za pomocą zapytań można uzyskać informacje o tym, co dzieje się w środowisku w czasie rzeczywistym.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby zakończyć pracę w tym przewodniku Szybki start, najpierw zakończ działanie aplikacji konsolowej. Ta akcja wyłącza połączenie z aplikacją Azure Digital Twins Explorer w przeglądarce. Nie będzie już można wyświetlać danych na żywo w przeglądarce. Możesz zamknąć kartę przeglądarki.

Następnie możesz wybrać zasoby, które chcesz usunąć, w zależności od tego, co chcesz zrobić dalej.

* **Jeśli planujesz kontynuować** pracę z samouczkami Azure Digital Twins samouczków, możesz ponownie użyć wystąpienia w tym przewodniku Szybki start dla tych artykułów i nie musisz go usuwać.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Możesz również usunąć folder projektu z komputera lokalnego.

## <a name="next-steps"></a>Następne kroki

Następnie przejdź do samouczków Azure Digital Twins, aby tworzyć własne, Azure Digital Twins scenariuszy i narzędzi interakcji.

> [!div class="nextstepaction"]
> [Samouczek: kod aplikacji klienckiej](tutorial-code.md)
