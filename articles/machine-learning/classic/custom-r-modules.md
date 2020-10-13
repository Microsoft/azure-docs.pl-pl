---
title: 'ML Studio (klasyczny): Tworzenie & wdrażanie niestandardowych modułów R — Azure'
description: Dowiedz się, jak tworzyć i wdrażać niestandardowe moduły R w ML Studio (klasyczne).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 7b5881651312e69ed840eb50388d497258ddeb27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362456"
---
# <a name="define-custom-r-modules-for-machine-learning-studio-classic"></a>Definiowanie niestandardowych modułów R dla Machine Learning Studio (klasyczny)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  

W tym temacie opisano sposób tworzenia i wdrażania niestandardowego programu R Studio (klasycznego). Wyjaśniono, jakie niestandardowe moduły R są i które pliki są używane do ich definiowania. Przedstawiono w nim sposób konstruowania plików, które definiują moduł i sposób rejestrowania modułu do wdrożenia w obszarze roboczym Machine Learning. Elementy i atrybuty używane w definicji modułu niestandardowego są następnie opisane bardziej szczegółowo. Omówiono również sposób korzystania z funkcji i plików pomocniczych oraz wielu danych wyjściowych. 

**Moduł niestandardowy** jest modułem zdefiniowanym przez użytkownika, który można przekazać do obszaru roboczego i wykonywać w ramach eksperymentu Azure Machine Learning Studio (klasycznego). **Niestandardowy moduł języka r** jest modułem niestandardowym, który wykonuje funkcję języka r zdefiniowaną przez użytkownika. **R** to język programowania na potrzeby statystycznych obliczeń i grafiki, który jest szeroko używany przez statystyków i analityków danych do wdrażania algorytmów. Obecnie język R jest jedynym obsługiwanym w modułach niestandardowych, ale Obsługa dodatkowych języków jest zaplanowana dla przyszłych wersji.

Moduły niestandardowe mają **stan pierwszej klasy** w Azure Machine Learning Studio (klasyczny) w sensie, że mogą być używane podobnie jak każdy inny moduł. Mogą być wykonywane z innymi modułami, zawartymi w opublikowanych eksperymentach lub w wizualizacjach. Masz kontrolę nad algorytmem implementowanym przez moduł, portami wejściowymi i wyjściowymi, które mają być używane, parametrami modelowania i innymi różnymi zachowaniami środowiska uruchomieniowego. Eksperyment zawierający moduły niestandardowe można również opublikować w Azure AI Gallery w celu ułatwienia udostępniania.

## <a name="files-in-a-custom-r-module"></a>Pliki w niestandardowym module języka R
Niestandardowy moduł języka R jest definiowany przy użyciu pliku zip, który zawiera co najmniej dwa pliki:

* **Plik źródłowy** , który implementuje funkcję języka R uwidocznioną przez moduł
* **Plik definicji XML** opisujący niestandardowy interfejs modułu

