---
title: Przenoszenie wystąpienia do innego regionu świadczenia usługi Azure
titleSuffix: Azure Digital Twins
description: Zobacz, jak przenieść wystąpienie Azure Digital Twins z jednego regionu świadczenia usługi Azure do innego.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 62db56ac9791cea7d6f1a40f794241ed68fa90fa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483583"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Przenoszenie wystąpienia Azure Digital Twins do innego regionu świadczenia usługi Azure

Jeśli musisz przenieść wystąpienie Azure Digital Twins z jednego regionu do innego, bieżącym procesem jest ponowne utworzenie zasobów w nowym regionie, a następnie usunięcie oryginalnych zasobów. Po zakończeniu tego procesu będziesz pracować z nowym wystąpieniem usługi Azure Digital Twins, które jest identyczne z pierwszym, z wyjątkiem zaktualizowanej lokalizacji.

Ten artykuł zawiera wskazówki dotyczące pełnego przenoszenia i kopiowania wszystkich potrzebnych danych, aby nowe wystąpienie było zgodne z oryginalnym.

Ten proces obejmuje następujące kroki:

1. Przygotowanie: pobierz oryginalne modele, bliźniacze i graf.
1. Przenoszenie: utwórz nowe Azure Digital Twins w nowym regionie.
1. Przeniesienie: ponownie przekłoń nowe Azure Digital Twins wystąpienie.
    - Przekaż oryginalne modele, bliźniacze i graf.
    - Utwórz ponownie punkty końcowe i trasy.
    - Ponowne łączenie połączonych zasobów.
1. Czyszczenie zasobów źródłowych: usuń oryginalne wystąpienie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed podjęciem próby ponownego utworzenia wystąpienia Azure Digital Twins przejdź przez składniki oryginalnego wystąpienia, aby uzyskać jasny obraz wszystkich elementów, które należy ponownie utworzyć.

Oto kilka kwestii, które należy wziąć pod uwagę:

* Jakie modele *są przekazywane* do mojego wystąpienia? Ilu jest?
* Jakie są *bliźniacze reprezentacji* w moim wystąpieniu? Ilu jest?
* Jaki jest ogólny kształt grafu *w* moim wystąpieniu? Ile jest relacji?
* Jakie *punkty końcowe* mam w moim wystąpieniu?
* Jakie *trasy* mam w moim wystąpieniu? Czy mają filtry?
* Gdzie moje wystąpienie łączy *się z innymi usługami platformy Azure?* Niektóre typowe punkty integracji to:

    - Azure Event Grid, Azure Event Hubs lub Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Usługa Azure IoT Hub Device Provisioning
* Jakie inne *aplikacje osobiste lub firmowe* mam, które łączą się z moim wystąpieniem?

