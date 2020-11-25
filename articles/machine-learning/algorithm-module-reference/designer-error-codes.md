---
title: Rozwiązywanie problemów z błędami modułu projektanta
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak czytać i rozwiązywać problemy z automatycznymi kodami błędów modułów w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 11/25/2020
ms.openlocfilehash: af7ac49fd6c1a31a8363c4ba0bf925787613ecc2
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030411"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>Wyjątki i kody błędów dla projektanta

W tym artykule opisano komunikaty o błędach i kody wyjątków w programie Azure Machine Learning Designer, które ułatwiają rozwiązywanie problemów z potokami uczenia maszynowego.

Komunikat o błędzie można znaleźć w projektancie, wykonując następujące czynności:  

- Wybierz moduł, który zakończył się niepowodzeniem. Przejdź do karty dane **wyjściowe i dzienniki** , aby znaleźć szczegółowy dziennik w pliku **70_driver_log.txt** w kategorii **Logs — dzienniki** .

- Szczegółowe instrukcje dotyczące błędu modułu można sprawdzić w error_info.jsw obszarze **module_statistics** kategorii.

Poniżej przedstawiono kody błędów modułów w projektancie.

## <a name="error-0001"></a>Błąd 0,001  
 Wyjątek występuje, jeśli nie można odnaleźć co najmniej jednej określonej kolumny zestawu danych.  

 Ten błąd zostanie wyświetlony w przypadku wybrania kolumny dla modułu, ale wybrane kolumny nie istnieją w zestawie danych wejściowych. Ten błąd może wystąpić, jeśli wpisano ręcznie nazwę kolumny lub jeśli selektor kolumny dostarczył sugerowaną kolumnę, która nie istnieje w zestawie danych podczas uruchamiania potoku.  

**Rozwiązanie:** Ponownie odwiedź moduł zgłaszający ten wyjątek i sprawdź, czy nazwa kolumny lub nazwy są poprawne i czy wszystkie kolumny, do których istnieją odwołania, istnieją.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie znaleziono co najmniej jednej podanej kolumny.|
|Nie znaleziono kolumny o nazwie lub indeksie "{column_id}".|
|Kolumna o nazwie lub indeksie "{column_id}" nie istnieje w "{arg_name_missing_column}".|
|Kolumna o nazwie lub indeksie "{column_id}" nie istnieje w "{arg_name_missing_column}", ale istnieje w "{arg_name_has_column}".|
|Nie znaleziono kolumn o nazwie lub indeksie "{column_names}".|
|Kolumny o nazwie lub indeksie "{column_names}" nie istnieją w "{arg_name_missing_column}".|
|Kolumny o nazwie lub indeksie "{column_names}" nie istnieją w "{arg_name_missing_column}", ale istnieją w "{arg_name_has_column}".|


## <a name="error-0002"></a>Błąd 0,002  
 Wyjątek występuje, jeśli nie można przeanalizować co najmniej jednego parametru lub przekonwertować go z określonego typu na wymagany przez typ metody docelowej.  

 Ten błąd występuje w Azure Machine Learning w przypadku określenia parametru jako danych wejściowych, a typ wartości różni się od oczekiwanego typu i nie można wykonać niejawnej konwersji.  

**Rozwiązanie:** Sprawdź wymagania modułu i określ, który typ wartości jest wymagany (String, Integer, Double itp.).  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przeanalizować parametru.|
|Nie można przeanalizować parametru "{arg_name_or_column}".|
|Nie można skonwertować parametru "{arg_name_or_column}" na "{to_type}".|
|Nie można skonwertować parametru "{arg_name_or_column}" z "{from_type}" na "{to_type}".|
|Nie można skonwertować wartości "{arg_name_or_column}" parametru "{arg_value}" z "{from_type}" na "{to_type}".|
|Nie można przekonwertować wartości "{arg_value}" w kolumnie "{arg_name_or_column}" z "{from_type}" na "{to_type}" przy użyciu podanego formatu "{FMT}".|


## <a name="error-0003"></a>0003 błędów  
 Wyjątek występuje, jeśli co najmniej jeden z wejść ma wartość null lub jest pusty.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli jakiekolwiek dane wejściowe lub parametry modułu mają wartość null lub są puste.  Ten błąd może wystąpić na przykład wtedy, gdy nie została wpisana żadna wartość parametru. Może się to zdarzyć również w przypadku wybrania zestawu danych, który ma brakujące wartości, lub pustego zestawu danych.  

**Rozwiązanie:**

+ Otwórz moduł, który wygenerował wyjątek, i sprawdź, czy zostały określone wszystkie dane wejściowe. Upewnij się, że wszystkie wymagane dane wejściowe są określone. 
+ Upewnij się, że dane, które są ładowane z usługi Azure Storage, są dostępne i że nazwa lub klucz konta nie uległ zmianie.  
+ Sprawdź dane wejściowe pod kątem brakujących wartości lub wartości null.
+ Jeśli używasz zapytania w źródle danych, sprawdź, czy dane są zwracane w oczekiwanym formacie. 
+ Sprawdź pisownię lub inne zmiany w specyfikacji danych.
  
|Komunikaty o wyjątkach|
|------------------------|
|Co najmniej jeden z wejść ma wartość null lub jest pusty.|
|Wejście "{name}" ma wartość null lub jest puste.|


## <a name="error-0004"></a>Błąd 0,004  
 Wyjątek występuje, jeśli parametr jest mniejszy lub równy określonej wartości.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli parametr w komunikacie jest poniżej wartości granicznej wymaganej przez moduł do przetwarzania danych.  

**Rozwiązanie:** Ponownie odwiedzający moduł zgłasza wyjątek i zmodyfikuj parametr tak, aby był większy niż określona wartość.  

|Komunikaty o wyjątkach|
|------------------------|
|Parametr musi być większy niż wartość graniczna.|
|Wartość parametru "{arg_name}" powinna być większa niż {lower_boundary}.|
|Parametr "{arg_name}" ma wartość "{actual_value}", która powinna być większa niż {lower_boundary}.|


## <a name="error-0005"></a>Błąd 0,005  
 Wyjątek występuje, jeśli parametr jest mniejszy niż określona wartość.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli parametr w komunikacie jest mniejszy lub równy wartości granicznej wymaganej przez moduł do przetwarzania danych.  

**Rozwiązanie:** Ponownie odwiedź moduł zgłaszający wyjątek i zmodyfikuj parametr tak, aby był większy lub równy podanej wartości.  

|Komunikaty o wyjątkach|
|------------------------|
|Wartość parametru powinna być większa lub równa wartości granicy.|
|Wartość parametru "{arg_name}" powinna być większa lub równa {lower_boundary}.|
|Parametr "{arg_name}" ma wartość "{value}", która powinna być większa lub równa {lower_boundary}.|


## <a name="error-0006"></a>0006 błędów  
 Wyjątek występuje, jeśli parametr jest większy lub równy określonej wartości.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli parametr w komunikacie jest większy lub równy wartości granicznej wymaganej przez moduł do przetwarzania danych.  

**Rozwiązanie:** Ponownie odwiedzający moduł zgłaszający wyjątek i zmodyfikuj parametr, aby był mniejszy niż określona wartość.  

