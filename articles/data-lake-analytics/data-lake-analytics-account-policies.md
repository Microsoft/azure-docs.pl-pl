---
title: Zarządzanie zasadami konta Azure Data Lake Analytics
description: Dowiedz się, w jaki sposób używać zasad konta do kontrolowania użycia konta Data Lake Analytics, takich jak maksymalna wartość i Maksymalna liczba zadań.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: cf64424d1d422e599585b76fc068c940f9311b05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87127710"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Zarządzanie Azure Data Lake Analytics przy użyciu zasad konta

Zasady konta ułatwiają kontrolę sposobu używania zasobów Azure Data Lake Analytics konta. Te zasady umożliwiają kontrolę kosztów używania Azure Data Lake Analytics. Na przykład przy użyciu tych zasad można uniknąć nieoczekiwanego wzrostu kosztów poprzez ograniczenie liczby jednostek, które mogą być jednocześnie używane przez konto. # # zasady na poziomie konta

Te zasady mają zastosowanie do wszystkich zadań w ramach konta Data Lake Analytics. # # # Maksymalna liczba jednostek kontrolujących na koncie usługi Data Lake Analytics określa, że można użyć konta Data Lake Analytics przez zasady. Domyślnie wartość jest równa 250. Na przykład jeśli ta wartość jest ustawiona na 250, można mieć jedno zadanie z przypisaną do niej 250 lub 10 zadaniami z 25 wystąpieniami. Dodatkowe przesłane zadania są umieszczane w kolejce do momentu zakończenia wykonywanych zadań. Gdy uruchomione zadania są zakończone, należy zwolnić miejsce na zadania w kolejce do uruchomienia.

