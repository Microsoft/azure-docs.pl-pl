---
title: Definiowanie niestandardowych modułów R
titleSuffix: ML Studio (classic) - Azure
description: W tym temacie opisano sposób tworzenia i wdrażania niestandardowego studia R Studio (klasycznego). Wyjaśniono, jakie są niestandardowe moduły R i jakie pliki są używane do ich definiowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 5b8dab14a9416795eccef1f71988a048c8bedb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218165"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Definiowanie niestandardowych modułów języka R dla usługi Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

W tym temacie opisano sposób tworzenia i wdrażania niestandardowego studia R Studio (klasycznego). Wyjaśniono, jakie są niestandardowe moduły R i jakie pliki są używane do ich definiowania. Ilustruje sposób konstruowania plików, które definiują moduł i jak zarejestrować moduł do wdrożenia w obszarze roboczym uczenia maszynowego. Elementy i atrybuty używane w definicji modułu niestandardowego są następnie opisane bardziej szczegółowo. Jak korzystać z funkcji pomocniczych i plików i wielu wyjść jest również omówione. 



## <a name="what-is-a-custom-r-module"></a>Co to jest niestandardowy moduł R?
**Moduł niestandardowy** to moduł zdefiniowany przez użytkownika, który można przekazać do obszaru roboczego i wykonać w ramach eksperymentu usługi Azure Machine Learning Studio (klasyczny). Niestandardowy **moduł Języka R** to niestandardowy moduł, który wykonuje zdefiniowaną przez użytkownika funkcję R. **R** jest językiem programowania dla obliczeń statystycznych i grafiki, który jest szeroko stosowany przez statystyków i analityków danych do wdrażania algorytmów. Obecnie R jest jedynym językiem obsługiwanym w modułach niestandardowych, ale obsługa dodatkowych języków jest zaplanowana dla przyszłych wydań.

Moduły niestandardowe mają **stan pierwszej klasy** w usłudze Azure Machine Learning Studio (klasyczny) w tym sensie, że mogą być używane tak samo jak każdy inny moduł. Mogą być wykonywane z innymi modułami, zawartymi w opublikowanych eksperymentach lub wizualizacjach. Masz kontrolę nad algorytmem zaimplementowanym przez moduł, porty wejściowe i wyjściowe, które mają być używane, parametry modelowania i inne różne zachowania środowiska uruchomieniowego. Eksperyment zawierający niestandardowe moduły można również opublikować w galerii sztucznej inteligencji platformy Azure w celu łatwego udostępniania.

## <a name="files-in-a-custom-r-module"></a>Pliki w niestandardowym module R
Niestandardowy moduł R jest definiowany przez plik zip, który zawiera co najmniej dwa pliki:

* **Plik źródłowy** implementacyjny funkcji R udostępnianej przez moduł
* **Plik definicji XML** opisujący niestandardowy interfejs modułu

