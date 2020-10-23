---
title: 'Samouczek: Eksplorowanie podstaw za pomocą przykładowej aplikacji klienckiej'
titleSuffix: Azure Digital Twins
description: Samouczek przedstawiający Eksplorowanie zestawów SDK Digital bliźniaczych reprezentacji platformy Azure przy użyciu przykładowej aplikacji wiersza polecenia
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d7c95317667999ac17803f08575e68641100b967
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460788"
---
# <a name="tutorial-explore-azure-digital-twins-with-a-sample-client-app"></a>Samouczek: Eksplorowanie usługi Azure Digital bliźniaczych reprezentacji za pomocą przykładowej aplikacji klienckiej

W tym samouczku przedstawiono przykładową aplikację, która implementuje aplikację kliencką wiersza polecenia, aby można było korzystać z usługi Azure Digital bliźniaczych reprezentacji. Aplikacja kliencka jest podobna do tej, która została zapisywana w [*samouczku: kod aplikacji klienckiej*](tutorial-code.md).

Możesz użyć tego przykładu, aby wykonać podstawowe akcje usługi Azure Digital bliźniaczych reprezentacji, takie jak przekazywanie modeli, tworzenie i modyfikowanie bliźniaczych reprezentacji oraz tworzenie relacji. Możesz również zapoznać się z kodem przykładu, aby dowiedzieć się więcej o interfejsie API Digital bliźniaczych reprezentacji platformy Azure, i ćwicz implementację własnych poleceń, modyfikując przykładowy projekt.

W tym samouczku będziesz...
> [!div class="checklist"]
> * Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji
> * Konfigurowanie przykładowej aplikacji wiersza polecenia do współpracy z wystąpieniem
> * Użyj aplikacji wiersza polecenia, aby poznać usługę Azure Digital bliźniaczych reprezentacji, w tym **modele**, **bliźniaczych reprezentacji Digital**, **relacje**i **zapytania**

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>Poznaj z przykładowym rozwiązaniem

Teraz, gdy zostanie skonfigurowane wystąpienie i Przykładowa aplikacja, będziesz używać przykładowego projektu i fragmentu kodu, który zawiera wstępnie zapisany przykładowy kod, aby utworzyć i poznać podstawowe rozwiązanie Digital bliźniaczych reprezentacji. Główne składniki rozwiązania to **modele**, **Digital bliźniaczych reprezentacji**i **relacje**, co prowadzi do wykresu queryableych **bliźniaczych** w środowisku.

### <a name="model-a-physical-environment-with-dtdl"></a>Modelowanie środowiska fizycznego za pomocą DTDL

Pierwszym krokiem tworzenia rozwiązania Digital bliźniaczych reprezentacji na platformie Azure jest definiowanie [**modeli**](concepts-models.md) bliźniaczych dla danego środowiska. 

Modele są podobne do klas w języku programowania zorientowanym na obiekty; udostępniają one szablony zdefiniowane przez użytkownika na potrzeby [cyfrowego bliźniaczych reprezentacji](concepts-twins-graph.md) i tworzenia wystąpienia w przyszłości. Są one zapisywane w języku przypominającym kod JSON o nazwie **Digital bliźniaczych reprezentacji Definition Language (DTDL)** i mogą definiować *Właściwości*, dane *telemetryczne*, *relacje*i *składniki*.

> [!NOTE]
> DTDL umożliwia także Definiowanie *poleceń* w Digital bliźniaczych reprezentacji. Polecenia nie są jednak obecnie obsługiwane w usłudze Azure Digital bliźniaczych reprezentacji.

W oknie programu Visual Studio, w którym jest otwarty projekt _**AdtE2ESample**_ , użyj okienka *Eksplorator rozwiązań* , aby przejść do folderu *AdtSampleApp\SampleClientApp\Models* . Ten folder zawiera przykładowe modele.

Wybierz pozycję *Room.jsna* , aby otworzyć ją w oknie edycji i zmienić ją w następujący sposób:

