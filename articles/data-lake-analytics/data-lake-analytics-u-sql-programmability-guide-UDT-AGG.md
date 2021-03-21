---
title: Podręcznik programowania U-SQL UDT i UDAGG dla Azure Data Lake
description: Dowiedz się więcej na temat programowania U-SQL UDT i UDAGG w Azure Data Lake Analytics, aby umożliwić tworzenie dobrego skryptu USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 10fcce9a667d9a08318f5adab804f482387052ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606661"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>Przewodnik programowania U-SQL — UDT i UDAGG



## <a name="use-user-defined-types-udt"></a>Użyj typów zdefiniowanych przez użytkownika: UDT
Typy zdefiniowane przez użytkownika lub UDT są kolejną funkcją programowalności języka U-SQL. Program U-SQL UDT działa jak zwykły typ zdefiniowany przez użytkownika w języku C#. C# jest jednoznacznie określonym językiem, który umożliwia korzystanie z wbudowanych i niestandardowych typów zdefiniowanych przez użytkownika.

Skrypt U-SQL nie może niejawnie serializować lub deserializować dowolnych UDTs, gdy UDT jest przenoszona między wierzchołkami w zestawach wierszy. Oznacza to, że użytkownik musi dostarczyć jawny program formatujący przy użyciu interfejsu IFormatter. Zapewnia to język U-SQL z metodami serializacji i deserializacji dla UDT.