Te informacje można zebrać przy użyciu interfejsów API Azure Portal [,](https://portal.azure.com) [Azure Digital Twins](how-to-use-apis-sdks.md)i zestawów [SDK,](how-to-use-cli.md)poleceń interfejsu wiersza polecenia Azure Digital Twins lub Azure Digital Twins Explorer [przykładu.](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)

## <a name="prepare"></a>Przygotowywanie

W tej sekcji przygotujesz się do ponownego utworzenia wystąpienia, pobierając oryginalne modele, bliźniacze i grafy z oryginalnego wystąpienia. W tym artykule użyto [przykładu Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) tego zadania.

>[!NOTE]
>Być może masz już pliki zawierające modele lub graf w twoim wystąpieniu. Jeśli tak, nie musisz ponownie pobierać wszystkiego — tylko te elementy, których brakuje, lub elementy, które mogły się zmienić od czasu przesłania tych plików. Na przykład możesz mieć bliźniacze reprezentacji, które zostały zaktualizowane o nowe dane.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Ograniczenia Azure Digital Twins Explorer

Przykład [Azure Digital Twins Explorer to](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) przykład aplikacji klienckiej, która obsługuje wizualną reprezentację grafu i zapewnia wizualną interakcję z wystąpieniem. W tym artykule pokazano, jak za jej pomocą pobierać modele, bliźniacze i grafy, a później ponownie je ładować.

Ten przykład nie jest kompletnym narzędziem. Nie została przetestowana pod kątem obciążenia i nie została zbudowana do obsługi grafów o dużym rozmiarze. W związku z tym należy pamiętać o następujących ograniczeniach przykładowych:

* Przykład został przetestowany tylko dla grafów o rozmiarach do 1000 węzłów i 2000 relacji.
* Przykład nie obsługuje ponawiania prób w przypadku sporadycznych awarii.
* Przykład nie musi powiadamiać użytkownika, jeśli przekazane dane są niekompletne.
* Przykład nie obsługuje błędów wynikających z bardzo dużych grafów, które przekraczają dostępne zasoby, takie jak pamięć.

Jeśli przykład nie jest w stanie obsłużyć rozmiaru grafu, możesz wyeksportować i zaimportować graf przy użyciu innych narzędzi Azure Digital Twins deweloperskie:

* [Azure Digital Twins poleceń interfejsu wiersza polecenia](how-to-use-cli.md)
* [Azure Digital Twins API i zestawy SDK](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Konfigurowanie aplikacji Azure Digital Twins Explorer aplikacji

Aby kontynuować Azure Digital Twins Explorer, najpierw pobierz przykładowy kod aplikacji i skonfiguruj go do uruchamiania na maszynie.

Aby uzyskać przykład, przejdź do [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Wybierz przycisk **Przeglądaj kod** pod tytułem, aby przejść do repozytorium GitHub dla przykładu. Wybierz przycisk **Kod** i pobierz **plik ZIP,** aby pobrać przykład jako *plik . Plik ZIP* na maszynie.

:::image type="content" source="media/how-to-move-regions/download-repo-zip.png" alt-text="Zrzut ekranu przedstawiający repozytorium digital-twins-explorer w witrynie GitHub. Po wybraniu przycisku Kod zostanie wyświetlone małe okno dialogowe, w którym wyróżniony jest przycisk Pobierz plik ZIP." lightbox="media/how-to-move-regions/download-repo-zip.png":::

Rozpakuj plik.

Następnie skonfiguruj uprawnienia dla Azure Digital Twins Explorer. Postępuj zgodnie z instrukcjami w [sekcji Azure Digital Twins i Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) przewodniku Szybki Azure Digital Twins przewodniku Szybki start. W tej sekcji przedstawiono następujące kroki:

1. Skonfiguruj wystąpienie Azure Digital Twins. Możesz pominąć tę część, ponieważ masz już wystąpienie.
1. Skonfiguruj lokalne poświadczenia platformy Azure, aby zapewnić dostęp do wystąpienia.
1. Uruchom Azure Digital Twins Explorer i skonfiguruj go w celu nawiązania połączenia z wystąpieniem. Użyjesz nazwy hosta *oryginalnego* wystąpienia Azure Digital Twins, które przenosisz.

Teraz powinna zostać uruchomiona Azure Digital Twins Explorer przykładowa aplikacja w przeglądarce na maszynie. Przykład powinien być połączony z oryginalnym Azure Digital Twins wystąpieniem.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Okno przeglądarki z wyświetloną aplikacją uruchamianą pod adresem localhost:3000. Aplikacja nosi nazwę Azure Digital Twins Explorer i zawiera pola Eksplorator zapytań, Widok modelu, Widok grafu i Eksplorator właściwości. Nie ma jeszcze żadnych danych na ekranie." lightbox="media/how-to-move-regions/explorer-blank.png":::

Aby sprawdzić połączenie,  wybierz przycisk Uruchom zapytanie, aby uruchomić domyślne zapytanie, które wyświetla wszystkie bliźniacze reprezentacji i relacje na grafie w **oknie EKSPLORATOR PROGRAMU GRAPH.**

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Przycisk Run Query (Uruchom zapytanie) w prawym górnym rogu okna jest wyróżniony." lightbox="media/how-to-move-regions/run-query.png":::

Możesz pozostawić Azure Digital Twins Explorer, ponieważ użyjesz go ponownie w dalszej części tego artykułu, aby ponownie załadować te elementy do nowego wystąpienia w regionie docelowym.

### <a name="download-models-twins-and-graph"></a>Pobieranie modeli, bliźniaczych reprezentacji i grafu

Następnie pobierz modele, bliźniacze i grafy w rozwiązaniu na swoją maszynę.

Aby pobrać wszystkie te elementy jednocześnie, najpierw upewnij się, że pełny graf jest widoczny w polu **WIDOK GRAFU.** Jeśli pełny wykres nie jest jeszcze wyświetlany, ponownie uruchomić domyślne zapytanie w `SELECT * FROM digitaltwins` **eksploratorze** zapytań.
 
Następnie wybierz ikonę **Eksportuj graf** w polu **WIDOK GRAFU.**

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="W polu Widok grafu wyróżniona jest ikona. Pokazuje strzałkę wskazującą w dół z chmury." lightbox="media/how-to-move-regions/export-graph.png":::

Ta akcja włącza link **Pobierz** w polu **WIDOK GRAFU.** Wybierz go, aby pobrać reprezentację wyniku zapytania opartą na danych JSON, która obejmuje modele, bliźniacze reprezentacje i relacje. Ta akcja powinna pobrać plik json na maszynę.

>[!NOTE]
>Jeśli pobrany plik wydaje się mieć inne rozszerzenie, spróbuj edytować rozszerzenie bezpośrednio i zmienić go na json.

## <a name="move"></a>Move

Następnie ukończysz "przenoszenie" wystąpienia, tworząc nowe wystąpienie w regionie docelowym. Następnie wypełnisz go danymi i składnikami z oryginalnego wystąpienia.

### <a name="create-a-new-instance"></a>Tworzenie nowego wystąpienia

Najpierw utwórz nowe wystąpienie klasy Azure Digital Twins w regionie docelowym. Wykonaj kroki opisane w [tece Instrukcje: Konfigurowanie wystąpienia i uwierzytelniania](how-to-set-up-instance-portal.md). Pamiętaj o tych wskaźnikach:

* Możesz zachować tę samą nazwę dla nowego *wystąpienia,* jeśli należy ono do innej grupy zasobów. Jeśli musisz użyć tej samej grupy zasobów, która zawiera Twoje oryginalne wystąpienie, nowe wystąpienie będzie potrzebować własnej odrębnej nazwy.
* Po wyświetleniu monitu o podanie lokalizacji wprowadź nowy region docelowy.

Po ukończeniu tego kroku będziesz potrzebować nazwy hosta nowego wystąpienia, aby kontynuować konfigurowanie go przy użyciu danych. Jeśli nie zanotowaliśmy nazwy hosta podczas instalacji, postępuj zgodnie z tymi instrukcjami, aby pobrać ją teraz z Azure Portal. [](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

### <a name="repopulate-the-old-instance"></a>Ponowne przeskaluj stare wystąpienie

Następnie skonfigurujesz nowe wystąpienie, aby było kopią oryginalnego wystąpienia.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Przekazywanie oryginalnych modeli, bliźniaczych reprezentacji i grafu przy użyciu Azure Digital Twins Explorer

W tej sekcji możesz ponownie załadować modele, bliźniacze i grafy do nowego wystąpienia. Jeśli nie masz żadnych modeli, bliźniaczych reprezentacji ani grafu w oryginalnym wystąpieniu lub nie chcesz przenieść ich do nowego wystąpienia, możesz od razu przejść do [następnej sekcji](#re-create-endpoints-and-routes).

W przeciwnym razie wróć do okna przeglądarki z Azure Digital Twins Explorer i wykonaj następujące kroki.

##### <a name="connect-to-the-new-instance"></a>Nawiązywanie połączenia z nowym wystąpieniem

Obecnie Azure Digital Twins Explorer jest połączony z oryginalnym Azure Digital Twins wystąpieniem. Przełącz połączenie, aby wskazać nowe wystąpienie, wybierając przycisk **Zaloguj** się w prawym górnym rogu okna.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure Digital Twins Explorer wyróżnić ikonę Zaloguj się w prawym górnym rogu okna. Ikona przedstawia prosty wygląd osoby nałogowej za pomocą ikony klucza." lightbox="media/how-to-move-regions/sign-in.png":::

Zastąp adres **URL narzędzia ADT,** aby odzwierciedlić nowe wystąpienie. Zmień tę wartość tak, aby odczytywała *https://{nazwa* hosta nowego wystąpienia} .

Wybierz pozycję **Połącz**. Może pojawić się prośba o zalogowanie się ponownie przy użyciu poświadczeń platformy Azure lub udzielenie tej zgody aplikacji dla wystąpienia.

##### <a name="upload-models-twins-and-graph"></a>Przekazywanie modeli, bliźniaczych reprezentacji i grafu

Następnie przekaż pobrane wcześniej składniki rozwiązania do nowego wystąpienia.

Aby przekazać modele, bliźniacze i grafy, wybierz ikonę **Importuj** graf w polu **WIDOK GRAFU.** Ta opcja umożliwia przekazanie wszystkich trzech z tych składników jednocześnie. Przesyła nawet modele, które nie są obecnie używane na grafie.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="W polu Widok grafu wyróżniona jest ikona. Pokazuje strzałkę wskazującą chmurę." lightbox="media/how-to-move-regions/import-graph.png":::

W polu selektora plików przejdź do pobranego grafu. Wybierz plik **grafów json,** a następnie wybierz pozycję **Otwórz**.

Po kilku sekundach program Azure Digital Twins Explorer widok **importu,** który pokazuje podgląd grafu do załadowania.

Aby potwierdzić przekazanie grafu, wybierz ikonę **Zapisz** w prawym górnym rogu pola **WIDOK GRAFU.**

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Wyróżnienie ikony Zapisz w okienku Podgląd programu Graph." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Azure Digital Twins Explorer teraz przekaże modele i graf (w tym bliźniacze i relacje) do nowego Azure Digital Twins wystąpienia. Powinien zostać wyświetlony komunikat o sukcesie z informacjami o tym, ile modeli, bliźniaczych reprezentacji i relacji zostało przekazanych.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Okno dialogowe wskazujące powodzenie importowania grafu. Jest w nim odczytywany &quot;Importowanie powiodło się. 2 zaimportowane modele. Zaimportowano 4 bliźniacze reprezentacji. 2 zaimportowane relacje&quot;." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Aby sprawdzić, czy wszystko  zostało przekazane pomyślnie, wybierz przycisk Uruchom zapytanie w polu EKSPLORATOR PROGRAMU **GRAPH,** aby uruchomić domyślne zapytanie, które wyświetla wszystkie bliźniacze reprezentacji i relacje na grafie. Ta akcja odświeża również listę modeli w **polu MODEL VIEW (WIDOK** MODELU).

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Wyróżnij wokół przycisku Uruchom zapytanie w prawym górnym rogu okna." lightbox="media/how-to-move-regions/run-query.png":::

W oknie EKSPLORATOR PROGRAMU GRAPH powinien zostać wyświetlony wykres ze wszystkimi bliźniaczych **reprezentacji i** relacjami. Modele powinny być również wyświetlane w polu **MODEL VIEW (WIDOK** MODELU).

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Widok przedstawiający dwa Azure Digital Twins Explorer wyróżnione w polu Widok modelu i wykres wyróżniony w oknie Eksplorator programu Graph." lightbox="media/how-to-move-regions/post-upload.png":::

Te widoki potwierdzają, że modele, bliźniacze i grafy zostały ponownie przekazane do nowego wystąpienia w regionie docelowym.

#### <a name="re-create-endpoints-and-routes"></a>Ponowne tworzenie punktów końcowych i tras

Jeśli masz punkty końcowe lub trasy w oryginalnym wystąpieniu, musisz je ponownie utworzyć w nowym wystąpieniu. Jeśli nie masz żadnych punktów końcowych ani tras w oryginalnym wystąpieniu lub nie chcesz przenieść ich do nowego wystąpienia, możesz od razu przejść do [następnej sekcji](#relink-connected-resources).

W przeciwnym razie wykonaj kroki opisane w [tece Instrukcje: Zarządzanie punktami końcowymi i trasami przy](how-to-manage-routes-portal.md) użyciu nowego wystąpienia. Pamiętaj o tych wskaźnikach:

* Nie *trzeba* ponownie tworzyć zasobu Event Grid, Event Hubs ani Service Bus zasobu, który jest używasz dla punktu końcowego. Aby uzyskać więcej informacji, zobacz sekcję "Wymagania wstępne" w instrukcjach dotyczących punktu końcowego. Wystarczy ponownie utworzyć punkt końcowy w wystąpieniu Azure Digital Twins końcowego.
* Możesz ponownie użyć nazw punktów końcowych i tras, ponieważ są one w zakresie innego wystąpienia.
* Pamiętaj, aby dodać wszystkie wymagane filtry do tworzyć tras.

#### <a name="relink-connected-resources"></a>Ponowne łączenie połączonych zasobów

Jeśli masz inne aplikacje lub zasoby platformy Azure połączone z oryginalnym wystąpieniem usługi Azure Digital Twins, musisz edytować połączenie, aby zamiast tego dotrzeć do nowego wystąpienia. Te zasoby mogą obejmować inne usługi platformy Azure lub aplikacje osobiste lub firmowe, które zostały skonfigurowani do pracy z Azure Digital Twins.

Jeśli nie masz żadnych innych zasobów połączonych z oryginalnym wystąpieniem lub nie chcesz przenieść ich do nowego wystąpienia, możesz od razu przejść do [następnej sekcji](#verify).

W przeciwnym razie rozważ połączone zasoby w swoim scenariuszu. Nie musisz usuwać ani ponownie tworzyć żadnych połączonych zasobów. Zamiast tego wystarczy edytować punkty, w których łączą się z wystąpieniem Azure Digital Twins za pomocą nazwy hosta. Następnie zaktualizuj te punkty, aby użyć nazwy hosta nowego wystąpienia zamiast oryginalnego.

Dokładne zasoby, które należy edytować, zależą od scenariusza, ale poniżej znajdują się niektóre typowe punkty integracji:

* Azure Functions. Jeśli masz funkcję platformy Azure, której kod zawiera nazwę hosta oryginalnego wystąpienia, zaktualizuj tę wartość do nazwy hosta nowego wystąpienia i ponownie opublikować funkcję.
* Event Grid, Event Hubs lub Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Aplikacje osobiste lub firmowe spoza platformy Azure, takie jak aplikacja klienca utworzona w [samouczku:](tutorial-code.md)kodowanie aplikacji klienckiej , która łączy się z wystąpieniem i Azure Digital Twins API.
* Nie trzeba ponownie *tworzyć rejestracji* aplikacji usługi Azure AD. Jeśli używasz rejestracji aplikacji [do](how-to-create-app-registration.md) nawiązywania połączenia z interfejsami API Azure Digital Twins, możesz ponownie użyć tej samej rejestracji aplikacji w nowym wystąpieniu.

Po ukończeniu tego kroku nowe wystąpienie w regionie docelowym powinno być kopią oryginalnego wystąpienia.

## <a name="verify"></a>Weryfikacja

Aby sprawdzić, czy nowe wystąpienie zostało prawidłowo skonfigurowane, użyj następujących narzędzi:

* Użycie witryny [Azure Portal](https://portal.azure.com). Portal jest odpowiedni do sprawdzania, czy nowe wystąpienie istnieje i znajduje się w poprawnym regionie docelowym. Jest również dobrym rozwiązaniem do weryfikowania punktów końcowych oraz tras i połączeń z innymi usługami platformy Azure.
* [Azure Digital Twins polecenia interfejsu wiersza polecenia](how-to-use-cli.md). Te polecenia są odpowiednie do weryfikowania, czy nowe wystąpienie istnieje i znajduje się w poprawnym regionie docelowym. Mogą one również służyć do weryfikowania danych wystąpienia.
* [Azure Digital Twins Explorer .](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) Azure Digital Twins Explorer jest dobrym rozwiązaniem do weryfikowania danych wystąpienia, takich jak modele, bliźniacze i grafy.
* [Azure Digital Twins interfejsów API i zestawów SDK.](how-to-use-apis-sdks.md) Te zasoby są dobre do weryfikowania danych wystąpienia, takich jak modele, bliźniacze reprezentacji i grafy. Są one również dobre do weryfikowania punktów końcowych i tras.

Możesz również spróbować uruchomić dowolne aplikacje niestandardowe lub przepływy typu end-to-end uruchomione z oryginalnym wystąpieniem, aby sprawdzić, czy działają one prawidłowo z nowym wystąpieniem.

## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Teraz, gdy nowe wystąpienie jest już ustawione w regionie docelowym z kopią danych i połączeń oryginalnego wystąpienia, możesz usunąć oryginalne wystąpienie.

Możesz użyć interfejsu [Azure Portal](https://portal.azure.com), interfejsu wiersza polecenia [platformy Azure](how-to-use-cli.md)lub [interfejsów API płaszczyzny sterowania.](how-to-use-apis-sdks.md#overview-control-plane-apis)

Aby usunąć wystąpienie przy użyciu narzędzia Azure Portal, otwórz [portal](https://portal.azure.com) w oknie przeglądarki i przejdź do oryginalnego wystąpienia usługi Azure Digital Twins, wyszukując nazwę na pasku wyszukiwania portalu.

Wybierz przycisk **Usuń** i postępuj zgodnie z monitami, aby zakończyć usuwanie.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Widok szczegółów Azure Digital Twins w Azure Portal na karcie Przegląd. Przycisk Usuń został wyróżniony.":::