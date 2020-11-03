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
ms.openlocfilehash: 4c2900ed5ebe0df3ed827acc1a16caff3beaf4d4
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241093"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Przenoszenie wystąpienia usługi Azure Digital bliźniaczych reprezentacji do innego regionu platformy Azure

Jeśli musisz przenieść wystąpienie usługi Azure Digital bliźniaczych reprezentacji z jednego regionu do innego, bieżący proces polega na ponownym **utworzeniu zasobów w nowym regionie** , a następnie usunięciu oryginalnych zasobów. Po zakończeniu tego procesu będziesz pracować z nowym wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, który jest identyczny z pierwszym, z wyjątkiem zaktualizowanej lokalizacji.

Ten artykuł zawiera wskazówki dotyczące sposobu wykonania pełnego przenoszenia i kopiowania nad wszystko, co należy zrobić, aby nowe wystąpienie było zgodne z oryginalną wersją.

Ten proces obejmuje następujące kroki:
1. Przygotuj: Pobierz oryginalne modele, bliźniaczych reprezentacji i Graf.
2. Przenoszenie: Utwórz nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji w nowym regionie.
3. Przenieś: ponownie Wypełnij nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji.
    - Przekazuj oryginalne modele, bliźniaczych reprezentacji i Graf.
    - Utwórz ponownie punkty końcowe i trasy.
    - Połącz ponownie połączone zasoby.
4. Czyszczenie zasobów źródłowych: Usuń oryginalne wystąpienie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed podjęciem próby ponownego utworzenia wystąpienia usługi Azure Digital bliźniaczych reprezentacji dobrym pomysłem jest przechodzenie między składnikami oryginalnego wystąpienia i uzyskanie jasnego pomysłu na wszystkie fragmenty, które będą musiały zostać ponownie utworzone.

Oto kilka pytań, które warto wziąć pod uwagę:
* Jakie **modele** są przekazywane do mojego wystąpienia? Ile tam istnieje?
* Co to jest **bliźniaczych reprezentacji** w moim wystąpieniu? Ile tam istnieje?
* Jaki jest ogólny kształt **grafu** w moim wystąpieniu? Ile relacji istnieje?
* Jakie **punkty końcowe** są dostępne w moim wystąpieniu?
* Jakie **trasy** są dostępne w moim wystąpieniu? Czy mają one filtry?
* Gdzie moje wystąpienie **nawiązuje połączenie z innymi usługami platformy Azure** ? Niektóre typowe punkty integracji obejmują...
    - Event Grid, centrum zdarzeń lub Service Bus
    - Azure Functions
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - Usługa Device Provisioning (DPS)
* Jakie inne **aplikacje osobiste lub firmowe** mają połączenie z moim wystąpieniem?

