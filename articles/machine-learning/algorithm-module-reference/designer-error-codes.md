---
title: Rozwiązywanie problemów z błędami modułu
titleSuffix: Azure Machine Learning
description: Rozwiązywanie problemów z wyjątkami modułów w projektancie usługi Azure Machine Learning przy użyciu kodów błędów
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/16/2020
ms.openlocfilehash: cc04d11475568af92ba6a617a1eb6b2b51accb45
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481670"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>Wyjątki i kody błędów dla projektanta (wersja zapoznawcza)

W tym artykule opisano komunikaty o błędach i kody wyjątków w projektancie usługi Azure Machine Learning (wersja zapoznawcza), aby ułatwić rozwiązywanie problemów z potokami uczenia maszynowego.

Komunikat o błędzie można znaleźć w projektancie, wykonując następujące kroki:  

- Wybierz moduł nie powiodło się, przejdź do **dane wyjściowe + dzienniki** kartę, można znaleźć szczegółowy dziennik w pliku **70_driver_log.txt** w kategorii **azureml-logs.**

- Aby uzyskać szczegółowy błąd modułu, można sprawdzić go w error_info.json w kategorii **module_statistics.**

Poniżej znajdują się kody błędów modułów w projektancie.

## <a name="error-0001"></a>Błąd 0001  
 Wyjątek występuje, jeśli nie można odnaleźć jednej lub więcej określonych kolumn zestawu danych.  

 Ten błąd zostanie wyświetlony, jeśli zostanie dokonany wybór kolumny dla modułu, ale wybrane kolumny nie istnieją w zestawie danych wejściowych. Ten błąd może wystąpić, jeśli ręcznie wpisano nazwę kolumny lub jeśli selektor kolumn dostarczył sugerowaną kolumnę, która nie istniała w zestawie danych podczas uruchamiania potoku.  

**Rozdzielczość:** Ponownie moduł zgłosić ten wyjątek i sprawdź, czy nazwa kolumny lub nazwy są poprawne i że istnieją wszystkie kolumny, do których istnieje.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie znaleziono co najmniej jednej określonej kolumny.|
|Nie znaleziono kolumny z nazwą lub indeksem "{column_id}".|
|Kolumna z nazwą lub indeksem "{column_id}" nie istnieje w "{arg_name_missing_column}".|
|Kolumna z nazwą lub indeksem "{column_id}" nie istnieje w "{arg_name_missing_column}", ale istnieje w "{arg_name_has_column}".|
|Nie znaleziono kolumn o nazwie lub indeksie "{column_names}".|
|Kolumny o nazwie lub indeksie "{column_names}" nie istnieją w "{arg_name_missing_column}".|
|Kolumny o nazwie lub indeksie "{column_names}" nie istnieją w "{arg_name_missing_column}", ale istnieją w "{arg_name_has_column}".|


## <a name="error-0002"></a>Błąd 0002  
 Wyjątek występuje, jeśli nie można przeanalizować jednego lub więcej parametrów z określonego typu na wymagany przez typ metody docelowej.  

 Ten błąd występuje w usłudze Azure Machine Learning po określeniu parametru jako danych wejściowych, a typ wartości różni się od typu, który jest oczekiwany, a niejawna konwersja nie może być wykonana.  

**Rozdzielczość:** Sprawdź wymagania modułu i określ, który typ wartości jest wymagany (ciąg, liczba całkowita, podwójna itp.)  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przeanalizować parametru.|
|Nie można przeanalizować parametru "{arg_name_or_column}".|
|Nie można przekonwertować parametru "{arg_name_or_column}" na "{to_type}".|
|Nie można przekonwertować parametru "{arg_name_or_column}" z "{from_type}" na "{to_type}".|
|Nie można przekonwertować wartości parametru "{arg_name_or_column}" "{arg_value}" z "{from_type}" na "{to_type}".|
|Nie można przekonwertować wartości "{arg_value}" w kolumnie "{arg_name_or_column}" z "{from_type}" na "{to_type}" przy użyciu podanego formatu "{fmt}".|


## <a name="error-0003"></a>Błąd 0003  
 Wyjątek występuje, jeśli co najmniej jedno dane wejściowe są puste lub puste.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli wszystkie dane wejściowe lub parametry do modułu są zerowe lub puste.  Ten błąd może wystąpić, na przykład, gdy nie wpisałeś żadnej wartości dla parametru. Może się to również zdarzyć, jeśli wybrano zestaw danych, który ma brakujące wartości lub pusty zestaw danych.  

**Rozdzielczość:**

+ Otwórz moduł, który wyprodukował wyjątek i sprawdź, czy określono wszystkie dane wejściowe. Upewnij się, że określono wszystkie wymagane dane wejściowe. 
+ Upewnij się, że dane ładowane z magazynu platformy Azure są dostępne i że nazwa konta lub klucz nie uległ zmianie.  
+ Sprawdź dane wejściowe pod kątem brakujących wartości lub wartości null.
+ Jeśli używasz kwerendy w źródle danych, sprawdź, czy dane są zwracane w oczekiwanym formacie. 
+ Sprawdź literówki lub inne zmiany w specyfikacji danych.
  
|Komunikaty o wyjątkach|
|------------------------|
|Co najmniej jedno wejście jest zerowych lub pustych.|
|Wejście "{name}" ma wartość null lub jest puste.|


## <a name="error-0004"></a>Błąd 0004  
 Wyjątek występuje, jeśli parametr jest mniejszy lub równy określonej wartości.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli parametr w komunikacie znajduje się poniżej wartości granicy wymaganej dla modułu do przetwarzania danych.  

**Rozdzielczość:** Ponownie moduł zgłosić wyjątek i zmodyfikować parametr jest większy niż określona wartość.  

|Komunikaty o wyjątkach|
|------------------------|
|Parametr powinien być większy niż wartość graniczna.|
|Wartość parametru "{arg_name}" powinna być większa niż {lower_boundary}.|
|Parametr "{arg_name}" ma wartość "{actual_value}", która powinna być większa niż {lower_boundary}.|


## <a name="error-0005"></a>Błąd 0005  
 Wyjątek występuje, jeśli parametr jest mniejszy niż określona wartość.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli parametr w komunikacie znajduje się poniżej lub jest równy wartości granicy wymaganej dla modułu do przetwarzania danych.  

**Rozdzielczość:** Ponownie moduł zgłosić wyjątek i zmodyfikować parametr jest większa lub równa określonej wartości.  

|Komunikaty o wyjątkach|
|------------------------|
|Parametr powinien być większy lub równy wartości granicznej.|
|Wartość parametru "{arg_name}" powinna być większa lub równa {lower_boundary}.|
|Parametr "{arg_name}" ma wartość "{value}", która powinna być większa lub równa {lower_boundary}.|


## <a name="error-0006"></a>Błąd 0006  
 Wyjątek występuje, jeśli parametr jest większy lub równy określonej wartości.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli parametr w wiadomości jest większa lub równa wartości granicy wymaganej dla modułu do przetwarzania danych.  

**Rozdzielczość:** Ponownie moduł zgłosić wyjątek i zmodyfikować parametr jest mniejsza niż określona wartość.  