Dodatkowe pliki pomocnicze mogą być również dołączone do pliku zip, który zapewnia funkcje, do których można uzyskać dostęp z modułu niestandardowego. Ta opcja jest omówiona w części **argumentów** **elementów Reference w pliku definicji XML** , po podaniu przykładu szybkiego startu.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Przykład szybkiego startu: Definiowanie, pakowanie i rejestrowanie niestandardowego modułu języka R
Ten przykład ilustruje sposób konstruowania plików wymaganych przez niestandardowy moduł języka R, pakowania ich do pliku zip, a następnie rejestrowania modułu w obszarze roboczym Machine Learning. Przykładowy pakiet ZIP i pliki przykładowe można pobrać z [pliku pobierania CustomAddRows.zip](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Plik źródłowy
Rozważmy przykład niestandardowego modułu **dodawania wierszy** , który modyfikuje standardową implementację modułu **Dodaj wiersze** służącą do łączenia wierszy (obserwacje) z dwóch zestawów danych (ramek z danymi). Moduł standardowe **Dodawanie wierszy** dołącza wiersze drugiego wejściowego zestawu danych do końca pierwszego wejściowego zestawu danych przy użyciu `rbind` algorytmu. Dostosowana `CustomAddRows` Funkcja w podobny sposób akceptuje dwa zestawy danych, ale również akceptuje parametr wymiany wartości logicznych jako dodatkowe dane wejściowe. Jeśli parametr swap ma wartość **false**, zwraca ten sam zestaw danych co standardowa implementacja. Ale jeśli parametr swap ma **wartość true**, funkcja dołącza wiersze pierwszego wejściowego zestawu danych do końca drugiego zestawu danych. Plik CustomAddRows. R, który zawiera implementację funkcji języka R `CustomAddRows` uwidocznioną w module **niestandardowego dodawania wierszy** , ma następujący kod R.

```r
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
```

### <a name="the-xml-definition-file"></a>Plik definicji XML
Aby udostępnić tę `CustomAddRows` funkcję jako moduł Azure Machine Learning Studio (klasyczny), należy utworzyć plik definicji XML, aby określić sposób wyglądu i zachowania **niestandardowego modułu dodawania wierszy** . 

```xml
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
```

Należy pamiętać, że wartość atrybutów **identyfikatora** elementów **wejściowych** i **ARG** w pliku XML musi być zgodna z nazwami parametrów funkcji kodu R w pliku CustomAddRows. R dokładnie: (*pozycję DataSet1*, *DataSet2*i *swap* w przykładzie). Podobnie wartość atrybutu **EntryPoint** elementu **Language** musi być zgodna z nazwą funkcji w skrypcie języka R dokładnie: (*CustomAddRows* w przykładzie). 

Natomiast atrybut **ID** dla elementu **Output** nie odpowiada żadnym zmiennym w skrypcie języka R. Gdy jest wymagane więcej niż jedno wyjście, wystarczy zwrócić listę z funkcji R z wynikami umieszczonymi *w tej samej kolejności* , co elementy **wyjściowe** są zadeklarowane w pliku XML.

### <a name="package-and-register-the-module"></a>Pakowanie i Rejestrowanie modułu
Zapisz te dwa pliki jako *CustomAddRows. R* i *CustomAddRows.xml* , a następnie zapisuj dwa pliki w pliku *CustomAddRows.zip* .

Aby zarejestrować je w obszarze roboczym Machine Learning, przejdź do obszaru roboczego w Azure Machine Learning Studio (klasyczny), kliknij przycisk **+ Nowy** u dołu i wybierz polecenie **moduł-> z pakietu zip** , aby załadować nowe **niestandardowe moduły dodawania wierszy** .

![Przekaż plik zip](./media/custom-r-modules/upload-from-zip-package.png)

Moduł **niestandardowe Dodawanie wierszy** jest teraz gotowy do uzyskania dostępu do Machine Learning eksperymentów.

## <a name="elements-in-the-xml-definition-file"></a>Elementy w pliku definicji XML
### <a name="module-elements"></a>Elementy modułu
Element **module** służy do definiowania modułu niestandardowego w pliku XML. Wiele modułów można zdefiniować w jednym pliku XML przy użyciu wielu elementów **modułu** . Każdy moduł w obszarze roboczym musi mieć unikatową nazwę. Zarejestruj niestandardowy moduł o takiej samej nazwie jak istniejący moduł niestandardowy i zastępuje istniejący moduł nowym. Moduły niestandardowe mogą jednak być zarejestrowane z taką samą nazwą jak istniejący moduł Azure Machine Learning Studio (klasyczny). Jeśli tak, pojawiają się one w kategorii **niestandardowe** palety modułów.

```xml
<Module name="Custom Add Rows" isDeterministic="false"> 
    <Owner>Microsoft Corporation</Owner>
    <Description>Appends one dataset to another...</Description>/> 
```

W obrębie elementu **module** można określić dwa dodatkowe elementy opcjonalne:

* element **Owner** , który jest osadzony w module  
* element **Description** zawierający tekst, który jest wyświetlany w szybkiej pomocy dla modułu i po umieszczeniu wskaźnika myszy na module w interfejsie użytkownika Machine Learning.

Reguły dotyczące znaków w elementach modułu:

* Wartość atrybutu **name** w elemencie **module** nie może przekraczać 64 znaków. 
* Zawartość elementu **Description** nie może przekraczać 128 znaków.
* Zawartość elementu **Owner** nie może przekraczać 32 znaków.

Wyniki modułu mogą być deterministyczne lub niedeterministyczne. * * Domyślnie wszystkie moduły są uznawane za deterministyczne. W odróżnieniu od zmiany zestawu parametrów wejściowych i danych moduł powinien zwrócić te same wyniki eacRAND lub czas funkcji, który jest uruchamiany. Uwzględniając to zachowanie, Azure Machine Learning Studio (klasyczne) uruchamia ponownie moduły oznaczone jako deterministyczne, jeśli parametr lub dane wejściowe uległy zmianie. Zwrócenie buforowanych wyników również zapewnia znacznie szybsze wykonywanie eksperymentów.

Istnieją funkcje, które są niejednoznaczne, takie jak RAND lub funkcja zwracająca bieżącą datę lub godzinę. Jeśli moduł używa funkcji niedeterministycznej, można określić, że moduł jest niedeterministyczny przez ustawienie opcjonalnego atrybutu **Isdeterministyczny** na **wartość false**. Oznacza to, że moduł jest uruchamiany ponownie za każdym razem, gdy eksperyment zostanie uruchomiony, nawet jeśli dane wejściowe i parametry modułu nie uległy zmianie. 

### <a name="language-definition"></a>Definicja języka
Element **Language** w pliku definicji XML jest używany do określenia niestandardowego języka modułu. Obecnie język R jest jedynym obsługiwanym językiem. Wartość atrybutu **SourceFile** musi być nazwą pliku R, który zawiera funkcję do wywołania, gdy moduł jest uruchomiony. Ten plik musi być częścią pakietu zip. Wartość atrybutu **EntryPoint** jest nazwą wywoływanej funkcji i musi być zgodna z prawidłową funkcją zdefiniowaną w pliku źródłowym.

```xml
<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />
```

### <a name="ports"></a>Porty
Porty wejściowe i wyjściowe dla modułu niestandardowego są określone w elementach podrzędnych sekcji **portów** w pliku definicji XML. Kolejność tych elementów określa układ (UX) przez użytkowników. Pierwsze podrzędne **dane wejściowe** lub **wyjściowe** wymienione w elemencie **Ports** pliku XML staną się największym portem wejściowym w Machine Learning środowisku użytkownika.
Każdy port wejściowy i wyjściowy może mieć opcjonalny element podrzędny **opisu** , który określa tekst wyświetlany po umieszczeniu wskaźnika myszy nad portem w interfejsie użytkownika Machine Learning.

**Reguły portów**:

* Maksymalna liczba **portów wejściowych i wyjściowych** to 8 dla każdej z nich.

### <a name="input-elements"></a>Elementy wejściowe
Porty wejściowe umożliwiają przekazywanie danych do funkcji i obszaru roboczego języka R. **Typy danych** , które są obsługiwane dla portów wejściowych są następujące: 

**DataTable:** Ten typ jest przesyłany do funkcji R jako dane. Frame. W rzeczywistości wszystkie typy (na przykład pliki CSV lub pliki ARFF), które są obsługiwane przez Machine Learning i są zgodne z elementem **DataTable** , są automatycznie konwertowane na ramkę danych. 

```xml
<Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
    <Description>Input Dataset 1</Description>
</Input>
```

Atrybut **ID** skojarzony z każdym portem wejściowym **DataTable** musi mieć unikatową wartość, a ta wartość musi być zgodna z odpowiadającym mu parametrem nazwanym w funkcji języka R.
Opcjonalne porty **DataTable** , które nie są przenoszone jako dane wejściowe w eksperymentie, mają wartość **null** przekazaną do funkcji R, a opcjonalne porty zip są ignorowane, jeśli dane wejściowe nie są połączone. Atrybut **isoption** jest opcjonalny dla typów **DataTable** i **zip** i domyślnie ma *wartość false* .

**Kod pocztowy:** Moduły niestandardowe mogą akceptować plik zip jako dane wejściowe. To dane wejściowe są rozpakowane do katalogu roboczego języka R funkcji

```xml
<Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
    <Description>Zip files to be extracted to the R working directory.</Description>
</Input>
```

W przypadku niestandardowych modułów R identyfikator dla portu zip nie musi być zgodny z żadnymi parametrami funkcji języka R. Dzieje się tak, ponieważ plik zip jest automatycznie wyodrębniany do katalogu roboczego języka R.

**Reguły wejściowe:**

* Wartość atrybutu **ID** elementu **wejściowego** musi być prawidłową nazwą zmiennej języka R.
* Wartość atrybutu **ID** elementu **wejściowego** nie może być dłuższa niż 64 znaków.
* Wartość atrybutu **name** elementu **wejściowego** nie może być dłuższa niż 64 znaków.
* Zawartość elementu **Description** nie może być dłuższa niż 128 znaków
* Wartość atrybutu **typu** elementu **wejściowego** musi być *zip* lub *DataTable*.
* Wartość atrybutu **isoption** elementu **wejściowego** nie jest wymagana (i ma wartość *false* domyślnie, jeśli nie zostanie określona); ale jeśli jest określony, musi mieć *wartość true* lub *false*.

### <a name="output-elements"></a>Elementy wyjściowe
**Standardowe porty wyjściowe:** Porty wyjściowe są mapowane na wartości zwracane z funkcji języka R, które mogą być następnie używane przez kolejne moduły. *DataTable* jest obecnie obsługiwany tylko dla standardowego typu portu wyjściowego. (Pomoc techniczna dla *Nauczyń* i *przekształceń* ). Dane wyjściowe *elementu DataTable* są zdefiniowane jako:

```xml
<Output id="dataset" name="Dataset" type="DataTable">
    <Description>Combined dataset</Description>
</Output>
```

W przypadku danych wyjściowych w niestandardowych modułach języka R wartość atrybutu **ID** nie musi odpowiadać żadnemu elementowi w skrypcie języka r, ale musi być unikatowa. W przypadku danych wyjściowych pojedynczego modułu wartość zwracana z funkcji R musi być typu *Data. Frame*. Aby wyprowadzić więcej niż jeden obiekt obsługiwanego typu danych, należy określić odpowiednie porty wyjściowe w pliku definicji XML, a obiekty muszą zostać zwrócone jako lista. Obiekty wyjściowe są przypisywane do portów wyjściowych od lewej do prawej, odzwierciedlając kolejność, w jakiej obiekty są umieszczane na zwracanej liście.

Na przykład, jeśli chcesz zmodyfikować **niestandardowy moduł dodawania wierszy** , aby wyprowadził oryginalne dwa zestawy danych, *pozycję DataSet1* i *DataSet2*, a także do nowego dołączonego elementu DataSet, *zestawu danych*(w kolejności, od lewej do prawej, jako: *DataSet*, *pozycję DataSet1*, *DataSet2*), a następnie zdefiniuj porty wyjściowe w pliku CustomAddRows.xml w następujący sposób:

```xml
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
```

I zwróć listę obiektów na liście w odpowiedniej kolejności w "CustomAddRows. R":

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
    if (swap) { dataset <- rbind(dataset2, dataset1)) } 
    else { dataset <- rbind(dataset1, dataset2)) 
    } 
    return (list(dataset, dataset1, dataset2)) 
} 
```

**Dane wyjściowe wizualizacji:** Możesz również określić port wyjściowy *wizualizacji*, który wyświetla dane wyjściowe z urządzenia grafiki R i danych wyjściowych konsoli. Ten port nie należy do danych wyjściowych funkcji języka R i nie zakłóca kolejności innych typów portów wyjściowych. Aby dodać port wizualizacji do modułów niestandardowych, Dodaj element **wyjściowy** z wartością *wizualizacji* dla atrybutu **typu** :

```xml
<Output id="deviceOutput" name="View Port" type="Visualization">
    <Description>View the R console graphics device output.</Description>
