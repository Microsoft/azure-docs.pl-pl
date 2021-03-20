---
title: 'Partycja i przykład: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać partycji i przykładowego modułu w Azure Machine Learning, aby wykonać próbkowanie na zestawie danych lub utworzyć partycje z zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a1f0a0dff4eb8a1aad0cd5e142959a636827a541
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898485"
---
# <a name="partition-and-sample-module"></a>Partycja i Przykładowa moduł

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Użyj partycji i przykładowego modułu do wykonania próbkowania dla zestawu danych lub utworzenia partycji z zestawu danych.

Próbkowanie jest ważnym narzędziem w uczeniu maszynowym, ponieważ umożliwia zmniejszenie rozmiaru zestawu danych przy zachowaniu tego samego stosunku wartości. Ten moduł obsługuje kilka powiązanych zadań, które są ważne w uczeniu maszynowym: 

- Dzielenie danych na wiele podczęści o takim samym rozmiarze. 

  Możesz użyć partycji na potrzeby wzajemnego sprawdzania poprawności lub przypisać przypadki do grup losowych.

- Oddzielanie danych do grup, a następnie praca z danymi z określonej grupy. 

  Po losowo przypisaniu przypadków do różnych grup może zajść potrzeba zmodyfikowania funkcji, które są skojarzone tylko z jedną grupą.

- Sond. 

  Można wyodrębnić procent danych, zastosować Próbkowanie losowe lub wybrać kolumnę, która ma być używana do równoważenia zestawu danych i wykonać stratified próbkowania na jego wartości.

- Tworzenie mniejszego zestawu danych do testowania. 

  Jeśli masz dużo danych, możesz chcieć użyć tylko pierwszych *n* wierszy podczas konfigurowania potoku, a następnie przełączyć się do korzystania z pełnego zestawu danych podczas kompilowania modelu. Można również użyć próbkowania do utworzenia mniejszego zestawu danych do użycia podczas tworzenia.

## <a name="configure-the-module"></a>Konfigurowanie modułu

Ten moduł obsługuje następujące metody dzielenia danych na partycje lub do próbkowania. Najpierw wybierz metodę, a następnie ustaw dodatkowe opcje wymagane przez metodę.

- Head
- Próbkowanie
- Przypisz do zagięć
- Wybierz złożenie

### <a name="get-top-n-rows-from-a-dataset"></a>Pobierz N pierwszych wierszy z zestawu danych

Użyj tego trybu, aby uzyskać tylko pierwsze *n* wierszy. Ta opcja jest przydatna, jeśli chcesz przetestować potok w niewielkiej liczbie wierszy i nie potrzebujesz, aby dane były równoważone lub próbkowane w jakikolwiek sposób.

1. Dodaj **partycję i przykładowy** moduł do potoku w interfejsie i Połącz zestaw danych.  

1. **Tryb partycji lub próbki**: Ustaw tę opcję na wartość **główna**.

1. **Liczba wierszy do wybrania**: wprowadź liczbę wierszy do zwrócenia.

   Liczba wierszy musi być nieujemną liczbą całkowitą. Jeśli liczba wybranych wierszy jest większa niż liczba wierszy w zestawie danych, zwracany jest cały zestaw danych.

1. Prześlij potok.

Moduł wyprowadza pojedynczy zestaw danych, który zawiera tylko określoną liczbę wierszy. Wiersze są zawsze odczytywane z góry zestawu danych.

### <a name="create-a-sample-of-data"></a>Tworzenie przykładu danych

Ta opcja obsługuje proste Próbkowanie losowe lub losowe próbkowanie stratified. Jest to przydatne, jeśli chcesz utworzyć mniejszy reprezentatywny przykładowy zestaw danych do testowania.

1. Dodawanie **partycji i przykładowego** modułu do potoku oraz łączenie zestawu danych.

1. **Tryb partycji lub próbki**: Ustaw tę opcję na  **próbkowanie**.