Aby zmienić liczbę jednostek dla konta Data Lake Analytics:

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Kliknij pozycję **limity i zasady**.
3. W obszarze **Maksymalna**wartość pola wyboru przesuń suwak, aby wybrać wartości, lub wprowadź wartość w polu tekstowym. 
4. Kliknij przycisk **Zapisz**.

   > [!NOTE]
   > Jeśli potrzebujesz więcej niż domyślnego (250), w portalu kliknij pozycję **Pomoc i obsługa techniczna** , aby przesłać żądanie pomocy technicznej. Można zwiększyć liczbę elementów dostępnych w Twoim koncie Data Lake Analytics.

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maksymalna liczba zadań, które mogą być uruchamiane jednocześnie
Te zasady ograniczają liczbę zadań, które mogą być uruchamiane jednocześnie. Domyślnie ta wartość jest równa 20. Jeśli Data Lake Analytics ma dostęp do tej jednostki, zaplanowano uruchomienie nowych zadań natychmiast do momentu, aż całkowita liczba uruchomionych zadań osiągnie wartość tych zasad. Po osiągnięciu maksymalnej liczby zadań, które mogą być uruchamiane jednocześnie, kolejne zadania są umieszczane w kolejności według priorytetu do momentu ukończenia jednego lub większej liczby uruchomionych zadań (w zależności od dostępnej wartości.

Aby zmienić liczbę zadań, które mogą być uruchamiane jednocześnie:

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Kliknij pozycję **limity i zasady**.
3. W obszarze **Maksymalna liczba uruchomionych zadań**przesuń suwak, aby wybrać wartość, lub wprowadź wartość w polu tekstowym. 
4. Kliknij przycisk **Zapisz**.

   > [!NOTE]
   > Jeśli potrzebujesz więcej niż domyślnej (20) liczby zadań, w portalu kliknij pozycję **Pomoc i obsługa techniczna** , aby przesłać żądanie pomocy technicznej. Można zwiększyć liczbę zadań, które mogą być uruchamiane jednocześnie na koncie Data Lake Analytics.

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Jak długo mają być przechowywane metadane i zasoby zadań 
Gdy użytkownicy uruchamiają zadania U-SQL, Usługa Data Lake Analytics zachowuje wszystkie powiązane pliki. Pliki te obejmują skrypt U-SQL, pliki DLL, do których odwołuje się skrypt U-SQL, skompilowane zasoby i statystyki. Pliki znajdują się w folderze/System/domyślnego konta Azure Data Lake Storage. Ta zasada kontroluje, jak długo te zasoby są przechowywane przed ich automatycznym usunięciem (wartość domyślna to 30 dni). Tych plików można używać do debugowania oraz do dostrajania wydajności zadań, które zostaną ponownie uruchomione w przyszłości.

Aby zmienić czas przechowywania metadanych i zasobów zadania:

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Kliknij pozycję **limity i zasady**.
3. W obszarze **dni, aby zachować zapytania dotyczące zadań**, przesuń suwak, aby wybrać wartość, lub wprowadź wartość w polu tekstowym.  
4. Kliknij przycisk **Zapisz**.

## <a name="job-level-policies"></a>Zasady na poziomie zadania

Zasady na poziomie zadania umożliwiają kontrolowanie maksymalnej wartości i maksymalnego priorytetu (lub członków określonych grup zabezpieczeń), które mogą być ustawione na zadania przesyłane. Te zasady umożliwiają kontrolę kosztów ponoszonych przez użytkowników. Pozwala również kontrolować wpływ zaplanowanych zadań na zadania produkcyjne o wysokim priorytecie, które są uruchomione na tym samym koncie Data Lake Analytics.

Data Lake Analytics ma dwie zasady, które można ustawić na poziomie zadania: * **Limit au dla każdego zadania**: użytkownicy mogą przesyłać tylko zadania, które mają do tej liczby. Domyślnie ten limit jest taki sam, jak maksymalny limit aktualizacji dla konta.
* **Priorytet**: użytkownicy mogą przesyłać tylko zadania o priorytecie niższym lub równym tej wartości. Wyższa liczba wskazuje niższy priorytet. Domyślnie ten limit jest ustawiony na 1, czyli najwyższy możliwy priorytet.

Na każdym koncie są ustawione zasady domyślne. Zasady domyślne dotyczą wszystkich użytkowników konta. Można utworzyć dodatkowe zasady dla określonych użytkowników i grup. 

> [!NOTE]
> Zasady na poziomie konta i zasady na poziomie zadania są stosowane jednocześnie.

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Dodawanie zasad dla określonego użytkownika lub grupy

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Kliknij pozycję **limity i zasady**.
3. W obszarze **limity dostarczania zadania**kliknij przycisk **Dodaj zasady** . Następnie wybierz lub wprowadź następujące ustawienia:
    1. **Nazwa zasad obliczeniowych**: Wprowadź nazwę zasad, aby przypominać o przeznaczeniu zasad.
    2. **Wybierz użytkownika lub grupę**: Wybierz użytkownika lub grupę, których dotyczą te zasady.
    3. **Ustaw limit opcji au zadania**: Ustaw limit aktualizacji automatycznych dotyczący wybranego użytkownika lub grupy.
    4. **Ustaw limit priorytetów**: Ustaw limit priorytetów dotyczący wybranego użytkownika lub grupy.

4. Kliknij przycisk **OK**.

5. Nowe zasady są wymienione w tabeli zasad **domyślnych** w obszarze limity dotyczące **wysyłania zadań**. # # # Usunięcie lub Edycja istniejących zasad

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Kliknij pozycję **limity i zasady**.
3. W obszarze **Limity wysyłania zadań**Znajdź zasady, które chcesz edytować.
4.  Aby wyświetlić opcje **usuwania** i **edycji** , w kolumnie po prawej stronie tabeli kliknij pozycję `...` . # # dodatkowe zasoby dla zasad dotyczących zadań
* [Wpis w blogu przegląd zasad](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Wpis w blogu dotyczący zasad na poziomie konta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Wpis w blogu dotyczący zasad na poziomie zadania](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)# # następne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Wprowadzenie do Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-get-started-portal.md)
* [Zarządzanie Azure Data Lake Analytics przy użyciu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)