Dodatkowe pliki pomocnicze mogą być również zawarte w pliku .zip, który zapewnia funkcje, które są dostępne z modułu niestandardowego. Ta opcja została omówiona w **części Argumenty** sekcji odwołania **Elementy w pliku definicji XML** zgodnie z przykładem przewodnika Szybki start.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Przykład szybki start: definiowanie, pakowanie i rejestrowanie niestandardowego modułu R
W tym przykładzie pokazano, jak skonstruować pliki wymagane przez niestandardowy moduł języka R, spakować je do pliku zip, a następnie zarejestrować moduł w obszarze roboczym uczenia maszynowego. Przykładowy pakiet zip i przykładowe pliki można pobrać z [pliku Download CustomAddRows.zip](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Plik źródłowy
Rozważmy przykład niestandardowego **dodaj wiersze** moduł, który modyfikuje standardową implementację **dodaj wiersze** moduł używany do łączenia wierszy (obserwacje) z dwóch zestawów danych (ramki danych). Standardowy moduł **Dodaj wiersze** dołącza wiersze drugiego zestawu danych wejściowych na końcu pierwszego `rbind` wejściowego zestawu danych przy użyciu algorytmu. Dostosowana `CustomAddRows` funkcja podobnie akceptuje dwa zestawy danych, ale również akceptuje parametr wymiany logicznej jako dodatkowe dane wejściowe. Jeśli parametr swap jest ustawiony na **FAŁSZ**, zwraca ten sam zestaw danych co standardowa implementacja. Ale jeśli parametr swap jest **TRUE**, funkcja dołącza wiersze pierwszego zestawu danych wejściowych na końcu drugiego zestawu danych zamiast tego. Plik CustomAddRows.R, który zawiera implementację funkcji R `CustomAddRows` udostępnianej przez moduł **Niestandardowe dodawanie wierszy** ma następujący kod R.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>Plik definicji XML
Aby udostępnić tę `CustomAddRows` funkcję jako moduł usługi Azure Machine Learning Studio (klasyczny), należy utworzyć plik definicji XML, aby określić, jak powinien wyglądać i zachowywać się moduł **Niestandardowe dodaj wiersze.** 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


Ważne jest, aby pamiętać, że wartość atrybutów **id** **input** i **Arg** w pliku XML musi być zgodna z nazwami parametrów funkcji kodu R w pliku CustomAddRows.R dokładnie: (*dataset1*, *dataset2*i *swap* w przykładzie). Podobnie wartość **atrybutu entryPoint** elementu **Language** musi być zgodna z nazwą funkcji w skrypcie R DOKŁADNIE: (*CustomAddRows* w przykładzie). 

Z drugiej strony atrybut **id** dla **Output** element nie odpowiada żadnych zmiennych w skrypcie R. Gdy wymagane jest więcej niż jedno dane wyjściowe, po prostu zwróć listę z funkcji R z wynikami umieszczonymi *w tej samej kolejności,* co elementy **Outputs** są zadeklarowane w pliku XML.

### <a name="package-and-register-the-module"></a>Zapakuj i zarejestruj moduł
Zapisz te dwa pliki jako *CustomAddRows.R* i *CustomAddRows.xml,* a następnie skompresuj dwa pliki razem w pliku *CustomAddRows.zip.*

Aby zarejestrować je w obszarze roboczym uczenia maszynowego, przejdź do obszaru roboczego w usłudze Azure Machine Learning Studio (klasyczny), kliknij **przycisk +NOWY** na dole i wybierz **pozycję MODULE -> FROM ZIP PACKAGE,** aby przekazać nowy moduł **Niestandardowe dodawanie wierszy.**

![Prześlij zip](./media/custom-r-modules/upload-from-zip-package.png)

Moduł **Niestandardowe dodawanie wierszy** jest teraz gotowy do uzyskania dostępu przez eksperymenty uczenia maszynowego.

## <a name="elements-in-the-xml-definition-file"></a>Elementy w pliku definicji XML
### <a name="module-elements"></a>Elementy modułu
Element **Moduł** służy do definiowania modułu niestandardowego w pliku XML. Wiele modułów można zdefiniować w jednym pliku XML przy użyciu wielu elementów **modułu.** Każdy moduł w obszarze roboczym musi mieć unikatową nazwę. Zarejestruj moduł niestandardowy o takiej samej nazwie jak istniejący moduł niestandardowy i zastępuje istniejący moduł nowym. Moduły niestandardowe można jednak zarejestrować pod taką samą nazwą jak istniejący moduł usługi Azure Machine Learning Studio (klasyczny). Jeśli tak, są one wyświetlane w kategorii **Niestandardowe** palety modułów.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


W module **element** można określić dwa dodatkowe elementy opcjonalne:

* Element **właściciel,** który jest osadzony w module  
* A **Description** element, który zawiera tekst, który jest wyświetlany w szybkiej pomocy dla modułu i po najechaniu kursorem na moduł w interfejsie użytkownika uczenia maszynowego.

Reguły dla limitów znaków w module elementów:

* Wartość atrybutu **name** w **Module** element nie może przekraczać 64 znaków długości. 
* Zawartość **description** element nie może przekraczać 128 znaków długości.
* Zawartość **Owner** elementu nie może przekraczać 32 znaków długości.

Wyniki modułu mogą być deterministyczne lub niedeterministyczne.** Domyślnie wszystkie moduły są uważane za deterministyczne. Oznacza to, że biorąc pod uwagę niezmienny zestaw parametrów wejściowych i danych, moduł powinien zwracać te same wyniki eacRAND lub czas funkcji jest uruchamiany. Biorąc pod uwagę to zachowanie, usługa Azure Machine Learning Studio (klasyczna) tylko ponownie wprowadza moduły oznaczone jako deterministyczne, jeśli parametr lub dane wejściowe uległy zmianie. Zwracanie wyników w pamięci podręcznej zapewnia również znacznie szybsze wykonywanie eksperymentów.

Istnieją funkcje, które są niedeterministyczne, takie jak RAND lub funkcja, która zwraca bieżącą datę lub godzinę. Jeśli moduł używa funkcji niedeterministycznej, można określić, że moduł nie jest deterministyczny, ustawiając opcjonalny atrybut **isDeterministic** na **FALSE**. Zapewnia to, że moduł jest uruchamiany ponownie przy każdym uruchomieniu eksperymentu, nawet jeśli dane wejściowe modułu i parametry nie uległy zmianie. 

### <a name="language-definition"></a>Definicja języka
**Element Język** w pliku definicji XML służy do określania niestandardowego języka modułu. Obecnie język R jest jedynym obsługiwanym językiem. Wartość **sourceFile** atrybut musi być nazwa pliku R, który zawiera funkcję do wywołania po uruchomieniu modułu. Ten plik musi być częścią pakietu zip. Wartość atrybutu **entryPoint** jest nazwą wywoływanej funkcji i musi być zgodna z prawidłową funkcją zdefiniowaną w pliku źródłowym.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Porty
Porty wejściowe i wyjściowe modułu niestandardowego są określone w elementach podrzędnych sekcji **Porty** pliku definicji XML. Kolejność tych elementów określa układ doświadczony (UX) przez użytkowników. Pierwsze **dane wejściowe** lub **wyjściowe podrzędne** wymienione w **ports** element pliku XML staje się najbardziej lewym portem wejściowym w ux uczenia maszynowego.
Każdy port wejściowy i wyjściowy może mieć opcjonalny element podrzędny **Opis,** który określa tekst wyświetlany po najechaniu kursorem myszy na port w interfejsie użytkownika uczenia maszynowego.

**Zasady portów:**

* Maksymalna liczba **portów wejściowych i wyjściowych** wynosi 8 dla każdego.

### <a name="input-elements"></a>Elementy wejściowe
Porty wejściowe umożliwiają przekazywanie danych do funkcji R i obszaru roboczego. **Typy danych,** które są obsługiwane dla portów wejściowych są następujące: 

**DataTable:** Ten typ jest przekazywany do funkcji R jako data.frame. W rzeczywistości wszystkie typy (na przykład pliki CSV lub pliki ARFF), które są obsługiwane przez uczenie maszynowe i które są zgodne z **DataTable,** są automatycznie konwertowane na plik data.frame. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

Atrybut **id** skojarzony z każdym portem wejściowym **DataTable** musi mieć unikatową wartość i ta wartość musi być zgodna z odpowiadającym mu nazwanym parametrem w funkcji R.
Opcjonalne porty **DataTable,** które nie są przekazywane jako dane wejściowe w eksperymencie mają wartość **NULL** przekazaną do funkcji R, a opcjonalne porty zip są ignorowane, jeśli dane wejściowe nie są podłączone. Atrybut **isOptional** jest opcjonalny dla typów **DataTable** i **Zip** i jest domyślnie *false.*

**Zamek błyskawiczny:** Moduły niestandardowe mogą akceptować plik zip jako dane wejściowe. To dane wejściowe są rozpakowane do katalogu roboczego R funkcji

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

W przypadku niestandardowych modułów R identyfikator portu Zip nie musi być zgodny z żadnymi parametrami funkcji R. Dzieje się tak, ponieważ plik zip jest automatycznie wyodrębniany do katalogu roboczego R.

**Reguły wprowadzania:**

* Wartość atrybutu **id** elementu **Input** musi być prawidłową nazwą zmiennej R.
* Wartość atrybutu **id** elementu **Input** nie może być dłuższa niż 64 znaki.
* Wartość atrybutu **name** elementu **Input** nie może być dłuższa niż 64 znaki.
* Zawartość elementu **Opis** nie może być dłuższa niż 128 znaków
* Wartość atrybutu **typu** elementu **Input** musi być *zip* lub *DataTable*.
* Wartość atrybutu **isOptional** elementu **Input** nie jest wymagana (i jest *domyślnie false,* gdy nie określono); ale jeśli jest określony, musi być *prawdziwe* lub *fałszywe*.

### <a name="output-elements"></a>Elementy wyjściowe
**Standardowe porty wyjściowe:** Porty wyjściowe są mapowane do wartości zwracanych z funkcji R, które mogą być następnie używane przez kolejne moduły. *DataTable* jest jedynym standardowym typem portu wyjściowego obsługiwanym obecnie. (Wsparcie dla *uczących się* i *przekształceń* jest nadchodzi.) Dane *wyjściowe jest* zdefiniowany jako:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

W przypadku wyjść w niestandardowych modułach języka R wartość atrybutu **id** nie musi odpowiadać nic w skrypcie języka R, ale musi być unikatowa. Dla pojedynczego wyjścia modułu wartość zwracana z funkcji R musi być *data.frame*. Aby wyprowadzić więcej niż jeden obiekt obsługiwanego typu danych, odpowiednie porty wyjściowe muszą być określone w pliku definicji XML, a obiekty muszą zostać zwrócone jako lista. Obiekty wyjściowe są przypisywane do portów wyjściowych od lewej do prawej, odzwierciedlając kolejność umieszczania obiektów na liście zwracanych.

Na przykład, jeśli chcesz zmodyfikować moduł **Niestandardowe dodawanie wierszy,** aby wyprowadzić oryginalne dwa zestawy danych, *zestaw danych1* i *zestaw danych2*, oprócz nowego połączonego zestawu danych, *zestawu danych*, (w kolejności, od lewej do prawej, jako: zestaw *danych*, *zestaw danych1*, *zestaw danych2*), a następnie zdefiniuj porty wyjściowe w pliku CustomAddRows.xml w następujący sposób:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


I zwrócić listę obiektów na liście w odpowiedniej kolejności w "CustomAddRows.R":

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Dane wyjściowe wizualizacji:** Można również określić port wyjściowy typu *Wizualizacja,* który wyświetla dane wyjściowe z urządzenia graficznego R i wyjścia konsoli. Ten port nie jest częścią wyjścia funkcji R i nie koliduje z kolejnością innych typów portów wyjściowych. Aby dodać port wizualizacji do modułów niestandardowych, dodaj element **Wyjście** z wartością *Wizualizacja* dla jego atrybutu **typu:**

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Reguły wyjściowe:**

* Wartość atrybutu **id** elementu **Output** musi być prawidłową nazwą zmiennej R.
* Wartość atrybutu **id** elementu **Output** nie może być dłuższa niż 32 znaki.
* Wartość atrybutu **name** elementu **Output** nie może być dłuższa niż 64 znaki.
* Wartość atrybutu **typu** elementu **Output** musi być *wizualizacja*.

### <a name="arguments"></a>Argumenty
Dodatkowe dane mogą być przekazywane do funkcji R za pomocą parametrów modułu, które są zdefiniowane w **arguments** elementu. Te parametry są wyświetlane w okienku właściwości po prawej stronie interfejsu użytkownika uczenia maszynowego, gdy moduł jest zaznaczony. Argumenty mogą być dowolnym z obsługiwanych typów lub można utworzyć niestandardowe wyliczenie w razie potrzeby. Podobnie jak w przypadku elementów **Ports,** elementy **Argumenty** mogą mieć opcjonalny element **Opis,** który określa tekst wyświetlany po najechaniu kursorem myszy na nazwę parametru.
Opcjonalne właściwości modułu, takie jak defaultValue, minValue i maxValue można dodać do dowolnego argumentu jako atrybuty do elementu **Właściwości.** Prawidłowe właściwości elementu **Właściwości** zależą od typu argumentu i są opisane za pomocą obsługiwanych typów argumentów w następnej sekcji. Argumenty z **isOptional** właściwość ustawiona na **"true"** nie wymagają od użytkownika, aby wprowadzić wartość. Jeśli wartość nie jest podana do argumentu, argument nie jest przekazywany do funkcji punktu wejścia. Argumenty funkcji punktu wejścia, które są opcjonalne, muszą być jawnie obsługiwane przez funkcję, np. Opcjonalny argument wymusza tylko inne ograniczenia argumentów, czyli min lub max, jeśli wartość jest dostarczana przez użytkownika.
Podobnie jak w odniesieniu do wejść i wyjść, ważne jest, aby każdy z parametrów miał unikatowe wartości identyfikatora skojarzone z nimi. W naszym przykładzie szybkiego startu skojarzony identyfikator/parametr został *zamieniony*.

### <a name="arg-element"></a>Element Arg
Parametr modułu jest definiowany przy użyciu elementu podrzędnego **Arg** sekcji **Argumenty** pliku definicji XML. Podobnie jak w odniesieniu do elementów podrzędnych w sekcji **Porty,** kolejność parametrów w sekcji **Argumenty** definiuje układ napotkany w ux. The parameters appear from top down in the UI in the same order in which they are defined in the XML file. Typy obsługiwane przez uczenie maszynowe dla parametrów są wymienione w tym miejscu. 

**int** – parametr typu Liczba całkowita (32-bitowa).

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Właściwości opcjonalne:* **min**, **max**, **domyślne** i **iOptional**

**double** – parametr podwójnego typu.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Właściwości opcjonalne:* **min**, **max**, **domyślne** i **iOptional**

**bool** – parametr logiczny reprezentowany przez pole wyboru w UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Właściwości opcjonalne:* **domyślnie** — false, jeśli nie są ustawione

**ciąg**znaków : ciąg standardowy

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Właściwości opcjonalne:* **domyślne** i **iOptional**

**ColumnPicker**: parametr wyboru kolumny. Ten typ renderuje w ux jako wybieracz kolumn. **Właściwość** Element jest używany w tym miejscu, aby określić identyfikator portu, z którego kolumny są zaznaczone, gdzie typ portu docelowego musi być *DataTable*. Wynik zaznaczenia kolumny jest przekazywany do funkcji R jako lista ciągów zawierających wybrane nazwy kolumn. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Wymagane właściwości:* **portId** - pasuje do identyfikatora elementu wejściowego z typem *DataTable*.
* *Właściwości opcjonalne:*
  
  * **allowedTypes** — filtruje typy kolumn, z których można wybrać. Prawidłowe wartości to: 
    
    * Liczbowe
    * Wartość logiczna
    * Podzielone na kategorie
    * Ciąg
    * Etykieta
    * Funkcja
    * Wynik
    * Wszystkie
  * **domyślne** — prawidłowe domyślne opcje wyboru selektora kolumn obejmują: 
    
    * Brak
    * NumeryczneWykonań
    * Oznaczenia Numeryczne
    * NumericScore (NumericScore)
    * NumericAll
    * BooleanWyjemość
    * Oznaczenia logiczne
    * Wartość logiczna
    * Wartość logicznaWszystki
    * KategoryczneWykonań
    * KategorycznaBela
    * CategoricalScore
    * KategoryczneWszystko
    * StringFeature (Nieujmko napis)
    * StringLabel (łań.)
    * StringScore (wskaz.
    * StringAll (Wszystko na sznurku)
    * AllLabel (AllLabel)
    * WszystkieWyjemne
    * AllScore (AllScore)
    * Wszystkie

**Lista rozwijana:** lista wyliczona przez użytkownika(lista rozwijana). Elementy rozwijane są określone w **właściwości** elementu za pomocą **elementu Element.** Identyfikator **id** dla każdego **elementu** musi być unikatowy i prawidłowa zmienna R. Wartość **nazwy** **Elementu** służy zarówno jako tekst, który widzisz, jak i wartość przekazywana do funkcji R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Właściwości opcjonalne:*
  * **domyślne** — wartość właściwości domyślnej musi odpowiadać wartości identyfikatora z jednego z elementów **Zapas.**

### <a name="auxiliary-files"></a>Pliki pomocnicze
Każdy plik, który jest umieszczony w pliku ZIP modułu niestandardowego będzie dostępny do użycia w czasie wykonywania. Wszystkie obecne struktury katalogów są zachowywane. Oznacza to, że pozyskiwanie plików działa tak samo lokalnie i w usłudze Azure Machine Learning Studio (klasyczne) wykonanie. 

> [!NOTE]
> Należy zauważyć, że wszystkie pliki są wyodrębniane do katalogu 'src', więc wszystkie ścieżki powinny mieć prefiks 'src/'.
> 
> 

Załóżmy na przykład, że chcesz usunąć wszystkie wiersze z urządzeniami zapisu z zestawem danych, a także usunąć wszystkie zduplikowane wiersze przed wysuwaniem ich do Funkcji CustomAddRows, a już napisałeś funkcję R, która robi to w pliku RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Plik pomocniczy RemoveDupNARows.R można pozyskać w funkcji CustomAddRows:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

Następnie prześlij plik zip zawierający "CustomAddRows.R", "CustomAddRows.xml" i "RemoveDupNARows.R" jako niestandardowy moduł R.

## <a name="execution-environment"></a>Środowisko wykonania
Środowisko wykonywania skryptu Języka R używa tej samej wersji języka R co moduł **Wykonywanie skryptu R** i może używać tych samych pakietów domyślnych. Możesz również dodać dodatkowe pakiety R do modułu niestandardowego, dołączając je do niestandardowego pakietu zip modułu. Wystarczy załadować je w skrypcie R, tak jak w swoim własnym środowisku R. 

**Ograniczenia środowiska wykonywania** obejmują:

* Nietrwały system plików: Pliki zapisane po uruchomieniu modułu niestandardowego nie są utrwalone w wielu uruchomieniach tego samego modułu.
* Brak dostępu do sieci