1. **Częstotliwość próbkowania**: wprowadź wartość z zakresu od 0 do 1. Ta wartość określa wartość procentową wierszy ze źródłowego zestawu danych, która powinna być uwzględniona w wyjściowym zestawie danych.

   Na przykład, jeśli chcesz tylko połowę oryginalnego zestawu danych, wpisz, `0.5` Aby wskazać, że częstotliwość próbkowania powinna wynosić 50%.

   Wiersze wejściowego zestawu danych są rozłożone i wybiórczo umieszczane w wyjściowym zestawie danych, zgodnie z określonym wskaźnikiem.

1. **Losowy inicjator do próbkowania**: Opcjonalnie wprowadź liczbę całkowitą, która ma być używana jako wartość inicjatora.

   Ta opcja jest ważna, jeśli wiersze mają być dzielone w taki sam sposób co czas. Wartość domyślna to **0**, co oznacza, że początkowy inicjator jest generowany na podstawie zegara systemowego. Ta wartość może prowadzić do nieco innych wyników przy każdym uruchomieniu potoku.

1. **Stratified podzielona na próbkowanie**: zaznacz tę opcję, jeśli ważne jest, aby wiersze w zestawie danych były dzielone równomiernie przez niektóre kolumny klucza przed próbkowanie.

   Dla **kolumny klucza stratyfikacji do próbkowania** wybierz jedną *kolumnę* , która ma być używana podczas dzielenia zestawu danych. Wiersze w zestawie danych są następnie podzielone w następujący sposób:

   1. Wszystkie wiersze wejściowe są pogrupowane (stratified) przez wartości w określonej kolumnie.

   1. Wiersze są losowo w poszczególnych grupach.

   1. Każda grupa jest wybiórczo dodawana do wyjściowego zestawu danych w celu spełnienia określonego stosunku.


1. Prześlij potok.

   Po wybraniu tej opcji moduł wyprowadza pojedynczy zestaw danych, który zawiera reprezentatywne próbkowanie danych. Pozostała część zestawu danych nie jest wyjściowa. 

## <a name="split-data-into-partitions"></a>Dzielenie danych na partycje

Użyj tej opcji, jeśli chcesz podzielić zestaw danych na podzbiory danych. Ta opcja jest również przydatna, gdy chcesz utworzyć niestandardową liczbę zagięć do krzyżowego sprawdzania poprawności lub podzielić wiersze na kilka grup.

1. Dodawanie **partycji i przykładowego** modułu do potoku oraz łączenie zestawu danych.

1. W obszarze **partycja lub przykład** wybierz pozycję **Przypisz do zgięcia**.

1. **Użyj zamiany na partycjonowanie**: zaznacz tę opcję, jeśli chcesz, aby wiersz próbkowany został umieszczony z powrotem w puli wierszy do ponownego użycia. W związku z tym ten sam wiersz może być przypisany do kilku zagięć.

   Jeśli nie używasz zamiany (opcja domyślna), wiersz próbkowania nie zostanie ponownie umieszczony w puli wierszy do ponownego użycia. W związku z tym każdy wiersz może być przypisany tylko do jednego złożenia.

1. **Losowy podział**: zaznacz tę opcję, jeśli chcesz, aby wiersze były losowo przypisywane do składania.

   Jeśli nie zaznaczysz tej opcji, wiersze są przypisywane do składania przez metodę okrężną.

1. **Losowy inicjator**: Opcjonalnie wprowadź liczbę całkowitą, która ma być używana jako wartość inicjatora. Ta opcja jest ważna, jeśli wiersze mają być dzielone w taki sam sposób co czas. W przeciwnym razie wartość domyślna **0** oznacza, że zostanie użyty losowy początkowy inicjator.