</Output>
```

**Reguły wyjściowe:**

* Wartość atrybutu **ID** elementu **wyjściowego** musi być prawidłową nazwą zmiennej języka R.
* Wartość atrybutu **ID** elementu **wyjściowego** nie może być dłuższa niż 32 znaków.
* Wartość atrybutu **name** elementu **wyjściowego** nie może być dłuższa niż 64 znaków.
* Wartość atrybutu **typu** elementu **wyjściowego** musi być *wizualizacją*.

### <a name="arguments"></a>Argumenty
Dodatkowe dane można przesłać do funkcji języka R za pośrednictwem parametrów modułu, które są zdefiniowane w elemencie **arguments** . Te parametry pojawiają się w okienku właściwości z prawej strony w interfejsie użytkownika Machine Learning, gdy moduł jest wybrany. Argumenty mogą być dowolnymi obsługiwanymi typami lub w razie konieczności można utworzyć niestandardowe Wyliczenie. Podobnie jak w przypadku elementów **Ports** , elementy **argumentów** mogą mieć opcjonalny element **opisu** , który określa tekst wyświetlany po umieszczeniu wskaźnika myszy nad nazwą parametru.
Opcjonalne właściwości dla modułu, takie jak DefaultValue, minValue i maxValue, można dodać do dowolnego argumentu jako atrybuty elementu **Properties** . Prawidłowe właściwości elementu **Properties** są zależne od typu argumentu i są opisane z obsługiwanymi typami argumentów w następnej sekcji. Argumenty z właściwością **isoption** ustawioną na **wartość "true"** nie wymagają od użytkownika wprowadzenia wartości. Jeśli wartość nie jest określona dla argumentu, argument nie zostanie przekazana do funkcji punktu wejścia. Argumenty funkcji punktu wejścia, które są opcjonalne, muszą być jawnie obsługiwane przez funkcję, np. przypisaną domyślną wartością NULL w definicji funkcji punktu wejścia. Opcjonalny argument wymusza tylko inne ograniczenia argumentów, np. min lub Max, jeśli wartość jest podana przez użytkownika.
Podobnie jak w przypadku danych wejściowych i wyjściowych, ma kluczowe znaczenie, że każdy z parametrów ma unikatowe wartości identyfikatorów skojarzonych z nimi. W naszym przykładzie szybkiego startu skojarzony identyfikator/parametr został *zamieniony*.

### <a name="arg-element"></a>ARG, element
Parametr modułu jest definiowany przy użyciu elementu podrzędnego **ARG** w sekcji **argumenty** w pliku definicji XML. Podobnie jak w przypadku elementów podrzędnych w sekcji **porty** , kolejność parametrów w sekcji **argumenty** definiuje układ w środowisku użytkownika. Parametry są wyświetlane od góry w interfejsie użytkownika w takiej samej kolejności, w jakiej są zdefiniowane w pliku XML. Typy obsługiwane przez Machine Learning parametrów są wymienione tutaj. 

**int** — parametr typu Integer (32-bitowy).

```xml
<Arg id="intValue1" name="Int Param" type="int">
    <Properties min="0" max="100" default="0" />
    <Description>Integer Parameter</Description>
