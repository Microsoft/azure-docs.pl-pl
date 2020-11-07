---
title: Przenieś wystąpienie do innego regionu platformy Azure
titleSuffix: Azure Digital Twins
description: Zobacz, jak przenieść wystąpienie usługi Azure Digital bliźniaczych reprezentacji z jednego regionu świadczenia usługi Azure do innego.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: cb532098cda290654d6bdebe9cec2edab8ccbf99
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94355791"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Przenoszenie wystąpienia usługi Azure Digital bliźniaczych reprezentacji do innego regionu platformy Azure

Jeśli musisz przenieść wystąpienie usługi Azure Digital bliźniaczych reprezentacji z jednego regionu do innego, bieżący proces polega na ponownym utworzeniu zasobów w nowym regionie, a następnie usunięciu oryginalnych zasobów. Po zakończeniu tego procesu będziesz pracować z nowym wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, który jest identyczny z pierwszym, z wyjątkiem zaktualizowanej lokalizacji.

Ten artykuł zawiera wskazówki dotyczące sposobu wykonywania kompletnej operacji przenoszenia i kopiowania nad wszystkimi elementami, które należy wykonać, aby nowe wystąpienie było zgodne z oryginalną wersją.

Ten proces obejmuje następujące kroki:

1. Przygotuj: Pobierz oryginalne modele, bliźniaczych reprezentacji i Graf.
1. Przenoszenie: Utwórz nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji w nowym regionie.
1. Przenieś: ponownie Wypełnij nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji.
    - Przekaż oryginalne modele, bliźniaczych reprezentacji i Graf.
    - Utwórz ponownie punkty końcowe i trasy.
    - Połącz ponownie połączone zasoby.
1. Czyszczenie zasobów źródłowych: Usuń oryginalne wystąpienie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed podjęciem próby ponownego utworzenia wystąpienia usługi Azure Digital bliźniaczych reprezentacji przejdź do składników oryginalnego wystąpienia, aby uzyskać jasne pomysły dotyczące wszystkich fragmentów, które będą musiały zostać ponownie utworzone.

Oto kilka kwestii, które należy wziąć pod uwagę:

* Jakie *modele* są przekazywane do mojego wystąpienia? Ile tam istnieje?
* Co to jest *bliźniaczych reprezentacji* w moim wystąpieniu? Ile tam istnieje?
* Jaki jest ogólny kształt *grafu* w moim wystąpieniu? Ile relacji istnieje?
* Jakie *punkty końcowe* są dostępne w moim wystąpieniu?
* Jakie *trasy* są dostępne w moim wystąpieniu? Czy mają one filtry?
* Gdzie moje wystąpienie *nawiązuje połączenie z innymi usługami platformy Azure* ? Niektóre typowe punkty integracji obejmują:

    - Azure Event Grid, Event Hubs platformy Azure lub Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Usługa Azure IoT Hub Device Provisioning
* Jakie inne *aplikacje osobiste lub firmowe* mają połączenie z moim wystąpieniem?