Te informacje można zbierać przy użyciu [Azure Portal](https://portal.azure.com), [interfejsów API Digital bliźniaczych reprezentacji platformy Azure i zestawów SDK](how-to-use-apis-sdks.md), [poleceń interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md)lub przykładowego [Eksploratora bliźniaczych reprezentacji (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

## <a name="prepare"></a>Przygotowywanie

Ta sekcja zostanie przygotowana do ponownego utworzenia wystąpienia, **pobierając oryginalne modele, bliźniaczych reprezentacji i Graf** z oryginalnego wystąpienia. W tym artykule jest używany przykład [Eksploratora Digital bliźniaczych reprezentacji (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

>[!NOTE]
>Być może masz już pliki zawierające modele i/lub Graf w wystąpieniu. Jeśli tak, nie musisz pobierać wszystkiego ponownie — tylko brakujące fragmenty lub elementy, które mogły ulec zmianie od pierwotnie przekazanego pliku (na przykład bliźniaczych reprezentacji, które mogły zostać zaktualizowane przy użyciu nowych danych).

### <a name="limitations-of-adt-explorer"></a>Ograniczenia dotyczące Eksploratora ADT

[Przykładowy Eksplorator usługi Azure Digital bliźniaczych reprezentacji (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) jest przykładem aplikacji klienta, który obsługuje wizualną reprezentację grafu i zapewnia wizualną interakcję z Twoim wystąpieniem. W tym artykule pokazano, jak używać go do pobierania i późniejszego ponownego przekazywania, modeli, bliźniaczych reprezentacji i wykresów.

Należy jednak pamiętać, że jest to **przykładowe** narzędzie, a nie kompletne. Nie został on przetestowany i nie został skompilowany do obsługi grafów o dużym rozmiarze. W związku z tym należy wziąć pod uwagę następujące wstępnie wbudowane ograniczenia dotyczące próbkowania:
* Próbka została obecnie przetestowana tylko w rozmiarze grafu do 1000 węzłów i relacji 2000
* Przykład nie obsługuje ponawiania próby w przypadku wszelkich sporadycznych błędów
* Przykład niekoniecznie powiadamia użytkownika, jeśli przekazane dane są niekompletne
* Przykład nie obsługuje błędów wynikających z bardzo dużych wykresów przekraczających dostępne zasoby, takie jak pamięć

Jeśli przykład nie jest w stanie obsłużyć rozmiaru grafu, możesz wyeksportować i zaimportować wykres przy użyciu innych narzędzi deweloperskich Digital bliźniaczych reprezentacji:
* [Polecenia interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md)
* [Interfejsy API i zestawy SDK Digital bliźniaczych reprezentacji na platformie Azure](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>Konfigurowanie aplikacji ADT Explorer

Aby kontynuować pracę z Eksploratorem ADT, najpierw Pobierz przykładowy kod aplikacji i skonfiguruj go do uruchamiania na komputerze. 

Przejdź do przykładu: [Eksplorator Digital bliźniaczych reprezentacji (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Naciśnij przycisk *Pobierz zip* , aby pobrać plik *. Plik ZIP* tego przykładowego kodu na maszynę jako _**Azure_Digital_Twins__ADT__explorer.zip**_. Rozpakuj plik.

Następnie skonfiguruj i Skonfiguruj uprawnienia dla Eksploratora ADT. Aby to zrobić, postępuj zgodnie z instrukcjami w sekcji [*Konfigurowanie programu Azure Digital bliźniaczych reprezentacji i ADT Explorer w programie*](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) Azure Digital bliźniaczych reprezentacji — Szybki Start. Ta sekcja przeprowadzi Cię przez następujące kroki:
1. Skonfiguruj wystąpienie usługi Azure Digital bliźniaczych reprezentacji (możesz pominąć tę część, ponieważ istnieje już wystąpienie)
2. Skonfiguruj lokalne poświadczenia platformy Azure, aby zapewnić dostęp do wystąpienia
3. Uruchom Eksploratora ADT i skonfiguruj go tak, aby łączył się z wystąpieniem. Będziesz używać **nazwy hosta** oryginalnego wystąpienia usługi Azure Digital bliźniaczych reprezentacji, które jest przenoszone.

Teraz aplikacja Przykładowa ADT Explorer jest uruchamiana w przeglądarce na komputerze. Próbka powinna być połączona z oryginalnym wystąpieniem usługi Azure Digital bliźniaczych reprezentacji.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Okno przeglądarki pokazujące aplikację uruchomioną na hoście lokalnym: 3000. Aplikacja jest nazywana Eksploratorem ADT i zawiera pola Eksploratora zapytań, modelu, widoku wykresu i Eksploratora właściwości. Nie ma jeszcze danych na ekranie." lightbox="media/how-to-move-regions/explorer-blank.png":::

Aby zweryfikować połączenie, możesz nacisnąć przycisk *Uruchom zapytanie* , aby uruchomić domyślne zapytanie, które wyświetla wszystkie bliźniaczych reprezentacji i relacje w grafie w oknie *Eksplorator grafów* .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Zostanie wyróżniony przycisk odczytu &quot;uruchom zapytanie&quot; w górnej części okna" lightbox="media/how-to-move-regions/run-query.png":::

Możesz pozostawić uruchomiony program ADT Explorer, ponieważ będzie on używany ponownie w dalszej części tego artykułu, aby ponownie przekazać te elementy do nowego wystąpienia w regionie docelowym.

### <a name="download-models-twins-and-graph"></a>Pobierz modele, bliźniaczych reprezentacji i Graf

Następnie Pobierz modele, bliźniaczych reprezentacji i Graf w rozwiązaniu na swoją maszynę.

Aby jednocześnie pobrać wszystkie z nich, należy najpierw upewnić się, że pełny wykres jest wyświetlany w polu *Widok wykresu* (możesz to zrobić, ponownie uruchamiając domyślne zapytanie `SELECT * FROM digitaltwins` w oknie *Eksplorator zapytań* ).
 
Następnie kliknij ikonę *Eksportuj wykres* w polu *Widok wykresu* .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="W polu widok wykresu zostanie wyróżniona ikona. Pokazuje strzałkę wskazującą w dół w chmurze." lightbox="media/how-to-move-regions/export-graph.png":::

Spowoduje to włączenie linku *pobierania* w *widoku wykresu*. Wybierz go, aby pobrać reprezentację wyniku zapytania w formacie JSON, w tym modele, bliźniaczych reprezentacji i relacje. Powinno to spowodować pobranie pliku *JSON* na komputer.

>[!NOTE]
>Jeśli pobrany plik ma inne rozszerzenie pliku, spróbuj edytować rozszerzenie bezpośrednio i zmienić je na *Format JSON*.

## <a name="move"></a>Move

Następnie zakończy się "Przenieś" wystąpienia, tworząc nowe wystąpienie w regionie docelowym i wypełniając je danymi i składnikami z oryginalnego wystąpienia.

### <a name="create-a-new-instance"></a>Utwórz nowe wystąpienie

Najpierw **Utwórz nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji w regionie docelowym**. Aby to zrobić, wykonaj kroki opisane w temacie [*How to: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md), pamiętając o następujących wskaźnikach:
* Tę samą nazwę można zachować, **Jeśli** znajduje się ona w innej grupie zasobów. Jeśli musisz użyć tej samej grupy zasobów, która zawiera oryginalne wystąpienie, nowe wystąpienie będzie potrzebować własnej odrębnej nazwy.
* Wprowadź nowy region docelowy po wyświetleniu monitu o lokalizację.

Po zakończeniu tej czynności potrzebna będzie **Nazwa hosta** nowego wystąpienia, aby kontynuować konfigurowanie z danymi. Jeśli nie zanotujesz tego podczas instalacji, możesz wykonać [te instrukcje](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) , aby pobrać je teraz z Azure Portal.

### <a name="repopulate-old-instance"></a>Ponownie Wypełnij stare wystąpienie

Następnie nowe wystąpienie zostanie skonfigurowane tak, aby było to kopia oryginalnego programu.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>Przekazywanie oryginalnych modeli, bliźniaczych reprezentacji i grafów przy użyciu Eksploratora ADT

W tej sekcji można przekazywać modele, bliźniaczych reprezentacji i wykresy do nowego wystąpienia. Jeśli nie masz żadnych modeli, bliźniaczych reprezentacji lub wykresów w oryginalnym wystąpieniu lub nie chcesz przenosić ich do nowego wystąpienia, możesz przejść do [następnej sekcji](#recreate-endpoints-and-routes).

W przeciwnym razie, aby kontynuować, Wróć do okna przeglądarki z uruchomionym **Eksploratorem ADT** i wykonaj poniższe kroki.

##### <a name="connect-to-the-new-instance"></a>Połącz z nowym wystąpieniem

Obecnie Eksplorator ADT jest połączony z oryginalnym wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Przełącz połączenie, aby wskazywało nowe wystąpienie, naciskając przycisk *Zaloguj* w górnej części okna. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="W Eksploratorze ADT Wyróżnij ikonę logowania w górnej części okna. Ikona pokazuje prostą Silhouette osoby nałożonej przy użyciu Silhouette klucza." lightbox="media/how-to-move-regions/sign-in.png":::

Zastąp *adres URL ADT* , aby odzwierciedlał nowe wystąpienie. Zmień tę wartość, aby odczytał *https://{New instance hostname}*.

*Połącz* trafień. Może zostać wyświetlony monit o zalogowanie się ponownie przy użyciu poświadczeń platformy Azure i/lub udzielenie tej aplikacji zgody na Twoje wystąpienie.

##### <a name="upload-models-twins-and-graph"></a>Przekazywanie modeli, bliźniaczych reprezentacji i grafów

Następnie Przekaż pobrane wcześniej składniki rozwiązania do nowego wystąpienia.

Aby przekazać **modele, bliźniaczych reprezentacji i Graf** , kliknij ikonę *importowania wykresu* w polu *Widok wykresu* . Ta opcja przekaże wszystkie trzy z tych składników jednocześnie (nawet modele, które nie są aktualnie używane na grafie).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="W polu widok wykresu zostanie wyróżniona ikona. Pokazuje strzałkę wskazującą chmurę." lightbox="media/how-to-move-regions/import-graph.png":::

W polu Selektor plików przejdź do pobranego wykresu. Wybierz plik Graph *. JSON* i *Otwórz* go.

Po kilku sekundach Eksplorator ADT otworzy widok *importu* z podglądem wykresu, który zostanie załadowany.

Aby potwierdzić przekazywanie wykresu, naciśnij ikonę *Zapisz* w prawym górnym rogu *widoku wykresu* :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Wyróżnianie ikony Zapisz w okienku podglądu wykresu" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Program ADT Explorer przekaże teraz modele i Graf (w tym bliźniaczych reprezentacji i relacje) do nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Powinien zostać wyświetlony komunikat o powodzeniu z zanotowania, ile modeli, bliźniaczych reprezentacji i relacji zostały przekazane:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Okno dialogowe informujące o powodzeniu importowania grafu. Plik &quot;Import&quot; został pomyślnie odczytany. zaimportowano 2 modele. 4 bliźniaczych reprezentacji zaimportowano. zaimportowano 2 relacje &quot;." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Aby sprawdzić, czy wszystko zostało przekazane pomyślnie, kliknij przycisk *Uruchom zapytanie* w oknie *Eksplorator grafu* , aby uruchomić domyślne zapytanie, które wyświetla wszystkie bliźniaczych reprezentacji i relacje na grafie. Spowoduje to również odświeżenie listy modeli w *widoku model*.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Zaznacz dookoła tego samego przycisku &quot;uruchom zapytanie&quot; od wcześniej w górnej części okna" lightbox="media/how-to-move-regions/run-query.png":::

Powinien zostać wyświetlony wykres ze wszystkimi bliźniaczych reprezentacji i relacjami wyświetlanymi w oknie *Eksplorator grafów* . W polu *Widok modelu* powinny również być widoczne modele.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Widok Eksploratora ADT przedstawiający 2 modele wyróżnione w polu &quot;model widoku&quot; i wykres wyróżniony w polu &quot;Eksplorator grafu&quot;" lightbox="media/how-to-move-regions/post-upload.png":::

To potwierdza, że modele, bliźniaczych reprezentacji i Graf zostały ponownie przekazane do nowego wystąpienia w regionie docelowym.

#### <a name="recreate-endpoints-and-routes"></a>Utwórz ponownie punkty końcowe i trasy

Jeśli masz **punkty końcowe i/lub trasy** w oryginalnym wystąpieniu, musisz odtworzyć je ponownie w nowym wystąpieniu. Jeśli nie masz żadnych punktów końcowych lub tras w oryginalnym wystąpieniu lub nie chcesz przenosić ich do nowego wystąpienia, możesz przejść do [następnej sekcji](#re-link-connected-resources).

W przeciwnym razie postępuj zgodnie z instrukcjami w temacie [*How to: Zarządzanie punktami końcowymi i trasami*](how-to-manage-routes-portal.md) przy użyciu nowego wystąpienia, pamiętając o następujących wskaźnikach: 
* Nie ma **potrzeby** ponownego tworzenia Event Grid, centrum zdarzeń lub zasobu Service Bus używanego dla punktu końcowego (sekcje *wymagań wstępnych* w instrukcjach punktu końcowego). Wystarczy ponownie utworzyć punkt końcowy w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.
* Można ponownie użyć punktów końcowych i **nazw** tras, ponieważ należą one do zakresu innego wystąpienia.
* Pamiętaj, aby dodać wszystkie wymagane **filtry** do tworzonych tras.

#### <a name="re-link-connected-resources"></a>Połącz ponownie połączone zasoby

Jeśli masz inne aplikacje lub zasoby platformy Azure, które są połączone z oryginalnym wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, musisz edytować połączenie, aby zamiast tego dotrzeć do nowego wystąpienia. Może to obejmować inne usługi platformy Azure lub aplikacje osobiste lub firmowe, które zostały skonfigurowane do pracy z usługą Azure Digital bliźniaczych reprezentacji.

Jeśli nie masz żadnych innych zasobów podłączonych do oryginalnego wystąpienia lub nie chcesz przenosić ich do nowego wystąpienia, możesz przejść do [następnej sekcji](#verify).

W przeciwnym razie, aby dojdzie do dalszego, weź pod uwagę połączone zasoby. Nie trzeba usuwać i ponownie tworzyć połączonych zasobów; Zamiast tego wystarczy edytować punkty, w których łączą się z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji za pomocą jego **nazwy hosta** , a następnie zaktualizować ten element, aby używał nazwy hosta nowego wystąpienia zamiast oryginalnego.

Dokładne zasoby, które należy edytować, zależą od danego scenariusza, ale poniżej przedstawiono kilka typowych punktów integracji:
* Azure Functions. Jeśli masz funkcję platformy Azure, której kod zawiera nazwę hosta oryginalnego wystąpienia, należy zaktualizować tę wartość do nazwy hosta nowej instancji i ponownie opublikować funkcję.
* Event Grid, Event Hubs lub Service Bus
* Logic Apps
* Time Series Insights
* Azure Maps
* Usługa Device Provisioning (DPS)
* Aplikacje osobiste lub firmowe poza platformą Azure, takie jak **aplikacja kliencka** utworzona w [*samouczku: kod aplikacji klienckiej*](tutorial-code.md), która nawiązuje połączenie z wystąpieniem i wywołuje interfejsy API Digital bliźniaczych reprezentacji platformy Azure
* **Nie trzeba** ponownie tworzyć rejestracji aplikacji usługi Azure AD. Jeśli używasz [rejestracji aplikacji](how-to-create-app-registration.md) do nawiązywania połączenia z interfejsami API Digital bliźniaczych reprezentacji platformy Azure, możesz ponownie użyć tej samej rejestracji aplikacji z nowym wystąpieniem.

Po wykonaniu tego kroku nowe wystąpienie w regionie docelowym powinno być kopią oryginalnego wystąpienia.

## <a name="verify"></a>Weryfikacja

Aby sprawdzić, czy nowe wystąpienie zostało prawidłowo skonfigurowane, możesz użyć następujących narzędzi:
* [**Azure Portal**](https://portal.azure.com) (dobry do sprawdzenia, czy nowe wystąpienie istnieje i znajduje się w poprawnym regionie docelowym, jest również dobrym sposobem na sprawdzanie punktów końcowych i tras oraz połączeń z innymi usługami platformy Azure)
* [ **Polecenie interfejsu wiersza polecenia** usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md) (dobre do sprawdzenia, czy nowe wystąpienie istnieje i znajduje się w poprawnym regionie docelowym, również może służyć do weryfikowania danych wystąpienia)
* [**Eksplorator ADT**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (dobry do weryfikowania danych wystąpienia, takich jak modele, bliźniaczych reprezentacji i Graph)
* [Interfejsy API i zestawy SDK Digital bliźniaczych reprezentacji na platformie Azure](how-to-use-apis-sdks.md) (przydatne do weryfikowania danych wystąpienia, takich jak modele, bliźniaczych reprezentacji i Graph; są również przydatne do sprawdzania punktów końcowych i tras)

Możesz również spróbować uruchomić dowolne aplikacje niestandardowe lub przepływy end-to-end uruchomione z oryginalnym wystąpieniem, aby sprawdzić, czy działają poprawnie z nowym wystąpieniem.

## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Teraz, gdy nowe wystąpienie jest skonfigurowane w regionie docelowym z kopią danych i połączeń oryginalnego wystąpienia, można **usunąć oryginalne wystąpienie**.

Można to zrobić w [Azure Portal](https://portal.azure.com)przy użyciu [interfejsu wiersza polecenia](how-to-use-cli.md)lub [interfejsów API płaszczyzny kontroli](how-to-use-apis-sdks.md#overview-control-plane-apis).

Aby usunąć wystąpienie przy użyciu Azure Portal, [Otwórz Portal](https://portal.azure.com) w oknie przeglądarki i przejdź do oryginalnego wystąpienia usługi Azure Digital bliźniaczych reprezentacji, wyszukując jego nazwę na pasku wyszukiwania portalu.

Naciśnij przycisk *Usuń* i postępuj zgodnie z monitami, aby zakończyć usuwanie.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Zobacz szczegóły wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal na karcie Przegląd. Przycisk Usuń jest wyróżniony":::