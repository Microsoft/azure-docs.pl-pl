---
title: Wprowadzenie z usługą Azure Storage przy użyciu programu Visual Studio (projekty WebJob)
description: Jak rozpocząć korzystanie z usługi Azure Table Storage w projekcie Azure WebJobs w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e4d8299c06bfa5b0f33bff8fa592a2fa549c695c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74707616"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Wprowadzenie z usługą Azure Storage (projekty zadań WebJob platformy Azure)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Omówienie
W tym artykule przedstawiono przykłady kodu w języku C#, które pokazują, jak używać zestawu SDK Azure WebJobs w wersji 1. x w usłudze Azure Table Storage. Przykłady kodu używają [zestawu SDK zadań WebJob](https://github.com/Azure/azure-webjobs-sdk/wiki) w wersji 1. x.

Usługa Azure Table Storage umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa to magazyn danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.  Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) .

Niektóre fragmenty kodu pokazują atrybut **tabeli** używany w funkcjach, które są wywoływane ręcznie, czyli nie przy użyciu jednego z atrybutów wyzwalacza.

## <a name="how-to-add-entities-to-a-table"></a>Jak dodać jednostki do tabeli

Aby dodać jednostki do tabeli, Użyj atrybutu **Table** z **ICollector\<>t** lub **IAsyncCollector\<t>** , gdzie **T** określa schemat jednostek, które chcesz dodać. Konstruktor atrybutu przyjmuje parametr ciągu, który określa nazwę tabeli.

Poniższy przykładowy kod dodaje jednostki **osoby** do tabeli o nazwie transfery *wejściowe*.

```csharp
[NoAutomaticTrigger]
public static void IngressDemo(
    [Table("Ingress")] ICollector<Person> tableBinding)
{
    for (int i = 0; i < 100000; i++)
    {
        tableBinding.Add(
            new Person() {
                PartitionKey = "Test",
                RowKey = i.ToString(),
                Name = "Name" }
            );
    }
}
```

Zazwyczaj typ używany z **ICollector** pochodzi z **klasy tableentity** lub implementuje **ITableEntity**, ale nie jest to konieczne. Jedna z następujących klas **osób** współpracuje z kodem pokazanym **w poprzedniej** metodzie transferu danych przychodzących.

```csharp
public class Person : TableEntity
{
    public string Name { get; set; }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Jeśli chcesz bezpośrednio współpracować z interfejsem API usługi Azure Storage, możesz dodać parametr **CloudStorageAccount** do sygnatury metody.

## <a name="real-time-monitoring"></a>Monitorowanie w czasie rzeczywistym

Ponieważ funkcja transferu danych przychodzących często przetwarza duże ilości danych, pulpit nawigacyjny zestawu SDK zadań WebJob udostępnia dane monitorowania w czasie rzeczywistym. Sekcja **dziennika wywołania** informuje o tym, czy funkcja jest nadal uruchomiona.

![Uruchomiono funkcję transferu danych przychodzących](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Na stronie **szczegóły wywołania** jest raportowany postęp funkcji (liczba utworzonych jednostek), gdy jest ona uruchomiona, i umożliwia jej przerwanie.

![Uruchomiono funkcję transferu danych przychodzących](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Po zakończeniu działania strony **szczegóły wywołania** zgłasza liczbę utworzonych wierszy.

![Zakończono funkcję transferu danych przychodzących](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Jak odczytać wiele jednostek z tabeli

Aby odczytać tabelę, Użyj atrybutu **Table** z parametrem **IQueryable\<T>** , gdzie Type **t** pochodzi z **klasy tableentity** lub implementuje **ITableEntity**.

Poniższy przykładowy kod odczytuje i rejestruje wszystkie wiersze z tabeli danych **wejściowych** :

```csharp
public static void ReadTable(
    [Table("Ingress")] IQueryable<Person> tableBinding,
    TextWriter logger)
{
    var query = from p in tableBinding select p;
    foreach (Person person in query)
    {
        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
            person.PartitionKey, person.RowKey, person.Name);
    }
}
```

### <a name="how-to-read-a-single-entity-from-a-table"></a>Jak odczytać pojedynczą jednostkę z tabeli

Istnieje Konstruktor atrybutu **tabeli** z dwoma dodatkowymi parametrami, które umożliwiają określenie klucza partycji i klucza wiersza, gdy chcesz powiązać z pojedynczą jednostką tabeli.

Poniższy przykładowy kod odczytuje wiersz tabeli dla jednostki **osoby** na podstawie klucza partycji i wartości klucza wiersza odebranych w komunikacie kolejki:

```csharp
public static void ReadTableEntity(
    [QueueTrigger("inputqueue")] Person personInQueue,
    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
    TextWriter logger)
{
    if (personInTable == null)
    {
        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                personInQueue.PartitionKey, personInQueue.RowKey);
    }
    else
    {
        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
    }
}
```

Klasa **Person** w tym przykładzie nie musi implementować **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Jak używać interfejsu API usługi .NET Storage bezpośrednio do pracy z tabelą

Można również użyć atrybutu **Table** z obiektem w **chmurze** , aby uzyskać większą elastyczność pracy z tabelą.

Poniższy przykład kodu używa obiektu tabeli w **chmurze** w celu dodania pojedynczej jednostki do tabeli danych *wejściowych* .

```csharp
public static void UseStorageAPI(
    [Table("Ingress")] CloudTable tableBinding,
    TextWriter logger)
{
    var person = new Person()
        {
            PartitionKey = "Test",
            RowKey = "100",
            Name = "Name"
        };
    TableOperation insertOperation = TableOperation.Insert(person);
    tableBinding.Execute(insertOperation);
}
```

Aby uzyskać więcej informacji o sposobach korzystania z obiektu w **chmurze** , zobacz [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Tematy pokrewne omówione w artykule Instrukcje dotyczące kolejek

Aby dowiedzieć się, jak obsłużyć przetwarzanie tabeli wyzwalane przez komunikat kolejki lub dla scenariuszy zestawu SDK zadań WebJob, które nie są specyficzne dla przetwarzania tabel, zobacz [wprowadzenie do usługi Azure queue storage i usług połączonych programu Visual Studio (projekty zadań WebJob)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze pracy z tabelami platformy Azure. Więcej informacji o sposobach używania Azure WebJobs i zestawu SDK usługi WebJobs znajduje się w temacie [Azure WebJobs zasoby dokumentacji](https://go.microsoft.com/fwlink/?linkid=390226).