</Arg>
```

* *Właściwości opcjonalne*: **min**, **Max**, **default** i **isoption**

**Double** — parametr typu Double.

```xml
<Arg id="doubleValue1" name="Double Param" type="double">
    <Properties min="0.000" max="0.999" default="0.3" />
    <Description>Double Parameter</Description>
</Arg>
```

* *Właściwości opcjonalne*: **min**, **Max**, **default** i **isoption**

**bool** — parametr logiczny, który jest reprezentowany przez pole wyboru w interfejsie użytkownika.

```xml
<Arg id="boolValue1" name="Boolean Param" type="bool">
    <Properties default="true" />
    <Description>Boolean Parameter</Description>
</Arg>
```

* *Właściwości opcjonalne*: **Domyślnie** -false, jeśli nie ustawiono

**String**: ciąg standardowy

```xml
<Arg id="stringValue1" name="My string Param" type="string">
    <Properties isOptional="true" />
    <Description>String Parameter 1</Description>
</Arg>    
```

* *Właściwości opcjonalne*: **domyślne** i **isoption**

**ColumnPicker**: parametr wyboru kolumny. Ten typ jest renderowany w środowisku użytkownika jako Selektor kolumn. Element **Property** służy tutaj do określania identyfikatora portu, z którego są wybierane kolumny, gdzie typem portu docelowego musi być *DataTable*. Wynik zaznaczenia kolumny jest przenoszona do funkcji R jako lista ciągów zawierających nazwy wybranych kolumn. 

```xml
<Arg id="colset" name="Column set" type="ColumnPicker">      
    <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
    <Description>Column set</Description>