> [!NOTE]
> Wbudowane wtyczki języka U-SQL i wypełniania nie można obecnie serializować lub deserializować danych UDT do lub z plików, nawet z zestawem IFormatter. Dlatego podczas zapisywania danych UDT do pliku z instrukcją OUTPUT lub odczytywania go za pomocą ekstraktora należy przekazać go jako ciąg lub tablicę bajtów. Następnie należy wywołać kod serializacji i deserializacji (oznacza to, że Metoda ToString () obiektu UDT jest jawnie. Zdefiniowane przez użytkownika programy wyodrębniające i wyskakujące, z drugiej strony, mogą odczytywać i zapisywać UDTs.

Jeśli spróbujemy użyć UDT w EKSTRAKTORze lub wypełnieniu (z poprzedniego wyboru), jak pokazano poniżej:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Zostanie wyświetlony następujący błąd:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Aby można było korzystać z UDT w wypełnieniu, musimy serializować go do ciągu za pomocą metody ToString () lub utworzyć niestandardowy element.

Nie można obecnie używać UDTs w grupie GROUP BY. Jeśli UDT jest używany w grupie przez, zgłaszany jest następujący błąd:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Aby zdefiniować UDT, musimy:

1. Dodaj następujące przestrzenie nazw:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. Dodaj `Microsoft.Analytics.Interfaces` , który jest wymagany dla interfejsów UDT. Ponadto `System.IO` może być konieczne zdefiniowanie interfejsu IFormatter.

3. Zdefiniuj użyty-zdefiniowany typ z atrybutem SqlUserDefinedType.

**SqlUserDefinedType** służy do oznaczania definicji typu w zestawie jako typu zdefiniowanego przez użytkownika (UDT) w języku U-SQL. Właściwości w atrybucie odzwierciedlają fizyczne cechy UDT. Klasa ta nie może być dziedziczona.

SqlUserDefinedType jest wymaganym atrybutem dla definicji UDT.

Konstruktor klasy:  

* SqlUserDefinedTypeAttribute (typ programu formatującego)

* Typ programu formatującego: parametr wymagany do zdefiniowania programu formatującego typu UDT — w `IFormatter` tym celu należy przesłać tutaj typ interfejsu.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typowy typ UDT wymaga również definicji interfejsu IFormatter, jak pokazano w następującym przykładzie:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter`Interfejs umożliwia serializacji i deserializacji grafu obiektów z typem głównym \<typeparamref name="T"> .

\<typeparam name="T">Typ główny grafu obiektów do serializacji i deserializacji.

* **Deserializacja: deserializacji dane** z dostarczonego strumienia i reprodukcji grafu obiektów.

* **Serializacja**: serializacja obiektu lub grafu obiektów z podanym elementem głównym do podanego strumienia.

`MyType` wystąpienie: wystąpienie typu.  
`IColumnWriter` składnik zapisywania/ `IColumnReader` czytelnik: źródłowy strumień kolumn.  
`ISerializationContext` Context: enum, który definiuje zestaw flag, który określa źródłowy lub docelowy kontekst strumienia podczas serializacji.

* **Pośredni**: określa, że kontekst źródłowy lub docelowy nie jest magazynem utrwalonym.

* **Trwałość**: określa, że kontekst źródłowy lub docelowy to utrwalony magazyn.

Jako zwykły typ C# definicja UDT języka U-SQL może zawierać przesłonięcia dla operatorów, takich jak +/= =/! =. Może również zawierać metody statyczne. Na przykład jeśli będziemy używać tego UDT jako parametru do minimalnej funkcji agregującej U-SQL, musimy zdefiniować < przesłonięcia operatora.

Wcześniej w tym przewodniku przedstawiono przykład identyfikacji okresu obrachunkowego z określonego dnia w formacie `Qn:Pn (Q1:P10)` . Poniższy przykład pokazuje, jak zdefiniować typ niestandardowy dla wartości okresu obrachunkowego.

Poniżej znajduje się przykład sekcji związanej z kodem z niestandardowym interfejsem UDT i IFormatter:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Zdefiniowany typ zawiera dwie liczby: kwartał i miesiąc. Operatory `==/!=/>/<` i metoda statyczna `ToString()` są zdefiniowane w tym miejscu.

Jak wspomniano wcześniej, można użyć UDT w wyrażeniach SELECT, ale nie można używać go w wypełnieniu/EKSTRAKTORze bez serializacji niestandardowej. Musi być serializowany jako ciąg z `ToString()` lub używany z niestandardowym wyciągiem/ekstraktorem.

Teraz omawiamy użycie UDT. W sekcji kodu została zmieniona nasza funkcja GetFiscalPeriod na następujące:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Jak widać, zwraca wartość naszego typu FiscalPeriod.

Tutaj przedstawiono przykład korzystania z niego w skrypcie podstawowym języka U-SQL. W tym przykładzie pokazano różne formy wywołania UDT ze skryptu U-SQL.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
        start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        fiscalquarter,
        fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Oto przykład pełnej sekcji dotyczącej kodu:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Użyj agregacji zdefiniowanych przez użytkownika: UDAGG
Agregacje zdefiniowane przez użytkownika to wszelkie funkcje związane z agregacją, które nie są dostarczane z użyciem języka U-SQL. Przykładem może być agregacja, która umożliwia wykonywanie niestandardowych obliczeń matematycznych, łączenie ciągów, manipulowanie za pomocą ciągów i tak dalej.

Zdefiniowana przez użytkownika definicja klasy bazowej agregacji jest następująca:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** wskazuje, że typ powinien być zarejestrowany jako agregacja zdefiniowana przez użytkownika. Klasa ta nie może być dziedziczona.

Atrybut SqlUserDefinedType jest **opcjonalny** dla definicji UDAGG.


Klasa bazowa umożliwia przekazywanie trzech parametrów abstrakcyjnych: dwa jako parametry wejściowe i jeden jako wynik. Typy danych są zmienne i powinny być zdefiniowane podczas dziedziczenia klas.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** wywołuje jeden raz dla każdej grupy podczas obliczania. Zapewnia procedurę inicjowania dla każdej grupy agregacji.  
* **Gromadzenie** jest wykonywane raz dla każdej wartości. Zapewnia główną funkcjonalność algorytmu agregacji. Może służyć do agregowania wartości przy użyciu różnych typów danych, które są zdefiniowane podczas dziedziczenia klas. Może akceptować dwa parametry zmiennych typów danych.
* **Zakończenie** jest wykonywane raz dla każdej grupy agregacji na końcu przetwarzania, aby wyprowadzić wynik dla każdej grupy.

Aby zadeklarować poprawne dane wejściowe i wyjściowe, Użyj definicji klasy w następujący sposób:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: pierwszy parametr do akumulacji
* T2: drugi parametr do akumulacji
* TResult: zwracany typ przerwania

Na przykład:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

lub

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Używanie UDAGG w języku U-SQL
Aby użyć UDAGG, najpierw Zdefiniuj ją w kodzie lub odwołuje się do niej z istniejącej biblioteki DLL programowania, jak opisano wcześniej.

Następnie użyj następującej składni:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Oto przykład UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

I podstawowy skrypt U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

W tym scenariuszu przypadku użycia łączymy identyfikatory GUID klas dla określonych użytkowników.

## <a name="next-steps"></a>Następne kroki
* [Przewodnik programowania U-SQL — Omówienie](data-lake-analytics-u-sql-programmability-guide.md)
* [Przewodnik programowania U-SQL — UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