|Komunikaty o wyjątkach|
|------------------------|
|Niezgodność parametrów. Jeden z parametrów powinien być mniejszy niż inny.|
|Wartość parametru "{arg_name}" powinna być mniejsza niż wartość parametru "{upper_boundary_parameter_name}".|
|Parametr "{arg_name}" ma wartość "{value}", która powinna być mniejsza niż {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Błąd 0007  
 Wyjątek występuje, jeśli parametr jest większy niż określona wartość.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli we właściwościach modułu określono wartość, która jest większa niż jest dozwolona. Na przykład można określić dane, które znajdują się poza zakresem obsługiwanych dat lub można wskazać, że pięć kolumn będzie używanych, gdy dostępne są tylko trzy kolumny. 

 Ten błąd może również zostać wyświetlony, jeśli określasz dwa zestawy danych, które muszą być w jakiś sposób dopasowane. Na przykład w przypadku zmiany nazwy kolumn i określenia kolumn według indeksu liczba podanych nazw musi odpowiadać liczbie indeksów kolumn. Innym przykładem może być operacja matematyczna, która używa dwóch kolumn, gdzie kolumny muszą mieć taką samą liczbę wierszy. 

**Rozdzielczość:**

 + Otwórz dany moduł i przejrzyj wszelkie ustawienia właściwości liczbowych.
 + Upewnij się, że wszystkie wartości parametrów mieszczą się w obsługiwanym zakresie wartości dla tej właściwości.
 + Jeśli moduł przyjmuje wiele wejść, upewnij się, że dane wejściowe są tego samego rozmiaru.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Sprawdź, czy zmienił się zestaw danych lub źródło danych. Czasami wartość, która pracowała z poprzednią wersją danych, zakończy się niepowodzeniem po zmianie liczby kolumn, typów danych kolumn lub rozmiaru danych.  

|Komunikaty o wyjątkach|
|------------------------|
|Niezgodność parametrów. Jeden z parametrów powinien być mniejszy lub równy drugiemu.|
|Wartość parametru "{arg_name}" powinna być mniejsza lub równa wartości parametru "{upper_boundary_parameter_name}".|
|Parametr "{arg_name}" ma wartość "{actual_value}", która powinna być mniejsza lub równa {upper_boundary}.|
|Wartość parametru "{arg_name}" {actual_value} powinna być mniejsza lub równa wartości parametru "{upper_boundary_parameter_name}" {upper_boundary}.|


## <a name="error-0008"></a>Błąd 0008  
 Wyjątek występuje, jeśli parametr nie znajduje się w zakresie.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli parametr w wiadomości jest poza granicami wymaganymi dla modułu do przetwarzania danych.  

 Na przykład ten błąd jest wyświetlany, jeśli spróbujesz użyć [Dodaj wiersze,](add-rows.md) aby połączyć dwa zestawy danych, które mają inną liczbę kolumn.  

**Rozdzielczość:** Ponownie moduł zgłosić wyjątek i zmodyfikować parametr, aby być w określonym zakresie.  

|Komunikaty o wyjątkach|
|------------------------|
|Wartość parametru nie znajduje się w określonym zakresie.|
|Wartość parametru "{arg_name}" nie znajduje się w zakresie.|
|Wartość parametru "{arg_name}" powinna znajdować się w zakresie [{lower_boundary}, {upper_boundary}].|
|Wartość parametru "{arg_name}" nie znajduje się w zakresie. {powód}|


## <a name="error-0009"></a>Błąd 0009  
 Wyjątek występuje, gdy nazwa konta magazynu platformy Azure lub nazwa kontenera jest określona niepoprawnie.  

Ten błąd występuje w projektancie usługi Azure Machine Learning po określeniu parametrów dla konta magazynu platformy Azure, ale nie można rozpoznać nazwy ani hasła. Błędy haseł lub nazw kont mogą się zdarzyć z wielu powodów:

 + Konto jest niewłaściwym typem. Niektóre nowe typy kont nie są obsługiwane do użytku z projektantem uczenia maszynowego. Zobacz [Importowanie danych, aby](import-data.md) uzyskać szczegółowe informacje.
 + Wprowadzono nieprawidłową nazwę konta
 + Konto już nie istnieje
 + Hasło do konta magazynu jest nieprawidłowe lub zostało zmienione
 + Nie określono nazwy kontenera lub kontener nie istnieje
 + Nie określono w pełni ścieżki pliku (ścieżka do obiektu blob)
   

**Rozdzielczość:**

Takie problemy często występują podczas próby ręcznego wprowadzenia nazwy konta, hasła lub ścieżki kontenera. Zaleca się użycie nowego kreatora modułu [Importuj dane,](import-data.md) który ułatwia wyszukanie i sprawdzanie nazw.

Sprawdź również, czy konto, kontener lub obiekt blob zostały usunięte. Użyj innego narzędzia magazynu platformy Azure, aby sprawdzić, czy nazwa konta i hasło zostały wprowadzone poprawnie i czy kontener istnieje. 

Niektóre nowsze typy kont nie są obsługiwane przez usługę Azure Machine Learning. Na przykład nowe typy magazynu "hot" lub "cold" nie mogą być używane do uczenia maszynowego. Zarówno klasyczne konta magazynu, jak i konta magazynu utworzone jako "Ogólnego przeznaczenia" działają poprawnie.

Jeśli określono pełną ścieżkę do obiektu blob, sprawdź, czy ścieżka jest określona jako **kontener/blobname**i czy kontener i obiekt blob istnieją na koncie.  

 Ścieżka nie powinna zawierać ukośnika wiodącego. Na przykład **/container/blob** jest niepoprawna i powinna zostać wprowadzona jako **kontener/obiekt blob**.  


|Komunikaty o wyjątkach|
|------------------------|
|Nazwa konta magazynu platformy Azure lub nazwa kontenera jest niepoprawna.|
|Nazwa konta magazynu platformy Azure "{account_name}" lub nazwa kontenera "{container_name}" jest niepoprawna; oczekiwano nazwy kontenera kontenera/obiektu blob formatu.|


## <a name="error-0010"></a>Błąd 0010  
 Wyjątek występuje, jeśli wejściowe zestawy danych mają nazwy kolumn, które powinny być zgodne, ale nie.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli indeks kolumn w wiadomości ma różne nazwy kolumn w dwóch wejściowych zestawów danych.  

**Rozdzielczość:** Użyj [funkcji Edytuj metadane](edit-metadata.md) lub zmodyfikuj oryginalny zestaw danych, aby mieć taką samą nazwę kolumny dla określonego indeksu kolumny.  

|Komunikaty o wyjątkach|
|------------------------|
|Kolumny z odpowiednim indeksem w wejściowych zestawach danych mają różne nazwy.|
|Nazwy kolumn nie są takie same dla kolumny {col_index} (zero-based) wejściowych zestawów danych ({dataset1} i {dataset2} odpowiednio).|


## <a name="error-0011"></a>Błąd 0011  
 Wyjątek występuje, jeśli przekazany argument zestawu kolumn nie ma zastosowania do żadnej z kolumn zestawu danych.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli określony wybór kolumny nie pasuje do żadnej z kolumn w danym zestawie danych.  

 Ten błąd można również uzyskać, jeśli nie wybrano kolumny i co najmniej jedna kolumna jest wymagana do działania modułu.  

**Rozdzielczość:** Zmodyfikuj zaznaczenie kolumny w module, tak aby było stosowane do kolumn w zestawie danych.  

 Jeśli moduł wymaga wybrania określonej kolumny, takiej jak kolumna etykiety, sprawdź, czy zaznaczona jest prawa kolumna.  

 Jeśli zaznaczone są nieodpowiednie kolumny, usuń je i uruchom ponownie potok.  

|Komunikaty o wyjątkach|
|------------------------|
|Określony zestaw kolumn nie ma zastosowania do żadnej z kolumn zestawu danych.|
|Określony zestaw kolumn "{column_set}" nie ma zastosowania do żadnej z kolumn zestawu danych.|


## <a name="error-0012"></a>Błąd 0012  
 Wyjątek występuje, jeśli nie można utworzyć wystąpienia klasy z przekazanym zestawem argumentów.  

**Rozdzielczość:** Ten błąd nie jest możliwe do zasłaniania przez użytkownika i zostanie przestarzały w przyszłej wersji.  

|Komunikaty o wyjątkach|
|------------------------|
|Nieprzeszkolony model, proszę najpierw trenować model.|
|Nieprzeszkolony model ({arg_name}), użyj modelu przeszkolonego.|


## <a name="error-0013"></a>Błąd 0013  
 Wyjątek występuje, jeśli uczeń przekazany do modułu jest nieprawidłowy typ.  

 Ten błąd występuje zawsze, gdy przeszkolony model jest niezgodny z podłączonym modułem oceniania. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Rozdzielczość:**

Określ typ ucznia, który jest produkowany przez moduł szkolenia i określić moduł punktacji, który jest odpowiedni dla ucznia. 

Jeśli model został przeszkolony przy użyciu dowolnego z wyspecjalizowanych modułów szkoleniowych, podłącz przeszkolony model tylko do odpowiedniego modułu punktacji specjalistycznej. 


|Typ modelu|Moduł szkoleniowy| Moduł punktacji|
|----|----|----|
|każdy klasyfikator|[Train Model (Trenuj model)](train-model.md) |[Klasyfikacja modelu](score-model.md)|
|dowolny model regresji|[Train Model (Trenuj model)](train-model.md) |[Klasyfikacja modelu](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Komunikaty o wyjątkach|
|------------------------|
|Uczący się nieprawidłowego typu jest przekazywany.|
|Uczący się "{arg_name}" ma nieprawidłowy typ.|
|Uczący się "{arg_name}" ma nieprawidłowy typ "{learner_type}".|


## <a name="error-0014"></a>Błąd 0014  
 Wyjątek występuje, jeśli liczba unikatowych wartości kolumny jest większa niż dozwolona.  

 Ten błąd występuje, gdy kolumna zawiera zbyt wiele unikatowych wartości.  Na przykład ten błąd może zostać wyświetlony, jeśli określisz, że kolumna będzie obsługiwana jako dane kategoryczne, ale w kolumnie jest zbyt wiele unikatowych wartości, aby umożliwić ukończenie przetwarzania. Ten błąd może również zostać wyświetlony, jeśli istnieje niezgodność między liczbą unikatowych wartości w dwóch danych wejściowych.   

**Rozdzielczość:**

Otwórz moduł, który wygenerował błąd i zidentyfikuj kolumny używane jako dane wejściowe. W przypadku niektórych modułów można kliknąć prawym przyciskiem myszy dane wejściowe zestawu danych i wybrać **opcję Wizualizuj,** aby uzyskać statystyki poszczególnych kolumn, w tym liczbę unikatowych wartości i ich dystrybucję.

W przypadku kolumn, które mają być używane do grupowania lub kategoryzacji, należy podjąć kroki w celu zmniejszenia liczby unikatowych wartości w kolumnach. Można zmniejszyć na różne sposoby, w zależności od typu danych kolumny. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Nie możesz znaleźć rozwiązania odpowiadającego twojemu scenariuszowi? Można przekazać opinię na ten temat, który zawiera nazwę modułu, który wygenerował błąd i typ danych i kardynalność kolumny. Użyjemy tych informacji, aby zapewnić bardziej ukierunkowane kroki rozwiązywania problemów dla typowych scenariuszy.   

|Komunikaty o wyjątkach|
|------------------------|
|Ilość unikatowych wartości kolumny jest większa niż dozwolona.|
|Liczba unikatowych wartości w kolumnie: "{column_name}" jest większa niż dozwolona.|
|Liczba unikatowych wartości w kolumnie: "{column_name}" przekracza liczbę krotek {limitation}.|


## <a name="error-0015"></a>Błąd 0015  
 Wyjątek występuje, jeśli połączenie z bazą danych nie powiodło się.  

 Ten błąd zostanie wyświetlony po wprowadzeniu niepoprawnej nazwy konta SQL, hasła, serwera bazy danych lub nazwy bazy danych lub jeśli nie można ustanowić połączenia z bazą danych z powodu problemów z bazą danych lub serwerem.  

**Rozdzielczość:** Sprawdź, czy nazwa konta, hasło, serwer bazy danych i baza danych zostały wprowadzone poprawnie i czy określone konto ma poprawny poziom uprawnień. Sprawdź, czy baza danych jest aktualnie dostępna.  

|Komunikaty o wyjątkach|
|------------------------|
|Błąd podczas nawiązywowego połączenia z bazą danych.|
|Błąd podczas nawiązywowego połączenia z bazą danych: {connection_str}.|


## <a name="error-0016"></a>Błąd 0016  
 Wyjątek występuje, jeśli wejściowe zestawy danych przekazywane do modułu powinny mieć zgodne typy kolumn, ale nie.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli typy kolumn przekazanych w dwóch lub więcej zestawów danych nie są ze sobą zgodne.  

**Rozdzielczość:** Użyj [funkcji Edytuj metadane](edit-metadata.md) lub zmodyfikuj oryginalny wejściowy zestaw danych<!--, or use [Convert to Dataset](convert-to-dataset.md)--> aby upewnić się, że typy kolumn są zgodne.  

|Komunikaty o wyjątkach|
|------------------------|
|Kolumny z odpowiednim indeksem w wejściowych zestawach danych mają niezgodne typy.|
|Kolumny "{first_col_names}" są niezgodne między danymi pociągu i testu.|
|Kolumny "{first_col_names}" i "{second_col_names}" są niezgodne.|
|Typy elementów kolumny nie są zgodne z kolumną "{first_col_names}" (zeronalicznie) wejściowych zestawów danych ({first_dataset_names} i {second_dataset_names}).|


## <a name="error-0017"></a>Błąd 0017  
 Wyjątek występuje, jeśli wybrana kolumna używa typu danych, który nie jest obsługiwany przez bieżący moduł.  

 Na przykład ten błąd może pojawić się w usłudze Azure Machine Learning, jeśli wybór kolumny zawiera kolumnę z typem danych, który nie może być przetwarzany przez moduł, na przykład kolumnę ciągu dla operacji matematycznej lub kolumnę wyników, w której wymagana jest kolumna funkcji kategorii.  

**Rozdzielczość:**
 1. Zidentyfikuj kolumnę, która jest problemem.
 2. Przejrzyj wymagania modułu.
 3. Zmodyfikuj kolumnę, aby była zgodna z wymaganiami. Może być konieczne użycie kilku z następujących modułów do wprowadzania zmian, w zależności od kolumny i konwersji, którą próbujesz:
    + Użyj [funkcji Edytuj metadane,](edit-metadata.md) aby zmienić typ danych kolumn lub zmienić użycie kolumny z funkcji na numeryczną, kategoryczną na nieekromniczą itd.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. W ostateczności może być konieczne zmodyfikowanie oryginalnego zestawu danych wejściowych.

> [!TIP]
> Nie możesz znaleźć rozwiązania odpowiadającego twojemu scenariuszowi? Można przekazać opinię na ten temat, który zawiera nazwę modułu, który wygenerował błąd i typ danych i kardynalność kolumny. Użyjemy tych informacji, aby zapewnić bardziej ukierunkowane kroki rozwiązywania problemów dla typowych scenariuszy. 

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przetworzyć kolumny bieżącego typu. Typ nie jest obsługiwany przez moduł.|
|Nie można przetworzyć kolumny typu {col_type}. Typ nie jest obsługiwany przez moduł.|
|Nie można przetworzyć kolumny "{col_name}" typu {col_type}. Typ nie jest obsługiwany przez moduł.|
|Nie można przetworzyć kolumny "{col_name}" typu {col_type}. Typ nie jest obsługiwany przez moduł. Nazwa parametru: {arg_name}.|


## <a name="error-0018"></a>Błąd 0018  
 Wyjątek występuje, jeśli wejściowy zestaw danych jest nieprawidłowy.  

**Rozdzielczość:** Ten błąd w usłudze Azure Machine Learning może pojawić się w wielu kontekstach, więc nie ma jednej rozdzielczości. Ogólnie rzecz biorąc błąd wskazuje, że dane dostarczone jako dane wejściowe do modułu ma niewłaściwą liczbę kolumn lub że typ danych nie odpowiada wymaganiom modułu. Przykład:  

-   Moduł wymaga kolumny etykiety, ale żadna kolumna nie jest oznaczona jako etykieta lub nie zaznaczono jeszcze kolumny etykiety.  
  
-   Moduł wymaga, aby dane były kategoryczne, ale dane są liczbowe.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Dane są w niewłaściwym formacie.  
  
-   Importowane dane zawierają nieprawidłowe znaki, nieprawidłowe wartości lub wartości poza zakresem.  
-   Kolumna jest pusta lub zawiera zbyt wiele brakujących wartości.  

 Aby określić wymagania i jak dane mogą, przejrzyj temat pomocy dla modułu, który będzie zużywał zestaw danych jako dane wejściowe.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Komunikaty o wyjątkach|
|------------------------|
|Zestaw danych jest nieprawidłowy.|
|{dataset1} zawiera nieprawidłowe dane.|
|{dataset1} i {dataset2} powinny być spójne w kolumnie.|
|{dataset1} zawiera nieprawidłowe dane, {przyczyna}.|
|{dataset1} zawiera {invalid_data_category}. {troubleshoot_hint}|
|{dataset1} jest nieprawidłowy, {przyczyna}. {troubleshoot_hint}|


## <a name="error-0019"></a>Błąd 0019  
 Wyjątek występuje, jeśli kolumna ma zawierać posortowane wartości, ale tak nie jest.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli określone wartości kolumn są niesa bez kolejności.  

**Rozdzielczość:** Sortuj wartości kolumn, ręcznie modyfikując wejściowy zestaw danych i uruchom ponownie moduł.  

|Komunikaty o wyjątkach|
|------------------------|
|Wartości w kolumnie nie są sortowane.|
|Wartości w kolumnie "{col_index}" nie są sortowane.|
|Wartości w kolumnie "{col_index}" zestawu danych "{dataset}" nie są sortowane.|


## <a name="error-0020"></a>Błąd 0020  
 Wyjątek występuje, jeśli liczba kolumn w niektórych zestawów danych przekazanych do modułu jest zbyt mała.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli dla modułu wybrano za mało kolumn.  

**Rozdzielczość:** Ponownie moduł i upewnij się, że selektor kolumn ma poprawną liczbę kolumn zaznaczone.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba kolumn w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna.|
|Liczba kolumn w wejściowym zestawie danych "{arg_name}" jest mniejsza niż dozwolone minimum.|
|Liczba kolumn w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna kolumna {required_columns_count}.|
|Liczba kolumn w wejściowym zestawie danych "{arg_name}" jest mniejsza niż dozwolona minimalna kolumna {required_columns_count}.|


## <a name="error-0021"></a>Błąd 0021  
 Wyjątek występuje, jeśli liczba wierszy w niektórych zestawów danych przekazanych do modułu jest zbyt mała.  

 Ten błąd w usłudze Azure Machine Learning, gdy nie ma wystarczającej liczby wierszy w zestawie danych do wykonania określonej operacji. Na przykład ten błąd może zostać wyświetlony, jeśli wejściowy zestaw danych jest pusty lub jeśli próbujesz wykonać operację, która wymaga minimalnej liczby wierszy, aby były prawidłowe. Takie operacje mogą obejmować (ale nie są ograniczone do) grupowanie lub klasyfikacja na podstawie metod statystycznych, niektóre rodzaje binning i uczenia się z liczebnie.  

**Rozdzielczość:**

 + Otwórz moduł, który zwrócił błąd i sprawdź wejściowy zestaw danych i właściwości modułu. 
 + Sprawdź, czy wejściowy zestaw danych nie jest pusty i czy istnieje wystarczająca liczba wierszy danych, aby spełnić wymagania opisane w pomocy modułu.  
 + Jeśli dane są ładowane ze źródła zewnętrznego, upewnij się, że źródło danych jest dostępne i że nie ma żadnego błędu lub zmiany w definicji danych, które mogłyby spowodować proces importowania, aby uzyskać mniej wierszy.
 + Jeśli wykonujesz operację na danych przed modułem, która może mieć wpływ na typ danych lub liczbę wartości, takich jak czyszczenie, dzielenie lub łączenie operacji, sprawdź dane wyjściowe tych operacji, aby określić liczbę zwróconych wierszy.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba wierszy w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna.|
|Liczba wierszy w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna liczba wierszy {required_rows_count}.|
|Liczba wierszy w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna liczba wierszy {required_rows_count}. {powód}|
|Liczba wierszy w wejściowym zestawie danych "{arg_name}" jest mniejsza niż dozwolona minimalna liczba wierszy {required_rows_count}.|
|Liczba wierszy w wejściowym zestawie danych "{arg_name}" to {actual_rows_count}, mniej niż dozwolone minimum wierszy {required_rows_count}.|
|Liczba wierszy "{row_type}" w wejściowym zestawie danych "{arg_name}" to {actual_rows_count}, mniej niż dozwolone minimum wierszy {required_rows_count}.|


## <a name="error-0022"></a>Błąd 0022  
 Wyjątek występuje, jeśli liczba wybranych kolumn w wejściowym zestawie danych nie jest równa oczekiwanej liczbie.  

 Ten błąd w usłudze Azure Machine Learning może wystąpić, gdy moduł podrzędny lub operacja wymaga określonej liczby kolumn lub danych wejściowych, a podano zbyt mało lub zbyt wiele kolumn lub danych wejściowych. Przykład:  

-   Określ pojedynczą kolumnę etykiety lub kolumnę klucza i przypadkowo zaznaczono wiele kolumn.  
  
-   Zmieniasz nazwę kolumn, ale podana jest więcej lub mniej nazw niż są kolumny.  
  
-   Liczba kolumn w źródle lub miejscu docelowym uległa zmianie lub nie odpowiada liczbie kolumn używanych przez moduł.  
  
-   Podano listę wartości dla danych wejściowych oddzielonych przecinkami, ale liczba wartości nie jest zgodna lub wiele danych wejściowych nie jest obsługiwanych.  

**Rozdzielczość:** Ponownie sprawdź moduł i sprawdź wybór kolumny, aby upewnić się, że wybrana jest prawidłowa liczba kolumn. Sprawdź dane wyjściowe modułów nadrzędnych i wymagania operacji podrzędnych.  

 Jeśli użyto jednej z opcji wyboru kolumny, która może wybrać wiele kolumn (indeksy kolumn, wszystkie operacje, wszystkie numeryczne itp.), sprawdź poprawność dokładnej liczby kolumn zwróconych przez zaznaczenie.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Sprawdź, czy liczba lub typ kolumn nadrzędnych nie uległa zmianie.  

 Jeśli używasz zestawu danych rekomendacji do szkolenia modelu, należy pamiętać, że polecający oczekuje ograniczonej liczby kolumn, odpowiadającej parom elementu użytkownika lub rankingom elementu użytkownika. Usuń dodatkowe kolumny przed szkoleniem modelu lub dzieleniem zestawów danych rekomendacji. Aby uzyskać więcej informacji, zobacz [Dzielenie danych](split-data.md).  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba wybranych kolumn w wejściowym zestawie danych nie jest równa oczekiwanej liczbie.|
|Liczba wybranych kolumn w wejściowym zestawie danych nie jest równa {expected_col_count}.|
|Wzorzec zaznaczenia kolumny "{selection_pattern_friendly_name}" zawiera liczbę wybranych kolumn w wejściowym zestawie danych, która nie jest równa {expected_col_count}.|
|Wzorzec zaznaczenia kolumny "{selection_pattern_friendly_name}" ma zapewnić kolumny {expected_col_count} wybrane w wejściowym zestawie danych, ale kolumny {selected_col_count} są faktycznie dostarczane.|


## <a name="error-0023"></a>Błąd 0023  

Wyjątek występuje, jeśli kolumna docelowa wejściowego zestawu danych nie jest prawidłowa dla bieżącego modułu trenera.  

Ten błąd w usłudze Azure Machine Learning występuje, jeśli kolumna docelowa (zgodnie z wybraną w parametrach modułu) nie jest prawidłowym typem danych, zawiera wszystkie brakujące wartości lub nie została sklasyfikowana zgodnie z oczekiwaniami.  

**Rozdzielczość:** Ponownie wprowadź moduł, aby sprawdzić zawartość kolumny etykiety/docelowej. Upewnij się, że nie ma wszystkich brakujących wartości. Jeśli moduł oczekuje, że kolumna docelowa będzie kategoryczna, upewnij się, że w kolumnie docelowej znajduje się więcej niż jedna odrębna wartość.  

|Komunikaty o wyjątkach|
|------------------------|
|Wejściowy zestaw danych ma nieobsługiwałą kolumnę docelową.|
|Wejściowy zestaw danych ma nieobsługiwałą kolumnę docelową "{column_index}".|
|Wejściowy zestaw danych ma nieobsługiwałą kolumnę docelową "{column_index}" dla ucznia typu {learner_type}.|


## <a name="error-0024"></a>Błąd 0024  
Wyjątek występuje, jeśli zestaw danych nie zawiera kolumny etykiety.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy moduł wymaga kolumny etykiety, a zestaw danych nie ma kolumny etykiet. Na przykład ocena ocenionego zestawu danych zwykle wymaga, aby kolumna etykiety była obecna w celu obliczenia metryk dokładności.  

Może się również zdarzyć, że kolumna etykiety jest obecny w zestawie danych, ale nie wykryto poprawnie przez usługę Azure Machine Learning.

**Rozdzielczość:**

+ Otwórz moduł, który wygenerował błąd i określ, czy kolumna etykiety jest obecna. Nazwa lub typ danych kolumny nie ma znaczenia, tak długo, jak kolumna zawiera pojedynczy wynik (lub zmienną zależną), który próbujesz przewidzieć. Jeśli nie masz pewności, która kolumna ma etykietę, poszukaj nazwy rodzajowej, takiej jak *Klasa* lub *Obiekt docelowy.* 
+  Jeśli zestaw danych nie zawiera kolumny etykiety, jest możliwe, że kolumna etykiety została jawnie lub przypadkowo usunięta w górę. Może być również, że zestaw danych nie jest dane wyjściowe modułu oceniania nadrzędnego.
+ Aby jawnie oznaczyć kolumnę jako kolumnę etykiety, dodaj moduł [Edytuj metadane](edit-metadata.md) i połącz zestaw danych. Zaznacz tylko kolumnę etykiety i wybierz **pozycję Etykieta** z listy rozwijanej **Pola.** 
+ Jeśli jako etykietę wybrano niewłaściwą kolumnę, możesz wybrać **wyczyść etykietę** z **pól,** aby naprawić metadane w kolumnie. 
  
|Komunikaty o wyjątkach|
|------------------------|
|W zestawie danych nie ma kolumny etykiet.|
|W "{dataset_name}" nie ma kolumny etykiety.|


## <a name="error-0025"></a>Błąd 0025  
 Wyjątek występuje, jeśli zestaw danych nie zawiera kolumny wyników.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli dane wejściowe do modelu oceny nie zawiera prawidłowe kolumny wynik. Na przykład użytkownik próbuje ocenić zestaw danych, zanim został oceniony przy pomocą poprawnego modelu przeszkolonego lub kolumna wyników została jawnie upstream. Ten wyjątek występuje również wtedy, gdy kolumny wyników na dwóch zestawach danych są niezgodne. Na przykład może być próba porównania dokładności regresora liniowego z klasyfikatorem binarnym.  

**Rozdzielczość:** Ponownie dane wejściowe do modelu oceny i sprawdzić, czy zawiera jedną lub więcej kolumn wynik. Jeśli nie, zestaw danych nie został oceniony lub kolumny wyników zostały usunięte w module nadrzędnym.  

|Komunikaty o wyjątkach|
|------------------------|
|W zestawie danych nie ma kolumny wyników.|
|W "{dataset_name}"nie ma kolumny wyników.|
|W "{dataset_name}" nie ma kolumny wyników, która jest wywoływana przez "{learner_type}". Ocena zestawu danych przy użyciu prawidłowego typu ucznia.|


## <a name="error-0026"></a>Błąd 0026  
 Wyjątek występuje, jeśli kolumny o tej samej nazwie nie są dozwolone.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli wiele kolumn ma taką samą nazwę. Jednym ze sposobów, w jaki może pojawić się ten błąd, jest to, że zestaw danych nie ma nazwy wiersza nagłówka i kolumny są automatycznie przypisywane: Col0, Col1 itp.  

**Rozdzielczość:** Jeśli kolumny mają taką samą nazwę, wstaw moduł [Edytuj metadane](edit-metadata.md) między wejściowym zestawem danych a modułem. Użyj selektora kolumn w [obszarze Edytowanie metadanych,](edit-metadata.md) aby wybrać kolumny do zmiany nazwy, wpisując nowe nazwy w polu tekstowym **Nowe nazwy kolumn.**  

|Komunikaty o wyjątkach|
|------------------------|
|Równe nazwy kolumn są określone w argumentach. Równe nazwy kolumn nie są dozwolone przez moduł.|
|Równe nazwy kolumn w argumentach "{arg_name_1}" i "{arg_name_2}" są niedozwolone. Podaj różne nazwy.|


## <a name="error-0027"></a>Błąd 0027  
 Wyjątek występuje w przypadku, gdy dwa obiekty muszą być tego samego rozmiaru, ale nie są.  

 Jest to typowy błąd w usłudze Azure Machine Learning i może być spowodowane przez wiele warunków.  

**Rozdzielczość:** Nie ma konkretnej rozdzielczości. Można jednak sprawdzić, czy warunki, takie jak:  

-   Jeśli zmieniasz nazwę kolumn, upewnij się, że każda lista (kolumny wejściowe i lista nowych nazw) ma taką samą liczbę elementów.  
  
-   Jeśli łączysz lub łączysz dwa zestawy danych, upewnij się, że mają ten sam schemat.  
  
-   Jeśli łączysz dwa zestawy danych, które mają wiele kolumn, upewnij się, że kolumny kluczy mają ten sam typ danych, i wybierz opcję **Zezwalaj na duplikaty i zachowaj kolejność kolumn w zaznaczeniu**.  

|Komunikaty o wyjątkach|
|------------------------|
|Rozmiar przekazanych obiektów jest niespójny.|
|Rozmiar "{friendly_name1}" jest niezgodny z rozmiarem "{friendly_name2}".|


## <a name="error-0028"></a>Błąd 0028  
 Wyjątek występuje w przypadku, gdy zestaw kolumn zawiera zduplikowane nazwy kolumn i nie jest dozwolone.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy nazwy kolumn są duplikowane; to znaczy, nie jest wyjątkowy.  

**Rozdzielczość:** Jeśli którekolwiek kolumny mają taką samą nazwę, dodaj [wystąpienie Edytuj metadane](edit-metadata.md) między wejściowym zestawem danych a modułem wywołującym błąd. Użyj selektora kolumn w [obszarze Edytowanie metadanych,](edit-metadata.md) aby wybrać kolumny do zmiany nazwy, i wpisz nowe nazwy kolumn w polu tekstowym **Nowe nazwy kolumn.** Jeśli zmieniasz nazwę wielu kolumn, upewnij się, że wartości wpisywanie nazw **kolumn są** unikatowe.  

|Komunikaty o wyjątkach|
|------------------------|
|Zestaw kolumn zawiera zduplikowane nazwy kolumn.|
|Nazwa "{duplicated_name}" jest duplikowana.|
|Nazwa "{duplicated_name}" jest duplikowana w "{arg_name}".|
|Nazwa "{duplicated_name}" jest duplikowana. Szczegóły: {szczegóły}|


## <a name="error-0029"></a>Błąd 0029  
 Wyjątek występuje w przypadku, gdy nieprawidłowy identyfikator URI jest przekazywana.  

 Ten błąd w usłudze Azure Machine Learning występuje w przypadku, gdy nieprawidłowy identyfikator URI jest przekazywany.  Ten błąd zostanie wyświetlony, jeśli spełniony jest którykolwiek z poniższych warunków:  

-   Identyfikator URI publicznych lub sygnatury dostępu Współdzielonego dla usługi Azure Blob Storage do odczytu lub zapisu zawiera błąd.  
  
-   Okno czasu dla sygnatury dostępu Współdzielonego wygasło.  
  
-   Adres URL sieci Web za pośrednictwem źródła HTTP reprezentuje plik lub identyfikator URI sprzężenia zwrotnego.  
  
-   Adres URL sieci Web za pośrednictwem protokołu HTTP zawiera niepoprawnie sformatowany adres URL.  
  
-   Adres URL nie może zostać rozpoznany przez źródło zdalne.  

**Rozdzielczość:** Ponownie moduł i sprawdź format identyfikatora URI. Jeśli źródłem danych jest adres URL sieci Web za pośrednictwem protokołu HTTP, sprawdź, czy zamierzone źródło nie jest plikiem ani identyfikatorem URI sprzężenia zwrotnego (localhost).  

|Komunikaty o wyjątkach|
|------------------------|
|Nieprawidłowy identyfikator Uri jest przekazywany.|
|Identyfikator Uri "{invalid_url}" jest nieprawidłowy.|


## <a name="error-0030"></a>Błąd 0030  
 Wyjątek występuje w przypadku, gdy nie jest możliwe pobranie pliku.  

 Ten wyjątek w usłudze Azure Machine Learning występuje, gdy nie jest możliwe pobranie pliku. Ten wyjątek zostanie wyświetlony, gdy próba odczytu ze źródła HTTP nie powiodła się po trzech (3) próbach ponowienia próby.  

**Rozdzielczość:** Sprawdź, czy identyfikator URI do źródła HTTP jest poprawny i czy witryna jest obecnie dostępna za pośrednictwem Internetu.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można pobrać pliku.|
|Błąd podczas pobierania pliku: {file_url}.|


## <a name="error-0031"></a>Błąd 0031  
 Wyjątek występuje, jeśli liczba kolumn w zestawie kolumn jest mniejsza niż jest to wymagane.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli liczba wybranych kolumn jest mniejsza niż jest to wymagane.  Ten błąd zostanie wyświetlony, jeśli minimalna wymagana liczba kolumn nie jest zaznaczona.  

**Rozdzielczość:** Dodawanie dodatkowych kolumn do zaznaczenia kolumny za pomocą **selektora kolumn**.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba kolumn w zestawie kolumn jest mniejsza niż wymagana.|
|Dla argumentu wejściowego "{arg_name}" należy określić co najmniej kolumny {required_columns_count}.|
|Dla argumentu wejściowego "{arg_name}" należy określić co najmniej kolumny {required_columns_count}. Rzeczywista liczba określonych kolumn to {input_columns_count}.|


## <a name="error-0032"></a>Błąd 0032  
 Wyjątek występuje, jeśli argument nie jest liczbą.  

 Ten błąd zostanie wyświetlony w usłudze Azure Machine Learning, jeśli argument jest double lub NaN.  

**Rozdzielczość:** Zmodyfikuj określony argument, aby użyć prawidłowej wartości.  

|Komunikaty o wyjątkach|
|------------------------|
|Argument nie jest liczbą.|
|"{arg_name}" nie jest liczbą.|


## <a name="error-0033"></a>Błąd 0033  
 Wyjątek występuje, jeśli argument jest Nieskończoność.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli argument jest nieskończony. Ten błąd zostanie wyświetlony, jeśli `double.NegativeInfinity` argument `double.PositiveInfinity`jest albo lub .  

**Rozdzielczość:** Zmodyfikuj określony argument jako prawidłową wartość.  

|Komunikaty o wyjątkach|
|------------------------|
|Argument musi być skończony.|
|"{arg_name}" nie jest skończony.|


## <a name="error-0034"></a>Błąd 0034  
 Wyjątek występuje, jeśli istnieje więcej niż jedna ocena dla danej pary elementu użytkownika.  

 Ten błąd w usłudze Azure Machine Learning występuje w zaleceniu, jeśli para elementu użytkownika ma więcej niż jedną wartość klasyfikacji.  

**Rozdzielczość:** Upewnij się, że para elementu użytkownika ma tylko jedną wartość klasyfikacji.  

|Komunikaty o wyjątkach|
|------------------------|
|Istnieje więcej niż jedna ocena wartości w zestawie danych.|
|Więcej niż jedna ocena dla użytkownika {użytkownik} i element {element} w tabeli danych prognozowania klasyfikacji.|
|Więcej niż jedna ocena dla użytkownika {użytkownik} i element {element} w {dataset}.|


## <a name="error-0035"></a>Błąd 0035  
 Wyjątek występuje, jeśli nie zostały podane żadne funkcje dla danego użytkownika lub elementu.  

 Ten błąd w usłudze Azure Machine Learning występuje próbujesz użyć modelu rekomendacji do oceniania, ale nie można odnaleźć wektora funkcji.  

**Rozdzielczość:**

Polecający matchbox ma pewne wymagania, które muszą być spełnione podczas korzystania z funkcji elementu lub funkcji użytkownika.  Ten błąd wskazuje, że wektor funkcji brakuje dla użytkownika lub elementu, który został podany jako dane wejściowe. Upewnij się, że wektor funkcji jest dostępny w danych dla każdego użytkownika lub elementu.  

 Na przykład jeśli przeszkoliłeś model rekomendacji przy użyciu funkcji, takich jak wiek, lokalizacja lub dochód użytkownika, ale teraz chcesz utworzyć wyniki dla nowych użytkowników, którzy nie byli widoczni podczas szkolenia, musisz podać równoważny zestaw funkcji (a mianowicie wiek, lokalizację i wartości dochodów) dla nowych użytkowników, aby zapewnić odpowiednie prognozy dla nich. 

 Jeśli nie masz żadnych funkcji dla tych użytkowników, należy wziąć pod uwagę inżynierii funkcji do generowania odpowiednich funkcji.  Na przykład jeśli nie masz indywidualnych wartości wieku lub dochodu użytkownika, możesz wygenerować przybliżone wartości do użycia dla grupy użytkowników. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Rozwiązanie nie ma zastosowania do sprawy? Możesz wysłać opinię na temat tego artykułu i podać informacje o scenariuszu, w tym moduł i liczbę wierszy w kolumnie. Wykorzystamy te informacje, aby zapewnić bardziej szczegółowe kroki rozwiązywania problemów w przyszłości.

|Komunikaty o wyjątkach|
|------------------------|
|Nie udostępniono żadnych funkcji dla wymaganego użytkownika lub elementu.|
|Funkcje dla {required_feature_name} wymagane, ale nie pod warunkiem.|


## <a name="error-0036"></a>Błąd 0036  
 Wyjątek występuje, jeśli dla danego użytkownika lub elementu podano wiele wektorów funkcji.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli wektor funkcji jest zdefiniowany więcej niż jeden raz.  

**Rozdzielczość:** Upewnij się, że wektor operacji nie jest zdefiniowany więcej niż jeden raz.  

|Komunikaty o wyjątkach|
|------------------------|
|Zduplikowana definicja funkcji dla użytkownika lub elementu.|


## <a name="error-0037"></a>Błąd 0037  
 Wyjątek występuje, jeśli określono wiele kolumn etykiet i tylko jedna jest dozwolona.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli więcej niż jedna kolumna jest zaznaczona jako nowa kolumna etykiety. Większość nadzorowanych algorytmów uczenia się wymaga, aby pojedyncza kolumna była oznaczona jako cel lub etykieta.  

**Rozdzielczość:** Pamiętaj, aby wybrać pojedynczą kolumnę jako nową kolumnę etykiety.  

|Komunikaty o wyjątkach|
|------------------------|
|Określono wiele kolumn etykiet.|
|W polu "{dataset_name}" określono wiele kolumn etykiet.|


## <a name="error-0039"></a>Błąd 0039  
 Wyjątek występuje, jeśli operacja nie powiodła się.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy nie można ukończyć operacji wewnętrznej.  

**Rozdzielczość:** Ten błąd jest spowodowany przez wiele warunków i nie ma konkretnego środka zaradczego.  
 Poniższa tabela zawiera ogólne komunikaty dotyczące tego błędu, po których następuje określony opis warunku. 

 Jeśli nie są dostępne żadne szczegóły, [wyślij opinię](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) i podaj informacje o modułach, które wygenerowały błąd i związane z nimi warunki.

|Komunikaty o wyjątkach|
|------------------------|
|Operacja nie powiodła się.|
|Błąd podczas wykonywania operacji: "{failed_operation}".|
|Błąd podczas wykonywania operacji: "{failed_operation}". Powód: "{reason}".|


## <a name="error-0042"></a>Błąd 0042  
 Wyjątek występuje, gdy nie jest możliwe do konwersji kolumny do innego typu.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy nie jest możliwe przekonwertowanie kolumny do określonego typu.  Ten błąd zostanie wyświetlony, jeśli moduł wymaga określonego typu danych, takich jak datetime, tekst, liczba zmiennoprzecinkowa lub liczba całkowita, ale nie jest możliwe przekonwertowanie istniejącej kolumny na wymagany typ.  

Na przykład można wybrać kolumnę i spróbować przekonwertować ją na typ danych liczbowych do użycia w operacji matematycznej i uzyskać ten błąd, jeśli kolumna zawierała nieprawidłowe dane. 

Innym powodem może pojawić się ten błąd, jeśli spróbujesz użyć kolumny zawierającej liczby zmiennoprzecinkowe lub wiele unikatowych wartości jako kolumny kategorycznej. 

**Rozdzielczość:**

+ Otwórz stronę pomocy dla modułu, który wygenerował błąd, i sprawdź wymagania dotyczące typu danych.
+ Przejrzyj typy danych kolumn w wejściowym zestawie danych.
+ Sprawdź dane pochodzące z tak zwanych źródeł danych bez schematu.
+ Sprawdź zestaw danych pod kątem brakujących wartości lub znaków specjalnych, które mogą blokować konwersję do żądanego typu danych. 
    + Typy danych liczbowych powinny być spójne: na przykład sprawdź liczby zmiennoprzecinkowe w kolumnie liczb całkowitych.
    + Poszukaj ciągów tekstowych lub wartości NA w kolumnie liczbowej. 
    + Wartości logiczne można przekonwertować na odpowiednią reprezentację w zależności od wymaganego typu danych.
    + Sprawdzanie kolumn tekstowych pod kątem znaków innych niż unicode, znaków tabulacji lub znaków kontrolnych
    + Dane Datetime powinny być spójne, aby uniknąć błędów modelowania, ale oczyszczanie może być skomplikowane ze względu na wiele formatów. Rozważ użycie <!--the [Execute R Script](execute-r-script.md) or -->Wykonaj moduły [skryptu Języka Python,](execute-python-script.md) aby wykonać oczyszczanie.  
+ W razie potrzeby zmodyfikuj wartości w wejściowym zestawie danych, tak aby kolumna mogła zostać pomyślnie przekonwertowana. Modyfikacja może obejmować operacje binningu, obcinania lub zaokrąglania, eliminację wartości odstających lub przypisanie brakujących wartości. Zobacz następujące artykuły dla niektórych typowych scenariuszy transformacji danych w uczeniu maszynowym:
    + [Czyszczenie brakujących danych](clean-missing-data.md)
    + [Normalizowanie danych](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Rozwiązanie niejasne, czy nie ma zastosowania do twojej sprawy? Możesz wysłać opinię na temat tego artykułu i podać informacje o scenariuszu, w tym moduł i typ danych kolumny. Wykorzystamy te informacje, aby zapewnić bardziej szczegółowe kroki rozwiązywania problemów w przyszłości.  

|Komunikaty o wyjątkach|
|------------------------|
|Niedozwolona konwersja.|
|Nie można przekonwertować kolumny typu {type1} na kolumnę typu {type2}.|
|Nie można przekonwertować kolumny "{col_name1}" typu {type1} na kolumnę typu {type2}.|
|Nie można przekonwertować kolumny "{col_name1}" typu {type1} na kolumnę "{col_name2}" typu {type2}.|


## <a name="error-0044"></a>Błąd 0044  
 Wyjątek występuje, gdy nie jest możliwe wyprowadzenie typu elementu kolumny z istniejących wartości.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy nie jest możliwe wywnioskowanie typu kolumny lub kolumn w zestawie danych. Zazwyczaj dzieje się tak podczas łączenia dwóch lub więcej zestawów danych z różnymi typami elementów. Jeśli usługa Azure Machine Learning nie jest w stanie określić typ wspólnego, który jest w stanie reprezentować wszystkie wartości w kolumnie lub kolumnach bez utraty informacji, wygeneruje ten błąd.  

**Rozdzielczość:** Upewnij się, że wszystkie wartości w danej kolumnie w obu zestawach danych są połączone są tego samego typu (numeryczne, logiczne, kategoryczne, ciąg, data itp.) lub mogą być wymuszane na tym samym typie.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można wyprowadzić typu elementu kolumny.|
|Nie można wyprowadzić typu elementu dla kolumny "{column_name}" -- wszystkie elementy są odwołaniami null.|
|Nie można wyprowadzić typu elementu dla kolumny "{column_name}" zestawu danych "{dataset_name}" -- wszystkie elementy są odwołaniami zerowymi.|


## <a name="error-0045"></a>Błąd 0045  
 Wyjątek występuje, gdy nie jest możliwe, aby utworzyć kolumnę z powodu mieszanych typów elementów w źródle.  

 Ten błąd w usłudze Azure Machine Learning jest produkowany, gdy typy elementów dwóch zestawów danych są łączone są różne.  

**Rozdzielczość:** Upewnij się, że wszystkie wartości w danej kolumnie w obu zestawach danych są połączone są tego samego typu (numeryczne, logiczne, kategoryczne, ciąg, data itp.).  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można utworzyć kolumny z mieszanymi typami elementów.|
|Nie można utworzyć kolumny o identyfikatorze "{column_id}" typów elementów mieszanych:<br />Typ danych[{row_1}, {column_id}] to "{type_1}". <br />Typ danych[{row_2}, {column_id}] to "{type_2}".|
|Nie można utworzyć kolumny o identyfikatorze "{column_id}" typów elementów mieszanych:<br />Wpisz fragment {chunk_id_1} to "{type_1}". <br />Wpisz fragment {chunk_id_2} to "{type_2}" o rozmiarze fragmentu: {chunk_size}.|


## <a name="error-0046"></a>Błąd 0046  
 Wyjątek występuje, gdy nie jest możliwe utworzenie katalogu na określonej ścieżce.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy nie jest możliwe utworzenie katalogu na określonej ścieżce. Ten błąd zostanie wyświetlony, jeśli dowolna część ścieżki do katalogu wyjściowego dla zapytania hive jest niepoprawna lub niedostępna.  

**Rozdzielczość:** Ponownie moduł i sprawdź, czy ścieżka katalogu jest poprawnie sformatowany i czy jest dostępny z bieżących poświadczeń.  

|Komunikaty o wyjątkach|
|------------------------|
|Określ prawidłowy katalog wyjściowy.|
|Katalog: {path} nie można utworzyć. Określ prawidłową ścieżkę.|


## <a name="error-0047"></a>Błąd 0047  
 Wyjątek występuje, jeśli liczba kolumn funkcji w niektórych zestawów danych przekazanych do modułu jest zbyt mała.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli wejściowy zestaw danych do szkolenia nie zawiera minimalnej liczby kolumn wymaganych przez algorytm. Zazwyczaj zestaw danych jest pusty lub zawiera tylko kolumny szkoleniowe.  

**Rozdzielczość:** Ponownie wejściowy zestaw danych, aby upewnić się, że istnieje jedna lub więcej dodatkowych kolumn oprócz kolumny etykiety.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba kolumn obiektów w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna.|
|Liczba kolumn obiektów w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna kolumna {required_columns_count}.|
|Liczba kolumn obiektów w wejściowym zestawie danych "{arg_name}" jest mniejsza niż dozwolona minimalna kolumna {required_columns_count}.|


## <a name="error-0048"></a>Błąd 0048  
 Wyjątek występuje w przypadku, gdy nie jest możliwe otwarcie pliku.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy nie jest możliwe otwarcie pliku do odczytu lub zapisu. Ten błąd może wystąpić z następujących powodów:  

-   Kontener lub plik (obiekt blob) nie istnieje  
  
-   Poziom dostępu do pliku lub kontenera nie umożliwia dostępu do pliku  
  
-   Plik jest zbyt duży, aby odczytać lub niewłaściwy format  

**Rozdzielczość:** Ponownie moduł i plik, który próbujesz odczytać.  

 Sprawdź, czy nazwy kontenera i pliku są poprawne.  

 Użyj klasycznego portalu platformy Azure lub narzędzia magazynu platformy Azure, aby sprawdzić, czy masz uprawnienia dostępu do pliku.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Komunikaty o wyjątkach|
|------------------------|
|Nie można otworzyć pliku.|
|Błąd podczas otwierania pliku: {file_name}.|
|Błąd podczas otwierania pliku: {file_name}. Komunikat o wyjątku magazynu: {wyjątek}.|


## <a name="error-0049"></a>Błąd 0049  
 Wyjątek występuje w przypadku, gdy nie jest możliwe do przeanalizowania pliku.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy nie jest możliwe przeanalizowanie pliku. Ten błąd zostanie wyświetlony, jeśli format pliku wybrany w module [Importuj dane](import-data.md) nie jest zgodny z rzeczywistym formatem pliku lub jeśli plik zawiera nierozpoznawalny znak.  

**Rozdzielczość:** Ponownie sprawdź moduł i popraw wybór formatu pliku, jeśli nie pasuje do formatu pliku. Jeśli to możliwe, sprawdź plik, aby potwierdzić, że nie zawiera żadnych nielegalnych znaków.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przeanalizować pliku.|
|Błąd podczas analizowania pliku {file_format}.|
|Błąd podczas analizowania pliku {file_format}: {file_name}.|
|Błąd podczas analizowania pliku {file_format}. Powód: {failure_reason}.|
|Błąd podczas analizowania pliku {file_format}: {file_name}. Powód: {failure_reason}.|


## <a name="error-0052"></a>Błąd 0052  
 Wyjątek występuje, jeśli klucz konta magazynu platformy Azure jest określony niepoprawnie.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli klucz używany do uzyskiwania dostępu do konta magazynu platformy Azure jest niepoprawny. Na przykład ten błąd może zostać wyświetlony, jeśli klucz magazynu platformy Azure został obcięty podczas kopiowania i wklejania lub jeśli użyto niewłaściwego klucza.  

 Aby uzyskać więcej informacji na temat uzyskiwania klucza dla konta magazynu platformy Azure, zobacz [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu do magazynu](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  

**Rozdzielczość:** Ponownie moduł i sprawdź, czy klucz magazynu platformy Azure jest poprawny dla konta; w razie potrzeby ponownie skopiuj klucz z klasycznego portalu platformy Azure.  

|Komunikaty o wyjątkach|
|------------------------|
|Klucz konta magazynu platformy Azure jest niepoprawny.|


## <a name="error-0053"></a>Błąd 0053  
 Wyjątek występuje w przypadku, gdy nie ma żadnych funkcji użytkownika lub elementów dla zaleceń matchbox.  

 Ten błąd w usłudze Azure Machine Learning jest produkowany, gdy nie można odnaleźć wektora funkcji.  

**Rozdzielczość:** Upewnij się, że wektor operacji jest obecny w wejściowym zestawie danych.  

|Komunikaty o wyjątkach|
|------------------------|
|Funkcje użytkownika i/i elementy są wymagane, ale nie są dostarczane.|


## <a name="error-0056"></a>Błąd 0056  
 Wyjątek występuje, jeśli kolumny wybrane dla operacji naruszają wymagania.  

 Ten błąd w usłudze Azure Machine Learning występuje podczas wybierania kolumn dla operacji, która wymaga kolumny być określonego typu danych. 

 Ten błąd może również wystąpić, jeśli kolumna jest poprawnym typem danych, ale używany moduł wymaga, aby kolumna była również oznaczona jako kolumna obiektowa, etykieta lub kolumna kategoryczna.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Rozdzielczość:**

1.  Przejrzyj typ danych kolumn, które są aktualnie zaznaczone. 

2. Sprawdź, czy wybrane kolumny są kolumnami kategorycznymi, etykietami czy kolumnami elementów.  
  
3.  Przejrzyj temat pomocy dla modułu, w którym dokonano wyboru kolumny, aby ustalić, czy istnieją określone wymagania dotyczące użycia typu danych lub kolumny.  
  
3.  Użyj [edytowania metadanych,](edit-metadata.md) aby zmienić typ kolumny na czas trwania tej operacji. Pamiętaj, aby zmienić typ kolumny z powrotem na oryginalną wartość, używając innego wystąpienia [Edytuj metadane](edit-metadata.md), jeśli jest to potrzebne do operacji podrzędnych.  

|Komunikaty o wyjątkach|
|------------------------|
|Co najmniej jedna wybrana kolumna nie znajdowała się w dozwolonej kategorii.|
|Kolumna o nazwie "{col_name}" nie należy do dozwolonej kategorii.|


## <a name="error-0057"></a>Błąd 0057  
 Wyjątek występuje podczas próby utworzenia pliku lub obiektu blob, który już istnieje.  

 Ten wyjątek występuje, gdy używasz [modułu Eksportuj dane](export-data.md) lub innego modułu, aby zapisać wyniki potoku w usłudze Azure Machine Learning do magazynu obiektów blob platformy Azure, ale próbujesz utworzyć plik lub obiekt blob, który już istnieje.   

**Rozdzielczość:**

 Ten błąd zostanie wyświetlony tylko wtedy, gdy wcześniej ustawiłeś **właściwość tryb zapisu magazynu obiektów blob** azure na **Błąd**. Zgodnie z projektem ten moduł powoduje wystąpienie błędu, jeśli użytkownik próbuje zapisać zestaw danych do obiektu blob, który już istnieje.

 - Otwórz właściwości modułu i zmień właściwość **trybu zapisu magazynu obiektów blob** platformy Azure na **Zastąpienie**.
 - Alternatywnie można wpisać nazwę innego docelowego obiektu blob lub pliku i należy określić obiekt blob, który jeszcze nie istnieje.  

|Komunikaty o wyjątkach|
|------------------------|
|Plik lub obiekt blob już istnieje.|
|Plik lub obiekt blob "{file_path}" już istnieje.|


## <a name="error-0058"></a>Błąd 0058  
 Ten błąd w usłudze Azure Machine Learning występuje, jeśli zestaw danych nie zawiera kolumny oczekiwanej etykiety.  

 Ten wyjątek może również wystąpić, gdy kolumna etykiety pod warunkiem nie pasuje do danych lub typ danych oczekiwanych przez ucznia lub ma nieprawidłowe wartości. Na przykład ten wyjątek jest produkowany podczas korzystania z kolumny etykiety o rzeczywistej wartości podczas szkolenia klasyfikatora binarnego.  

**Rozdzielczość:** Rozdzielczość zależy od ucznia lub trenera, którego używasz, i typów danych kolumn w zestawie danych. Najpierw sprawdź wymagania algorytmu uczenia maszynowego lub modułu szkoleniowego.  

 Ponownie wejściowy zestaw danych. Sprawdź, czy kolumna, której oczekujesz, że będzie traktowana jako etykieta, ma odpowiedni typ danych dla modela, który tworzysz.  

 Sprawdź dane wejściowe pod kątem brakujących wartości i w razie potrzeby wyeliminuj je lub wymień.  

 W razie potrzeby dodaj moduł [Edytuj metadane](edit-metadata.md) i upewnij się, że kolumna etykiety jest oznaczona jako etykieta.  

|Komunikaty o wyjątkach|
|------------------------|
|Wartości kolumn etykiet i wartości kolumn etykiet scored nie są porównywalne.|
|Kolumna etykiety nie jest zgodnie z oczekiwaniami w polu "{dataset_name}".|
|Kolumna etykiety nie jest zgodnie z oczekiwaniami w "{dataset_name}", {reason}.|
|Kolumna etykiety "{column_name}" nie jest oczekiwana w "{dataset_name}".|
|Kolumna etykiety "{column_name}" nie jest oczekiwana w "{dataset_name}", {reason}.|


## <a name="error-0059"></a>Błąd 0059  
 Wyjątek występuje, jeśli indeks kolumn określony w selektorze kolumn nie może być analizowany.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli indeks kolumny określony podczas korzystania z selektora kolumn nie może być analizowany.  Ten błąd zostanie wyświetlony, gdy indeks kolumny jest w nieprawidłowym formacie, którego nie można przeanalizować.  

**Rozdzielczość:** Zmodyfikuj indeks kolumny, aby użyć prawidłowej wartości indeksu.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przeanalizować co najmniej jednego określonego indeksu kolumn lub zakresów indeksów.|
|Nie można przeanalizować indeksu kolumny ani zakresu "{column_index_or_range}".|


## <a name="error-0060"></a>Błąd 0060  
 Wyjątek występuje, gdy zakres kolumn poza zakresem jest określony w selektorze kolumn.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy zakres kolumn poza zakresem jest określony w selektorze kolumn. Ten błąd zostanie wyświetlony, jeśli zakres kolumn w selektorze kolumn nie odpowiada kolumnom w zestawie danych.  

**Rozdzielczość:** Zmodyfikuj zakres kolumn w selektorze kolumn, aby odpowiadał kolumnom w zestawie danych.  

|Komunikaty o wyjątkach|
|------------------------|
|Określono nieprawidłowy lub poza zakresem indeksu kolumny.|
|Zakres kolumn "{column_range}" jest nieprawidłowy lub poza zakresem.|


## <a name="error-0061"></a>Błąd 0061  
 Wyjątek występuje podczas próby dodania wiersza do DataTable, który ma inną liczbę kolumn niż tabela.  

 Ten błąd w usłudze Azure Machine Learning występuje podczas próby dodania wiersza do zestawu danych, który ma inną liczbę kolumn niż zestaw danych.  Ten błąd zostanie wyświetlony, jeśli wiersz, który jest dodawany do zestawu danych ma inną liczbę kolumn z wejściowego zestawu danych.  Wiersz nie może być dołączany do zestawu danych, jeśli liczba kolumn jest inna.  

**Rozdzielczość:** Zmodyfikuj wejściowy zestaw danych, aby mieć taką samą liczbę kolumn, jak dodany wiersz, lub zmodyfikuj dodany wiersz, aby mieć taką samą liczbę kolumn jak zestaw danych.  

|Komunikaty o wyjątkach|
|------------------------|
|Wszystkie tabele muszą mieć taką samą liczbę kolumn.|
|Kolumny w fragmencie "{chunk_id_1}" różnią się od fragmentu "{chunk_id_2}" o rozmiarze fragmentu: {chunk_size}.|
|Liczba kolumn w pliku "{filename_1}" (count={column_count_1}) różni się od pliku "{filename_2}" (count={column_count_2}).|


## <a name="error-0062"></a>Błąd 0062  
 Wyjątek występuje podczas próby porównania dwóch modeli z różnymi typami ucznia.  

 Ten błąd w usłudze Azure Machine Learning jest produkowany, gdy nie można porównać metryk oceny dla dwóch różnych zestawów danych według oceny. W takim przypadku nie jest możliwe porównanie skuteczności modeli używanych do tworzenia dwóch zestawów danych ocenionych.  

**Rozdzielczość:** Sprawdź, czy wyniki uzyskane są tworzone przez ten sam rodzaj modelu uczenia maszynowego (klasyfikacja binarna, regresja, klasyfikacja wieloklasowa, zalecenie, klastrowanie, wykrywanie anomalii itp.) Wszystkie porównywane modele muszą mieć ten sam typ ucznia.  

|Komunikaty o wyjątkach|
|------------------------|
|Wszystkie modele muszą mieć ten sam typ ucznia.|
|Masz niezgodny typ ucznia: "{actual_learner_type}". Oczekiwane typy uczniów to: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Błąd 0064  
 Wyjątek występuje, jeśli nazwa konta magazynu platformy Azure lub klucz magazynu jest określony niepoprawnie.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli nazwa konta magazynu platformy Azure lub klucz magazynu jest określony niepoprawnie. Ten błąd zostanie wyświetlony, jeśli wprowadzisz nieprawidłową nazwę konta lub hasło do konta magazynu. Może to nastąpić, jeśli ręcznie wprowadzisz nazwę konta lub hasło. Może również wystąpić, jeśli konto zostało usunięte.  

**Rozdzielczość:** Sprawdź, czy nazwa konta i hasło zostały wprowadzone poprawnie i czy konto istnieje.  

|Komunikaty o wyjątkach|
|------------------------|
|Nazwa konta magazynu platformy Azure lub klucz magazynu jest niepoprawna.|
|Nazwa konta magazynu platformy Azure "{account_name}" lub klucz magazynu dla nazwy konta jest niepoprawna.|


## <a name="error-0065"></a>Błąd 0065  
 Wyjątek występuje, jeśli nazwa obiektu blob platformy Azure jest określona niepoprawnie.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli nazwa obiektu blob platformy Azure jest określona niepoprawnie.  Zostanie wyświetlony błąd, jeśli:  

-   Nie można odnaleźć obiektu blob w określonym kontenerze.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Tylko kontener został określony jako źródło w [żądaniu importu danych,](import-data.md) gdy format był Excel lub CSV z kodowaniem; łączenie zawartości wszystkich obiektów blob w kontenerze nie jest dozwolone w tych formatach.  
  
-   Identyfikator URI sygnatury dostępu Współdzielonego nie zawiera nazwy prawidłowego obiektu blob.  

**Rozdzielczość:** Ponownie moduł zgłosić wyjątek. Sprawdź, czy określony obiekt blob istnieje w kontenerze na koncie magazynu i że uprawnienia umożliwiają wyświetlanie obiektu blob. Sprawdź, czy dane wejściowe są pochodzić z nazwy **kontenera formularza/nazwy pliku,** jeśli masz excela lub CSV z formatami kodowania. Sprawdź, czy identyfikator URI sygnatury dostępu Współdzielonego zawiera nazwę prawidłowego obiektu blob.  

|Komunikaty o wyjątkach|
|------------------------|
|Nazwa obiektu blob magazynu platformy Azure jest niepoprawna.|
|Nazwa obiektu blob magazynu platformy Azure "{blob_name}" jest niepoprawna.|
|Nazwa obiektu blob magazynu platformy Azure z prefiksem "{blob_name_prefix}" nie istnieje.|
|Nie można odnaleźć żadnych obiektów blob magazynu platformy Azure w kontenerze "{container_name}".|
|Nie można odnaleźć żadnych obiektów blob magazynu platformy Azure ze ścieżką wieloznaczną "{blob_wildcard_path}".|


## <a name="error-0066"></a>Błąd 0066  
 Wyjątek występuje, jeśli nie można przekazać zasobu do obiektu blob platformy Azure.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli nie można przekazać zasobu do obiektu blob platformy Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Oba są zapisywane na tym samym koncie magazynu platformy Azure, co konto zawierające plik wejściowy.  

**Rozdzielczość:** Ponownie moduł. Sprawdź, czy nazwa konta platformy Azure, klucz magazynu i kontener są poprawne i czy konto ma uprawnienia do zapisu w kontenerze.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przekazać zasobu do magazynu platformy Azure.|
|Nie można przekazać pliku "{source_path}" do magazynu platformy Azure jako "{dest_path}".|


## <a name="error-0067"></a>Błąd 0067  
 Wyjątek występuje, jeśli zestaw danych ma inną liczbę kolumn niż oczekiwano.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli zestaw danych ma inną liczbę kolumn niż oczekiwano.  Ten błąd zostanie wyświetlony, gdy liczba kolumn w zestawie danych różnią się od liczby kolumn, których moduł oczekuje podczas wykonywania.  

**Rozdzielczość:** Zmodyfikuj wejściowy zestaw danych lub parametry.  

|Komunikaty o wyjątkach|
|------------------------|
|Nieoczekiwana liczba kolumn w datatable.|
|Nieoczekiwana liczba kolumn w zestawie danych "{dataset_name}".|
|Oczekiwano kolumn "{expected_column_count}", ale zamiast tego znaleziono kolumny "{actual_column_count}".|
|W wejściowym zestawie danych "{dataset_name}" oczekiwano kolumn "{expected_column_count}", ale zamiast tego znaleziono kolumny "{actual_column_count}".|


## <a name="error-0068"></a>Błąd 0068  
 Wyjątek występuje, jeśli określony skrypt hive nie jest poprawny.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli istnieją błędy składni w skrypcie QL hive lub jeśli interpreter hive napotka błąd podczas wykonywania kwerendy lub skryptu.  

**Rozdzielczość:**

Komunikat o błędzie z gałęzi jest zwykle zgłaszane z powrotem w dzienniku błędów, dzięki czemu można podjąć działania na podstawie określonego błędu. 

+ Otwórz moduł i sprawdź kwerendę pod kątem błędów.  
+ Sprawdź, czy kwerenda działa poprawnie poza usługą Azure Machine Learning, logując się do konsoli gałęzi klastra Hadoop i uruchamiając kwerendę.  
+ Spróbuj umieścić komentarze w skrypcie hive w osobnym wierszu, w przeciwieństwie do mieszania instrukcji wykonywalnych i komentarzy w jednym wierszu.  

### <a name="resources"></a>Zasoby

Zobacz następujące artykuły, aby uzyskać pomoc dotyczącą zapytań hive dotyczących uczenia maszynowego:

+ [Tworzenie tabel gałęzi i ładowanie danych z usługi Azure Blob Storage](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Eksplorowanie danych w tabelach za pomocą zapytań hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Tworzenie funkcji dla danych w klastrze usługi Hadoop przy użyciu zapytań Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Gałąź dla użytkowników SQL Ściągawka (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Komunikaty o wyjątkach|
|------------------------|
|Skrypt gałęzi jest niepoprawny.|


## <a name="error-0069"></a>Błąd 0069  
 Wyjątek występuje, jeśli określony skrypt SQL nie jest poprawny.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli określony skrypt SQL ma problemy ze składnią lub jeśli kolumny lub tabela określona w skrypcie jest nieprawidłowa. 

 Ten błąd zostanie wyświetlony, jeśli aparat SQL napotka błąd podczas wykonywania kwerendy lub skryptu. Komunikat o błędzie SQL jest zwykle raportowane z powrotem w dzienniku błędów, dzięki czemu można podjąć działania na podstawie określonego błędu.  

**Rozdzielczość:** Ponownie moduł i sprawdź zapytanie SQL pod kątem błędów.  

 Sprawdź, czy kwerenda działa poprawnie poza usługą Azure ML, logując się bezpośrednio do serwera bazy danych i uruchamiając kwerendę.  

 Jeśli istnieje komunikat wygenerowany sql zgłaszane przez wyjątek modułu, należy podjąć działania na podstawie zgłoszonego błędu. Na przykład komunikaty o błędach czasami zawierają szczegółowe wskazówki dotyczące prawdopodobnego błędu:
+ *Brak takiej kolumny lub brakującej bazy danych,* co oznacza, że nazwa kolumny została wpisana nieprawidłowo. Jeśli masz pewność, że nazwa kolumny jest poprawna, spróbuj ująć identyfikator kolumny za pomocą nawiasów lub cudzysłowów.
+ *Błąd logiczny \<SQL\>w pobliżu słowa kluczowego SQL*, wskazujący, że może być wystąpił błąd składni przed określonym słowem kluczowym

  
|Komunikaty o wyjątkach|
|------------------------|
|Skrypt SQL jest niepoprawny.|
|Kwerenda SQL "{sql_query}" jest nieprawidłowa.|
|Kwerenda SQL "{sql_query}" jest nieprawidłowa. Komunikat o wyjątku: {wyjątek}.|


## <a name="error-0070"></a>Błąd 0070  
 Wyjątek występuje podczas próby uzyskania dostępu do nieistniejącej tabeli platformy Azure.  

 Ten błąd w usłudze Azure Machine Learning występuje podczas próby uzyskania dostępu do nieistniejącej tabeli platformy Azure. Ten błąd zostanie wyświetlony, jeśli określisz tabelę w usłudze Azure Storage, która nie istnieje podczas odczytywania lub zapisywania do usługi Azure Table Storage. Może się tak zdarzyć, jeśli błędnie wpiszesz nazwę żądanej tabeli lub masz niezgodność między nazwą obiektu docelowego a typem magazynu. Na przykład zamierzałeś odczytać z tabeli, ale zamiast tego wprowadzono nazwę obiektu blob.  

**Rozdzielczość:** Ponownie moduł, aby sprawdzić, czy nazwa tabeli jest poprawna.  

|Komunikaty o wyjątkach|
|------------------------|
|Tabela platformy Azure nie istnieje.|
|Tabela platformy Azure "{table_name}" nie istnieje.|


## <a name="error-0072"></a>Błąd 0072  
 Wyjątek występuje w przypadku limitu czasu połączenia.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy limit czasu połączenia. Ten błąd zostanie wyświetlony, jeśli obecnie występują problemy z łącznością ze źródłem lub miejscem docelowym danych, takie jak powolne połączenie z Internetem lub jeśli zestaw danych jest duży i/lub kwerenda SQL do odczytu w danych wykonuje skomplikowane przetwarzanie.  

**Rozdzielczość:** Określ, czy obecnie występują problemy z powolnymi połączeniami z magazynem platformy Azure lub Internetem.  

|Komunikaty o wyjątkach|
|------------------------|
|Wystąpił limit czasu połączenia.|


## <a name="error-0073"></a>Błąd 0073  
 Wyjątek występuje, jeśli wystąpi błąd podczas konwertowania kolumny na inny typ.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy nie jest możliwe przekonwertowanie kolumny na inny typ.  Ten błąd zostanie wyświetlony, jeśli moduł wymaga określonego typu i nie jest możliwe przekonwertowanie kolumny na nowy typ.  

**Rozdzielczość:** Zmodyfikuj wejściowy zestaw danych, tak aby kolumna mogła zostać przekonwertowana na podstawie wyjątku wewnętrznego.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przekonwertować kolumny.|
|Nie można przekonwertować kolumny na {target_type}.|


## <a name="error-0075"></a>Błąd 0075  
Wyjątek występuje, gdy nieprawidłowa funkcja binning jest używana podczas kwantyfikacji zestawu danych.  

Ten błąd w usłudze Azure Machine Learning występuje, gdy próbujesz bin danych przy użyciu nieobsługiwał metody lub gdy kombinacje parametrów są nieprawidłowe.  

**Rozdzielczość:**

Obsługa błędów dla tego zdarzenia została wprowadzona we wcześniejszej wersji usługi Azure Machine Learning, która umożliwiała bardziej dostosowywanie metod binningu. Obecnie wszystkie metody binningu są oparte na wyborze z listy rozwijanej, więc technicznie nie powinno być już możliwe uzyskanie tego błędu.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->

|Komunikaty o wyjątkach|
|------------------------|
|Używana nieprawidłowa funkcja binningu.|


## <a name="error-0077"></a>Błąd 0077  
 Wyjątek występuje, gdy nieznany tryb zapisu pliku obiektu blob przeszedł.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli nieprawidłowy argument jest przekazywany w specyfikacjach miejsca docelowego lub źródła pliku obiektu blob.  

**Rozdzielczość:** W prawie wszystkich modułach, które importują lub eksportują dane do i z magazynu obiektów blob platformy Azure wartości parametrów sterujących trybem zapisu są przypisywane przy użyciu listy rozwijanej; w związku z tym nie jest możliwe do przekazania nieprawidłową wartość i ten błąd nie powinien się pojawić. Ten błąd zostanie przestarzały w nowszej wersji.  

|Komunikaty o wyjątkach|
|------------------------|
|Nieobsługiwał trybu zapisu obiektu blob.|
|Nieobsługiwał trybu zapisu obiektu blob: {blob_write_mode}.|


## <a name="error-0078"></a>Błąd 0078  
 Wyjątek występuje, gdy opcja HTTP dla [danych importowych](import-data.md) odbiera kod stanu 3xx wskazujący przekierowanie.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy opcja HTTP dla [danych importu](import-data.md) odbiera kod stanu 3xx (301, 302, 304 itp.) wskazujący przekierowanie. Ten błąd zostanie wyświetlony, jeśli spróbujesz połączyć się ze źródłem HTTP, które przekierowuje przeglądarkę na inną stronę. Ze względów bezpieczeństwa przekierowanie witryn sieci Web nie jest dozwolone jako źródła danych dla usługi Azure Machine Learning.  

**Rozdzielczość:** Jeśli witryna jest zaufaną witryną sieci Web, wprowadź przekierowany adres URL bezpośrednio.  

|Komunikaty o wyjątkach|
|------------------------|
|Przekierowanie http nie jest dozwolone.|


## <a name="error-0079"></a>Błąd 0079  
 Wyjątek występuje, jeśli nazwa kontenera magazynu platformy Azure jest określona niepoprawnie.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli nazwa kontenera magazynu platformy Azure jest określona niepoprawnie. Ten błąd zostanie wyświetlony, jeśli nie określono zarówno kontenera, jak i nazwy obiektu blob (pliku) przy użyciu **path to blob, począwszy od** opcji kontenera podczas zapisywania w usłudze Azure Blob Storage.  

**Rozdzielczość:** Odwiedź ponownie moduł [Eksportuj dane](export-data.md) i sprawdź, czy określona ścieżka do obiektu blob zawiera zarówno kontener, jak i nazwę pliku w **formacie kontenera/nazwy pliku.**  

|Komunikaty o wyjątkach|
|------------------------|
|Nazwa kontenera magazynu platformy Azure jest niepoprawna.|
|Nazwa kontenera magazynu platformy Azure "{container_name}" jest niepoprawna; oczekiwano nazwy kontenera kontenera/obiektu blob formatu.|


## <a name="error-0080"></a>Błąd 0080  
 Wyjątek występuje, gdy kolumna z brakującymi wartościami jest niedozwolona przez moduł.  

 Ten błąd w usłudze Azure Machine Learning jest produkowany, gdy jedna lub więcej kolumn używane przez moduł zawiera wszystkie brakujące wartości. Na przykład jeśli moduł oblicza statystyki zagregowane dla każdej kolumny, nie może działać w kolumnie zawierającej brak danych. W takich przypadkach wykonanie modułu jest zatrzymane z tym wyjątkiem.  

**Rozdzielczość:** Ponownie wprowadź ponownie wejściowy zestaw danych i usuń wszystkie kolumny, które zawierają wszystkie brakujące wartości.  

|Komunikaty o wyjątkach|
|------------------------|
|Kolumny z brakującymi wartościami nie są dozwolone.|
|W kolumnie {col_index_or_name} brakuje wszystkich wartości.|


## <a name="error-0081"></a>Błąd 0081  
 Wyjątek występuje w module PCA, jeśli liczba wymiarów do zmniejszenia jest równa liczbie kolumn elementów w wejściowym zestawie danych, zawierających co najmniej jedną kolumnę funkcji rozrzedzonych.  

 Ten błąd w usłudze Azure Machine Learning jest produkowany, jeśli spełnione są następujące warunki: (a) wejściowy zestaw danych ma co najmniej jedną kolumnę rozrzedzonych i (b) ostateczna liczba żądanych wymiarów jest taka sama jak liczba wymiarów wejściowych.  

**Rozdzielczość:** Należy rozważyć zmniejszenie liczby wymiarów w danych wyjściowych, aby była mniejsza niż liczba wymiarów w danych wejściowych. Jest to typowe w zastosowaniach PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Komunikaty o wyjątkach|
|------------------------|
|Dla zestawu danych zawierającego rozrzedzone kolumny obiektowe liczba wymiarów do zmniejszenia powinna być mniejsza niż liczba kolumn obiektowych.|


## <a name="error-0082"></a>Błąd 0082  
 Wyjątek występuje, gdy model nie może być pomyślnie zdemalizowany.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy zapisany model uczenia maszynowego lub transformacji nie mogą zostać załadowane przez nowszą wersję środowiska wykonawczego usługi Azure Machine Learning w wyniku zmiany podziału.  

**Rozdzielczość:** Potok szkolenia, który wyprodukował model lub transformacja musi zostać ponownie uruchom, a model lub transformacja musi zostać ponownie wypisane.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można deserializować modelu, ponieważ prawdopodobnie jest serializowany ze starszym formatem serializacji. Przekwalifikowanie i ponowne zapisywanie modelu.|


## <a name="error-0083"></a>Błąd 0083  
 Wyjątek występuje, jeśli zestaw danych używany do szkolenia nie może być używany dla konkretnego typu ucznia.  

 Ten błąd w usłudze Azure Machine Learning jest produkowany, gdy zestaw danych jest niezgodny z uczniem jest przeszkolony. Na przykład zestaw danych może zawierać co najmniej jedną brakującą wartość w każdym wierszu, a w rezultacie cały zestaw danych zostanie pominięty podczas szkolenia. W innych przypadkach niektóre algorytmy uczenia maszynowego, takie jak wykrywanie anomalii, nie oczekują obecności etykiet i mogą zgłaszać ten wyjątek, jeśli etykiety są obecne w zestawie danych.  

**Rozdzielczość:** Zapoznaj się z dokumentacją ucznia używanego do sprawdzania wymagań dotyczących wejściowego zestawu danych. Sprawdź kolumny, aby zobaczyć wszystkie wymagane kolumny są obecne.  

|Komunikaty o wyjątkach|
|------------------------|
|Zestaw danych używany do szkolenia jest nieprawidłowy.|
|{data_name} zawiera nieprawidłowe dane do szkolenia.|
|{data_name} zawiera nieprawidłowe dane do szkolenia. Typ ucznia: {learner_type}.|
|{data_name} zawiera nieprawidłowe dane do szkolenia. Typ ucznia: {learner_type}. Powód: {reason}.|
|Nie można zastosować akcji "{action_name}" w odniesieniu do danych szkoleniowych {data_name}. Powód: {reason}.|


## <a name="error-0084"></a>Błąd 0084  
 Wyjątek występuje, gdy wyniki produkowane ze skryptu R są oceniane. Jest to obecnie nieobsługiwało.  

 Ten błąd w usłudze Azure Machine Learning występuje, jeśli spróbujesz użyć jednego z modułów do oceny modelu z danych wyjściowych ze skryptu R, który zawiera wyniki.  

**Rozdzielczość:**

|Komunikaty o wyjątkach|
|------------------------|
|Ocena wyników wyprodukowanych przez model niestandardowy jest obecnie nieobsługiwała.|


## <a name="error-0085"></a>Błąd 0085  
 Wyjątek występuje, gdy ocena skryptu kończy się niepowodzeniem z błędem.  

 Ten błąd w usłudze Azure Machine Learning występuje podczas uruchamiania skryptu niestandardowego, który zawiera błędy składni.  

**Rozdzielczość:** Przejrzyj kod w edytorze zewnętrznym i sprawdź, czy nie ma błędów.  

|Komunikaty o wyjątkach|
|------------------------|
|Błąd podczas oceny skryptu.|
|Podczas oceny skryptu wystąpił następujący błąd, aby uzyskać więcej informacji na temat dziennika danych wyjściowych:<br />---------- Komunikat o błędzie Początek komunikatu o błędzie z ---------- interpretera {script_language}<br />{wiadomość}<br />---------- Komunikat o błędzie Koniec błędu z ---------- interpretera {script_language}|


## <a name="error-0090"></a>Błąd 0090  
 Wyjątek występuje, gdy tworzenie tabeli gałęzi kończy się niepowodzeniem.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy używasz [eksportu danych](export-data.md) lub innej opcji zapisywania danych w klastrze HDInsight i nie można utworzyć określonej tabeli hive.  

**Rozdzielczość:** Sprawdź nazwę konta magazynu platformy Azure skojarzoną z klastrem i sprawdź, czy używasz tego samego konta we właściwościach modułu.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można utworzyć tabeli Hive. W przypadku klastra HDInsight upewnij się, że nazwa konta magazynu platformy Azure skojarzona z klastrem jest taka sama, jak to, co jest przekazywane za pośrednictwem parametru modułu.|
|Nie można utworzyć tabeli Hive "{table_name}". W przypadku klastra HDInsight upewnij się, że nazwa konta magazynu platformy Azure skojarzona z klastrem jest taka sama, jak to, co jest przekazywane za pośrednictwem parametru modułu.|
|Nie można utworzyć tabeli Hive "{table_name}". W przypadku klastra USŁUGI HDInsight upewnij się, że nazwa konta magazynu platformy Azure skojarzona z klastrem to "{cluster_name}".|


## <a name="error-0102"></a>Błąd 0102  
 Rzutowany, gdy nie można wyodrębnić pliku ZIP.  

 Ten błąd w usłudze Azure Machine Learning występuje podczas importowania spakowanego pakietu z rozszerzeniem .zip, ale pakiet nie jest plikiem zip lub plik nie używa obsługiwanego formatu zip.  

**Rozdzielczość:** Upewnij się, że wybrany plik jest prawidłowym plikiem zip i że został skompresowany przy użyciu jednego z obsługiwanych algorytmów kompresji.  

 Jeśli ten błąd pojawia się podczas importowania zestawów danych w formacie skompresowanym, sprawdź, czy wszystkie zawarte pliki używają jednego z obsługiwanych formatów plików i są w formacie Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Spróbuj odczytać żądane pliki do nowego skompresowanego folderu ze skompresowanym i spróbuj ponownie dodać moduł niestandardowy.  

|Komunikaty o wyjątkach|
|------------------------|
|Podany plik ZIP nie jest w prawidłowym formacie.|


## <a name="error-0105"></a>Błąd 0105  
 Ten błąd jest wyświetlany, gdy plik definicji modułu zawiera nieobsługiwany typ parametru  
  
 Ten błąd w usłudze Azure Machine Learning jest produkowany podczas tworzenia niestandardowej definicji xml modułu i typ parametru lub argumentu w definicji nie pasuje do obsługiwanego typu.  
  
**Rozdzielczość:** Upewnij się, że właściwość typu dowolnego elementu **Arg** w pliku definicji xml modułu niestandardowego jest obsługiwanym typem.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwał typ parametru.|  
|Nieobsługiwał typ{0}parametru ' ' określony.|  


## <a name="error-0107"></a>Błąd 0107  
 Rzutowany, gdy plik definicji modułu definiuje nieobsługiwany typ wyjściowy  
  
 Ten błąd w usłudze Azure Machine Learning jest produkowany, gdy typ portu wyjściowego w niestandardowej definicji xml modułu nie pasuje do obsługiwanego typu.  
  
**Rozdzielczość:** Upewnij się, że właściwość typu elementu Output w pliku definicji xml modułu niestandardowego jest obsługiwanym typem.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwał typ wyjściowy.|  
|Nieobsługiwał typ wyjściowy "{output_type}" określony.|  


## <a name="error-0125"></a>Błąd 0125  
 Rzutowane, gdy schemat dla wielu zestawów danych nie jest zgodny.  

**Rozdzielczość:**

|Komunikaty o wyjątkach|
|------------------------|
|Schemat zestawu danych nie jest zgodny.|


## <a name="error-0127"></a>Błąd 0127  
 Rozmiar piksela obrazu przekracza dozwolony limit  

 Ten błąd występuje, jeśli odczytujesz obrazy z zestawu danych obrazu do klasyfikacji i obrazy są większe niż model może obsłużyć.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Komunikaty o wyjątkach|
|------------------------|
|Rozmiar piksela obrazu przekracza dozwolony limit.|
|Rozmiar piksela obrazu w pliku "{file_path}" przekracza dozwolony limit: "{size_limit}".|


## <a name="error-0128"></a>Błąd 0128  
 Liczba prawdopodobieństw warunkowych dla kolumn kategorii przekracza limit.  

**Rozdzielczość:**

|Komunikaty o wyjątkach|
|------------------------|
|Liczba prawdopodobieństw warunkowych dla kolumn kategorii przekracza limit.|
|Liczba prawdopodobieństw warunkowych dla kolumn kategorii przekracza limit. Kolumny "{column_name_or_index_1}" i "{column_name_or_index_2}" są parą problematyczną.|


## <a name="error-0129"></a>Błąd 0129  
 Liczba kolumn w zestawie danych przekracza dozwolony limit.  

**Rozdzielczość:**

|Komunikaty o wyjątkach|
|------------------------|
|Liczba kolumn w zestawie danych przekracza dozwolony limit.|
|Liczba kolumn w zestawie danych w polu "{dataset_name}" przekracza dozwoloną liczbę kolumn.|
|Liczba kolumn w zestawie danych w polu "{dataset_name}" przekracza dozwolony limit "{component_name}".|
|Liczba kolumn w zestawie danych w polu "{dataset_name}" przekracza dozwolony limit "{limit_columns_count}" "{component_name}".|


## <a name="error-0134"></a>Błąd 0134
Wyjątek występuje, gdy brakuje kolumny etykiety lub nie ma wystarczającej liczby wierszy oznaczonych etykietą.  

Ten błąd występuje, gdy moduł wymaga kolumny etykiety, ale nie zawiera jeden w zaznaczeniu kolumny lub kolumna etykieta brakuje zbyt wiele wartości.

Ten błąd może również wystąpić, gdy poprzednia operacja zmienia zestaw danych w taki sposób, że niewystarczające wiersze są dostępne dla operacji podrzędnej. Załóżmy na przykład, że wyrażenie w module **Partycja i Próbka** służy do dzielenia zestawu danych według wartości. Jeśli nie zostaną znalezione żadne dopasowania dla wyrażenia, jeden z zestawów danych wynikających z partycji będzie pusty.

Rozwiązanie: 

 Jeśli do wyboru kolumny zostanie dołączona kolumna etykiety, ale nie jest ona rozpoznawana, użyj modułu [Edytuj metadane, aby oznaczyć](edit-metadata.md) ją jako kolumnę etykiet.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Następnie można użyć [modułu Czyste brakujące dane,](clean-missing-data.md) aby usunąć wiersze z brakującymi wartościami w kolumnie etykiety. 

 Sprawdź zestawy danych wejściowych, aby upewnić się, że zawierają one prawidłowe dane i tyle wierszy, aby spełnić wymagania operacji. Wiele algorytmów wygeneruje komunikat o błędzie, jeśli wymagają one minimalnej liczby wierszy danych, ale dane zawierają tylko kilka wierszy lub tylko nagłówek.

|Komunikaty o wyjątkach|
|------------------------|
|Wyjątek występuje, gdy brakuje kolumny etykiety lub nie ma wystarczającej liczby wierszy oznaczonych etykietą.|
|Wyjątek występuje, gdy brakuje kolumny etykiety lub ma mniej niż {required_rows_count} wierszy z etykietą.|
|Wyjątek występuje, gdy brakuje kolumny etykiety w zestawie danych {dataset_name} lub ma mniej niż {required_rows_count} oznaczonych wierszami.|


## <a name="error-0138"></a>Błąd 0138  
 Pamięć została wyczerpana, nie można ukończyć pracy modułu. Downsampling zestawu danych może pomóc w złagodzeniu problemu.  

 Ten błąd występuje, gdy moduł, który jest uruchomiony wymaga więcej pamięci niż jest dostępna w kontenerze platformy Azure. Może się tak zdarzyć, jeśli pracujesz z dużym zestawem danych, a bieżąca operacja nie mieści się w pamięci.  

**Rozdzielczość:** Jeśli próbujesz odczytać duży zestaw danych i nie można ukończyć operacji, próbkowanie w dół zestawu danych może pomóc.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Komunikaty o wyjątkach|
|------------------------|
|Pamięć została wyczerpana, nie można ukończyć pracy modułu.|
|Pamięć została wyczerpana, nie można ukończyć pracy modułu. Szczegóły: {szczegóły}|


## <a name="error-0141"></a>Błąd 0141  
 Wyjątek występuje, jeśli liczba wybranych kolumn numerycznych i unikatowych wartości w kolumnach kategorycznych i ciągów jest za mała.  

 Ten błąd w usłudze Azure Machine Learning występuje, gdy nie ma wystarczającej liczby unikatowych wartości w wybranej kolumnie do wykonania operacji.  

**Rozdzielczość:** Niektóre operacje wykonują operacje statystyczne na kolumnach elementów i kategorii, a jeśli nie ma wystarczającej liczby wartości, operacja może zakończyć się niepowodzeniem lub zwrócić nieprawidłowy wynik. Sprawdź zestaw danych, aby zobaczyć, ile wartości znajduje się w kolumnach funkcji i etykiet, i określ, czy operacja, którą próbujesz wykonać, jest statystycznie prawidłowa.  

 Jeśli źródłowy zestaw danych jest prawidłowy, można również sprawdzić, czy niektóre upstream manipulacji danych lub operacji metadanych zmienił dane i usunąć niektóre wartości.  

 Jeśli operacje nadrzędne obejmują dzielenie, próbkowanie lub ponowne próbkowanie, sprawdź, czy dane wyjściowe zawierają oczekiwaną liczbę wierszy i wartości.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba wybranych kolumn numerycznych i unikatowych wartości w kolumnach kategorycznych i ciągów jest za mała.|
|Całkowita liczba wybranych kolumn liczbowych i unikatowych wartości w kolumnach kategorii i ciągów (obecnie {actual_num}) powinna wynosić co najmniej {lower_boundary}.|


## <a name="error-0154"></a>Błąd 0154  
 Wyjątek występuje, gdy użytkownik próbuje dołączyć dane do kolumn kluczy z niezgodnym typem kolumny.

|Komunikaty o wyjątkach|
|------------------------|
|Typy elementów kolumny klucza nie są zgodne.|
|Typy elementów kolumny klucza nie są zgodne. (po lewej: {keys_left}; po prawej: {keys_right})|


## <a name="error-0155"></a>Błąd 0155  
 Wyjątek występuje, gdy nazwy kolumn zestawu danych nie są ciągiem.

|Komunikaty o wyjątkach|
|------------------------|
|Nazwa kolumny dataframe musi być typem ciągu. Nazwy kolumn nie są ciągami.|
|Nazwa kolumny dataframe musi być typem ciągu. Nazwy kolumn {column_names} nie są ciągami znaków.|


## <a name="error-0156"></a>Błąd 0156  
 Wyjątek występuje, gdy nie można odczytać danych z usługi Azure SQL Database.

|Komunikaty o wyjątkach|
|------------------------|
|Nie można odczytać danych z bazy danych SQL usługi Azure.|
|Nie można odczytać danych z bazy danych SQL usługi Azure: {detailed_message} DB: {database_server_name}:{database_name} Kwerenda: {sql_statement}|


## <a name="error-0157"></a>Błąd 0157  
 Nie znaleziono magazynu danych.

|Komunikaty o wyjątkach|
|------------------------|
|Informacje o magazynie danych są nieprawidłowe.|
|Informacje o magazynie danych są nieprawidłowe. Nie można uzyskać magazynu danych usługi AzureML "{datastore_name}" w obszarze roboczym "{workspace_name}".|


## <a name="error-0158"></a>Błąd 0158
 Zgłaszany, gdy katalog transformacji jest nieprawidłowy.

|Komunikaty o wyjątkach|
|------------------------------------------------------------|
|Given TransformationDirectory jest nieprawidłowy.|
|TransformationDirectory "{arg_name}" jest nieprawidłowy. Powód: {reason}. Uruchom ponownie eksperyment szkoleniowy, który generuje plik Transform. Jeśli eksperyment szkoleniowy został usunięty, należy ponownie utworzyć i zapisać plik przekształcania.|


## <a name="error-1000"></a>Błąd 1000  
Wyjątek biblioteki wewnętrznej.  

Ten błąd jest dostarczany do przechwytywania inaczej nieobsługiwał błędów silnika wewnętrznego. W związku z tym przyczyną tego błędu może być inna w zależności od modułu, który wygenerował błąd.  

Aby uzyskać więcej pomocy, zaleca się opublikować szczegółowy komunikat, który towarzyszy błąd na forum usługi Azure Machine Learning, wraz z opisem scenariusza, w tym dane używane jako dane wejściowe. Ta informacja zwrotna pomoże nam ustalić priorytety błędów i zidentyfikować najważniejsze kwestie do dalszej pracy.  

|Komunikaty o wyjątkach|
|------------------------|
|Wyjątek biblioteki.|
|Wyjątek biblioteki: {wyjątek}.|
|Nieznany wyjątek biblioteki: {wyjątek}. {customer_support_guidance}.|