* **Zaktualizuj numer wersji**, aby wskazać, że dostarczasz nowszą wersję tego modelu. W tym celu należy zmienić *1* na końcu `@id` wartości na *2*. Dowolna liczba większa niż bieżący numer wersji również będzie działała.
* **Edytuj Właściwość**. Zmień nazwę `Humidity` właściwości na *HumidityLevel* (lub coś innego, jeśli chcesz. Jeśli używasz czegoś innego niż *HumidityLevel*, Zapamiętaj, które z nich korzystasz, i Kontynuuj, korzystając z niego, zamiast *HumidityLevel* w całym samouczku).
* **Dodaj właściwość**. Poniżej `HumidityLevel` właściwości, która zostanie zakończona w wierszu 15, wklej następujący kod, aby dodać `RoomName` Właściwość do pokoju:

    ```json
    ,
    {
      "@type": "Property",
      "name": "RoomName",
      "schema": "string"
    }
    ```
* **Dodaj relację**. Poniżej `RoomName` dodawanej właściwości wklej poniższy kod, aby dodać możliwość dla tego typu sznurka do formularza *zawiera* relacje z innymi bliźniaczych reprezentacji:

    ```json
    ,
    {
      "@type": "Relationship",
      "name": "contains",
    }
    ```

Gdy skończysz, zaktualizowany model powinien wyglądać następująco:

:::image type="content" source="media/tutorial-command-line-app/room-model.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację" border="false":::

Pamiętaj, aby zapisać plik przed przechodzeniem.

> [!TIP]
> Jeśli chcesz spróbować utworzyć własny model, możesz wkleić kod modelu *pokoju* do nowego pliku zapisanego przy użyciu rozszerzenia *JSON* w folderze *AdtSampleApp\SampleClientApp\Models* . Następnie zapoznaj się z dodawaniem właściwości i relacji, aby reprezentować wszystko, co chcesz. W przypadku pomysłów można także zapoznać się z innymi przykładowymi modelami w tym folderze.

> [!TIP] 
> Istnieje [przykład modułu sprawdzania poprawności](/samples/azure-samples/dtdl-validator/dtdl-validator) języka niezależny od DTDL, którego można użyć do sprawdzenia dokumentów modelu, aby upewnić się, że DTDL jest prawidłowy. Jest ona oparta na bibliotece analizatora DTDL, którą można dowiedzieć się więcej na temat postanowień [*: analizowanie i weryfikowanie modeli*](how-to-parse-models.md).

### <a name="get-started-with-the-command-line-app"></a>Wprowadzenie do aplikacji wiersza polecenia

Po zdefiniowaniu modelu pozostałe kroki obejmują korzystanie z przykładowej aplikacji w celu współdziałania z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Uruchom projekt przy użyciu tego przycisku na pasku narzędzi:

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

Otworzy się okno konsoli, przeprowadzisz uwierzytelnianie i poczekasz na polecenie. 
* Uwierzytelnianie jest obsługiwane w przeglądarce: domyślna przeglądarka sieci Web zostanie otwarta z monitem o uwierzytelnienie. Użyj tego monitu, aby zalogować się przy użyciu poświadczeń platformy Azure. Następnie można zamknąć kartę lub okno przeglądarki.

Poniżej znajduje się zrzut ekranu przedstawiający wygląd konsoli projektu:

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

> [!TIP]
> Aby uzyskać listę wszystkich możliwych poleceń, których można użyć z tym projektem, wprowadź `help` w konsoli projektu i naciśnij przycisk Return.
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

Pozostaw uruchomioną konsolę projektu dla pozostałej części kroków tego samouczka.

#### <a name="upload-models-to-azure-digital-twins"></a>Przekazywanie modeli do usługi Azure Digital bliźniaczych reprezentacji

