---
title: Tworzenie zdefiniowanych przez użytkownika operatorów U-SQL — Azure Data Lake Analytics
description: Dowiedz się, jak opracowywać zdefiniowane przez użytkownika operatory do użycia i ponownego użycia w Azure Data Lake Analytics zadaniach.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: 11efdb727bacadb674fb49374ef1c70fcc788ecc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219993"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Tworzenie zdefiniowanych przez użytkownika operatorów U-SQL (udo)
W tym artykule opisano sposób tworzenia operatorów zdefiniowanych przez użytkownika w celu przetwarzania danych w zadaniu U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definiowanie i używanie operatora zdefiniowanego przez użytkownika w języku U-SQL

### <a name="to-create-and-submit-a-u-sql-job"></a>Aby utworzyć i przesłać zadanie U-SQL

1. W programie Visual Studio wybierz kolejno pozycje **plik > nowy > projekt > projekt U-SQL**.
2. Kliknij przycisk **OK**. Program Visual Studio tworzy rozwiązanie przy użyciu pliku Script. usql.
3. W **Eksplorator rozwiązań** rozwiń węzeł Script. usql, a następnie kliknij dwukrotnie pozycję **Script. usql. cs**.
4. Wklej poniższy kod do pliku:

   ```usql
   using Microsoft.Analytics.Interfaces;
   using System.Collections.Generic;
   namespace USQL_UDO
   {
       public class CountryName : IProcessor
       {
           private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
           {
               {
                   "Deutschland", "Germany"
               },
               {
                   "Suisse", "Switzerland"
               },
               {
                   "UK", "United Kingdom"
               },
               {
                   "USA", "United States of America"
               },
               {
                   "中国", "PR China"
               }
           };
           public override IRow Process(IRow input, IUpdatableRow output)
           {
               string UserID = input.Get<string>("UserID");
               string Name = input.Get<string>("Name");
               string Address = input.Get<string>("Address");
               string City = input.Get<string>("City");
               string State = input.Get<string>("State");
               string PostalCode = input.Get<string>("PostalCode");
               string Country = input.Get<string>("Country");
               string Phone = input.Get<string>("Phone");
               if (CountryTranslation.Keys.Contains(Country))
               {
                   Country = CountryTranslation[Country];
               }
               output.Set<string>(0, UserID);
               output.Set<string>(1, Name);
               output.Set<string>(2, Address);
               output.Set<string>(3, City);
               output.Set<string>(4, State);
               output.Set<string>(5, PostalCode);
               output.Set<string>(6, Country);
               output.Set<string>(7, Phone);
               return output.AsReadOnly();
           }
       }
   }
   ```

5. Otwórz **skrypt. usql** i wklej następujący skrypt U-SQL:

   ```usql
   @drivers =
       EXTRACT UserID      string,
               Name        string,
               Address     string,
               City        string,
               State       string,
               PostalCode  string,
               Country     string,
               Phone       string
       FROM "/Samples/Data/AmbulanceData/Drivers.txt"
       USING Extractors.Tsv(Encoding.Unicode);

   @drivers_CountryName =
       PROCESS @drivers
       PRODUCE UserID string,
               Name string,
               Address string,
               City string,
               State string,
               PostalCode string,
               Country string,
               Phone string
       USING new USQL_UDO.CountryName();

   OUTPUT @drivers_CountryName
       TO "/Samples/Outputs/Drivers.csv"
       USING Outputters.Csv(Encoding.Unicode);
   ```

6. Określ konto usługi Data Lake Analytics, bazę danych i schemat.
7. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Build Script** (Kompiluj skrypt).
8. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Submit Script** (Prześlij skrypt).
9. Jeśli nie masz połączenia z subskrypcją platformy Azure, zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure.
10. Kliknij przycisk **Prześlij**. Wyniki przesłania i link zadania są dostępne w oknie wyników po zakończeniu przesłania.
11. Kliknij przycisk **Odśwież** , aby wyświetlić najnowszy stan zadania i odświeżyć ekran.

### <a name="to-see-the-output"></a>Aby wyświetlić dane wyjściowe

1. W **Eksplorator serwera** rozwiń węzeł **Azure**, rozwiń pozycję **Data Lake Analytics**, rozwiń konto Data Lake Analytics, rozwiń węzeł **konta magazynu**, kliknij prawym przyciskiem myszy magazyn domyślny, a następnie kliknij pozycję **Eksplorator**.

2. Rozwiń pozycję przykłady, rozwiń węzeł dane wyjściowe, a następnie kliknij dwukrotnie przycisk **Drivers.csv**.

## <a name="next-steps"></a>Następne kroki

* [Rozszerzanie wyrażeń U-SQL z kodem użytkownika](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Używanie narzędzi Data Lake Tools for Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
