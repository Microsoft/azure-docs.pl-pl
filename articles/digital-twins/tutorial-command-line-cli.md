---
title: 'Samouczek: Tworzenie grafu w usłudze Azure Digital bliźniaczych reprezentacji (interfejs wiersza polecenia)'
titleSuffix: Azure Digital Twins
description: Samouczek dotyczący tworzenia scenariusza bliźniaczych reprezentacji Digital platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 578befe3e26ebb42fa2172976e07d0a5836e3743
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107163"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Samouczek: Tworzenie wykresu Digital bliźniaczych reprezentacji na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

W tym samouczku utworzysz wykres w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu modeli, bliźniaczych reprezentacji i relacji. Narzędziem dla tego samouczka jest [polecenie Azure Digital bliźniaczych reprezentacji Set dla **interfejsu wiersza polecenia platformy Azure**](how-to-use-cli.md). 

Za pomocą poleceń interfejsu wiersza polecenia można wykonywać podstawowe akcje usługi Azure Digital bliźniaczych reprezentacji, takie jak przekazywanie modeli, tworzenie i modyfikowanie bliźniaczych reprezentacji oraz tworzenie relacji. Możesz także zapoznać się z [dokumentacją referencyjną polecenia *AZ DT* Set](/cli/azure/dt) , aby zobaczyć pełny zestaw poleceń interfejsu wiersza polecenia.

W tym samouczku będziesz...
> [!div class="checklist"]
> * Modelowanie środowiska
> * Tworzenie cyfrowych reprezentacji bliźniaczych
> * Dodawanie relacji w celu utworzenia grafu
> * Zbadaj wykres, aby odpowiedzieć na pytania

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, należy najpierw wykonać poniższe wymagania wstępne.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem **Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

### <a name="download-the-sample-models"></a>Pobierz modele przykładowe

W tym samouczku są używane dwa wstępnie napisywane modele, które są częścią [kompleksowego projektu](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) w języku C# dla usługi Azure Digital bliźniaczych reprezentacji. Pliki modelu znajdują się tutaj: 
* [*Room.jsna*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*Floor.jsna*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Aby pobrać pliki na maszynie, użyj linków nawigacji powyżej i skopiuj treści plików do plików lokalnych na komputerze o tych samych nazwach (*Room.jswłączone* i *Floor.jsna*).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Konfigurowanie sesji Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Przygotowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

Aby można było korzystać z usługi Azure Digital bliźniaczych reprezentacji w tym artykule, należy najpierw **skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji** i wymagane uprawnienia do korzystania z niego. Jeśli masz już skonfigurowane wystąpienie usługi Azure Digital bliźniaczych reprezentacji na podstawie poprzedniej pracy, możesz użyć tego wystąpienia.

W przeciwnym razie postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-cli.md). Instrukcje zawierają również kroki umożliwiające zweryfikowanie, że wszystkie czynności zostały wykonane pomyślnie i są gotowe do przejścia do korzystania z nowego wystąpienia.

Po skonfigurowaniu wystąpienia usługi Azure Digital bliźniaczych reprezentacji należy pamiętać o następujących wartościach, które będą potrzebne do późniejszego nawiązania połączenia z wystąpieniem:
* **_Nazwa hosta_ wystąpienia**
* **subskrypcja platformy Azure** , która została użyta do utworzenia wystąpienia. 

Obie te wartości dla wystąpienia można uzyskać w danych wyjściowych następującego polecenia platformy Azure: 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Zrzut ekranu przedstawiający okno przeglądarki Cloud Shell z danymi wyjściowymi polecenia AZ DT show. Pole nazwy hosta i Identyfikator subskrypcji (część pola identyfikatora) są wyróżnione.":::

## <a name="model-a-physical-environment-with-dtdl"></a>Modelowanie środowiska fizycznego za pomocą DTDL

Teraz, gdy jest skonfigurowane wystąpienie interfejsu wiersza polecenia i usługi Azure Digital bliźniaczych reprezentacji, możesz rozpocząć tworzenie grafu scenariusza. 

Pierwszym krokiem tworzenia rozwiązania Digital bliźniaczych reprezentacji na platformie Azure jest definiowanie [**modeli**](concepts-models.md) bliźniaczych dla danego środowiska. 