Po zaprojektowaniu modeli należy przekazać je do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Spowoduje to skonfigurowanie wystąpienia usługi Digital bliźniaczych reprezentacji na platformie Azure przy użyciu własnego słownika domen niestandardowych. Po przekazaniu modeli można utworzyć dwuosiowe wystąpienia, które z nich korzystają.

W oknie Konsola projektu uruchom następujące polecenie, aby przekazać zaktualizowany model *pokoju* , a także model *podłogowy* , który będzie również używany w następnej sekcji do tworzenia różnych typów bliźniaczych reprezentacji.

```cmd/sh
CreateModels Room Floor
```

Dane wyjściowe powinny wskazywać, że modele zostały utworzone pomyślnie.

> [!TIP]
> Jeśli wcześniej zaprojektowano własny model, można go również przekazać w tym miejscu, dodając jego nazwę pliku (można opuścić rozszerzenie) na `Room Floor` liście w powyższym poleceniu.

Sprawdź, czy modele zostały utworzone przez uruchomienie polecenia `GetModels true` . Spowoduje to wysłanie zapytania do wystąpienia usługi Azure Digital bliźniaczych reprezentacji dla wszystkich przekazanych modeli i wydrukowanie ich pełnych informacji. Poszukaj edytowanego modelu *pokoju* w wynikach:

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

#### <a name="errors"></a>błędy

Przykładowa aplikacja obsługuje także błędy usługi. 

Uruchom ponownie `CreateModels` polecenie, aby ponowić próbę przekazania jednego z tych samych modeli, które zostały przekazane po raz drugi:

```cmd/sh
CreateModels Room
```