</Arg>
```

* *Wymagane właściwości*: **Identyfikator portu** — dopasowuje identyfikator elementu wejściowego typu *DataTable*.
* *Właściwości opcjonalne*:
  
  * **allowedTypes** — filtruje typy kolumn, z których można wybierać. Prawidłowe wartości to: 
    
    * Liczbowe
    * Boolean (wartość logiczna)
    * Podzielone na kategorie
    * Ciąg
    * Etykieta
    * Cecha
    * Wynik
    * Wszystkie
  * **domyślne** ustawienia domyślne dla selektora kolumn są prawidłowe: 
    
    * Brak
    * NumericFeature
    * NumericLabel
    * NumericScore
    * Wartości numeryczne
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Wszystkie

**Menu rozwijane**: Lista wyliczeniowa określona przez użytkownika (lista rozwijana). Elementy rozwijane są określone w elemencie **Properties** przy użyciu elementu **Item** . **Identyfikator** każdego **elementu** musi być unikatowy i prawidłową zmienną języka R. Wartość **nazwy** **elementu** służy zarówno jako wyświetlany tekst, jak i wartość, która jest przesyłana do funkcji języka R.

```xml
<Arg id="color" name="Color" type="DropDown">
    <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
    </Properties>
    <Description>Select a color.</Description>
