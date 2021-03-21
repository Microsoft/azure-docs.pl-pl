---
title: 'Samouczek: Tworzenie grafu w usłudze Azure Digital bliźniaczych reprezentacji (aplikacja kliencka)'
titleSuffix: Azure Digital Twins
description: Samouczek dotyczący tworzenia scenariusza bliźniaczych reprezentacji Digital na platformie Azure przy użyciu przykładowej aplikacji wiersza polecenia
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493712"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Samouczek: Tworzenie wykresu Digital bliźniaczych reprezentacji na platformie Azure za pomocą przykładowej aplikacji klienckiej

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

W tym samouczku utworzysz wykres w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu modeli, bliźniaczych reprezentacji i relacji. Narzędzie dla tego samouczka to **Przykładowa aplikacja kliencka wiersza polecenia** służąca do współdziałania z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Aplikacja kliencka jest podobna do tej, która została zapisywana w [*samouczku: kod aplikacji klienckiej*](tutorial-code.md).

Możesz użyć tego przykładu, aby wykonać podstawowe akcje usługi Azure Digital bliźniaczych reprezentacji, takie jak przekazywanie modeli, tworzenie i modyfikowanie bliźniaczych reprezentacji oraz tworzenie relacji. Możesz również zapoznać się z [kodem przykładu](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) , aby dowiedzieć się więcej o interfejsie API Digital bliźniaczych reprezentacji platformy Azure, i ćwicz implementację własnych poleceń, modyfikując przykładowy projekt.

W tym samouczku będziesz...
> [!div class="checklist"]
> * Modelowanie środowiska
> * Tworzenie cyfrowych reprezentacji bliźniaczych
> * Dodawanie relacji w celu utworzenia grafu
> * Zbadaj wykres, aby odpowiedzieć na pytania

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Uruchamianie przykładowego projektu

Po skonfigurowaniu aplikacji i uwierzytelniania Uruchom projekt za pomocą tego przycisku na pasku narzędzi:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Zrzut ekranu przycisku Start programu Visual Studio (projekt SampleClientApp)." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Otworzy się okno konsoli, przeprowadzisz uwierzytelnianie i poczekasz na polecenie. 
* Uwierzytelnianie jest obsługiwane w przeglądarce: domyślna przeglądarka sieci Web zostanie otwarta z monitem o uwierzytelnienie. Użyj tego monitu, aby zalogować się przy użyciu poświadczeń platformy Azure. Następnie można zamknąć kartę lub okno przeglądarki.

Poniżej znajduje się zrzut ekranu przedstawiający wygląd konsoli projektu:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Zrzut ekranu przedstawiający Komunikat powitalny z aplikacji wiersza polecenia." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> Aby uzyskać listę wszystkich możliwych poleceń, których można użyć z tym projektem, wprowadź `help` w konsoli projektu i naciśnij przycisk Return.

Pozostaw uruchomioną konsolę projektu dla pozostałej części kroków tego samouczka.

## <a name="model-a-physical-environment-with-dtdl"></a>Modelowanie środowiska fizycznego za pomocą DTDL

Teraz, gdy zostanie skonfigurowane wystąpienie usługi Azure Digital bliźniaczych reprezentacji i Przykładowa aplikacja, możesz rozpocząć tworzenie grafu scenariusza. 

Pierwszym krokiem tworzenia rozwiązania Digital bliźniaczych reprezentacji na platformie Azure jest definiowanie [**modeli**](concepts-models.md) bliźniaczych dla danego środowiska. 

Modele są podobne do klas w języku programowania zorientowanym na obiekty; udostępniają one szablony zdefiniowane przez użytkownika na potrzeby [cyfrowego bliźniaczych reprezentacji](concepts-twins-graph.md) i tworzenia wystąpienia w przyszłości. Są one zapisywane w języku przypominającym kod JSON o nazwie **Digital bliźniaczych reprezentacji Definition Language (DTDL)** i mogą definiować *Właściwości*, dane *telemetryczne*, *relacje* i *składniki*.

> [!NOTE]
> DTDL umożliwia także Definiowanie *poleceń* w Digital bliźniaczych reprezentacji. Polecenia nie są jednak obecnie obsługiwane w usłudze Azure Digital bliźniaczych reprezentacji.

W oknie programu Visual Studio, w którym jest otwarty projekt _**AdtE2ESample**_ , użyj okienka *Eksplorator rozwiązań* , aby przejść do *folderu AdtSampleApp\SampleClientApp\Models*. Ten folder zawiera przykładowe modele.