Te informacje można zbierać przy użyciu [Azure Portal](https://portal.azure.com), [interfejsów API Digital bliźniaczych reprezentacji oraz zestawów SDK](how-to-use-apis-sdks.md), [interfejsu wiersza polecenia platformy Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md)lub przykładowego [Eksploratora bliźniaczych reprezentacji (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

## <a name="prepare"></a>Przygotowywanie

W tej sekcji przygotujesz się do ponownego utworzenia wystąpienia, pobierając oryginalne modele, bliźniaczych reprezentacji i Graf z oryginalnego wystąpienia. W tym artykule w tym zadaniu jest stosowane przykładowe [ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

>[!NOTE]
>Być może masz już pliki, które zawierają modele lub Graf w wystąpieniu. Jeśli tak, nie musisz pobierać wszystkiego ponownie — tylko brakujące fragmenty lub elementy, które mogły ulec zmianie od pierwotnie przekazano te pliki. Na przykład możesz mieć bliźniaczych reprezentacji, które zostały zaktualizowane przy użyciu nowych danych.

### <a name="limitations-of-adt-explorer"></a>Ograniczenia dotyczące Eksploratora ADT

[Przykładowy Eksplorator ADT](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) jest przykładem aplikacji klienckiej, która obsługuje wizualną reprezentację grafu i zapewnia wizualną interakcję z Twoim wystąpieniem. W tym artykule pokazano, jak używać go do pobierania i późniejszego przekazywania, modeli, bliźniaczych reprezentacji i grafów.

Ten przykład nie jest kompletnym narzędziem. Nie zostały one przetestowane i nie zostały skompilowane w celu obsługi grafów o dużym rozmiarze. W związku z tym należy wziąć pod uwagę następujące wstępnie wbudowane ograniczenia dotyczące próbkowania:

* Próbka została obecnie przetestowana tylko w rozmiarze grafu do 1 000 węzłów i relacji 2 000.
* Przykład nie obsługuje ponawiania próby w przypadku wszelkich sporadycznych awarii.
* Przykład niekoniecznie powiadomi użytkownika, jeśli przekazane dane są niekompletne.
* Przykład nie obsługuje błędów wynikających z bardzo dużych wykresów, które przekraczają dostępne zasoby, takie jak pamięć.

Jeśli przykład nie jest w stanie obsłużyć rozmiaru grafu, możesz wyeksportować i zaimportować Graf przy użyciu innych narzędzi deweloperskich Digital bliźniaczych reprezentacji:

* [Polecenia interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md)
* [Interfejsy API i zestawy SDK Digital bliźniaczych reprezentacji na platformie Azure](how-to-use-apis-sdks.md)

### <a name="set-up-the-adt-explorer-application"></a>Konfigurowanie aplikacji ADT Explorer

Aby kontynuować pracę z Eksploratorem ADT, najpierw Pobierz przykładowy kod aplikacji i skonfiguruj go do uruchamiania na komputerze.

Aby uzyskać przykład, zobacz [ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Wybierz przycisk **Pobierz zip** , aby pobrać plik zip tego przykładowego kodu na maszynę jako **Azure_Digital_Twins__ADT__explorer.zip**. Rozpakuj plik.

Następnie skonfiguruj i Skonfiguruj uprawnienia dla Eksploratora ADT. Postępuj zgodnie z instrukcjami w sekcji [Konfigurowanie programu Azure Digital bliźniaczych reprezentacji i ADT Explorer w programie](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) Azure Digital bliźniaczych reprezentacji — Szybki Start. Ta sekcja przeprowadzi Cię przez następujące kroki:

1. Skonfiguruj wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Możesz pominąć tę część, ponieważ masz już wystąpienie.
1. Skonfiguruj lokalne poświadczenia platformy Azure, aby zapewnić dostęp do wystąpienia.
1. Uruchom Eksploratora ADT i skonfiguruj go tak, aby łączył się z wystąpieniem. Będziesz używać *nazwy hosta* oryginalnego wystąpienia usługi Azure Digital bliźniaczych reprezentacji, które jest przenoszone.

Teraz aplikacja Przykładowa ADT Explorer jest uruchamiana w przeglądarce na komputerze. Próbka powinna być połączona z oryginalnym wystąpieniem usługi Azure Digital bliźniaczych reprezentacji.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Okno przeglądarki pokazujące aplikację uruchomioną na hoście lokalnym: 3000. Aplikacja jest nazywana Eksploratorem ADT i zawiera pola Eksploratora zapytań, modelu, widoku wykresu i Eksploratora właściwości. Nie ma jeszcze danych na ekranie." lightbox="media/how-to-move-regions/explorer-blank.png":::

Aby zweryfikować połączenie, wybierz przycisk **Uruchom zapytanie** , aby uruchomić domyślne zapytanie, które wyświetla wszystkie bliźniaczych reprezentacji i relacje w grafie w oknie **Eksplorator grafów** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="W prawym górnym rogu okna jest wyróżniony przycisk odczytujący przebieg kwerendy." lightbox="media/how-to-move-regions/run-query.png":::

Możesz pozostawić uruchomiony program ADT Explorer, ponieważ będzie on używany ponownie w dalszej części tego artykułu, aby przekazywać te elementy do nowego wystąpienia w regionie docelowym.

### <a name="download-models-twins-and-graph"></a>Pobierz modele, bliźniaczych reprezentacji i Graf

Następnie Pobierz modele, bliźniaczych reprezentacji i Graf w rozwiązaniu na swoją maszynę.

Aby pobrać wszystkie te elementy jednocześnie, należy najpierw upewnić się, że pełny wykres pojawia się w polu **Widok wykresu** . Jeśli pełny wykres nie pojawia się już, uruchom ponownie zapytanie domyślne `SELECT * FROM digitaltwins` w oknie **Eksplorator zapytań** .
 
Następnie wybierz ikonę **Eksportuj wykres** w polu **Widok wykresu** .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="W polu widok wykresu zostanie wyróżniona ikona. Pokazuje strzałkę wskazującą w dół w chmurze." lightbox="media/how-to-move-regions/export-graph.png":::

Ta akcja włącza link **pobierania** w polu **Widok wykresu** . Wybierz go, aby pobrać reprezentację wyniku zapytania w formacie JSON, która obejmuje modele, bliźniaczych reprezentacji i relacje. Ta akcja powinna pobrać plik JSON na komputer.

>[!NOTE]
>Jeśli pobrany plik ma inne rozszerzenie pliku, spróbuj edytować rozszerzenie bezpośrednio i zmienić je na format JSON.

## <a name="move"></a>Move

Następnie Zakończ "Przenoszenie" wystąpienia, tworząc nowe wystąpienie w regionie docelowym. Następnie wypełnisz ją danymi i składnikami z oryginalnego wystąpienia.

### <a name="create-a-new-instance"></a>Utwórz nowe wystąpienie

Najpierw utwórz nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji w regionie docelowym. Wykonaj kroki opisane w temacie [Konfigurowanie wystąpienia i uwierzytelniania](how-to-set-up-instance-portal.md). Należy pamiętać o następujących wskaźnikach:

* Tę samą nazwę można zachować, *Jeśli* znajduje się ona w innej grupie zasobów. Jeśli musisz użyć tej samej grupy zasobów, która zawiera oryginalne wystąpienie, nowe wystąpienie będzie potrzebować własnej odrębnej nazwy.
* Wprowadź nowy region docelowy po wyświetleniu monitu o lokalizację.

Po zakończeniu tego kroku będzie potrzebna nazwa hosta nowego wystąpienia, aby kontynuować konfigurowanie z danymi. Jeśli podczas instalacji nie zawarto zanotować nazwy hosta, postępuj zgodnie z poniższymi [instrukcjami](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) , aby pobrać ją teraz z Azure Portal.

### <a name="repopulate-the-old-instance"></a>Ponownie Wypełnij stare wystąpienie

Następnie skonfigurujemy nowe wystąpienie tak, aby było ono kopią oryginalnego programu.

#### <a name="upload-the-original-models-twins-and-graph-by-using-adt-explorer"></a>Przekazywanie oryginalnych modeli, bliźniaczych reprezentacji i grafów przy użyciu Eksploratora ADT

W tej sekcji można przekazywać modele, bliźniaczych reprezentacji i Graf do nowego wystąpienia. Jeśli nie masz żadnych modeli, bliźniaczych reprezentacji lub grafu w oryginalnym wystąpieniu lub nie chcesz przenosić ich do nowego wystąpienia, możesz przejść do [następnej sekcji](#re-create-endpoints-and-routes).

W przeciwnym razie Wróć do okna przeglądarki z uruchomionym Eksploratorem ADT i wykonaj następujące kroki.

##### <a name="connect-to-the-new-instance"></a>Połącz z nowym wystąpieniem

Obecnie Eksplorator ADT jest połączony z oryginalnym wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Przełącz połączenie, aby wskazywało nowe wystąpienie, wybierając przycisk **Zaloguj** w prawym górnym rogu okna.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="W Eksploratorze ADT Wyróżnij ikonę logowania w prawym górnym rogu okna. Ikona pokazuje prostą Silhouette osoby nałożonej przy użyciu Silhouette klucza." lightbox="media/how-to-move-regions/sign-in.png":::

Zastąp **adres URL ADT** , aby odzwierciedlał nowe wystąpienie. Zmień tę wartość tak, aby odczytał *https://{New instance Name Host}*.

Wybierz pozycję **Połącz**. Może zostać wyświetlony monit o ponowne zalogowanie się przy użyciu poświadczeń platformy Azure lub udzielenie tej aplikacji zgody na Twoje wystąpienie.

##### <a name="upload-models-twins-and-graph"></a>Przekazywanie modeli, bliźniaczych reprezentacji i grafów

Następnie Przekaż pobrane wcześniej składniki rozwiązania do nowego wystąpienia.

Aby przekazać modele, bliźniaczych reprezentacji i Graf, wybierz ikonę **importowania grafu** w polu **Widok wykresu** . Ta opcja umożliwia przekazanie wszystkich trzech z tych składników jednocześnie. Jest to nawet przekazywanie modeli, które nie są aktualnie używane na grafie.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="W polu widok wykresu zostanie wyróżniona ikona. Pokazuje strzałkę wskazującą chmurę." lightbox="media/how-to-move-regions/import-graph.png":::

W polu Selektor plików przejdź do pobranego grafu. Wybierz plik Graph **. JSON** , a następnie wybierz pozycję **Otwórz**.

Po kilku sekundach Eksplorator ADT otwiera widok **importu** , który pokazuje podgląd grafu do załadowania.

Aby potwierdzić przekazywanie wykresu, wybierz ikonę **Zapisz** w prawym górnym rogu pola **Widok wykresu** .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Wyróżnianie ikony Zapisz w okienku podglądu wykresu." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Eksplorator ADT teraz przekazuje modele i Graf (w tym bliźniaczych reprezentacji i relacje) do nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Powinien zostać wyświetlony komunikat o powodzeniu z zanotowania, ile modeli, bliźniaczych reprezentacji i relacji zostały przekazane.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Okno dialogowe informujące o powodzeniu importowania grafu. Plik &quot;Import&quot; został pomyślnie odczytany. zaimportowano 2 modele. 4 bliźniaczych reprezentacji zaimportowano. zaimportowano 2 relacje &quot;." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Aby sprawdzić, czy wszystko zostało pomyślnie przekazane, wybierz przycisk **Uruchom zapytanie** w oknie **Eksplorator grafu** , aby uruchomić domyślne zapytanie, które wyświetla wszystkie bliźniaczych reprezentacji i relacje na grafie. Ta akcja powoduje również odświeżenie listy modeli w polu **Widok modelu** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Zaznacz wokół przycisku Uruchom zapytanie w prawym górnym rogu okna." lightbox="media/how-to-move-regions/run-query.png":::

Powinien zostać wyświetlony wykres ze wszystkimi bliźniaczych reprezentacji i relacjami wyświetlanymi w oknie **Eksplorator grafów** . W polu **Widok modelu** powinny również być widoczne modele.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Widok Eksploratora ADT, w którym wyświetlane są dwa modele wyróżnione w polu widok modelu i wykres wyróżniony w polu Eksplorator grafów." lightbox="media/how-to-move-regions/post-upload.png":::

Te widoki potwierdzają, że modele, bliźniaczych reprezentacji i Graf zostały ponownie przekazane do nowego wystąpienia w regionie docelowym.

#### <a name="re-create-endpoints-and-routes"></a>Utwórz ponownie punkty końcowe i trasy

Jeśli masz punkty końcowe lub trasy w oryginalnym wystąpieniu, musisz je ponownie utworzyć w nowym wystąpieniu. Jeśli nie masz żadnych punktów końcowych lub tras w oryginalnym wystąpieniu lub nie chcesz przenosić ich do nowego wystąpienia, możesz przejść do [następnej sekcji](#relink-connected-resources).

W przeciwnym razie postępuj zgodnie z instrukcjami w temacie [How to: Manage Endpoints and Routes using a](how-to-manage-routes-portal.md) New instance. Należy pamiętać o następujących wskaźnikach:

* *Nie* trzeba ponownie tworzyć zasobu Event Grid, Event Hubs lub Service Bus używanego dla punktu końcowego. Aby uzyskać więcej informacji, zobacz sekcję "wymagania wstępne" w instrukcjach punktu końcowego. Wystarczy ponownie utworzyć punkt końcowy w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.
* Można ponownie użyć punktów końcowych i nazw tras, ponieważ należą one do zakresu innego wystąpienia.
* Pamiętaj, aby dodać wszystkie wymagane filtry do tworzonych tras.

#### <a name="relink-connected-resources"></a>Połącz ponownie połączone zasoby

Jeśli masz inne aplikacje lub zasoby platformy Azure, które są połączone z oryginalnym wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, musisz edytować połączenie, aby zamiast tego dotrzeć do nowego wystąpienia. Te zasoby mogą obejmować inne usługi platformy Azure lub aplikacje osobiste lub firmowe, które zostały skonfigurowane do pracy z usługą Azure Digital bliźniaczych reprezentacji.

Jeśli nie masz żadnych innych zasobów podłączonych do oryginalnego wystąpienia lub nie chcesz przenosić ich do nowego wystąpienia, możesz przejść do [następnej sekcji](#verify).

W przeciwnym razie należy wziąć pod uwagę połączone zasoby w danym scenariuszu. Nie trzeba usuwać i ponownie tworzyć żadnych połączonych zasobów. Zamiast tego wystarczy edytować punkty, w których łączą się z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji za pomocą nazwy hosta. Następnie należy zaktualizować te punkty, aby używały nazwy hosta nowego wystąpienia zamiast oryginalnego.

Dokładne zasoby, które należy edytować, zależą od danego scenariusza, ale poniżej przedstawiono kilka typowych punktów integracji:

* Azure Functions. Jeśli masz funkcję platformy Azure, której kod zawiera nazwę hosta oryginalnego wystąpienia, należy zaktualizować tę wartość do nazwy hosta nowego wystąpienia i ponownie opublikować funkcję.
* Event Grid, Event Hubs lub Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Aplikacje osobiste lub firmowe poza platformą Azure, takie jak aplikacja kliencka utworzona w [samouczku: kod aplikacji klienckiej](tutorial-code.md), która nawiązuje połączenie z wystąpieniem i wywołuje interfejsy API Digital bliźniaczych reprezentacji platformy Azure.
* *Nie* trzeba ponownie tworzyć rejestracji aplikacji usługi Azure AD. Jeśli używasz [rejestracji aplikacji](how-to-create-app-registration.md) do nawiązywania połączenia z interfejsami API Digital bliźniaczych reprezentacji platformy Azure, możesz ponownie użyć tej samej rejestracji aplikacji z nowym wystąpieniem.

Po zakończeniu tego kroku nowe wystąpienie w regionie docelowym powinno być kopią oryginalnego wystąpienia.

## <a name="verify"></a>Weryfikacja

Aby sprawdzić, czy nowe wystąpienie zostało prawidłowo skonfigurowane, użyj następujących narzędzi:

* Użycie witryny [Azure Portal](https://portal.azure.com). Portal jest dobry do sprawdzenia, czy nowe wystąpienie istnieje i znajduje się w poprawnym regionie docelowym. Jest również dobrym sposobem na sprawdzanie punktów końcowych i tras oraz połączeń z innymi usługami platformy Azure.
* [Polecenia interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md). Te polecenia są dobre do sprawdzenia, czy nowe wystąpienie istnieje i znajduje się w poprawnym regionie docelowym. Mogą one również służyć do weryfikowania danych wystąpienia.
* [ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Eksplorator ADT jest dobry do weryfikowania danych wystąpienia, takich jak modele, bliźniaczych reprezentacji i wykresy.
* [Interfejsy API i zestawy SDK Digital bliźniaczych reprezentacji na platformie Azure](how-to-use-apis-sdks.md). Te zasoby są przydatne do weryfikowania danych wystąpienia, takich jak modele, bliźniaczych reprezentacji i wykresy. Są one również dobre dla weryfikowania punktów końcowych i tras.

Możesz również spróbować uruchomić dowolne aplikacje niestandardowe lub przepływy end-to-end uruchomione z oryginalnym wystąpieniem, aby ułatwić sprawdzenie, czy działają poprawnie z nowym wystąpieniem.

## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Teraz, gdy nowe wystąpienie jest skonfigurowane w regionie docelowym z kopią danych i połączeń oryginalnego wystąpienia, można usunąć oryginalne wystąpienie.

Możesz użyć [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](how-to-use-cli.md)lub [interfejsów API płaszczyzny kontroli](how-to-use-apis-sdks.md#overview-control-plane-apis).

Aby usunąć wystąpienie przy użyciu Azure Portal, [Otwórz Portal](https://portal.azure.com) w oknie przeglądarki i przejdź do oryginalnego wystąpienia usługi Azure Digital bliźniaczych reprezentacji, wyszukując nazwę na pasku wyszukiwania portalu.

Wybierz przycisk **Usuń** i postępuj zgodnie z monitami, aby zakończyć usuwanie.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Zobacz szczegóły wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal na karcie Przegląd. Przycisk Usuń jest wyróżniony.":::