Modele są podobne do klas w języku programowania zorientowanym na obiekty; udostępniają one szablony zdefiniowane przez użytkownika na potrzeby [cyfrowego bliźniaczych reprezentacji](concepts-twins-graph.md) i tworzenia wystąpienia w przyszłości. Są one zapisywane w języku przypominającym kod JSON o nazwie **Digital bliźniaczych reprezentacji Definition Language (DTDL)** i mogą definiować *Właściwości*, dane *telemetryczne*, *relacje* i *składniki*.

> [!NOTE]
> DTDL umożliwia także Definiowanie *poleceń* w Digital bliźniaczych reprezentacji. Polecenia nie są jednak obecnie obsługiwane w usłudze Azure Digital bliźniaczych reprezentacji.

Przejdź do maszyny *Room.js* pliku, który został utworzony w sekcji [wymagania wstępne](#prerequisites) . Otwórz go w edytorze kodu i zmień go w następujący sposób:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Przekazywanie modeli do usługi Azure Digital bliźniaczych reprezentacji

Po zaprojektowaniu modeli należy przekazać je do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Spowoduje to skonfigurowanie wystąpienia usługi Digital bliźniaczych reprezentacji na platformie Azure przy użyciu własnego słownika domen niestandardowych. Po przekazaniu modeli można utworzyć dwuosiowe wystąpienia, które z nich korzystają.

1. Aby dodać modele przy użyciu Cloud Shell, musisz przekazać pliki modelu do magazynu Cloud Shell, aby pliki były dostępne po uruchomieniu polecenia Cloud Shell, które z nich korzysta. W tym celu wybierz ikonę "przekazywanie/pobieranie plików" i wybierz pozycję "Przekaż".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Zrzut ekranu przedstawiający okno przeglądarki Cloud Shell, w którym jest wyświetlana ikona przekazywania.":::
    
    Przejdź do *Room.js* pliku na komputerze i wybierz pozycję "Otwórz". Następnie powtórz ten krok dla *Floor.js*.

1. Następnie użyj polecenia [**AZ DT model Create**](/cli/azure/dt/model#az_dt_model_create) , jak pokazano poniżej, aby przekazać zaktualizowany model *pokoju* do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Drugie polecenie przekazuje inny model, *piętro*, którego również będziesz używać w następnej sekcji do tworzenia różnych typów bliźniaczych reprezentacji.

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    Dane wyjściowe z każdego polecenia będą zawierać informacje o pomyślnie przekazanym modelu.

    >[!TIP]
    >Możesz również przekazać wszystkie modele w katalogu w tym samym czasie, używając `--from-directory` opcji dla polecenia model Create. Aby uzyskać więcej informacji, zobacz [Parametry opcjonalne dla *AZ DT model Create*](/cli/azure/dt/model#az_dt_model_create-optional-parameters).

1. Sprawdź, czy modele zostały utworzone za pomocą polecenia [**AZ DT model list**](/cli/azure/dt/model#az_dt_model_list) , jak pokazano poniżej. Spowoduje to wydrukowanie listy wszystkich modeli, które zostały przekazane do wystąpienia usługi Azure Digital bliźniaczych reprezentacji z pełnymi informacjami. 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    Poszukaj edytowanego modelu *pokoju* w wynikach:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Zrzut ekranu przedstawiający Cloud Shell pokazujący wynik polecenia listy modeli, który obejmuje zaktualizowany model pokoju." lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>błędy

Interfejs wiersza polecenia obsługuje także błędy usługi. 

Uruchom ponownie `az dt model create` polecenie, aby ponowić próbę przekazania jednego z tych samych modeli, które zostały przekazane po raz drugi:

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

Ponieważ modeli nie można zastąpić, spowoduje to zwrócenie błędu o kodzie `ModelIdAlreadyExists` .

## <a name="create-digital-twins"></a>Tworzenie cyfrowych reprezentacji bliźniaczych

Teraz, gdy niektóre modele zostały przekazane do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, można utworzyć [**cyfrowy bliźniaczych reprezentacji**](concepts-twins-graph.md) na podstawie definicji modelu. Bliźniaczych reprezentacji Digital reprezentuje jednostki w środowisku biznesowym — takie jak czujniki w farmie, pokoje w budynku lub lampy w kabinie. 

Aby utworzyć dwuosiową cyfrę, użyj polecenia [**AZ DT bliźniaczy Create**](/cli/azure/dt/twin#az_dt_twin_create) . Należy odwoływać się do modelu, w którym bazuje sznury, i opcjonalnie definiować wartości początkowe dla każdej właściwości w modelu. Nie trzeba przekazywać żadnych informacji o relacji na tym etapie.

1. Uruchom ten kod w Cloud Shell, aby utworzyć kilka bliźniaczych reprezentacji w oparciu o model *pokoju* , który został wcześniej zaktualizowany i inny model, *piętro*. Odwołaj ten *Pokój* ma trzy właściwości, więc możesz podać argumenty z początkowymi wartościami. (Zazwyczaj inicjowanie wartości właściwości jest opcjonalne, ale są one niezbędne dla tego samouczka).

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Jeśli używasz Cloud Shell w środowisku programu PowerShell, może zajść potrzeba ucieczki znaków cudzysłowu w celu `--properties` poprawnego przeanalizowania wartości JSON. W przypadku tej edycji polecenia służące do tworzenia pokoju bliźniaczych reprezentacji wyglądać następująco:
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Jest to odzwierciedlone na poniższym zrzucie ekranu.
    
    Dane wyjściowe każdego polecenia będą zawierać informacje o pomyślnie utworzonym bliźniaczym przędze (łącznie z właściwościami bliźniaczych reprezentacji pokoju, które zostały zainicjowane z nimi).

1. Można sprawdzić, czy bliźniaczych reprezentacji zostały utworzone za pomocą polecenia [**AZ DT bliźniaczy Query**](/cli/azure/dt/twin#az_dt_twin_query) , jak pokazano poniżej. W zapytaniu znajduje się wszystkie cyfrowe bliźniaczych reprezentacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    W wynikach Szukaj *room0*, *room1*, *floor0* i *floor1* bliźniaczych reprezentacji. Poniżej znajduje się fragment pokazujący część wyniku tego zapytania.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Zrzut ekranu przedstawiający Cloud Shell pokazujący częściowe wyniki zapytania bliźniaczyego, w tym room0 i room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Modyfikowanie cyfrowej sieci dwuosiowej

Możesz również zmodyfikować właściwości utworzonego przez siebie przędzę. 

1. Uruchom to polecenie [**AZ DT bliźniaczy Update**](/cli/azure/dt/twin#az_dt_twin_update) , aby zmienić *Room0* pokoju z *room0* na *PresidentialSuite*:

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Jeśli używasz Cloud Shell w środowisku programu PowerShell, może zajść potrzeba ucieczki znaków cudzysłowu w celu `--json-patch` poprawnego przeanalizowania wartości JSON. W przypadku tej edycji polecenie aktualizowania sznurka wygląda następująco:
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Jest to odzwierciedlone na poniższym zrzucie ekranu.
    
    Dane wyjściowe tego polecenia będą przedstawiać bieżące informacje o przędze i powinna zostać wyświetlona nowa wartość `RoomName` w wyniku.

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Zrzut ekranu przedstawiający Cloud Shell wyświetlenia wyniku polecenia Update, który zawiera wartość Roomname z PresidentialSuite." lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. Aby sprawdzić, czy aktualizacja zakończyła się powodzeniem, możesz uruchomić polecenie [**AZ DT bliźniaczy show**](/cli/azure/dt/twin#az_dt_twin_show) , aby zobaczyć informacje o *room0*:

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    Dane wyjściowe powinny odzwierciedlać zaktualizowaną nazwę.

## <a name="create-a-graph-by-adding-relationships"></a>Tworzenie wykresu przez Dodawanie relacji

Następnie można utworzyć niektóre **relacje** między tymi bliźniaczych reprezentacjiami, aby połączyć je z [**wykresem bliźniaczym**](concepts-twins-graph.md). Wykresy bliźniaczy są używane do reprezentowania całego środowiska. 

Typy relacji, które można utworzyć z jednej przędzy w innej, są definiowane w ramach [modeli](#model-a-physical-environment-with-dtdl) , które zostały przekazane wcześniej. [Definicja modelu *podłogi*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) określa, że piętra mogą mieć typ relacji o nazwie *Contains*. Dzięki temu można utworzyć relację *zawierania* typu z każdego przędzy *podłogowej* do odpowiadającego pokoju, które zawiera.

Aby dodać relację, użyj polecenia [**AZ DT bliźniaczy Relationship Create**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_create) . Określ dwuosiową, z której pochodzi relacja, typ relacji i splot, z którym jest nawiązywane połączenie. Na koniec nadaj relacji unikatowy identyfikator. Jeśli relacja została zdefiniowana w taki sposób, aby zawierała właściwości, można także zainicjować właściwości relacji w tym poleceniu.

1. Uruchom Poniższy kod, aby dodać relację *Contains*-Type z każdego utworzonego wcześniej bliźniaczych reprezentacji *piętra* do odpowiedniej sieci *salonu* . Relacje mają nazwę *relationship0* i *relationship1*.

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >Relacja *Contains* w modelu [ *podłogi*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) została również zdefiniowana z dwiema właściwościami, `ownershipUser` `ownershipDepartment` więc można także podać argumenty z wartościami początkowymi dla tych podczas tworzenia relacji.
    > Aby utworzyć relację z tymi właściwościami, należy dodać `--properties` opcję do jednego z powyższych poleceń, takich jak:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Jeśli używasz Cloud Shell w środowisku programu PowerShell, może zajść potrzeba ucieczki znaków cudzysłowu w celu `--properties` poprawnego przeanalizowania wartości JSON.
    
    Dane wyjściowe każdego polecenia będą zawierać informacje o pomyślnym utworzeniu relacji.

1. Można sprawdzić relacje z dowolnym z poniższych poleceń, które wykonują zapytania dotyczące relacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.
    * Aby wyświetlić wszystkie relacje wychodzące z każdego piętra (wyświetlanie relacji z jednej strony):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * Aby wyświetlić wszystkie relacje docierające do każdego pokoju (wyświetlanie relacji z boku "inne"):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * Aby wyszukać te relacje pojedynczo, według identyfikatora:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

Bliźniaczych reprezentacji i relacje, które zostały skonfigurowane w tym samouczku, tworzą następujący wykres koncepcyjny:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Diagram przedstawiający wykres koncepcyjny. floor0 jest połączony za pośrednictwem relationship0 do room0, a floor1 jest połączony za pośrednictwem relationship1 do room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Zbadaj Graf bliźniaczy, aby odpowiedzieć na pytania dotyczące środowiska

Główną funkcją usługi Azure Digital bliźniaczych reprezentacji jest możliwość łatwego i wydajnego [wykonywania zapytań](concepts-query-language.md) na pytania dotyczące środowiska. W interfejsie wiersza polecenia platformy Azure jest to realizowane za pomocą poleceń [**AZ DT bliźniaczy Query**](/cli/azure/dt/twin#az_dt_twin_query) .

Uruchom następujące zapytania w Cloud Shell, aby odpowiedzieć na kilka pytań dotyczących przykładowego środowiska.

1. **Jakie są wszystkie jednostki z mojego środowiska reprezentowane w usłudze Azure Digital bliźniaczych reprezentacji?** (kwerenda All)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    Pozwala to na błyskawiczne przejęcie zasobów w środowisku i upewnienie się, że wszystko jest reprezentowane, tak jak w przypadku usługi Azure Digital bliźniaczych reprezentacji. Wynikiem tego jest dane wyjściowe zawierające każdą z nich. Oto fragment:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Zrzut ekranu przedstawiający Cloud Shell pokazujący częściowe wyniki zapytania bliźniaczyego, w tym room0 i room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >Można sprawdzić, czy jest to to samo polecenie, które zostało użyte wcześniej w sekcji [*Create Digital bliźniaczych reprezentacji*](#create-digital-twins) , aby znaleźć wszystkie usługi Azure Digital bliźniaczych reprezentacji w wystąpieniu.

1. **Jakie są wszystkie pokoje w moim środowisku?** (zapytanie według modelu)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    Możesz ograniczyć zapytanie do bliźniaczych reprezentacji pewnego typu, aby uzyskać bardziej szczegółowe informacje na temat tego, co zostało reprezentowane. W wyniku tego są wyświetlane *room0* i *room1*, **ale nie są** wyświetlane *floor0* ani *floor1* (ponieważ są to piętra, a nie pokoje).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Zrzut ekranu przedstawiający Cloud Shell pokazujący wynik zapytania modelu, który obejmuje tylko room0 i room1." lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Jakie są wszystkie pokoje w witrynie *floor0*?** (zapytanie według relacji)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    Możesz wykonywać zapytania na podstawie relacji w grafie, aby uzyskać informacje na temat sposobu połączenia bliźniaczych reprezentacji lub ograniczenia zapytania do określonego obszaru. Tylko *room0* jest na *floor0*, więc jest to jedyne miejsce w wyniku.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Zrzut ekranu przedstawiający Cloud Shell wyświetlenia wyniku zapytania relacji, który obejmuje room0." lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > Zwróć uwagę, że w polu metadanych zostanie wyszukiwany identyfikator przędzy (na przykład *floor0* w powyższym zapytaniu) `$dtId` . 
    >
    >W przypadku używania Cloud Shell do uruchamiania zapytania z polami metadanych, takimi jak te, które zaczynają się od, należy wypróbować, `$` `$` aby umożliwić Cloud Shell wiedzieć, że nie jest to zmienna i powinna być używana jako literał w tekście zapytania. Jest to odzwierciedlone na powyższym zrzucie ekranu.

1. **Co to jest wszystkie bliźniaczych reprezentacji w moim środowisku z temperaturą powyżej 75?** (zapytanie według właściwości)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    Możesz wysyłać zapytania do grafu na podstawie właściwości, aby odpowiedzieć na różne pytania, w tym Znajdowanie wartości odstających w środowisku, które mogą wymagać uwagi. Obsługiwane są również inne operatory porównania ( *<* , *>* ,, *=* lub *! =*). w tym miejscu *room1* pojawia się w wynikach, ponieważ ma temperaturę 80.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Zrzut ekranu przedstawiający Cloud Shell pokazujący wynik zapytania właściwości, który zawiera tylko room1." lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Jakie są wszystkie pokoje *floor0* z temperaturą powyżej 75?** (zapytanie złożone)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    Możesz również połączyć wcześniejsze zapytania, takie jak w przypadku języka SQL, przy użyciu operatorów kombinacji takich jak `AND` , `OR` , `NOT` . To zapytanie używa `AND` do powyższej kwerendy dotyczącej pozostałej liczby pozostałych temperatur. Teraz wynik obejmuje tylko pokoje z temperaturą powyżej 75, które znajdują się w *floor0*— w tym przypadku nie ma żadnego z nich. Zestaw wyników jest pusty.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Zrzut ekranu przedstawiający Cloud Shell pokazujący wynik zapytania złożonego, które nie zawiera żadnych elementów." lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu tego samouczka możesz wybrać zasoby, które chcesz usunąć, w zależności od tego, co chcesz zrobić dalej.

* **Jeśli planujesz przejść do następnego samouczka**, możesz zachować tutaj skonfigurowane zasoby i ponownie użyć wystąpienia bliźniaczych reprezentacji Digital na platformie Azure bez rozliczania wszystkiego między nimi.

* **Jeśli chcesz kontynuować korzystanie z wystąpienia usługi Azure Digital bliźniaczych reprezentacji, ale wyczyścisz wszystkie jego modele, bliźniaczych reprezentacji i relacje**, możesz użyć polecenia [**AZ DT bliźniaczy Relationship**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_delete)Delete, [**AZ DT bliźniaczy Delete**](/cli/azure/dt/twin#az_dt_twin_delete)i [**AZ DT model Delete**](/cli/azure/dt/model#az_dt_model_delete) Commands, aby wyczyścić odpowiednio relacje, bliźniaczych reprezentacji i modele w wystąpieniu.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Można również usunąć pliki modelu utworzone na komputerze lokalnym.

## <a name="next-steps"></a>Następne kroki 

W tym samouczku przedstawiono rozpoczęcie pracy z usługą Azure Digital bliźniaczych reprezentacji przez utworzenie grafu w wystąpieniu przy użyciu interfejsu wiersza polecenia platformy Azure. Utworzono modele, bliźniaczych reprezentacji cyfrowy i relacje, aby utworzyć wykres. Uruchomiono również pewne zapytania na grafie, aby poznać, jakie rodzaje pytań można znaleźć w usłudze Azure Digital bliźniaczych reprezentacji.

Przejdź do następnego samouczka, aby połączyć usługę Azure Digital bliźniaczych reprezentacji z innymi usługami platformy Azure, aby ukończyć Scenariusz oparty na danych, kompleksowo:
> [!div class="nextstepaction"]
> [*Samouczek: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)