1. **Określ metodę partycjonowania**: wskaż, w jaki sposób dane mają zostać rozdzielone na każdą partycję przy użyciu następujących opcji:

   - **Podziel na partycje równomiernie**: Użyj tej opcji, aby umieścić równą liczbę wierszy w każdej partycji. Aby określić liczbę partycji wyjściowych, wprowadź liczbę całkowitą w polu **Określ liczbę zagięć, które mają być równo rozdzielone** .

   - **Podziel na partycje przy użyciu dostosowanych proporcji**: Użyj tej opcji, aby określić rozmiar każdej partycji jako listę rozdzieloną przecinkami.

     Załóżmy na przykład, że chcesz utworzyć trzy partycje. Pierwsza partycja będzie zawierać 50 procent danych. Pozostałe dwie partycje będą zawierać 25 procent danych. Na **liście proporcji rozdzielonych przecinkami** wprowadź następujące liczby: **.5,. 25,. 25**.

     Suma wszystkich rozmiarów partycji musi być dokładnie równa 1.

     Jeśli wprowadzisz liczby, które dodają do *mniej niż 1*, zostanie utworzona dodatkowa partycja do przechowywania pozostałych wierszy. Na przykład, jeśli wprowadzisz wartości **.2** i **.3**, trzecia partycja zostanie utworzona, aby pomieścić pozostałe 50 procent wszystkich wierszy.
     
     Jeśli wprowadzisz liczby, które dodają do *więcej niż 1*, zostanie zgłoszony błąd podczas uruchamiania potoku.

1. **Stratified**: zaznacz tę opcję, jeśli chcesz, aby wiersze były Stratified podczas dzielenia, a następnie wybierz _kolumnę strata_.

1. Prześlij potok.

   Po wybraniu tej opcji moduł wyprowadza wiele zestawów danych. Zestawy danych są partycjonowane zgodnie z określonymi regułami.

### <a name="use-data-from-a-predefined-partition"></a>Korzystanie z danych ze wstępnie zdefiniowanej partycji  

Użyj tej opcji, jeśli zestaw danych został podzielony na wiele partycji i teraz chcesz załadować każdą partycję w celu przeprowadzenia dalszej analizy lub przetwarzania.

1. Dodawanie **partycji i przykładowego** modułu do potoku.

1. Połącz moduł z danymi wyjściowymi poprzedniego wystąpienia **partycji i przykładu**. To wystąpienie musi używać opcji **Assign to zgięcia** w celu wygenerowania pewnej liczby partycji.

1. **Tryb partycji lub próbki**: wybierz pozycję Utwórz **złożenie**.

1. **Określ, z których składanie ma być próbkowane**: wybierz partycję, która ma zostać użyta, wprowadzając jej indeks. Indeksy partycji są oparte na 1. Na przykład jeśli zestaw danych został podzielony na trzy części, partycje będą miały indeksy 1, 2 i 3.

   Jeśli wprowadzisz nieprawidłową wartość indeksu, zostanie zgłoszony błąd czasu projektowania: "Error 0018: DataSet zawiera nieprawidłowe dane".

   Oprócz grupowania zestawu danych przez zgięcia można rozdzielić zestaw danych na dwie grupy: zgięcie docelowe i wszystko inne. W tym celu wprowadź indeks pojedynczego zgięcia, a następnie wybierz opcję **Wybierz uzupełnienie dla wybranego zgięcia** , aby uzyskać wszystko, ale dane w określonej ścieżce.

1. Jeśli pracujesz z wieloma partycjami, musisz dodać więcej wystąpień **partycji i przykładowego** modułu, aby obsłużyć każdą partycję.

   Na przykład **partycja i Przykładowa** moduł w drugim wierszu jest ustawiona do **przypisywania do zgięciów**, a moduł w trzecim wierszu ma ustawioną opcję **Wybierz złożenie**.   

   ![Partycja i przykład](./media/module/partition-and-sample.png)

1. Prześlij potok.

   Po wybraniu tej opcji moduł wyprowadza pojedynczy zestaw danych, który zawiera tylko wiersze przypisane do tego złożenia.

> [!NOTE]
>  Nie można bezpośrednio wyświetlić oznaczeń składania. Są one obecne tylko w metadanych.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 