Ponieważ modeli nie można zastąpić, spowoduje to zwrócenie błędu usługi.
Aby uzyskać szczegółowe informacje na temat usuwania istniejących modeli, zobacz [*How to: Manage Custom models*](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

### <a name="create-digital-twins"></a>Utwórz cyfrowy bliźniaczych reprezentacji

Teraz, gdy niektóre modele zostały przekazane do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, można utworzyć [**cyfrowy bliźniaczych reprezentacji**](concepts-twins-graph.md) na podstawie definicji modelu. Bliźniaczych reprezentacji Digital reprezentuje jednostki w środowisku biznesowym — takie jak czujniki w farmie, pokoje w budynku lub lampy w kabinie. 

Aby utworzyć dwuosiową cyfrę, użyj `CreateDigitalTwin` polecenia. Należy odwoływać się do modelu, w którym bazuje sznury, i opcjonalnie definiować wartości początkowe dla każdej właściwości w modelu. Nie trzeba przekazywać żadnych informacji o relacji na tym etapie.

Uruchom ten kod w uruchomionej konsoli projektu, aby utworzyć kilka bliźniaczych reprezentacji w oparciu o model *pokoju* , który został zaktualizowany wcześniej, i inny model, *piętro*. Odwołaj ten *Pokój* ma trzy właściwości, więc możesz podać argumenty z początkowymi wartościami.

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> Jeśli wcześniej przekazano własny model, spróbuj wykonać własne polecenie na `CreateDigitalTwin` podstawie powyższych poleceń, aby dodać dwuosiowy Typ modelu.

Dane wyjściowe tych poleceń powinny wskazywać, że bliźniaczych reprezentacji zostały utworzone pomyślnie. 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

Możesz również sprawdzić, czy bliźniaczych reprezentacji zostały utworzone przez uruchomienie `Query` polecenia. To polecenie powoduje wysłanie zapytania do wystąpienia usługi Azure Digital bliźniaczych reprezentacji dla wszystkich bliźniaczych reprezentacji, które zawiera. W wynikach Szukaj *floor0*, *floor1*, *room0*i *room1* bliźniaczych reprezentacji.

#### <a name="modify-a-digital-twin"></a>Modyfikowanie cyfrowej sieci dwuosiowej

Możesz również zmodyfikować właściwości utworzonego przez siebie przędzę. Spróbuj uruchomić to polecenie, aby zmienić *Room0*pokoju z *room0* na *PresidentialSuite*:

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

Dane wyjściowe powinny wskazywać, że sznury zostały pomyślnie zaktualizowane.

Możesz również sprawdzić, uruchamiając to polecenie, aby zobaczyć informacje o *room0*:

```cmd/sh
GetDigitalTwin room0
```

Dane wyjściowe powinny odzwierciedlać zaktualizowaną nazwę.

> [!NOTE]
> Podstawowy interfejs API REST używa poprawki JSON do definiowania aktualizacji dla sznurka. Aplikacja wiersza polecenia odzwierciedla ten format, dzięki czemu można eksperymentować z rzeczywistymi oczekiwaniami interfejsów API.

### <a name="create-a-graph-by-adding-relationships"></a>Tworzenie wykresu przez Dodawanie relacji

Następnie można utworzyć niektóre **relacje** między tymi bliźniaczych reprezentacjiami, aby połączyć je z [**wykresem bliźniaczym**](concepts-twins-graph.md). Wykresy bliźniaczy są używane do reprezentowania całego środowiska. 

Aby dodać relację, użyj `CreateRelationship` polecenia. Określ splot, z którego pochodzi relacja, typ relacji do dodania oraz splot, z którym jest nawiązywane połączenie. Na koniec Podaj nazwę (identyfikator) dla relacji.

Uruchom Poniższy kod, aby dodać relację "Contains" z każdego utworzonego wcześniej bliźniaczych reprezentacji *podłogi* do odpowiedniej sieci *salonu* . Należy zauważyć, że w modelu *podłogi* musi istnieć relacja *Contains* , która jest możliwa.

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

Dane wyjściowe tych poleceń potwierdzają, że relacje zostały utworzone pomyślnie:

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

Możesz również sprawdzić relacje z dowolnym z poniższych poleceń, które wykonują zapytania dotyczące relacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.
* Aby wyświetlić wszystkie relacje wychodzące z każdego piętra (wyświetlanie relacji z jednej strony),
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* Aby wyświetlić wszystkie relacje docierające do każdego pokoju (wyświetlanie relacji na stronie "inne"),
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* Aby wykonać zapytanie dotyczące tych relacji pojedynczo, 
    ```cmd/sh
    GetRelationship floor0 relationship0
    GetRelationship floor1 relationship1
    ```

Bliźniaczych reprezentacji i relacje, które zostały skonfigurowane w tym samouczku, tworzą następujący wykres koncepcyjny:

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>Zbadaj Graf bliźniaczy, aby odpowiedzieć na pytania dotyczące środowiska

Główną funkcją usługi Azure Digital bliźniaczych reprezentacji jest możliwość łatwego i wydajnego [wykonywania zapytań](concepts-query-language.md) na pytania dotyczące środowiska. Uruchom następujące polecenia w uruchomionej konsoli projektu, aby uzyskać pomysł dotyczący tego, co się dzieje.

* **Jakie są wszystkie jednostki w moim środowisku reprezentowane w usłudze Azure Digital bliźniaczych reprezentacji?** (kwerenda All)

    ```cmd/sh
    Query
    ```

    Pozwala to na błyskawiczne przejęcie zasobów w środowisku i upewnienie się, że wszystko jest reprezentowane, tak jak w przypadku usługi Azure Digital bliźniaczych reprezentacji. Wynikiem tego jest dane wyjściowe zawierające każdą z nich. Oto fragment:

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

    >[!NOTE]
    >W przykładowym projekcie polecenie `Query` bez żadnych dodatkowych argumentów jest równoważne `Query SELECT * FROM DIGITALTWINS` . Aby wykonać zapytanie dotyczące wszystkich bliźniaczych reprezentacji w wystąpieniu przy użyciu [interfejsów API zapytań](/rest/api/digital-twins/dataplane/query) lub [poleceń interfejsu wiersza polecenia](how-to-use-cli.md), użyj dłuższego (kompletnego) zapytania.

* **Jakie są wszystkie pokoje w moim środowisku?** (zapytanie według modelu)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Możesz ograniczyć zapytanie do bliźniaczych reprezentacji pewnego typu, aby uzyskać bardziej szczegółowe informacje na temat tego, co zostało reprezentowane. W wyniku tego są wyświetlane *room0* i *room1*, **ale nie są** wyświetlane *floor0* ani *floor1* (ponieważ są to piętra, a nie pokoje).
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

* **Jakie są wszystkie pokoje w witrynie *floor0*?** (zapytanie według relacji)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Możesz wykonywać zapytania na podstawie relacji w grafie, aby uzyskać informacje na temat sposobu połączenia bliźniaczych reprezentacji lub ograniczenia zapytania do określonego obszaru. Tylko *room0* jest na *floor0*, więc jest to jedyne miejsce w wyniku.

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

* **Co to jest wszystkie bliźniaczych reprezentacji w moim środowisku z temperaturą powyżej 75?** (zapytanie według właściwości)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Możesz wysyłać zapytania do grafu na podstawie właściwości, aby odpowiedzieć na różne pytania, w tym Znajdowanie wartości odstających w środowisku, które mogą wymagać uwagi. Obsługiwane są również inne operatory porównania ( *<* , *>* ,, *=* lub *! =*). w tym miejscu *room1* pojawia się w wynikach, ponieważ ma temperaturę 80.

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

* **Jakie są wszystkie pokoje *floor0* z temperaturą powyżej 75?** (zapytanie złożone)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Możesz również połączyć wcześniejsze zapytania, takie jak w przypadku języka SQL, przy użyciu operatorów kombinacji takich jak `AND` , `OR` , `NOT` . To zapytanie używa `AND` do powyższej kwerendy dotyczącej pozostałej liczby pozostałych temperatur. Teraz wynik obejmuje tylko pokoje z temperaturą powyżej 75, które znajdują się w *floor0*— w tym przypadku nie ma żadnego z nich. Zestaw wyników jest pusty.

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="Edytowano Room.jsprzy użyciu zaktualizowanego numeru wersji, właściwości HumidityLevel i Roomname oraz zawiera relację":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Projekt w tym samouczku stanowi podstawę dla następnego samouczka, [*Samouczek: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md). Jeśli planujesz przejść do następnego samouczka, możesz zachować tutaj skonfigurowane zasoby, aby nadal korzystać z tego wystąpienia usługi Azure Digital bliźniaczych reprezentacji i skonfigurowanej przykładowej aplikacji.
* W takim przypadku można użyć przykładowej aplikacji `DeleteAllTwins` i `DeleteAllModels` poleceń, aby wyczyścić odpowiednio bliźniaczych reprezentacji i modele w wystąpieniu. Spowoduje to wyczyszczenie do następnego samouczka.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Na koniec Usuń folder przykładowego projektu pobrany na komputer lokalny.

## <a name="next-steps"></a>Następne kroki 

W tym samouczku przedstawiono rozpoczęcie pracy z usługą Azure Digital bliźniaczych reprezentacji przez skonfigurowanie wystąpienia i aplikacji klienckiej w celu korzystania z tego wystąpienia. Aplikacja kliencka została użyta do eksplorowania usługi Azure Digital bliźniaczych reprezentacji, tworzenia modeli, cyfrowego bliźniaczych reprezentacji i relacji. W rozwiązaniu uruchomiono również pewne zapytania, aby poznać, jakie rodzaje pytań można znaleźć w usłudze Azure Digital bliźniaczych reprezentacji.

Przejdź do następnego samouczka, aby użyć przykładowej aplikacji wiersza polecenia w połączeniu z innymi usługami platformy Azure, aby ukończyć Scenariusz oparty na danych, kompleksowo:
> [!div class="nextstepaction"]
> [*Samouczek: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)