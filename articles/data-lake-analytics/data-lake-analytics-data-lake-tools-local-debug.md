---
title: Debuguj kod Azure Data Lake Analytics lokalnie
description: Dowiedz się, jak za pomocą Azure Data Lake Tools for Visual Studio debugować zadania U-SQL na lokalnej stacji roboczej.
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: how-to
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 7176e2f64faa8e5b43574084a9cbd947dbd576d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87125640"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Debuguj kod Azure Data Lake Analytics lokalnie

Za pomocą Azure Data Lake Tools for Visual Studio można uruchamiać i debugować kod Azure Data Lake Analytics na lokalnej stacji roboczej, podobnie jak w przypadku usługi Azure Data Lake Analytics.

Dowiedz się, jak [uruchomić skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Debugowanie skryptów i zestawów języka C# lokalnie

Można debugować zestawy języka C# bez przesyłania i rejestrowania ich w usłudze Azure Data Lake Analytics. Można ustawić punkty przerwania zarówno w pliku związanym z kodem, jak i w przywoływanym projekcie języka C#.

### <a name="debug-local-code-in-a-code-behind-file"></a>Debuguj kod lokalny w pliku związanym z kodem

1. Ustaw punkty przerwania w pliku związanym z kodem.
2. Wybierz klawisz **F5** , aby debugować skrypt lokalnie.

> [!NOTE]
   > Poniższa procedura działa tylko w programie Visual Studio 2015. W starszych wersjach programu Visual Studio może być konieczne ręczne dodanie plików **PDB** .  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Debuguj kod lokalny w przywoływanym projekcie języka C#

1. Utwórz projekt zestawu języka C# i skompiluj go w celu wygenerowania wyjściowego pliku **dll** .
2. Zarejestruj plik **dll** przy użyciu instrukcji języka U-SQL:

   ```sql
   CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
   ```
   
3. Ustaw punkty przerwania w kodzie C#.
4. Wybierz klawisz **F5** , aby debugować skrypt, odwołując się do pliku **dll** języka C# lokalnie.


## <a name="next-steps"></a>Następne kroki

- Przykład bardziej złożonej kwerendy znajduje się w temacie [Analizowanie dzienników witryn sieci Web przy użyciu Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Aby wyświetlić szczegóły zadania, zobacz [Korzystanie z przeglądarki zadań i widoku zadań dla Azure Data Lake Analytics zadań](data-lake-analytics-data-lake-tools-view-jobs.md).
- Aby skorzystać z widoku wykonywania wierzchołków, zobacz temat [Używanie widoku wykonywania wierzchołka w Data Lake narzędzia dla programu Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