</Arg>    
```

* *Właściwości opcjonalne*:
  * **default** — wartość właściwości Default musi odpowiadać wartości identyfikatora z jednego z elementów **Item** .

### <a name="auxiliary-files"></a>Pliki pomocnicze
Każdy plik umieszczony w pliku ZIP modułu niestandardowego będzie dostępny do użycia w czasie wykonywania. Wszystkie istniejące struktury katalogów są zachowywane. Oznacza to, że pozyskiwanie plików działa tak samo lokalnie i w Azure Machine Learning Studio (klasyczne). 

> [!NOTE]
> Zwróć uwagę, że wszystkie pliki są wyodrębniane do katalogu "src", więc wszystkie ścieżki powinny mieć prefiks "src/".
> 
> 

Na przykład załóżmy, że chcesz usunąć wszystkie wiersze z zestawem danych, a także usunąć wszystkie zduplikowane wiersze, zanim przeniesiesz je do CustomAddRows, i już napisane funkcję języka R, która robi w pliku RemoveDupNARows. R:

```r
RemoveDupNARows <- function(dataFrame) {
    #Remove Duplicate Rows:
    dataFrame <- unique(dataFrame)
    #Remove Rows with NAs:
    finalDataFrame <- dataFrame[complete.cases(dataFrame),]
    return(finalDataFrame)
}
```

Plik pomocniczy RemoveDupNARows. R można uzyskać w funkcji CustomAddRows:

```r
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
```

Następnie Przekaż plik zip zawierający "CustomAddRows. R", "CustomAddRows.xml" i "RemoveDupNARows. R" jako niestandardowy moduł języka R.

## <a name="execution-environment"></a>Środowisko wykonawcze
Środowisko wykonawcze dla skryptu języka R używa tej samej wersji języka R jak modułu **wykonywania skryptu języka r** i może korzystać z tych samych pakietów domyślnych. Możesz również dodać dodatkowe pakiety języka R do modułu niestandardowego, dołączając je do niestandardowego pakietu zip modułu. Po prostu załaduj je w skrypcie języka R tak samo jak w środowisku języka R. 

**Ograniczenia środowiska wykonawczego** obejmują:

* Nietrwały system plików: pliki zapisywane podczas uruchamiania modułu niestandardowego nie są utrwalane w wielu przebiegach tego samego modułu.
* Brak dostępu do sieci