|Komunikaty o wyjątkach|
|------------------------|
|Niezgodność parametrów. Jeden z parametrów powinien być mniejszy niż inny.|
|Wartość parametru "{arg_name}" powinna być mniejsza niż wartość parametru "{upper_boundary_parameter_name}".|
|Parametr "{arg_name}" ma wartość "{value}", która powinna być mniejsza niż {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>0007 błędów  
 Wyjątek występuje, jeśli parametr jest większy niż określona wartość.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning Jeśli we właściwościach modułu zostanie określona wartość większa niż dozwolona. Można na przykład określić dane, które znajdują się poza zakresem obsługiwanych dat, lub można wskazać, że pięć kolumn ma być używanych, gdy dostępne są tylko trzy kolumny. 

 Ten błąd może również pojawić się, jeśli określisz dwa zestawy danych, które muszą być zgodne w jakiś sposób. Na przykład, jeśli zmieniasz nazwy kolumn i określisz kolumny według indeksu, liczba dostarczonych nazw musi być zgodna z liczbą indeksów kolumn. Innym przykładem może być operacja matematyczna korzystająca z dwóch kolumn, gdzie kolumny muszą zawierać taką samą liczbę wierszy. 

**Rozwiązanie:**

 + Otwórz dany moduł i przejrzyj wszystkie ustawienia właściwości liczbowych.
 + Upewnij się, że wszystkie wartości parametrów mieszczą się w obsługiwanym zakresie wartości dla tej właściwości.
 + Jeśli moduł pobiera wiele danych wejściowych, upewnij się, że dane wejściowe mają ten sam rozmiar.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Sprawdź, czy zestaw danych lub źródło danych zostały zmienione. Czasami wartość, która działała z poprzednią wersją danych, zakończy się niepowodzeniem po liczbie kolumn, typach danych kolumny lub rozmiarze danych.  

|Komunikaty o wyjątkach|
|------------------------|
|Niezgodność parametrów. Jeden z parametrów powinien być mniejszy lub równy innemu.|
|Wartość parametru "{arg_name}" powinna być mniejsza lub równa wartości parametru "{upper_boundary_parameter_name}".|
|Parametr "{arg_name}" ma wartość "{actual_value}", która nie może być większa niż {upper_boundary}.|
|Wartość {actual_value} parametru "{arg_name}" musi być mniejsza lub równa parametrowi "{upper_boundary_parameter_name}" o wartości {upper_boundary}.|
|Wartość {actual_value} parametru "{arg_name}" musi być mniejsza lub równa {upper_boundary_meaning} wartości {upper_boundary}.|


## <a name="error-0008"></a>0008 błędów  
 Wyjątek występuje, jeśli parametr nie należy do zakresu.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli parametr w komunikacie wykracza poza granice wymagane przez moduł do przetwarzania danych.  

 Na przykład ten błąd jest wyświetlany, jeśli spróbujesz użyć [Dodawanie wierszy](add-rows.md) do łączenia dwóch zestawów danych, które mają różną liczbę kolumn.  

**Rozwiązanie:** Ponownie odwiedzający moduł zgłaszający wyjątek i zmodyfikuj parametr tak, aby znajdował się w określonym zakresie.  

|Komunikaty o wyjątkach|
|------------------------|
|Wartość parametru nie znajduje się w określonym zakresie.|
|Wartość parametru "{arg_name}" nie należy do zakresu.|
|Wartość parametru "{arg_name}" musi należeć do zakresu [{lower_boundary}, {upper_boundary}].|
|Wartość parametru "{arg_name}" nie należy do zakresu. dotycząca|


## <a name="error-0009"></a>0009 błędów  
 Wyjątek występuje, gdy nazwa konta usługi Azure Storage lub nazwa kontenera została określona nieprawidłowo.  

Ten błąd występuje w projektancie Azure Machine Learning podczas określania parametrów dla konta usługi Azure Storage, ale nie można rozpoznać nazwy ani hasła. Błędy na hasłach lub nazwach kont mogą mieć wiele przyczyn:

 + Typ konta jest nieprawidłowy. Niektóre nowe typy kont nie są obsługiwane przez program Machine Learning Designer. Aby uzyskać szczegółowe informacje, zobacz [Importowanie danych](import-data.md) .
 + Wprowadzono niepoprawną nazwę konta
 + Konto już nie istnieje
 + Hasło do konta magazynu jest nieprawidłowe lub zostało zmienione
 + Nie określono nazwy kontenera lub kontener nie istnieje
 + Nie określono w pełni ścieżki pliku (ścieżka do obiektu BLOB)
   

**Rozwiązanie:**

Takie problemy często występują podczas próby ręcznego wprowadzenia nazwy konta, hasła lub ścieżki kontenera. Zalecamy używanie nowego kreatora dla modułu [Importuj dane](import-data.md) , który ułatwia wyszukiwanie i sprawdzanie nazw.

Sprawdź również, czy konto, kontener lub obiekt BLOB został usunięty. Użyj innego narzędzia usługi Azure Storage, aby sprawdzić, czy nazwa konta i hasło zostały wprowadzone poprawnie oraz czy kontener istnieje. 

Niektóre nowsze typy kont nie są obsługiwane przez Azure Machine Learning. Na przykład nowe typy magazynów "gorąca" lub "zimne" nie mogą być używane do uczenia maszynowego. Zarówno klasyczne konta magazynu, jak i konta magazynu utworzone jako "ogólnego przeznaczenia" działają prawidłowo.

Jeśli podano pełną ścieżkę do obiektu BLOB, należy sprawdzić, czy ścieżka jest określona jako **Container/blobname**, i czy zarówno kontener, jak i obiekt BLOB istnieją na koncie.  

 Ścieżka nie powinna zawierać ukośnika wiodącego. Na przykład **/Container/BLOB** jest niepoprawny i powinien zostać wprowadzony jako **kontener/obiekt BLOB**.  


|Komunikaty o wyjątkach|
|------------------------|
|Nazwa konta usługi Azure Storage lub nazwa kontenera jest niepoprawna.|
|Nazwa konta usługi Azure Storage "{account_name}" lub nazwa kontenera "{container_name}" jest niepoprawna; Oczekiwano nazwy kontenera/obiektu BLOB.|


## <a name="error-0010"></a>Błąd 0,010  
 Wyjątek występuje, jeśli wejściowe zestawy danych mają nazwy kolumn, które powinny być zgodne, ale nie.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli indeks kolumny w komunikacie ma inne nazwy kolumn w dwóch wejściowych zestawach danych.  

**Rozwiązanie:** Użyj [Edytuj metadanych](edit-metadata.md) lub zmodyfikuj oryginalny zestaw danych tak, aby miał tę samą nazwę kolumny dla określonego indeksu kolumn.  

|Komunikaty o wyjątkach|
|------------------------|
|Kolumny z odpowiednim indeksem wejściowych zestawów danych mają różne nazwy.|
|Nazwy kolumn nie są takie same dla kolumny {col_index} (od zera) wejściowych zestawów danych (odpowiednio {pozycję DataSet1} i {DataSet2}).|


## <a name="error-0011"></a>0011 błędów  
 Wyjątek występuje, jeśli argument zestawu kolumn przekazywania nie ma zastosowania do żadnej z kolumn zestawu danych.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli określona kolumna nie jest zgodna z żadną kolumną w danym zestawie danych.  

 Możesz również uzyskać ten błąd, jeśli nie wybrano kolumny i co najmniej jedna kolumna jest wymagana do działania modułu.  

**Rozwiązanie:** Zmodyfikuj wybór kolumny w module, tak aby miał zastosowanie do kolumn w zestawie danych.  

 Jeśli moduł wymaga wybrania konkretnej kolumny, takiej jak kolumna etykiety, sprawdź, czy wybrano prawą kolumnę.  

 Jeśli wybrane są nieodpowiednie kolumny, usuń je i ponownie uruchom potok.  

|Komunikaty o wyjątkach|
|------------------------|
|Określony zestaw kolumn nie ma zastosowania do żadnej z kolumn zestawu danych.|
|Określony zestaw kolumn "{column_set}" nie ma zastosowania do żadnej z kolumn zestawu danych.|


## <a name="error-0012"></a>0012 błędów  
 Wyjątek występuje, jeśli nie można utworzyć wystąpienia klasy przy użyciu przekazanego zestawu argumentów.  

**Rozwiązanie:** Ten błąd nie jest funkcjonalny przez użytkownika i zostanie uznany za przestarzały w przyszłych wydaniach.  

|Komunikaty o wyjątkach|
|------------------------|
|Nauczenie modelu, najpierw wybierz model.|
|Niepociąg model ({arg_name}), używanie przeszkolonego modelu.|


## <a name="error-0013"></a>0013 błędów  
 Wyjątek występuje, jeśli osoba docelowa przeniesiona do modułu jest nieprawidłowym typem.  

 Ten błąd występuje zawsze, gdy szkolony model jest niezgodny z połączonym modułem oceniania. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Rozwiązanie:**

Określ typ, który jest wytwarzany przez moduł szkoleń, i określ moduł oceniający, który jest odpowiedni dla tego samouczka. 

Jeśli model został przeszkolony przy użyciu dowolnego z wyspecjalizowanych modułów szkoleniowych, Połącz model szkolony tylko z odpowiednim wyspecjalizowanym modułem oceniania. 


|Typ modelu|Moduł szkoleniowy| Moduł oceniania|
|----|----|----|
|dowolny klasyfikator|[Trenowanie modelu](train-model.md) |[Klasyfikacja modelu](score-model.md)|
|dowolny model regresji|[Trenowanie modelu](train-model.md) |[Klasyfikacja modelu](score-model.md)|

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
|Przeszedł nieprawidłowy typ.|
|Informacje o typie "{arg_name}" są nieprawidłowe.|
|Dowiedz się "{arg_name}" w nieprawidłowym typie "{learner_type}".|
|Przeszedł nieprawidłowy typ. Komunikat o wyjątku: {exception_message}|


## <a name="error-0014"></a>0014 błędów  
 Wyjątek występuje, gdy liczba unikatowych wartości kolumny jest większa niż dozwolona.  

 Ten błąd występuje, gdy kolumna zawiera zbyt wiele unikatowych wartości.  Na przykład ten błąd może pojawić się, jeśli określisz, że kolumna będzie obsługiwana jako dane kategorii, ale w kolumnie jest zbyt wiele unikatowych wartości, aby zezwolić na ukończenie przetwarzania. Ten błąd może również pojawić się, jeśli występuje niezgodność między liczbą unikatowych wartości w dwóch danych wejściowych.   

Błąd unikatowych wartości jest większy niż dozwolona, jeśli spełnione **są następujące warunki** :

- Więcej niż 97% wystąpień jednej kolumny to unikatowe wartości, co oznacza, że prawie wszystkie kategorie różnią się od siebie.
- Jedna kolumna ma więcej niż 1000 unikatowych wartości.

**Rozwiązanie:**

Otwórz moduł, który wygenerował błąd, i zidentyfikuj kolumny używane jako dane wejściowe. W przypadku niektórych modułów można kliknąć prawym przyciskiem myszy dane wejściowe zestawu danych i wybrać polecenie **Wizualizuj** , aby uzyskać statystyki dla poszczególnych kolumn, w tym liczbę unikatowych wartości i ich rozkład.

W przypadku kolumn, które mają być używane do grupowania lub kategoryzacji, wykonaj kroki, aby zmniejszyć liczbę unikatowych wartości w kolumnach. Możesz zmniejszyć różne sposoby, w zależności od typu danych kolumny. 

Zwykle w tym scenariuszu kolumna będąca przyczyną błędu jest bez znaczenia jako funkcja do uczenia modeli. W związku z tym możesz użyć opcji [Edytuj metadane](../algorithm-module-reference/edit-metadata.md) , aby oznaczyć tę kolumnę jako **funkcję czyszczenia** i nie będzie ona używana podczas uczenia modelu. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Nie można znaleźć rozwiązania zgodnego z Twoim scenariuszem? Można przekazać opinię na temat tego tematu, który zawiera nazwę modułu, który wygenerował błąd, oraz typ danych i Kardynalność kolumny. Będziemy używać tych informacji, aby zapewnić bardziej precyzyjną procedurę rozwiązywania problemów dla typowych scenariuszy.   

|Komunikaty o wyjątkach|
|------------------------|
|Liczba unikatowych wartości kolumny jest większa niż dozwolona.|
|Liczba unikatowych wartości w kolumnie: "{column_name}" jest większa niż dozwolona.|
|Liczba unikatowych wartości w kolumnie: "{column_name}" przekracza liczbę krotek wynoszącą {ograniczenia}.|


## <a name="error-0015"></a>0015 błędów  
 Wyjątek występuje, jeśli połączenie z bazą danych nie powiodło się.  

 Ten błąd zostanie wyświetlony w przypadku wprowadzenia nieprawidłowej nazwy konta SQL, hasła, serwera bazy danych lub nazwy bazy danych lub jeśli nie można nawiązać połączenia z bazą danych z powodu problemów z bazą danych lub serwerem.  

**Rozwiązanie:** Sprawdź, czy nazwa konta, hasło, serwer bazy danych i baza danych zostały wprowadzone poprawnie oraz czy określone konto ma prawidłowy poziom uprawnień. Sprawdź, czy baza danych jest obecnie dostępna.  

|Komunikaty o wyjątkach|
|------------------------|
|Wystąpił błąd podczas tworzenia połączenia z bazą danych.|
|Wystąpił błąd podczas nawiązywania połączenia z bazą danych: {connection_str}.|


## <a name="error-0016"></a>0016 błędów  
 Wyjątek występuje, jeśli wejściowe zestawy danych przesłane do modułu powinny mieć zgodne typy kolumn, ale nie.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli typy kolumn przesyłanych w co najmniej dwóch zestawach danych nie są zgodne ze sobą.  

**Rozwiązanie:** Użyj [edycji metadanych](edit-metadata.md) lub zmodyfikuj oryginalny wejściowy zestaw danych<!--, or use [Convert to Dataset](convert-to-dataset.md)--> Aby upewnić się, że typy kolumn są zgodne.  

|Komunikaty o wyjątkach|
|------------------------|
|Kolumny z odpowiednim indeksem w wejściowych zestawach danych mają niezgodne typy.|
|Kolumny "{first_col_names}" są niezgodne między danymi pociągowymi i testowymi.|
|Kolumny "{first_col_names}" i "{second_col_names}" są niezgodne.|
|Typy elementów kolumn są niezgodne dla kolumny "{first_col_names}" (liczony od zera) zestawów danych wejściowych (odpowiednio {first_dataset_names} i {second_dataset_names}).|


## <a name="error-0017"></a>0017 błędów  
 Wyjątek występuje, jeśli wybrana kolumna używa typu danych, który nie jest obsługiwany przez bieżący moduł.  

 Na przykład ten błąd może pojawić się w Azure Machine Learning, jeśli wybór kolumny zawiera kolumnę z typem danych, który nie może być przetwarzany przez moduł, taki jak kolumna ciągów dla operacji matematycznych, lub kolumna oceny, w której jest wymagana kolumna funkcji kategorii.  

**Rozwiązanie:**
 1. Określ kolumnę, której dotyczy problem.
 2. Zapoznaj się z wymaganiami modułu.
 3. Zmodyfikuj kolumnę, aby była zgodna z wymaganiami. Może być konieczne użycie kilku z następujących modułów, aby wprowadzić zmiany, w zależności od kolumny i konwersji, którą próbujesz wykonać:
    + Użyj opcji [Edytuj metadane](edit-metadata.md) , aby zmienić typ danych kolumn lub zmienić użycie kolumny z funkcji na wartość numeryczną, kategorii na inne niż kategorii i tak dalej.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Jako ostatni należy zmodyfikować oryginalny wejściowy zestaw danych.

> [!TIP]
> Nie można znaleźć rozwiązania zgodnego z Twoim scenariuszem? Można przekazać opinię na temat tego tematu, który zawiera nazwę modułu, który wygenerował błąd, oraz typ danych i Kardynalność kolumny. Będziemy używać tych informacji, aby zapewnić bardziej precyzyjną procedurę rozwiązywania problemów dla typowych scenariuszy. 

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przetworzyć kolumny bieżącego typu. Ten typ nie jest obsługiwany przez moduł.|
|Nie można przetworzyć kolumny typu {col_type}. Ten typ nie jest obsługiwany przez moduł.|
|Nie można przetworzyć kolumny "{col_name}" typu {col_type}. Ten typ nie jest obsługiwany przez moduł.|
|Nie można przetworzyć kolumny "{col_name}" typu {col_type}. Ten typ nie jest obsługiwany przez moduł. Nazwa parametru: {arg_name}.|


## <a name="error-0018"></a>0018 błędów  
 Wyjątek występuje, jeśli wejściowy zestaw danych jest nieprawidłowy.  

**Rozwiązanie:** Ten błąd w Azure Machine Learning może występować w wielu kontekstach, więc nie istnieje pojedyncze rozwiązanie. Ogólnie rzecz biorąc, błąd wskazuje, że dane podane jako dane wejściowe modułu mają nieprawidłową liczbę kolumn lub że typ danych nie jest zgodny z wymaganiami modułu. Na przykład:  

-   Moduł wymaga kolumny etykieta, ale żadna kolumna nie jest oznaczona jako etykieta lub nie wybrano jeszcze kolumny etykieta.  
  
-   Moduł wymaga, aby dane były kategorii, ale dane są liczbowe.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Dane mają zły format.  
  
-   Importowane dane zawierają nieprawidłowe znaki, złe wartości lub wartości spoza zakresu.  
-   Kolumna jest pusta lub zawiera zbyt wiele wartości.  

 Aby określić wymagania i sposób, w jaki dane mogą się zapoznać, przejrzyj temat pomocy dla modułu, który będzie zużywał zestaw danych jako dane wejściowe.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Komunikaty o wyjątkach|
|------------------------|
|Element dataset jest nieprawidłowy.|
|{pozycję DataSet1} zawiera nieprawidłowe dane.|
|{pozycję DataSet1} i {DataSet2} należy zapewnić spójność columnwise.|
|{pozycję DataSet1} zawiera nieprawidłowe dane, {powód}.|
|{pozycję DataSet1} zawiera {invalid_data_category}. {troubleshoot_hint}|
|{pozycję DataSet1} jest nieprawidłowy, {przyczyna}. {troubleshoot_hint}|


## <a name="error-0019"></a>0019 błędów  
 Wyjątek występuje, jeśli kolumna powinna zawierać posortowane wartości, ale nie.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli określone wartości kolumn są nieuporządkowane.  

**Rozwiązanie:** Posortuj wartości kolumn, ręcznie modyfikując wejściowy zestaw danych i ponownie uruchom moduł.  

|Komunikaty o wyjątkach|
|------------------------|
|Wartości w kolumnie nie są sortowane.|
|Wartości w kolumnie "{col_index}" nie są sortowane.|
|Wartości w kolumnie "{col_index}" zestawu danych "{DataSet}" nie są sortowane.|
|Wartości w argumencie "{arg_name}" nie są sortowane w kolejności "{sorting_order}".|


## <a name="error-0020"></a>0020 błędów  
 Wyjątek występuje, jeśli liczba kolumn w niektórych zestawach danych przeniesiona do modułu jest za mała.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli nie wybrano wystarczającej liczby kolumn dla modułu.  

**Rozwiązanie:** Ponownie odwiedź moduł i upewnij się, że wybrano poprawną liczbę kolumn selektora kolumn.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba kolumn w wejściowym zestawie danych jest mniejsza niż dozwolona wartość minimalna.|
|Liczba kolumn w wejściowym zestawie danych "{arg_name}" jest mniejsza niż dozwolona wartość minimalna.|
|Liczba kolumn w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna liczba kolumn: {required_columns_count}.|
|Liczba kolumn w wejściowym zestawie danych "{arg_name}" jest mniejsza niż dozwolona minimalna liczba kolumn: {required_columns_count}.|


## <a name="error-0021"></a>0021 błędów  
 Wyjątek występuje, jeśli liczba wierszy w niektórych zestawach danych przeniesiona do modułu jest za mała.  

 Ten błąd pojawia się w Azure Machine Learning, gdy w zestawie danych nie ma wystarczającej liczby wierszy do wykonania określonej operacji. Na przykład ten błąd może pojawić się, jeśli wejściowy zestaw danych jest pusty lub jeśli próbujesz wykonać operację, która wymaga podania pewnej minimalnej liczby wierszy. Takie operacje mogą obejmować (ale nie ograniczone do) grupowanie lub klasyfikację na podstawie metod statystycznych, niektórych typów pakowania i uczenia się z liczbą.  

**Rozwiązanie:**

 + Otwórz moduł, który zwrócił błąd, i sprawdź wejściowy zestaw danych i właściwości modułu. 
 + Sprawdź, czy wejściowy zestaw danych nie jest pusty i czy jest wystarczająco dużo wierszy danych, aby spełnić wymagania opisane w pomocy modułu.  
 + Jeśli dane są ładowane z zewnętrznego źródła, upewnij się, że źródło danych jest dostępne i że nie ma żadnych błędów lub zmian w definicji danych, które mogłyby spowodować, że proces importowania uzyska mniej wierszy.
 + Jeśli wykonujesz operację na nadrzędnym strumieniu danych modułu, który może mieć wpływ na typ danych lub liczbę wartości, takich jak czyszczenie, dzielenie lub sprzęganie operacji, sprawdź wyniki tych operacji, aby określić liczbę zwracanych wierszy.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba wierszy w wejściowym zestawie danych jest mniejsza niż dozwolona wartość minimalna.|
|Liczba wierszy w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna liczba wierszy: {required_rows_count}.|
|Liczba wierszy w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna liczba wierszy: {required_rows_count}. dotycząca|
|Liczba wierszy w wejściowym zestawie danych "{arg_name}" jest mniejsza niż dozwolona minimalna liczba wierszy: {required_rows_count}.|
|Liczba wierszy w wejściowym zestawie danych "{arg_name}" to {actual_rows_count}, mniejsza niż dozwolona minimalna liczba wierszy: {required_rows_count}.|
|Liczba wierszy "{row_type}" w wejściowym zestawie danych "{arg_name}" to {actual_rows_count}, mniejsza niż dozwolona minimalna liczba wierszy: {required_rows_count}.|


## <a name="error-0022"></a>0022 błędów  
 Wyjątek występuje, jeśli liczba wybranych kolumn w wejściowym zestawie danych nie jest równa oczekiwanej liczbie.  

 Ten błąd w Azure Machine Learning może wystąpić, gdy moduł podrzędny lub operacja wymaga określonej liczby kolumn lub danych wejściowych, a podano zbyt mało lub zbyt wiele kolumn lub danych wejściowych. Na przykład:  

-   Należy określić pojedynczą kolumnę etykiety lub kolumnę klucza i przypadkowo zaznaczyć wiele kolumn.  
  
-   Zmieniasz nazwy kolumn, ale podano więcej lub mniej nazw niż w przypadku kolumn.  
  
-   Liczba kolumn w źródle lub miejscu docelowym została zmieniona lub nie jest zgodna z liczbą kolumn używanych przez moduł.  
  
-   Podano rozdzieloną przecinkami listę wartości dla danych wejściowych, ale liczba wartości nie jest zgodna lub wiele danych wejściowych nie jest obsługiwanych.  

**Rozwiązanie:** Ponownie odwiedź moduł i zaznacz kolumnę, aby upewnić się, że wybrano poprawną liczbę kolumn. Sprawdź dane wyjściowe modułów nadrzędnych i wymagania operacji podrzędnych.  

 Jeśli użyto jednej z opcji wyboru kolumn, które mogą wybrać wiele kolumn (indeksów kolumn, wszystkie funkcje, wszystkie liczbowe itp.), sprawdź dokładną liczbę kolumn zwracanych przez zaznaczenie.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Sprawdź, czy liczba lub typ kolumn nadrzędnych nie została zmieniona.  

 Jeśli używasz zestawu danych rekomendacji do uczenia modelu, pamiętaj, że zaleca się, aby polecał ograniczoną liczbę kolumn, odpowiadającą par elementów użytkownika lub klasyfikację elementów użytkownika. Usuń dodatkowe kolumny przed przeszkoleniem modelu lub zestawami danych rekomendacji. Aby uzyskać więcej informacji, zobacz [Split Data](split-data.md).  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba wybranych kolumn w wejściowym zestawie danych nie jest równa oczekiwanej liczbie.|
|Liczba wybranych kolumn w wejściowym zestawie danych nie jest równa {expected_col_count}.|
|Wzorzec wyboru kolumny "{selection_pattern_friendly_name}" zawiera liczbę wybranych kolumn w wejściowym zestawie danych, które nie są równe {expected_col_count}.|
|Wzorzec wyboru kolumny "{selection_pattern_friendly_name}" oczekuje, że podano kolumny {expected_col_count} wybrane w wejściowym zestawie danych, ale kolumny {selected_col_count} są w rzeczywistości udostępniane.|


## <a name="error-0023"></a>0023 błędów  

Wyjątek występuje, jeśli docelowa kolumna wejściowego zestawu danych jest nieprawidłowa dla bieżącego modułu Trainer.  

Ten błąd w Azure Machine Learning występuje, jeśli kolumna docelowa (wybrana w parametrach modułu) nie jest prawidłowym typem danych, zawiera wszystkie brakujące wartości lub nie została kategorii zgodnie z oczekiwaniami.  

**Rozwiązanie:** Ponownie odwiedź dane wejściowe modułu, aby sprawdzić zawartość kolumny etykieta/cel. Upewnij się, że nie ma wszystkich wartości, które nie istnieją. Jeśli moduł oczekuje kolumny docelowej do kategorii, upewnij się, że w kolumnie Target znajduje się więcej niż jedna wartość DISTINCT.  

|Komunikaty o wyjątkach|
|------------------------|
|Wejściowy zestaw danych zawiera nieobsługiwaną kolumnę docelową.|
|Wejściowy zestaw danych zawiera nieobsługiwaną kolumnę docelową "{column_index}".|
|Wejściowy zestaw danych zawiera nieobsługiwaną kolumnę docelową "{column_index}" dla dowiedzenia typu {learner_type}.|


## <a name="error-0024"></a>0024 błędów  
Wyjątek występuje, jeśli zestaw danych nie zawiera kolumny Label.  

 Ten błąd w Azure Machine Learning występuje, gdy moduł wymaga kolumny etykieta, a zestaw danych nie ma kolumny etykieta. Na przykład Ocena zestawu danych z oceną ocenia zazwyczaj wymaga, aby kolumna etykiet była obecna, aby obliczyć metryki dokładności.  

Może się również zdarzyć, że kolumna etykieta jest obecna w zestawie danych, ale nie została prawidłowo wykryta przez Azure Machine Learning.

**Rozwiązanie:**

+ Otwórz moduł, który wygenerował błąd, i ustal, czy kolumna etykieta jest obecna. Nazwa lub typ danych kolumny nie ma znaczenia, pod warunkiem, że kolumna zawiera pojedynczy wynik (lub zmienną zależną), którą próbujesz przewidzieć. Jeśli nie masz pewności, która kolumna ma etykietę, poszukaj nazwy generycznej, takiej jak  *Class* lub *Target*. 
+  Jeśli zestaw danych nie zawiera kolumny etykieta, istnieje możliwość, że kolumna etykieta została jawnie lub przypadkowo usunięta. Może to być również, że zestaw danych nie jest danymi wyjściowymi nadrzędnego modułu oceniania.
+ Aby jawnie oznaczyć kolumnę jako kolumnę etykiety, Dodaj moduł [Edytuj metadane](edit-metadata.md) i Połącz zestaw danych. Zaznacz tylko kolumnę etykieta i wybierz pozycję **etykieta** z listy rozwijanej **pola** . 
+ Jeśli jako etykieta została wybrana nieprawidłowa kolumna, możesz wybrać pozycję **Wyczyść etykietę** z **pól** , aby naprawić metadane w kolumnie. 
  
|Komunikaty o wyjątkach|
|------------------------|
|Brak kolumny etykiety w elemencie dataset.|
|Brak kolumny etykiety w "{dataset_name}".|


## <a name="error-0025"></a>Błąd 0,025  
 Wyjątek występuje, jeśli zestaw danych nie zawiera kolumny wyników.  

 Ten błąd w Azure Machine Learning występuje, jeśli dane wejściowe dla modelu szacowania nie zawierają prawidłowych kolumn oceny. Na przykład użytkownik próbuje dokonać oceny zestawu danych przed jego oceną przy użyciu prawidłowego, przeszkolonego modelu lub kolumna punktacji została jawnie porzucona. Ten wyjątek występuje również wtedy, gdy kolumny oceny w dwóch zestawach danych są niezgodne. Można na przykład próbować porównać dokładność regresor liniowego z klasyfikatorem binarnym.  

**Rozwiązanie:** Odwiedzaj dane wejściowe do modelu oceny i sprawdź, czy zawiera on co najmniej jedną kolumnę oceny. Jeśli nie, zestaw danych nie został oceniony lub kolumny wynikowe zostały porzucone w module nadrzędnym.  

|Komunikaty o wyjątkach|
|------------------------|
|Brak kolumny punktacji w elemencie dataset.|
|Brak kolumny punktacji w "{dataset_name}".|
|Brak kolumny Score w "{dataset_name}", która jest generowana przez "{learner_type}". Poznanie zestawu danych przy użyciu poprawnego typu.|


## <a name="error-0026"></a>0026 błędów  
 Wyjątek występuje, jeśli kolumny o tej samej nazwie są niedozwolone.  

 Ten błąd w Azure Machine Learning występuje, jeśli wiele kolumn ma taką samą nazwę. Jednym ze sposobów na wyświetlenie tego błędu jest to, że zestaw danych nie ma wiersza nagłówka, a nazwy kolumn są automatycznie przypisywane: Col0, Kol1 itd.  

**Rozwiązanie:** Jeśli kolumny mają tę samą nazwę, Wstaw moduł [Edytuj metadane](edit-metadata.md) między wejściowym zestawem danych i modułem. Użyj selektora kolumn w obszarze [Edytuj metadane](edit-metadata.md) , aby wybrać kolumny, które mają zostać zmienione, wpisując nowe nazwy do **nowych nazw kolumn** pole tekstowe.  

|Komunikaty o wyjątkach|
|------------------------|
|W argumentach są określone równe nazwy kolumn. Nie można używać równych nazw kolumn w module.|
|Takie same nazwy kolumn w argumentach "{arg_name_1}" i "{arg_name_2}" są niedozwolone. Podaj różne nazwy.|


## <a name="error-0027"></a>0027 błędów  
 Wyjątek występuje w przypadku, gdy dwa obiekty muszą mieć taki sam rozmiar, ale nie są.  

 Jest to typowy błąd w Azure Machine Learning i może być spowodowany przez wiele warunków.  

**Rozwiązanie:** Nie ma konkretnego rozwiązania. Można jednak sprawdzić, czy są spełnione następujące warunki:  

-   W przypadku zmieniania nazw kolumn upewnij się, że każda lista (kolumny wejściowe i lista nowych nazw) ma taką samą liczbę elementów.  
  
-   W przypadku sprzęgania lub łączenia dwóch zestawów danych upewnij się, że mają one ten sam schemat.  
  
-   W przypadku sprzęgania dwóch zestawów danych z wieloma kolumnami upewnij się, że kolumny klucza mają ten sam typ danych i wybierz opcję **Zezwalaj na duplikaty i Zachowaj kolejność kolumn w zaznaczeniu**.  

|Komunikaty o wyjątkach|
|------------------------|
|Rozmiar zakończonych obiektów jest niespójny.|
|Rozmiar "{friendly_name1}" jest niespójny z rozmiarem "{friendly_name2}".|


## <a name="error-0028"></a>0028 błędów  
 Wyjątek występuje w przypadku, gdy zestaw kolumn zawiera zduplikowane nazwy kolumn i nie jest dozwolony.  

 Ten błąd w Azure Machine Learning występuje, gdy nazwy kolumn są zduplikowane; to nie jest unikatowa.  

**Rozwiązanie:** Jeśli dowolna kolumna ma taką samą nazwę, Dodaj wystąpienie [edycji metadanych](edit-metadata.md) między wejściowym zestawem danych i modułem wywołującym błąd. Użyj selektora kolumn w obszarze [Edytuj metadane](edit-metadata.md) , aby wybrać kolumny, które chcesz zmienić, a następnie wpisz nazwy nowych kolumn w **nowej kolumnie** pole tekstowe. W przypadku zmiany nazwy wielu kolumn upewnij się, że wartości wpisane w **nowych nazwach kolumn** są unikatowe.  

|Komunikaty o wyjątkach|
|------------------------|
|Zestaw kolumn zawiera zduplikowane nazwy kolumn.|
|Nazwa "{duplicated_name}" jest zduplikowana.|
|Nazwa "{duplicated_name}" jest zduplikowana w "{arg_name}".|
|Nazwa "{duplicated_name}" jest zduplikowana. Szczegóły: {szczegóły}|


## <a name="error-0029"></a>0029 błędów  
 Wyjątek występuje w przypadku przekazywania nieprawidłowego identyfikatora URI.  

 Ten błąd w Azure Machine Learning występuje w przypadku przekazanie nieprawidłowego identyfikatora URI.  Ten błąd zostanie wyświetlony w przypadku spełnienia dowolnego z następujących warunków:  

-   Publiczny lub identyfikator URI sygnatury dostępu współdzielonego podany dla Blob Storage platformy Azure dla odczytu lub zapisu zawiera błąd.  
  
-   Przedział czasu dla sygnatury dostępu współdzielonego wygasł.  
  
-   Adres URL sieci Web za pośrednictwem źródła HTTP reprezentuje plik lub identyfikator URI sprzężenia zwrotnego.  
  
-   Adres URL sieci Web za pośrednictwem protokołu HTTP zawiera niepoprawnie sformatowany adres URL.  
  
-   Nie można rozpoznać adresu URL przez źródło zdalne.  

**Rozwiązanie:** Ponownie odwiedź moduł i sprawdź format identyfikatora URI. Jeśli źródło danych jest adresem URL sieci Web za pośrednictwem protokołu HTTP, sprawdź, czy zamierzone źródło nie jest plikiem lub identyfikatorem URI sprzężenia zwrotnego (localhost).  

|Komunikaty o wyjątkach|
|------------------------|
|Przeszedł nieprawidłowy identyfikator URI.|
|Identyfikator URI "{invalid_url}" jest nieprawidłowy.|


## <a name="error-0030"></a>0030 błędów  
 Wyjątek występuje w przypadku, gdy nie można pobrać pliku.  

 Ten wyjątek w Azure Machine Learning występuje, gdy nie można pobrać pliku. Ten wyjątek zostanie wyświetlony, jeśli próba odczytania ze źródła HTTP zakończyła się niepowodzeniem po trzech (3) ponownych próbach.  

**Rozwiązanie:** Sprawdź, czy identyfikator URI źródła HTTP jest poprawny i czy lokacja jest obecnie dostępna za pośrednictwem Internetu.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można pobrać pliku.|
|Wystąpił błąd podczas pobierania pliku: {file_url}.|


## <a name="error-0031"></a>0031 błędów  
 Wyjątek występuje, jeśli liczba kolumn w zestawie kolumn jest mniejsza niż wymagana.  

 Ten błąd w Azure Machine Learning występuje, jeśli wybrana liczba kolumn jest mniejsza niż wymagana.  Ten błąd zostanie wyświetlony, jeśli minimalna wymagana liczba kolumn nie została wybrana.  

**Rozwiązanie:** Dodaj dodatkowe kolumny do zaznaczenia kolumny przy użyciu **selektora kolumn**.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba kolumn w zestawie kolumn jest mniejsza niż wymagana.|
|Dla argumentu wejściowego "{arg_name}" należy określić co najmniej następującą kolumnę: {required_columns_count}.|
|Dla argumentu wejściowego "{arg_name}" należy określić co najmniej następującą kolumnę: {required_columns_count}. Rzeczywista liczba określonych kolumn to {input_columns_count}.|


## <a name="error-0032"></a>Błąd 0032  
 Wyjątek występuje, jeśli argument nie jest liczbą.  

 Ten błąd zostanie wyświetlony w Azure Machine Learning, jeśli argument jest typu Double lub NaN.  

**Rozwiązanie:** Zmodyfikuj określony argument, aby użyć prawidłowej wartości.  

|Komunikaty o wyjątkach|
|------------------------|
|Argument nie jest liczbą.|
|"{arg_name}" nie jest liczbą.|


## <a name="error-0033"></a>Błąd 0033  
 Wyjątek występuje, jeśli argument jest nieskończony.  

 Ten błąd w Azure Machine Learning występuje, jeśli argument jest nieskończony. Ten błąd zostanie wyświetlony, jeśli argument ma wartość `double.NegativeInfinity` lub `double.PositiveInfinity` .  

**Rozwiązanie:** Zmodyfikuj określony argument jako prawidłową wartość.  

|Komunikaty o wyjątkach|
|------------------------|
|Argument musi być skończona.|
|"{arg_name}" nie jest skończona.|
|Kolumna "{column_name}" zawiera nieskończone wartości.|


## <a name="error-0034"></a>Błąd 0034  
 Wyjątek występuje, jeśli istnieje więcej niż jedna Klasyfikacja dla danej pary elementu użytkownika.  

 Ten błąd w Azure Machine Learning występuje, jeśli para elementu użytkownika ma więcej niż jedną wartość rankingu.  

**Rozwiązanie:** Upewnij się, że para elementów użytkownika posiada tylko jedną wartość klasyfikacji.  

|Komunikaty o wyjątkach|
|------------------------|
|Istnieje więcej niż jedna Klasyfikacja dla wartości w zestawie danych.|
|Więcej niż jedna Klasyfikacja dla użytkownika {User} i elementu {Item} w tabeli danych prognozowania klasyfikacji.|
|Więcej niż jedna Klasyfikacja dla użytkownika {User} i elementu {Item} w {DataSet}.|


## <a name="error-0035"></a>Błąd 0035  
 Wyjątek występuje, jeśli nie podano żadnych funkcji dla danego użytkownika lub elementu.  

 Ten błąd w Azure Machine Learning powoduje, że próbujesz użyć modelu rekomendacji do oceniania, ale nie można odnaleźć wektora funkcji.  

**Rozwiązanie:**

Zalecenie Matchbox ma pewne wymagania, które należy spełnić podczas korzystania z funkcji elementu lub funkcji użytkownika.  Ten błąd wskazuje, że brakuje wektora funkcji dla użytkownika lub elementu, który został dostarczony jako dane wejściowe. Upewnij się, że wektor funkcji jest dostępny w danych dla każdego użytkownika lub elementu.  

 Na przykład w przypadku przeszkolonego modelu rekomendacji przy użyciu funkcji, takich jak wiek, lokalizacja lub dochód użytkownika, ale teraz chcesz utworzyć wyniki dla nowych użytkowników, którzy nie byli w trakcie szkolenia, musisz zapewnić pewien odpowiedni zestaw funkcji (tj. wieku, lokalizacji i wartości przychodu) dla nowych użytkowników w celu zapewnienia odpowiednich prognoz. 

 Jeśli nie masz żadnych funkcji dla tych użytkowników, weź pod uwagę Inżynieria funkcji, aby generować odpowiednie funkcje.  Jeśli na przykład nie masz indywidualnych wartości wieku lub dochodu użytkownika, możesz wygenerować przybliżone wartości do użycia dla grupy użytkowników. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Rozwiązanie nie dotyczy Twojego przypadku? Zapraszamy do wysłania opinii na temat tego artykułu i podania informacji o tym scenariuszu, w tym modułu i liczbie wierszy w kolumnie. Będziemy używać tych informacji w celu zapewnienia bardziej szczegółowych kroków rozwiązywania problemów w przyszłości.

|Komunikaty o wyjątkach|
|------------------------|
|Nie podano żadnych funkcji dla wymaganego użytkownika lub elementu.|
|Funkcja dla {required_feature_name} jest wymagana, ale nie została podana.|


## <a name="error-0036"></a>Błąd 0036  
 Wyjątek występuje, jeśli dla danego użytkownika lub elementu podano wiele wektorów funkcji.  

 Ten błąd w Azure Machine Learning występuje, jeśli wektor funkcji jest zdefiniowany więcej niż raz.  

**Rozwiązanie:** Upewnij się, że wektor funkcji nie jest zdefiniowany więcej niż raz.  

|Komunikaty o wyjątkach|
|------------------------|
|Zduplikowana definicja funkcji dla użytkownika lub elementu.|


## <a name="error-0037"></a>Błąd 0037  
 Wyjątek występuje, jeśli określono wiele kolumn etykiet i tylko jeden z nich jest dozwolony.  

 Ten błąd w Azure Machine Learning występuje, gdy została wybrana więcej niż jedna kolumna jako nowa kolumna etykiety. Większość nadzorowanych algorytmów uczenia wymaga, aby jedna kolumna była oznaczona jako element docelowy lub etykieta.  

**Rozwiązanie:** Pamiętaj o zaznaczeniu pojedynczej kolumny jako nowej kolumny etykieta.  

|Komunikaty o wyjątkach|
|------------------------|
|Określono wiele kolumn etykiet.|
|W "{dataset_name}" określono wiele kolumn etykiet.|


## <a name="error-0039"></a>Błąd 0039  
 Wyjątek występuje, jeśli operacja zakończyła się niepowodzeniem.  

 Ten błąd w Azure Machine Learning występuje, gdy nie można ukończyć operacji wewnętrznej.  

**Rozwiązanie:** Ten błąd jest spowodowany przez wiele warunków i nie ma konkretnego rozwiązania.  
 Poniższa tabela zawiera komunikaty ogólne dla tego błędu, po którym następuje określony opis warunku. 

 Jeśli żadne szczegóły nie są dostępne, [firma Microsoft Q&stronie pytania dotyczącej wysyłania opinii](/answers/topics/azure-machine-learning-studio-classic.html) i zawiera informacje o modułach, które wygenerowały błąd i powiązane warunki.

|Komunikaty o wyjątkach|
|------------------------|
|Operacja nie powiodła się.|
|Wystąpił błąd podczas kończenia operacji: "{failed_operation}".|
|Wystąpił błąd podczas kończenia operacji: "{failed_operation}". Przyczyna: "{powód}".|


## <a name="error-0042"></a>Błąd 0042  
 Wyjątek występuje, gdy nie jest możliwe przekonwertowanie kolumny na inny typ.  

 Ten błąd w Azure Machine Learning występuje, gdy nie jest możliwe przekonwertowanie kolumny na określony typ.  Ten błąd zostanie wyświetlony, jeśli moduł wymaga określonego typu danych, takiego jak DateTime, text, Number zmiennoprzecinkowa lub Integer, ale nie jest możliwe przekonwertowanie istniejącej kolumny na wymagany typ.  

Na przykład można wybrać kolumnę i spróbować skonwertować ją na typ danych liczbowych do użycia w operacji matematycznej i uzyskać ten błąd, jeśli kolumna zawiera nieprawidłowe dane. 

Kolejną przyczyną tego błędu może być próba użycia kolumny zawierającej liczby zmiennoprzecinkowe lub wiele unikatowych wartości jako kolumny kategorii. 

**Rozwiązanie:**

+ Otwórz stronę pomocy dla modułu, który wygenerował błąd, i Sprawdź wymagania dotyczące typu danych.
+ Przejrzyj typy danych kolumn w wejściowym zestawie danych.
+ Sprawdź dane pochodzące z tego — nazywane źródłami danych bez schematu.
+ Sprawdź zestaw danych pod kątem brakujących wartości lub znaków specjalnych, które mogą blokować konwersję do żądanego typu danych. 
    + Numeryczne typy danych powinny być spójne: na przykład wyszukaj liczby zmiennoprzecinkowe w kolumnie liczb całkowitych.
    + Wyszukaj ciągi tekstowe lub NA wartości w kolumnie liczbowej. 
    + Wartości logiczne można przekonwertować na odpowiednią reprezentację w zależności od wymaganego typu danych.
    + Sprawdzanie kolumn tekstowych dla znaków innych niż Unicode, znaków tabulacji lub znaków kontrolnych
    + Dane daty i godziny powinny być spójne, aby uniknąć błędów modelowania, ale oczyszczanie może być złożone z powodu wielu formatów. Rozważ użycie <!--the [Execute R Script](execute-r-script.md) or -->[Wykonaj moduły skryptów języka Python](execute-python-script.md) , aby przeprowadzić oczyszczanie.  
+ W razie potrzeby zmodyfikuj wartości w wejściowym zestawie danych, aby można było pomyślnie przekonwertować kolumnę. Modyfikacja może obejmować operacje pakowania, obcinania lub zaokrąglania, eliminację elementów odstających lub nie przypisywania brakujących wartości. Zapoznaj się z następującymi artykułami dotyczącymi niektórych typowych scenariuszy transformacji danych w usłudze Machine Learning:
    + [Czyszczenie brakujących danych](clean-missing-data.md)
    + [Normalizowanie danych](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Czy rozwiązanie jest niejasne lub nie dotyczy Twojego przypadku? Zapraszamy do wysłania opinii na temat tego artykułu i podania informacji o tym scenariuszu, w tym modułu i typu danych kolumny. Będziemy używać tych informacji w celu zapewnienia bardziej szczegółowych kroków rozwiązywania problemów w przyszłości.  

|Komunikaty o wyjątkach|
|------------------------|
|Konwersja nie jest dozwolona.|
|Nie można skonwertować kolumny typu {type1} do kolumny typu {Type2}.|
|Nie można skonwertować kolumny "{col_name1}" typu {type1} do kolumny typu {Type2}.|
|Nie można skonwertować kolumny "{col_name1}" typu {type1} do kolumny "{col_name2}" typu {Type2}.|


## <a name="error-0044"></a>Błąd 0044  
 Wyjątek występuje, gdy nie można utworzyć typu elementu kolumny z istniejących wartości.  

 Ten błąd w Azure Machine Learning występuje, gdy nie można wywnioskować typu kolumny lub kolumn w zestawie danych. Jest to zwykle wykonywane podczas łączenia dwóch lub większej liczby zestawów danych z różnymi typami elementów. Jeśli Azure Machine Learning nie może określić typu wspólnego, który może reprezentować wszystkie wartości w kolumnie lub kolumnach bez utraty informacji, wygeneruje ten błąd.  

**Rozwiązanie:** Upewnij się, że wszystkie wartości w danej kolumnie w łączonych zestawach danych są takie same jak typ (numeric, Boolean, kategorii, String, Date itp.) lub można go przekształcić na ten sam typ.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można utworzyć elementu pochodny dla kolumny.|
|Nie można utworzyć pochodnej typu elementu dla kolumny "{column_name}" — wszystkie elementy są odwołaniami o wartości null.|
|Nie można utworzyć pochodnej typu elementu dla kolumny "{column_name}" zestawu danych "{dataset_name}" — wszystkie elementy są odwołaniami o wartości null.|


## <a name="error-0045"></a>Błąd 0045  
 Wyjątek występuje, gdy nie można utworzyć kolumny z powodu typów elementów mieszanych w źródle.  

 Ten błąd w Azure Machine Learning jest tworzony, gdy typy elementów dwóch połączonych zestawów danych są różne.  

**Rozwiązanie:** Upewnij się, że wszystkie wartości w danej kolumnie w łączonych zestawach danych są tego samego typu (liczbowe, Boolean, kategorii, String, Date itp.).  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można utworzyć kolumny z mieszanymi typami elementów.|
|Nie można utworzyć kolumny o identyfikatorze "{column_id}" typów elementów mieszanych:<br />Typ danych [{row_1}, {column_id}] to "{type_1}". <br />Typ danych [{row_2}, {column_id}] to "{type_2}".|
|Nie można utworzyć kolumny o identyfikatorze "{column_id}" typów elementów mieszanych:<br />Typ w fragmencie {chunk_id_1} to "{type_1}". <br />Typ w fragmencie {chunk_id_2} to "{type_2}" o rozmiarze fragmentu: {chunk_size}.|


## <a name="error-0046"></a>Błąd 0046  
 Wyjątek występuje, gdy nie można utworzyć katalogu w określonej ścieżce.  

 Ten błąd w Azure Machine Learning występuje, gdy nie można utworzyć katalogu w określonej ścieżce. Ten błąd zostanie wyświetlony, jeśli jakakolwiek część ścieżki do katalogu wyjściowego zapytania Hive jest niepoprawna lub niedostępna.  

**Rozwiązanie:** Ponownie odwiedź moduł i sprawdź, czy ścieżka katalogu jest poprawnie sformatowana i czy jest dostępna z bieżącymi poświadczeniami.  

|Komunikaty o wyjątkach|
|------------------------|
|Określ prawidłowy katalog wyjściowy.|
|Nie można utworzyć katalogu: {Path}. Określ prawidłową ścieżkę.|


## <a name="error-0047"></a>Błąd 0047  
 Wyjątek występuje, jeśli liczba kolumn funkcji w niektórych zestawach danych przeniesiona do modułu jest za mała.  

 Ten błąd w Azure Machine Learning występuje, jeśli wejściowy zestaw danych do szkolenia nie zawiera minimalnej liczby kolumn wymaganych przez algorytm. Zazwyczaj zestaw danych jest pusty lub zawiera tylko kolumny szkoleniowe.  

**Rozwiązanie:** Ponownie przejdź do wejściowego zestawu danych, aby upewnić się, że istnieje co najmniej jedna dodatkowa kolumna poza kolumną etykieta.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba kolumn funkcji w wejściowym zestawie danych jest mniejsza niż dozwolona wartość minimalna.|
|Liczba kolumn funkcji w wejściowym zestawie danych jest mniejsza niż dozwolona minimalna liczba kolumn: {required_columns_count}.|
|Liczba kolumn funkcji w wejściowym zestawie danych "{arg_name}" jest mniejsza niż dozwolona minimalna liczba kolumn: {required_columns_count}.|


## <a name="error-0048"></a>Błąd 0048  
 Wyjątek występuje w przypadku, gdy nie można otworzyć pliku.  

 Ten błąd w Azure Machine Learning występuje, gdy nie można otworzyć pliku do odczytu lub zapisu. Ten błąd może pojawić się z następujących powodów:  

-   Kontener lub plik (BLOB) nie istnieje  
  
-   Poziom dostępu pliku lub kontenera nie pozwala na dostęp do pliku  
  
-   Plik jest za duży, aby można go było odczytać lub mieć zły format  

**Rozwiązanie:** Ponownie odwiedzanie modułu i pliku, który próbujesz odczytać.  

 Sprawdź, czy nazwy kontenera i pliku są poprawne.  

 Aby sprawdzić, czy masz uprawnienia dostępu do pliku, użyj klasycznego portalu Azure lub narzędzia Azure Storage.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Komunikaty o wyjątkach|
|------------------------|
|Nie można otworzyć pliku.|
|Wystąpił błąd podczas otwierania pliku: {file_name}.|
|Wystąpił błąd podczas otwierania pliku: {file_name}. Komunikat o wyjątku magazynu: {Exception}.|


## <a name="error-0049"></a>Błąd 0049  
 Wyjątek występuje w przypadku, gdy nie można przeanalizować pliku.  

 Ten błąd w Azure Machine Learning występuje, gdy nie można przeanalizować pliku. Ten błąd zostanie wyświetlony, jeśli format pliku wybrany w module [Importuj dane](import-data.md) nie jest zgodny z rzeczywistym formatem pliku lub jeśli plik zawiera nierozpoznawalny znak.  

**Rozwiązanie:** Ponownie odwiedzaj moduł i popraw wybór formatu pliku, jeśli nie jest zgodny z formatem pliku. Jeśli to możliwe, sprawdź, czy plik nie zawiera żadnych niedozwolonych znaków.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przeanalizować pliku.|
|Wystąpił błąd podczas analizowania pliku {file_format}.|
|Wystąpił błąd podczas analizowania pliku {file_format}: {file_name}.|
|Wystąpił błąd podczas analizowania pliku {file_format}. Przyczyna: {failure_reason}.|
|Wystąpił błąd podczas analizowania pliku {file_format}: {file_name}. Przyczyna: {failure_reason}.|


## <a name="error-0052"></a>Błąd 0052  
 Wyjątek występuje, jeśli klucz konta usługi Azure Storage został określony nieprawidłowo.  

 Ten błąd w Azure Machine Learning występuje, jeśli klucz używany do uzyskiwania dostępu do konta usługi Azure Storage jest niepoprawny. Na przykład ten błąd może pojawić się, jeśli klucz usługi Azure Storage został obcięty podczas kopiowania i wklejania, lub jeśli użyto nieprawidłowego klucza.  

 Aby uzyskać więcej informacji na temat uzyskiwania klucza dla konta usługi Azure Storage, zobacz [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu do magazynu](../../storage/common/storage-account-create.md).  

**Rozwiązanie:** Ponownie odwiedź moduł i sprawdź, czy klucz usługi Azure Storage jest prawidłowy dla tego konta. w razie potrzeby skopiuj klucz z klasycznego portalu Azure.  

|Komunikaty o wyjątkach|
|------------------------|
|Klucz konta usługi Azure Storage jest nieprawidłowy.|


## <a name="error-0053"></a>Błąd 0053  
 Wyjątek występuje w przypadku, gdy nie ma żadnych funkcji użytkownika ani elementów dla zaleceń Matchbox.  

 Ten błąd w Azure Machine Learning jest tworzony, gdy nie można odnaleźć wektora funkcji.  

**Rozwiązanie:** Upewnij się, że wektor funkcji jest obecny w wejściowym zestawie danych.  

|Komunikaty o wyjątkach|
|------------------------|
|Funkcje użytkownika lub/i elementy są wymagane, ale nie są dostępne.|


## <a name="error-0056"></a>Błąd 0056  
 Wyjątek występuje, jeśli kolumny wybrane dla operacji naruszają wymagania.  

 Ten błąd w Azure Machine Learning występuje po wybraniu kolumn dla operacji, które wymagają, aby kolumna była określonego typu danych. 

 Ten błąd może również wystąpić, jeśli kolumna jest prawidłowym typem danych, ale używany moduł wymaga, aby kolumna była również oznaczona jako kolumna funkcji, etykiety lub kategorii.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Rozwiązanie:**

1.  Sprawdź typ danych aktualnie wybranych kolumn. 

2. Należy upewnić się, czy wybrane kolumny są kolumnami kategorii, Label i feature.  
  
3.  Zapoznaj się z tematem pomocy dla modułu, w którym została wybrana kolumna, aby określić, czy istnieją określone wymagania dotyczące użycia typu danych lub kolumny.  
  
3.  Użyj [Edytuj metadane](edit-metadata.md) , aby zmienić typ kolumny dla czasu trwania tej operacji. Pamiętaj, aby zmienić typ kolumny z powrotem na oryginalną wartość przy użyciu innego wystąpienia [edycji metadanych](edit-metadata.md), jeśli jest to potrzebne dla operacji podrzędnych.  

|Komunikaty o wyjątkach|
|------------------------|
|Co najmniej jedna z wybranych kolumn nie była w dozwolonej kategorii.|
|Kolumna o nazwie "{col_name}" nie jest w dozwolonej kategorii.|


## <a name="error-0057"></a>Błąd 0057  
 Wystąpił wyjątek podczas próby utworzenia pliku lub obiektu BLOB, który już istnieje.  

 Ten wyjątek występuje, gdy używasz modułu [eksportu danych](export-data.md) lub innego modułu do zapisywania wyników potoku w Azure Machine Learning do magazynu obiektów blob platformy Azure, ale podjęto próbę utworzenia pliku lub obiektu BLOB, który już istnieje.   

**Rozwiązanie:**

 Ten błąd zostanie wyświetlony tylko wtedy, gdy wcześniej ustawisz właściwość **tryb zapisu magazynu obiektów blob platformy Azure** na **błąd**. Zgodnie z projektem ten moduł zgłasza błąd w przypadku próby zapisania zestawu danych do obiektu BLOB, który już istnieje.

 - Otwórz właściwości modułu i zmień właściwość **tryb zapisu magazynu obiektów blob platformy Azure** , aby **zastąpić**.
 - Alternatywnie można wpisać nazwę innego docelowego obiektu BLOB lub pliku i upewnić się, że obiekt BLOB jeszcze nie istnieje.  

|Komunikaty o wyjątkach|
|------------------------|
|Plik lub obiekt BLOB już istnieje.|
|Plik lub obiekt BLOB "{file_path}" już istnieje.|


## <a name="error-0058"></a>Błąd 0058  
 Ten błąd w Azure Machine Learning występuje, jeśli zestaw danych nie zawiera oczekiwanej kolumny etykiety.  

 Ten wyjątek może również wystąpić, jeśli podana kolumna etykiet nie jest zgodna z danymi lub typem danych oczekiwanym przez dowiedzenie lub ma nieprawidłowe wartości. Na przykład ten wyjątek jest tworzony podczas korzystania z kolumny etykiet o wartości rzeczywistej podczas szkolenia klasyfikatora binarnego.  

**Rozwiązanie:** Rozwiązanie zależy od podanej informacji lub Trainer, a także typów danych kolumn w zestawie danych. Najpierw Sprawdź wymagania dotyczące algorytmu uczenia maszynowego lub modułu szkoleniowego.  

 Ponownie odwiedza wejściowy zestaw danych. Sprawdź, czy kolumna, która powinna być traktowana jako etykieta, ma odpowiedni typ danych dla tworzonego modelu.  

 Sprawdź dane wejściowe pod kątem brakujących wartości i Wyeliminuj lub zastąp je w razie potrzeby.  

 W razie potrzeby Dodaj moduł [Edytowanie metadanych](edit-metadata.md) i upewnij się, że kolumna etykieta jest oznaczona jako etykieta.  

|Komunikaty o wyjątkach|
|------------------------|
|Wartości kolumn etykiet i kolumny etykiet z wynikami nie są porównywalne.|
|Kolumna etykiety nie jest zgodna z oczekiwaną w "{dataset_name}".|
|Kolumna etykiety nie jest zgodna z oczekiwaną w "{dataset_name}", {powód}.|
|Nie oczekiwano kolumny "{column_name}" w etykiecie "{dataset_name}".|
|Nie oczekiwano kolumny "{column_name}" w etykiecie "{dataset_name}", {przyczyna}.|


## <a name="error-0059"></a>Błąd 0059  
 Wyjątek występuje, jeśli nie można przeanalizować indeksu kolumn określonego w selektorze kolumn.  

 Ten błąd w Azure Machine Learning występuje, jeśli nie można przeanalizować indeksu kolumny określonego podczas używania selektora kolumn.  Ten błąd zostanie wyświetlony, gdy indeks kolumny ma nieprawidłowy format, którego nie można przeanalizować.  

**Rozwiązanie:** Zmodyfikuj indeks kolumny, aby używał prawidłowej wartości indeksu.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przeanalizować co najmniej jednego z określonych indeksów kolumn lub zakresów indeksów.|
|Nie można przeanalizować indeksu kolumn lub zakresu "{column_index_or_range}".|


## <a name="error-0060"></a>Błąd 0060  
 Wyjątek występuje, gdy w selektorze kolumn określono poza zakresem kolumn spoza zakresu.  

 Ten błąd w Azure Machine Learning występuje, gdy zakres kolumn poza zakresem jest określony w selektorze kolumn. Ten błąd zostanie wyświetlony, jeśli zakres kolumn w selektorze kolumn nie odpowiada kolumnom w zestawie danych.  

**Rozwiązanie:** Zmodyfikuj zakres kolumn w selektorze kolumn, aby odpowiadały kolumnom w zestawie danych.  

|Komunikaty o wyjątkach|
|------------------------|
|Określono nieprawidłowy zakres indeksu kolumn lub poza zakresem.|
|Zakres kolumn "{column_range}" jest nieprawidłowy lub poza zakresem.|


## <a name="error-0061"></a>Błąd 0061  
 Wystąpił wyjątek podczas próby dodania wiersza do elementu DataTable, który ma inną liczbę kolumn niż tabela.  

 Ten błąd w Azure Machine Learning występuje podczas próby dodania wiersza do zestawu danych, który ma inną liczbę kolumn niż zestaw danych.  Ten błąd zostanie wyświetlony, jeśli wiersz, który jest dodawany do zestawu danych, ma inną liczbę kolumn z wejściowego zestawu danych.  Nie można dołączyć wiersza do zestawu danych, jeśli liczba kolumn jest różna.  

**Rozwiązanie:** Zmodyfikuj wejściowy zestaw danych tak, aby miał taką samą liczbę kolumn jak wiersz dodany, lub zmodyfikuj wiersz dodany, aby mieć taką samą liczbę kolumn jak zestaw danych.  

|Komunikaty o wyjątkach|
|------------------------|
|Wszystkie tabele muszą mieć taką samą liczbę kolumn.|
|Kolumny w fragmencie "{chunk_id_1}" różnią się od fragmentu "{chunk_id_2}" rozmiarem fragmentu: {chunk_size}.|
|Liczba kolumn w pliku "{filename_1}" (Count = {column_count_1}) różni się od pliku "{filename_2}" (Count = {column_count_2}).|


## <a name="error-0062"></a>Błąd 0062  
 Wystąpił wyjątek podczas próby porównania dwóch modeli z różnymi typami uczenia.  

 Ten błąd w Azure Machine Learning jest tworzony, gdy metryki oceny dla dwóch różnych zestawów danych z oceną nie można porównać. W takim przypadku nie jest możliwe Porównanie skuteczności modeli używanych do tworzenia dwóch wynikowych zestawów danych.  

**Rozwiązanie:** Sprawdź, czy wyniki oceny są tworzone w ramach tego samego rodzaju modelu uczenia maszynowego (klasyfikacja binarna, regresja, klasyfikacja wieloklasowa, zalecenie, klastrowanie, wykrywanie anomalii itp.) Wszystkie porównywane modele muszą mieć ten sam typ.  

|Komunikaty o wyjątkach|
|------------------------|
|Wszystkie modele muszą mieć ten sam typ informacji.|
|Uzyskano niezgodny typ uczenia: "{actual_learner_type}". Oczekiwane typy dowiedzenia: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Błąd 0064  
 Wyjątek występuje, gdy nazwa konta usługi Azure Storage lub klucz magazynu jest niepoprawnie określony.  

 Ten błąd w Azure Machine Learning występuje, jeśli nazwa konta usługi Azure Storage lub klucz magazynu został określony nieprawidłowo. Ten błąd zostanie wyświetlony w przypadku wprowadzenia nieprawidłowej nazwy konta lub hasła dla konta magazynu. Może się tak zdarzyć w przypadku ręcznego wprowadzenia nazwy konta lub hasła. Może również wystąpić, jeśli konto zostało usunięte.  

**Rozwiązanie:** Sprawdź, czy nazwa konta i hasło zostały wprowadzone prawidłowo i czy konto istnieje.  

|Komunikaty o wyjątkach|
|------------------------|
|Nazwa konta usługi Azure Storage lub klucz magazynu są nieprawidłowe.|
|Nazwa konta usługi Azure Storage "{account_name}" lub klucz magazynu dla nazwy konta jest niepoprawna.|


## <a name="error-0065"></a>Błąd 0065  
 Wyjątek występuje, jeśli nazwa obiektu blob platformy Azure została określona nieprawidłowo.  

 Ten błąd w Azure Machine Learning występuje, jeśli nazwa obiektu blob platformy Azure została określona nieprawidłowo.  Zostanie wyświetlony komunikat o błędzie, jeśli:  

-   Nie można odnaleźć obiektu BLOB w określonym kontenerze.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Tylko kontener został określony jako źródło w żądaniu [danych importu](import-data.md) , jeśli format był program Excel lub CSV z kodowaniem; łączenie zawartości wszystkich obiektów BLOB w kontenerze nie jest dozwolone w tych formatach.  
  
-   Identyfikator URI sygnatury dostępu współdzielonego nie zawiera nazwy prawidłowego obiektu BLOB.  

**Rozwiązanie:** Ponownie odwiedzający moduł zgłaszający wyjątek. Sprawdź, czy określony obiekt BLOB istnieje w kontenerze na koncie magazynu i czy uprawnienia umożliwiają wyświetlanie obiektu BLOB. Upewnij się, że dane wejściowe mają postać **ContainerName/filename** , jeśli masz program Excel lub CSV z formatami kodowania. Sprawdź, czy identyfikator URI sygnatury dostępu współdzielonego zawiera nazwę prawidłowego obiektu BLOB.  

|Komunikaty o wyjątkach|
|------------------------|
|Nazwa obiektu BLOB usługi Azure Storage jest niepoprawna.|
|Nazwa obiektu BLOB usługi Azure Storage "{blob_name}" jest niepoprawna.|
|Nazwa obiektu BLOB usługi Azure Storage z prefiksem "{blob_name_prefix}" nie istnieje.|
|Nie można znaleźć żadnych obiektów BLOB usługi Azure Storage w kontenerze "{container_name}".|
|Nie można znaleźć żadnych obiektów BLOB usługi Azure Storage z ścieżką wieloznaczną "{blob_wildcard_path}".|


## <a name="error-0066"></a>Błąd 0066  
 Wyjątek występuje, jeśli nie można przekazać zasobu do obiektu blob platformy Azure.  

 Ten błąd w Azure Machine Learning występuje, jeśli nie można przekazać zasobu do obiektu blob platformy Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Oba są zapisywane na tym samym koncie usługi Azure Storage, co konto zawierające plik wejściowy.  

**Rozwiązanie:** Ponownie przejdź do modułu. Sprawdź, czy nazwa konta platformy Azure, klucz magazynu i kontener są poprawne i czy konto ma uprawnienia do zapisu w kontenerze.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można przekazać zasobu do usługi Azure Storage.|
|Nie można przekazać pliku "{source_path}" do usługi Azure Storage jako "{dest_path}".|


## <a name="error-0067"></a>Błąd 0067  
 Wyjątek występuje, jeśli zestaw danych ma inną liczbę kolumn niż oczekiwano.  

 Ten błąd w Azure Machine Learning występuje, jeśli zestaw danych ma inną liczbę kolumn niż oczekiwano.  Ten błąd zostanie wyświetlony, gdy liczba kolumn w zestawie danych różni się od liczby kolumn oczekiwanych przez moduł podczas wykonywania.  

**Rozwiązanie:** Zmodyfikuj wejściowy zestaw danych lub parametry.  

|Komunikaty o wyjątkach|
|------------------------|
|Nieoczekiwana liczba kolumn w elemencie DataTable.|
|Nieoczekiwana liczba kolumn w zestawie danych "{dataset_name}".|
|Oczekiwano kolumn ("{expected_column_count}"), ale zamiast nich znaleziono kolumny "{actual_column_count}".|
|W wejściowym zestawie danych "{dataset_name}" oczekiwano następującej liczb kolumn: "{expected_column_count}", ale zamiast nich znaleziono kolumny "{actual_column_count}".|


## <a name="error-0068"></a>Błąd 0068  
 Wyjątek występuje, jeśli określony skrypt Hive jest niepoprawny.  

 Ten błąd w Azure Machine Learning występuje, jeśli w skrypcie Hive QL występują błędy składniowe lub jeśli interpreter Hive napotka błąd podczas wykonywania zapytania lub skryptu.  

**Rozwiązanie:**

Komunikat o błędzie z usługi Hive jest zwykle raportowany ponownie w dzienniku błędów, aby można było wykonać akcję na podstawie konkretnego błędu. 

+ Otwórz moduł i zbadaj zapytanie pod kątem błędów.  
+ Sprawdź, czy zapytanie działa prawidłowo poza Azure Machine Learning, logując się do konsoli programu Hive w klastrze usługi Hadoop i uruchamiając zapytanie.  
+ Spróbuj umieścić komentarze w skrypcie Hive w osobnym wierszu, zamiast mieszania instrukcji i komentarzy wykonywalnych w jednym wierszu.  

### <a name="resources"></a>Zasoby

Zapoznaj się z następującymi artykułami, aby uzyskać pomoc dotyczącą zapytań programu Hive dotyczących uczenia maszynowego:

+ [Tworzenie tabel programu Hive i ładowanie danych z usługi Azure Blob Storage](../team-data-science-process/move-hive-tables.md)
+ [Eksplorowanie danych w tabelach przy użyciu zapytań Hive](../team-data-science-process/explore-data-hive-tables.md)
+ [Tworzenie funkcji dla danych w klastrze usługi Hadoop przy użyciu zapytań Hive](../team-data-science-process/create-features-hive.md)
+ [Gałąź programu SQL users Ściągawka arkusz (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Komunikaty o wyjątkach|
|------------------------|
|Skrypt Hive jest nieprawidłowy.|


## <a name="error-0069"></a>Błąd 0069  
 Wyjątek występuje, jeśli określony skrypt SQL jest niepoprawny.  

 Ten błąd w Azure Machine Learning występuje, jeśli określony skrypt SQL zawiera problemy ze składnią lub jeśli kolumny lub tabele określone w skrypcie są nieprawidłowe. 

 Ten błąd zostanie wyświetlony, jeśli podczas wykonywania zapytania lub skryptu wystąpi błąd w aparacie SQL. Komunikat o błędzie SQL jest zwykle raportowany ponownie w dzienniku błędów, aby można było wykonać akcję na podstawie konkretnego błędu.  

**Rozwiązanie:** Ponownie odwiedź moduł i sprawdź, czy zapytanie SQL zawiera błędy.  

 Sprawdź, czy zapytanie działa prawidłowo poza platformą Azure, logując się do serwera bazy danych bezpośrednio i uruchamiając zapytanie.  

 Jeśli w wyjątku modułu jest raportowany komunikat generowany przez program SQL, należy wykonać akcję na podstawie zgłoszonego błędu. Na przykład komunikaty o błędach czasami zawierają określone wskazówki dotyczące przyczyny błędu:
+ *Nie ma takiej kolumny ani brakującej bazy danych*, co oznacza, że być może wpisano nieprawidłową nazwę kolumny. Jeśli masz pewność, że nazwa kolumny jest poprawna, spróbuj użyć nawiasów lub znaków cudzysłowu, aby ująć identyfikator kolumny.
+ *Błąd logiki SQL w \<SQL keyword\> sąsiedztwie*, wskazujący, że może wystąpić błąd składniowy przed określonym słowem kluczowym

  
|Komunikaty o wyjątkach|
|------------------------|
|Skrypt SQL jest nieprawidłowy.|
|Zapytanie SQL "{sql_query}" jest niepoprawne.|
|Zapytanie SQL "{sql_query}" jest niepoprawne. Komunikat o wyjątku: {Exception}.|


## <a name="error-0070"></a>Błąd 0070  
 Wystąpił wyjątek podczas próby uzyskania dostępu do nieistniejącej tabeli platformy Azure.  

 Ten błąd w Azure Machine Learning występuje podczas próby uzyskania dostępu do nieistniejącej tabeli platformy Azure. Ten błąd zostanie wyświetlony, jeśli określisz tabelę w usłudze Azure Storage, która nie istnieje podczas odczytywania lub zapisywania do Table Storage platformy Azure. Taka sytuacja może wystąpić, jeśli wpiszesz nazwę odpowiedniej tabeli lub występuje niezgodność między nazwą docelową a typem magazynu. Na przykład, zamierzasz odczytać z tabeli, ale zamiast tego wprowadzić nazwę obiektu BLOB.  

**Rozwiązanie:** Ponownie odwiedź moduł, aby sprawdzić, czy nazwa tabeli jest poprawna.  

|Komunikaty o wyjątkach|
|------------------------|
|Tabela platformy Azure nie istnieje.|
|Tabela platformy Azure "{table_name}" nie istnieje.|


## <a name="error-0072"></a>Błąd 0072  
 Wyjątek występuje w przypadku przekroczenia limitu czasu połączenia.  

 Ten błąd w Azure Machine Learning występuje, gdy upłynął limit czasu połączenia. Ten błąd zostanie wyświetlony w przypadku problemów z łącznością ze źródłem danych lub miejscem docelowym, na przykład w przypadku powolnej łączności z Internetem lub jeśli zestaw danych jest duży i/lub zapytanie SQL do odczytu w danych wykonuje skomplikowane przetwarzanie.  

**Rozwiązanie:** Ustal, czy występują obecnie problemy z wolnym połączeniem z usługą Azure Storage lub Internetem.  

|Komunikaty o wyjątkach|
|------------------------|
|Przekroczono limit czasu połączenia.|


## <a name="error-0073"></a>Błąd 0073  
 Wyjątek występuje, jeśli wystąpi błąd podczas konwertowania kolumny na inny typ.  

 Ten błąd w Azure Machine Learning występuje, gdy nie jest możliwe przekonwertowanie kolumny na inny typ.  Ten błąd zostanie wyświetlony, jeśli moduł wymaga określonego typu i nie jest możliwe przekonwertowanie kolumny na nowy typ.  

**Rozwiązanie:** Zmodyfikuj wejściowy zestaw danych, aby można było przekonwertować kolumnę na podstawie wewnętrznego wyjątku.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można skonwertować kolumny.|
|Nie można przekonwertować kolumny na {target_type}.|


## <a name="error-0075"></a>Błąd 0075  
Wyjątek występuje, gdy podczas quantizing zestawu danych zostanie użyta nieprawidłowa Funkcja pakowania.  

Ten błąd w Azure Machine Learning występuje podczas próby zapełnienia danych za pomocą nieobsługiwanej metody lub w przypadku, gdy kombinacje parametrów są nieprawidłowe.  

**Rozwiązanie:**

Obsługa błędów dla tego zdarzenia została wprowadzona we wcześniejszej wersji Azure Machine Learning, która pozwala na dalsze dostosowywanie metod pakowania. Obecnie wszystkie metody pakowania są oparte na wyborze z listy rozwijanej, więc nie powinno być już możliwe uzyskanie tego błędu.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|Komunikaty o wyjątkach|
|------------------------|
|Użyto nieprawidłowej funkcji pakowania.|


## <a name="error-0077"></a>Błąd 0077  
 Wyjątek występuje, gdy przeszedł nieznany tryb zapisywania pliku BLOB.  

 Ten błąd w Azure Machine Learning występuje, jeśli nieprawidłowy argument w specyfikacjach dla obiektu docelowego lub źródła pliku BLOB.  

**Rozwiązanie:** W prawie wszystkie moduły importujące lub eksportujące dane do i z usługi Azure Blob Storage, wartości parametrów kontrolujących tryb zapisu są przypisywane przy użyciu listy rozwijanej. w związku z tym nie jest możliwe przekazanie nieprawidłowej wartości i ten błąd nie powinien być wyświetlany. Ten błąd zostanie uznany za przestarzały w nowszej wersji.  

|Komunikaty o wyjątkach|
|------------------------|
|Nieobsługiwany tryb zapisu obiektu BLOB.|
|Nieobsługiwany tryb zapisu obiektu BLOB: {blob_write_mode}.|


## <a name="error-0078"></a>Błąd 0078  
 Wyjątek występuje, gdy opcja HTTP dla [danych importu](import-data.md) odbiera kod stanu 3xx wskazujący przekierowania.  

 Ten błąd w Azure Machine Learning występuje, gdy opcja HTTP dla [danych importu](import-data.md) odbiera 3xx (301, 302, 304 itp.) kod stanu wskazujący przekierowania. Ten błąd zostanie wyświetlony w przypadku próby nawiązania połączenia ze źródłem HTTP, które przekierowuje przeglądarkę do innej strony. Ze względów bezpieczeństwa Przekierowywanie witryn sieci Web nie jest dozwolone jako źródła danych dla Azure Machine Learning.  

**Rozwiązanie:** Jeśli witryna sieci Web jest zaufaną witryną sieci Web, wprowadź adres URL przekierowane bezpośrednio.  

|Komunikaty o wyjątkach|
|------------------------|
|Przekierowywanie HTTP jest niedozwolone.|


## <a name="error-0079"></a>Błąd 0079  
 Wyjątek występuje, jeśli nazwa kontenera usługi Azure Storage została określona nieprawidłowo.  

 Ten błąd w Azure Machine Learning występuje, jeśli nazwa kontenera usługi Azure Storage została określona nieprawidłowo. Ten błąd zostanie wyświetlony, jeśli nie określono zarówno kontenera, jak i nazwy obiektu BLOB (pliku) przy użyciu **ścieżki do obiektu BLOB rozpoczynającego** się od opcji kontenera podczas zapisywania na platformie Azure Blob Storage.  

**Rozwiązanie:** Ponownie odwiedź moduł [eksportu danych](export-data.md) i sprawdź, czy określona ścieżka do obiektu BLOB zawiera zarówno kontener, jak i nazwę pliku, w formacie **Container/filename**.  

|Komunikaty o wyjątkach|
|------------------------|
|Nazwa kontenera usługi Azure Storage jest niepoprawna.|
|Nazwa kontenera usługi Azure Storage "{container_name}" jest niepoprawna; Oczekiwano nazwy kontenera/obiektu BLOB.|


## <a name="error-0080"></a>Błąd 0080  
 Wyjątek występuje, gdy kolumna zawierająca wszystkie wartości nie jest dozwolona przez moduł.  

 Ten błąd w Azure Machine Learning jest tworzony, gdy co najmniej jedna z kolumn używanych przez moduł zawiera wszystkie brakujące wartości. Na przykład, jeśli moduł przetwarza zagregowane statystyki dla każdej kolumny, nie może działać na kolumnie zawierającej nie dane. W takich przypadkach wykonywanie modułu zostało zatrzymane z powodu tego wyjątku.  

**Rozwiązanie:** Ponownie odwiedza wejściowy zestaw danych i Usuń wszystkie kolumny, które zawierają wszystkie brakujące wartości.  

|Komunikaty o wyjątkach|
|------------------------|
|Kolumny zawierające wszystkie brakujące wartości są niedozwolone.|
|Brak wszystkich wartości w kolumnie {col_index_or_name}.|


## <a name="error-0081"></a>Błąd 0081  
 W module UPW występuje wyjątek, jeśli liczba wymiarów do zmniejszenia jest równa liczbie kolumn funkcji w wejściowym zestawie danych zawierającym co najmniej jedną kolumnę funkcji rozrzedzonej.  

 Ten błąd w Azure Machine Learning jest generowany w przypadku spełnienia następujących warunków: (a) wejściowy zestaw danych ma co najmniej jedną kolumnę rozrzedzoną i (b) końcowa Liczba żądanych wymiarów jest taka sama jak liczba wymiarów wejściowych.  

**Rozwiązanie:** Rozważ zmniejszenie liczby wymiarów w danych wyjściowych na mniejszą niż liczba wymiarów w danych wejściowych. Jest to typowe w aplikacjach asystenta UPW.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Komunikaty o wyjątkach|
|------------------------|
|Dla zestawu danych zawierającego kolumny funkcji rozrzedzonej Liczba wymiarów do zmniejszenia do powinna być mniejsza niż liczba kolumn funkcji.|


## <a name="error-0082"></a>Błąd 0082  
 Wyjątek występuje, gdy nie można pomyślnie deserializować modelu.  

 Ten błąd w Azure Machine Learning występuje, gdy zapisany model uczenia maszynowego lub transformacja nie może zostać załadowana przez nowszą wersję środowiska uruchomieniowego Azure Machine Learning w wyniku istotnej zmiany.  

**Rozwiązanie:** Należy ponownie uruchomić potok szkoleniowy, który wygenerował model lub transformację, a model lub przekształcenie musi zostać zapisane.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można zdeserializować modelu, ponieważ prawdopodobnie jest on serializowany ze starszym formatem serializacji. Ponownie poszkol i Zapisz model.|


## <a name="error-0083"></a>Błąd 0083  
 Wyjątek występuje, jeśli zestaw danych używany do uczenia nie może być używany w przypadku konkretnego typu uczenia.  

 Ten błąd w Azure Machine Learning jest tworzony, gdy zestaw danych jest niezgodny z przeszkolonym nauką. Na przykład zestaw danych może zawierać co najmniej jedną brakującą wartość w każdym wierszu, a w rezultacie cały zestaw danych zostanie pominięty podczas szkolenia. W innych przypadkach niektóre algorytmy uczenia maszynowego, takie jak wykrywanie anomalii, nie oczekują na obecność etykiet i mogą zgłosić ten wyjątek, jeśli etykiety są obecne w zestawie danych.  

**Rozwiązanie:** Zapoznaj się z dokumentacją dostosowanej do sprawdzania wymagań dla wejściowego zestawu danych. Sprawdź kolumny, aby wyświetlić wszystkie wymagane kolumny.  

|Komunikaty o wyjątkach|
|------------------------|
|Zestaw danych używany do szkolenia jest nieprawidłowy.|
|{data_name} zawiera nieprawidłowe dane na potrzeby szkolenia.|
|{data_name} zawiera nieprawidłowe dane na potrzeby szkolenia. Typ Dowiedz się: {learner_type}.|
|{data_name} zawiera nieprawidłowe dane na potrzeby szkolenia. Typ Dowiedz się: {learner_type}. Przyczyna: {powód}.|
|Nie można zastosować akcji "{action_name}" dotyczącej danych szkoleniowych {data_name}. Przyczyna: {powód}.|


## <a name="error-0084"></a>Błąd 0084  
 Wyjątek występuje, gdy oceniane są wyniki uzyskane ze skryptu języka R. Nie jest to obecnie obsługiwane.  

 Ten błąd w Azure Machine Learning występuje, jeśli spróbujesz użyć jednego z modułów do oceny modelu z danymi wyjściowymi skryptu języka R, który zawiera wyniki.  

**Rozwiązanie:**

|Komunikaty o wyjątkach|
|------------------------|
|Ocenianie wyników wytwarzanych przez model niestandardowy nie jest obecnie obsługiwane.|


## <a name="error-0085"></a>Błąd 0085  
 Wyjątek występuje, gdy Ocena skryptu kończy się niepowodzeniem z powodu błędu.  

 Ten błąd w Azure Machine Learning występuje, gdy jest uruchomiony skrypt niestandardowy zawierający błędy składni.  

**Rozwiązanie:** Przejrzyj kod w edytorze zewnętrznym i sprawdź pod kątem błędów.  

|Komunikaty o wyjątkach|
|------------------------|
|Wystąpił błąd podczas obliczania skryptu.|
|Wystąpił następujący błąd podczas obliczania skryptu. więcej informacji można znaleźć w dzienniku danych wyjściowych:<br />----------Początek komunikatu o błędzie z interpretera {script_language}----------<br />pojawi<br />----------Koniec komunikatu o błędzie z interpretera {script_language}----------|


## <a name="error-0090"></a>Błąd 0090  
 Wyjątek występuje, gdy utworzenie tabeli Hive zakończy się niepowodzeniem.  

 Ten błąd w Azure Machine Learning występuje, gdy używane są [dane eksportu](export-data.md) lub inna opcja zapisywania danych w klastrze usługi HDInsight i nie można utworzyć określonej tabeli programu Hive.  

**Rozwiązanie:** Sprawdź nazwę konta usługi Azure Storage skojarzoną z klastrem i sprawdź, czy używasz tego samego konta we właściwościach modułu.  

|Komunikaty o wyjątkach|
|------------------------|
|Nie można utworzyć tabeli programu Hive. Upewnij się, że nazwa konta usługi Azure Storage skojarzona z klastrem jest taka sama jak w przypadku przekazywania przez parametr module.|
|Nie można utworzyć tabeli programu Hive "{table_name}". Upewnij się, że nazwa konta usługi Azure Storage skojarzona z klastrem jest taka sama jak w przypadku przekazywania przez parametr module.|
|Nie można utworzyć tabeli programu Hive "{table_name}". Upewnij się, że nazwa konta usługi Azure Storage skojarzona z klastrem to "{cluster_name}".|


## <a name="error-0102"></a>Błąd 0102  
 Zgłaszany, gdy nie można wyodrębnić pliku ZIP.  

 Ten błąd w Azure Machine Learning występuje podczas importowania pakietu spakowanego z rozszerzeniem zip, ale pakiet nie jest plikiem ZIP lub plik nie korzysta z obsługiwanego formatu zip.  

**Rozwiązanie:** Upewnij się, że wybrany plik jest prawidłowym plikiem zip i że został skompresowany przy użyciu jednego z obsługiwanych algorytmów kompresji.  

 Jeśli ten błąd wystąpi podczas importowania zestawów danych w formacie skompresowanym, upewnij się, że wszystkie zawarte pliki korzystają z jednego z obsługiwanych formatów plików i są w formacie Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Spróbuj odczytać odpowiednie pliki do nowego skompresowanego folderu spakowanego i ponownie spróbuj dodać moduł niestandardowy.  

|Komunikaty o wyjątkach|
|------------------------|
|Dany plik ZIP nie ma poprawnego formatu.|


## <a name="error-0105"></a>Błąd 0105  
 Ten błąd jest wyświetlany, gdy plik definicji modułu zawiera nieobsługiwany typ parametru  
  
 Ten błąd w Azure Machine Learning jest tworzony podczas tworzenia niestandardowej definicji XML modułu, a typ parametru lub argumentu w definicji nie jest zgodny z obsługiwanym typem.  
  
**Rozwiązanie:** Upewnij się, że Właściwość Type dowolnego elementu **ARG** w pliku definicji XML modułu niestandardowego jest obsługiwana.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwany typ parametru.|  
|Określono nieobsługiwany typ parametru " {0} ".|  


## <a name="error-0107"></a>Błąd 0107  
 Zgłaszany, gdy plik definicji modułu definiuje nieobsługiwany typ wyjściowy  
  
 Ten błąd w Azure Machine Learning jest tworzony, gdy typ portu wyjściowego w definicji XML modułu niestandardowego nie jest zgodny z obsługiwanym typem.  
  
**Rozwiązanie:** Upewnij się, że Właściwość Type elementu wyjściowego w pliku definicji XML modułu niestandardowego jest obsługiwana.  
  
|Komunikaty o wyjątkach|  
|------------------------|  
|Nieobsługiwany typ danych wyjściowych.|  
|Określono nieobsługiwany typ wyjściowy "{output_type}".|  


## <a name="error-0125"></a>Błąd 0125  
 Zgłaszany, gdy schemat dla wielu zestawów danych nie jest zgodny.  

**Rozwiązanie:**

|Komunikaty o wyjątkach|
|------------------------|
|Schemat zestawu danych nie jest zgodny.|


## <a name="error-0127"></a>Błąd 0127  
 Rozmiar pikseli obrazu przekracza dozwolony limit  

 Ten błąd występuje, gdy odczytujesz obrazy z zestawu danych obrazu dla klasyfikacji, a obrazy są większe niż obsługiwane przez model.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Komunikaty o wyjątkach|
|------------------------|
|Rozmiar pikseli obrazu przekracza dozwolony limit.|
|Rozmiar pikseli obrazu w pliku "{file_path}" przekracza dozwolony limit: "{size_limit}".|


## <a name="error-0128"></a>Błąd 0128  
 Liczba prawdopodobieństwa warunkowego dla kolumn kategorii przekracza limit.  

**Rozwiązanie:**

|Komunikaty o wyjątkach|
|------------------------|
|Liczba prawdopodobieństwa warunkowego dla kolumn kategorii przekracza limit.|
|Liczba prawdopodobieństwa warunkowego dla kolumn kategorii przekracza limit. Kolumny "{column_name_or_index_1}" i "{column_name_or_index_2}" są problematyczną parą.|


## <a name="error-0129"></a>Błąd 0129  
 Liczba kolumn w zestawie danych przekracza dozwolony limit.  

**Rozwiązanie:**

|Komunikaty o wyjątkach|
|------------------------|
|Liczba kolumn w zestawie danych przekracza dozwolony limit.|
|Liczba kolumn w zestawie danych w elemencie "{dataset_name}" przekracza dozwolone.|
|Liczba kolumn w zestawie danych w elemencie "{dataset_name}" przekracza dozwolony limit wynoszący "{component_name}".|
|Liczba kolumn w zestawie danych w elemencie "{dataset_name}" przekracza dozwolony limit "{limit_columns_count}" ("{component_name}").|


## <a name="error-0134"></a>Błąd 0134
Wyjątek występuje, gdy brakuje kolumny etykiety lub zawiera ona niewystarczającą liczbę wierszy z etykietą.  

Ten błąd występuje, gdy moduł wymaga kolumny etykieta, ale nie dołączysz go do zaznaczenia kolumny lub w kolumnie etykieta brakuje zbyt wielu wartości.

Ten błąd może również wystąpić, gdy Poprzednia operacja zmienia zestaw danych, taki jak niewystarczająca liczba wierszy dla operacji podrzędnej. Załóżmy na przykład, że używasz wyrażenia z **partycji i przykładowego** modułu do dzielenia zestawu danych przez wartości. Jeśli nie znaleziono żadnych dopasowań dla wyrażenia, jeden z zestawów danych uzyskanych z partycji będzie pusty.

Rozwiązanie: 

 Jeśli w zaznaczeniu kolumny zostanie uwzględniona kolumna etykieta, ale nie zostanie ona rozpoznana, użyj modułu [Edytuj metadane](edit-metadata.md) , aby oznaczyć ją jako kolumnę etykiety.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Następnie możesz użyć [nieoczyszczonego modułu danych](clean-missing-data.md) , aby usunąć z kolumny etykieta brakujące wartości. 

 Sprawdź wejściowe zestawy danych, aby upewnić się, że zawierają one prawidłowe dane, oraz wystarczającą liczbę wierszy, aby spełnić wymagania operacji. Wiele algorytmów generuje komunikat o błędzie, jeśli wymagają pewnej minimalnej liczby wierszy danych, ale dane zawierają tylko kilka wierszy lub tylko nagłówek.

|Komunikaty o wyjątkach|
|------------------------|
|Wyjątek występuje, gdy brakuje kolumny etykiety lub zawiera ona niewystarczającą liczbę wierszy z etykietą.|
|Wyjątek występuje, gdy brakuje kolumny etykiety lub ma ona mniej niż {required_rows_count} wierszy z etykietą.|
|Wyjątek występuje, gdy brakuje kolumny Label w zestawie danych {dataset_name} lub jest ona mniejsza niż {required_rows_count} wierszy z etykietą.|


## <a name="error-0138"></a>Błąd 0138  
 Pamięć została wyczerpana, nie można ukończyć działania modułu. Próbkowanie w dół zestawu danych może pomóc w zmniejszeniu problemu.  

 Ten błąd występuje, gdy uruchomiony moduł wymaga więcej pamięci niż jest dostępny w kontenerze platformy Azure. Taka sytuacja może wystąpić, jeśli pracujesz z dużym zestawem danych, a bieżąca operacja nie może pasować do pamięci.  

**Rozwiązanie:** Jeśli próbujesz odczytać duży zestaw danych, a operacja nie może zostać zakończona, próbkowanie zestawu danych może pomóc.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Komunikaty o wyjątkach|
|------------------------|
|Pamięć została wyczerpana, nie można ukończyć działania modułu.|
|Pamięć została wyczerpana, nie można ukończyć działania modułu. Szczegóły: {szczegóły}|


## <a name="error-0141"></a>Błąd 0141  
 Wyjątek występuje, jeśli liczba wybranych kolumn liczbowych i unikatowych wartości w kolumnach kategorii i String jest za mała.  

 Ten błąd w Azure Machine Learning występuje, gdy w wybranej kolumnie nie ma wystarczającej liczby unikatowych wartości, aby wykonać tę operację.  

**Rozwiązanie:** Niektóre operacje wykonują operacje statystyczne w kolumnach funkcji i kategorii, a jeśli nie ma wystarczającej wartości, operacja może zakończyć się niepowodzeniem lub zwracać nieprawidłowy wynik. Sprawdź zestaw danych, aby zobaczyć, ile wartości znajduje się w kolumnach funkcja i etykieta, i sprawdź, czy operacja, którą próbujesz wykonać, jest statystycznie ważna.  

 Jeśli źródłowy zestaw danych jest prawidłowy, możesz również sprawdzić, czy niektóre operacje operowania danymi nadrzędnymi lub metadanych spowodowały zmianę danych i Usunięto niektóre wartości.  

 Jeśli operacje nadrzędne obejmują dzielenie, próbkowanie lub ponowne próbkowanie, sprawdź, czy dane wyjściowe zawierają oczekiwaną liczbę wierszy i wartości.  

|Komunikaty o wyjątkach|
|------------------------|
|Liczba wybranych kolumn liczbowych i unikatowych wartości w kolumnach kategorii i String jest za mała.|
|Łączna liczba wybranych kolumn liczbowych i unikatowych wartości w kolumnach kategorii i String (obecnie {actual_num}) powinna wynosić co najmniej {lower_boundary}.|


## <a name="error-0154"></a>Błąd 0154  
 Wyjątek występuje, gdy użytkownik próbuje dołączyć dane do kolumn kluczy z niezgodnym typem kolumny.

|Komunikaty o wyjątkach|
|------------------------|
|Typy elementów kolumny klucza nie są zgodne.|
|Typy elementów kolumny klucza nie są zgodne. (Left: {keys_left}; prawy: {keys_right})|


## <a name="error-0155"></a>Błąd 0155  
 Wyjątek występuje, gdy nazwy kolumn zestawu danych nie są ciągami.

|Komunikaty o wyjątkach|
|------------------------|
|Nazwa kolumny Dataframe musi być typu String. Nazwy kolumn nie są ciągami.|
|Nazwa kolumny Dataframe musi być typu String. Nazwy kolumn {column_names} nie są ciągami.|


## <a name="error-0156"></a>Błąd 0156  
 Wyjątek występuje, gdy nie można odczytać danych z Azure SQL Database.

|Komunikaty o wyjątkach|
|------------------------|
|Nie można odczytać danych z Azure SQL Database.|
|Nie można odczytać danych z Azure SQL Database: {detailed_message} DB: {database_server_name}: {database_name} kwerenda: {sql_statement}|


## <a name="error-0157"></a>Błąd 0157  
 Nie znaleziono magazynu danych.

|Komunikaty o wyjątkach|
|------------------------|
|Informacje o magazynie danych są nieprawidłowe.|
|Informacje o magazynie danych są nieprawidłowe. Nie można pobrać magazynu danych Azure "{datastore_name}" w obszarze roboczym "{workspace_name}".|


## <a name="error-0158"></a>Błąd 0158
 Zgłaszany, gdy katalog transformacji jest nieprawidłowy.

|Komunikaty o wyjątkach|
|------------------------------------------------------------|
|Określony TransformationDirectory jest nieprawidłowy.|
|TransformationDirectory "{arg_name}" jest nieprawidłowy. Przyczyna: {powód}. Wykonaj ponownie eksperyment szkoleniowy, który generuje plik transformacji. Jeśli eksperyment szkoleniowy został usunięty, utwórz go ponownie i Zapisz.|
|TransformationDirectory "{arg_name}" jest nieprawidłowy. Przyczyna: {powód}. {troubleshoot_hint}|


## <a name="error-0159"></a>Błąd 0159
 Wyjątek występuje, jeśli katalog modelu modułu jest nieprawidłowy. 

|Komunikaty o wyjątkach|
|------------------------------------------------------------|
|Określony ModelDirectory jest nieprawidłowy.|
|ModelDirectory "{arg_name}" jest nieprawidłowy.|
|ModelDirectory "{arg_name}" jest nieprawidłowy. Przyczyna: {powód}.|
|ModelDirectory "{arg_name}" jest nieprawidłowy. Przyczyna: {powód}. {troubleshoot_hint}|


## <a name="error-1000"></a>Błąd 1000  
Wewnętrzny wyjątek biblioteki.  

Ten błąd jest dostarczany w celu przechwycenia nieobsłużonych błędów aparatu wewnętrznego. W związku z tym przyczyny tego błędu mogą się różnić w zależności od modułu, który wygenerował błąd.  

Aby uzyskać dalszą pomoc, zalecamy opublikowanie szczegółowego komunikatu zawierającego błąd do [forum Azure Machine Learning](/answers/topics/azure-machine-learning.html), wraz z opisem scenariusza, włącznie z danymi używanymi jako dane wejściowe. Ta opinia pomoże nam określić priorytety błędów i zidentyfikować Najważniejsze problemy w celu dalszej pracy.  

|Komunikaty o wyjątkach|
|------------------------|
|Wyjątek biblioteki.|
|Wyjątek biblioteki: {Exception}.|
|Nieznany wyjątek biblioteki: {Exception}. {customer_support_guidance}.|