Wybierz pozycję *Room.jsna* , aby otworzyć ją w oknie edycji i zmienić ją w następujący sposób:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Przekazywanie modeli do usługi Azure Digital bliźniaczych reprezentacji

Po zaprojektowaniu modeli należy przekazać je do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Spowoduje to skonfigurowanie wystąpienia usługi Digital bliźniaczych reprezentacji na platformie Azure przy użyciu własnego słownika domen niestandardowych. Po przekazaniu modeli można utworzyć dwuosiowe wystąpienia, które z nich korzystają.

1. W oknie Konsola projektu uruchom następujące polecenie, aby przekazać zaktualizowany model *pokoju* , a także model *podłogowy* , który będzie również używany w następnej sekcji do tworzenia różnych typów bliźniaczych reprezentacji.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    Dane wyjściowe powinny wskazywać, że modele zostały utworzone pomyślnie.

1. Sprawdź, czy modele zostały utworzone przez uruchomienie polecenia `GetModels true` . Spowoduje to wysłanie zapytania do wystąpienia usługi Azure Digital bliźniaczych reprezentacji dla wszystkich przekazanych modeli i wydrukowanie ich pełnych informacji. Poszukaj edytowanego modelu *pokoju* w wynikach:

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Zrzut ekranu przedstawiający wynik z elementu GetModels, który pokazuje zaktualizowany model pokoju." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>błędy

Przykładowa aplikacja obsługuje także błędy usługi. 

Uruchom ponownie `CreateModels` polecenie, aby ponowić próbę przekazania jednego z tych samych modeli, które zostały przekazane po raz drugi:

```cmd/sh
CreateModels Room
```

Ponieważ modeli nie można zastąpić, spowoduje to zwrócenie błędu usługi.
Aby uzyskać szczegółowe informacje na temat usuwania istniejących modeli, zobacz [*How to: Manage DTDL models*](how-to-manage-model.md).
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

## <a name="create-digital-twins"></a>Tworzenie cyfrowych reprezentacji bliźniaczych

Teraz, gdy niektóre modele zostały przekazane do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, można utworzyć [**cyfrowy bliźniaczych reprezentacji**](concepts-twins-graph.md) na podstawie definicji modelu. Bliźniaczych reprezentacji Digital reprezentuje jednostki w środowisku biznesowym — takie jak czujniki w farmie, pokoje w budynku lub lampy w kabinie. 

Aby utworzyć dwuosiową cyfrę, użyj `CreateDigitalTwin` polecenia. Należy odwoływać się do modelu, w którym bazuje sznury, i opcjonalnie definiować wartości początkowe dla każdej właściwości w modelu. Nie trzeba przekazywać żadnych informacji o relacji na tym etapie.

1. Uruchom ten kod w uruchomionej konsoli projektu, aby utworzyć kilka bliźniaczych reprezentacji w oparciu o model *pokoju* , który został zaktualizowany wcześniej, i inny model, *piętro*. Odwołaj ten *Pokój* ma trzy właściwości, więc możesz podać argumenty z początkowymi wartościami. (Zazwyczaj inicjowanie wartości właściwości jest opcjonalne, ale są one niezbędne dla tego samouczka).

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    Dane wyjściowe tych poleceń powinny wskazywać, że bliźniaczych reprezentacji zostały utworzone pomyślnie. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Zrzut ekranu przedstawiający fragment wyniku poleceń CreateDigitalTwin, w tym floor0, floor1, room0 i room1." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. Można sprawdzić, czy bliźniaczych reprezentacji zostały utworzone przez uruchomienie `Query` polecenia. To polecenie powoduje wysłanie zapytania do wystąpienia usługi Azure Digital bliźniaczych reprezentacji dla wszystkich bliźniaczych reprezentacji, które zawiera. W wynikach Szukaj *room0*, *room1*, *floor0* i *floor1* bliźniaczych reprezentacji.

### <a name="modify-a-digital-twin"></a>Modyfikowanie cyfrowej sieci dwuosiowej

Możesz również zmodyfikować właściwości utworzonego przez siebie przędzę. 

> [!NOTE]
> Podstawowy interfejs API REST używa formatu [poprawek JSON](http://jsonpatch.com/) do definiowania aktualizacji dla sznurka. Aplikacja wiersza polecenia używa również tego formatu, aby zapewnić prawdziwą pracę w przypadku wystąpienia odpowiednich interfejsów API.

1. Uruchom to polecenie, aby zmienić *Room0* pokoju z *room0* na *PresidentialSuite*:
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    Dane wyjściowe powinny wskazywać, że sznury zostały pomyślnie zaktualizowane.

1. Aby sprawdzić, czy aktualizacja zakończyła się powodzeniem, należy uruchomić to polecenie, aby wyświetlić informacje o *room0*:

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    Dane wyjściowe powinny odzwierciedlać zaktualizowaną nazwę.


## <a name="create-a-graph-by-adding-relationships"></a>Tworzenie wykresu przez Dodawanie relacji

Następnie można utworzyć niektóre **relacje** między tymi bliźniaczych reprezentacjiami, aby połączyć je z [**wykresem bliźniaczym**](concepts-twins-graph.md). Wykresy bliźniaczy są używane do reprezentowania całego środowiska. 

Typy relacji, które można utworzyć z jednej przędzy w innej, są definiowane w ramach [modeli](#model-a-physical-environment-with-dtdl) , które zostały przekazane wcześniej. [Definicja modelu *podłogi*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) określa, że piętra mogą mieć typ relacji o nazwie *Contains*. Dzięki temu można utworzyć relację *zawierania* typu z każdego przędzy *podłogowej* do odpowiadającego pokoju, które zawiera.

Aby dodać relację, użyj `CreateRelationship` polecenia. Określ dwuosiową, z której pochodzi relacja, typ relacji i splot, z którym jest nawiązywane połączenie. Na koniec nadaj relacji unikatowy identyfikator.

1. Uruchom Poniższy kod, aby dodać relację "Contains" z każdego utworzonego wcześniej bliźniaczych reprezentacji *podłogi* do odpowiedniej sieci *salonu* . Relacje mają nazwę *relationship0* i *relationship1*.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >Relacja *Contains* w modelu [ *podłogi*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) została również zdefiniowana z dwiema właściwościami ciągu, `ownershipUser` a `ownershipDepartment` więc można także podać argumenty z wartościami początkowymi dla tych podczas tworzenia relacji.
    > Oto alternatywna wersja powyższego polecenia, aby utworzyć *relationship0* , które określa również początkowe wartości dla tych właściwości:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    Dane wyjściowe tych poleceń potwierdzają, że relacje zostały utworzone pomyślnie:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Zrzut ekranu przedstawiający fragment z wyniku poleceń tworzenia relacji, w tym relationship0 i relationship1." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. Można sprawdzić relacje z dowolnym z poniższych poleceń, które wykonują zapytania dotyczące relacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.
    * Aby wyświetlić wszystkie relacje wychodzące z każdego piętra (wyświetlanie relacji z jednej strony):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * Aby wyświetlić wszystkie relacje docierające do każdego pokoju (wyświetlanie relacji z boku "inne"):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * Aby wyszukać te relacje pojedynczo, według identyfikatora:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

Bliźniaczych reprezentacji i relacje, które zostały skonfigurowane w tym samouczku, tworzą następujący wykres koncepcyjny:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Diagram przedstawiający wykres koncepcyjny. floor0 jest połączony za pośrednictwem relationship0 do room0, a floor1 jest połączony za pośrednictwem relationship1 do room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Zbadaj Graf bliźniaczy, aby odpowiedzieć na pytania dotyczące środowiska

Główną funkcją usługi Azure Digital bliźniaczych reprezentacji jest możliwość łatwego i wydajnego [wykonywania zapytań](concepts-query-language.md) na pytania dotyczące środowiska. 

Uruchom następujące polecenia w uruchomionej konsoli programu Project, aby odpowiedzieć na niektóre pytania dotyczące przykładowego środowiska.

1. **Jakie są wszystkie jednostki z mojego środowiska reprezentowane w usłudze Azure Digital bliźniaczych reprezentacji?** (kwerenda All)

    ```cmd/sh
    Query
    ```

    Pozwala to na błyskawiczne przejęcie zasobów w środowisku i upewnienie się, że wszystko jest reprezentowane, tak jak w przypadku usługi Azure Digital bliźniaczych reprezentacji. Wynikiem tego jest dane wyjściowe zawierające każdą z nich. Oto fragment:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Zrzut ekranu przedstawiający częściowe wyniki zapytania bliźniaczyego, w tym room0 i floor1.":::

    >[!NOTE]
    >W przykładowym projekcie polecenie `Query` bez żadnych dodatkowych argumentów jest równoważne `Query SELECT * FROM DIGITALTWINS` . Aby wykonać zapytanie dotyczące wszystkich bliźniaczych reprezentacji w wystąpieniu przy użyciu [interfejsów API zapytań](/rest/api/digital-twins/dataplane/query) lub [poleceń interfejsu wiersza polecenia](how-to-use-cli.md), użyj dłuższego (kompletnego) zapytania.

1. **Jakie są wszystkie pokoje w moim środowisku?** (zapytanie według modelu)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Możesz ograniczyć zapytanie do bliźniaczych reprezentacji pewnego typu, aby uzyskać bardziej szczegółowe informacje na temat tego, co zostało reprezentowane. W wyniku tego są wyświetlane *room0* i *room1*, **ale nie są** wyświetlane *floor0* ani *floor1* (ponieważ są to piętra, a nie pokoje).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Zrzut ekranu przedstawiający wynik zapytania dotyczącego modelu, który zawiera tylko room0 i room1.":::

1. **Jakie są wszystkie pokoje w witrynie *floor0*?** (zapytanie według relacji)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Możesz wykonywać zapytania na podstawie relacji w grafie, aby uzyskać informacje na temat sposobu połączenia bliźniaczych reprezentacji lub ograniczenia zapytania do określonego obszaru. Tylko *room0* jest na *floor0*, więc jest to jedyne miejsce w wyniku.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Zrzut ekranu przedstawiający wynik zapytania relacji, który zawiera room0.":::

1. **Co to jest wszystkie bliźniaczych reprezentacji w moim środowisku z temperaturą powyżej 75?** (zapytanie według właściwości)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Możesz wysyłać zapytania do grafu na podstawie właściwości, aby odpowiedzieć na różne pytania, w tym Znajdowanie wartości odstających w środowisku, które mogą wymagać uwagi. Obsługiwane są również inne operatory porównania ( *<* , *>* ,, *=* lub *! =*). w tym miejscu *room1* pojawia się w wynikach, ponieważ ma temperaturę 80.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Zrzut ekranu przedstawiający wynik z kwerendy właściwości, który zawiera tylko room1.":::

1. **Jakie są wszystkie pokoje *floor0* z temperaturą powyżej 75?** (zapytanie złożone)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Możesz również połączyć wcześniejsze zapytania, takie jak w przypadku języka SQL, przy użyciu operatorów kombinacji takich jak `AND` , `OR` , `NOT` . To zapytanie używa `AND` do powyższej kwerendy dotyczącej pozostałej liczby pozostałych temperatur. Teraz wynik obejmuje tylko pokoje z temperaturą powyżej 75, które znajdują się w *floor0*— w tym przypadku nie ma żadnego z nich. Zestaw wyników jest pusty.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Zrzut ekranu wyniku zapytania złożonego, w którym nie są wyświetlane żadne wyniki." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu tego samouczka możesz wybrać zasoby, które chcesz usunąć, w zależności od tego, co chcesz zrobić dalej.

* **Jeśli planujesz przejść do następnego samouczka**, możesz zachować tutaj skonfigurowane zasoby, aby nadal korzystać z tego wystąpienia usługi Azure Digital bliźniaczych reprezentacji i skonfigurowanej przykładowej aplikacji do następnego samouczka

* **Jeśli chcesz kontynuować korzystanie z wystąpienia usługi Azure Digital bliźniaczych reprezentacji, ale wyczyścisz wszystkie modele, bliźniaczych reprezentacji i relacje**, możesz użyć przykładowej aplikacji `DeleteAllTwins` i `DeleteAllModels` poleceń, aby wyczyścić odpowiednio bliźniaczych reprezentacji i modele w wystąpieniu.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Możesz również usunąć folder projektu z komputera lokalnego.

## <a name="next-steps"></a>Następne kroki 

W tym samouczku przedstawiono rozpoczęcie pracy z usługą Azure Digital bliźniaczych reprezentacji przez utworzenie grafu w wystąpieniu przy użyciu przykładowej aplikacji klienckiej. Utworzono modele, bliźniaczych reprezentacji cyfrowy i relacje, aby utworzyć wykres. Uruchomiono również pewne zapytania na grafie, aby poznać, jakie rodzaje pytań można znaleźć w usłudze Azure Digital bliźniaczych reprezentacji.

Przejdź do następnego samouczka, aby połączyć usługę Azure Digital bliźniaczych reprezentacji z innymi usługami platformy Azure, aby ukończyć Scenariusz oparty na danych, kompleksowo:
> [!div class="nextstepaction"]
> [*Samouczek: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)