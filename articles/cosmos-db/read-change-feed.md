---
title: Uzyskiwanie dostępu do źródła zmian w Azure Cosmos DB Azure Cosmos DB
description: W tym artykule opisano różne opcje dostępne do odczytu i dostępu do źródła zmian w Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 266a13b7702f567e69129c0b4e92c4bd7c0f29ef
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982500"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Odczytywanie zestawienia zmian w usłudze Azure Cosmos DB

Ze źródłem zmian Azure Cosmos DB można korzystać z jednej z następujących opcji:

* Korzystanie z usługi Azure Functions
* Używanie procesora źródła zmian
* Korzystanie z zestawu SDK interfejsu API SQL usługi Azure Cosmos DB
* Korzystanie z modelu ściągania zmian (wersja zapoznawcza)

## <a name="using-azure-functions"></a>Korzystanie z usługi Azure Functions

Azure Functions jest najprostszą i zalecaną opcją. Po utworzeniu wyzwalacza Azure Functions dla Cosmos DB, można wybrać kontener do połączenia, a funkcja platformy Azure zostanie wyzwolona za każdym razem, gdy nastąpi zmiana do kontenera. Wyzwalacze można tworzyć przy użyciu portalu Azure Functions, portalu Azure Cosmos DB lub programowo przy użyciu zestawów SDK. Program Visual Studio i VS Code zapewniają pomoc techniczną do pisania Azure Functions i można nawet użyć interfejsu wiersza polecenia Azure Functions do tworzenia aplikacji dla wielu platform. Można napisać i debugować kod na pulpicie, a następnie wdrożyć funkcję jednym kliknięciem. Aby dowiedzieć się więcej, zobacz artykuł [Przetwarzanie baz danych bez serwera przy użyciu Azure Functions](serverless-computing-database.md) i [Używanie kanału informacyjnego zmian z Azure Functions](change-feed-functions.md)).

## <a name="using-the-change-feed-processor"></a>Używanie procesora źródła zmian

Procesor źródła zmian powoduje ukrycie złożoności i zapewnia pełną kontrolę nad źródłem zmian. Biblioteka jest zgodna ze wzorcem obserwatora, gdzie funkcja przetwarzania jest wywoływana przez bibliotekę. W przypadku kanału informacyjnego o wysokiej przepływności można utworzyć wystąpienie wielu klientów w celu odczytania źródła zmian. Ze względu na to, że korzystasz z biblioteki procesora źródła zmian, będzie ona automatycznie dzielić obciążenie między różnymi klientami, bez konieczności implementowania tej logiki. Cała złożoność jest obsługiwana przez bibliotekę. Aby dowiedzieć się więcej, zobacz [using the Change podajnik procesora](change-feed-processor.md). Procesor kanału informacyjnego zmian jest częścią [zestawu Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Korzystanie z zestawu SDK interfejsu API SQL usługi Azure Cosmos DB

Zestaw SDK otrzymuje kontrolę nad źródłem zmian. Punkt kontrolny można zarządzać, uzyskiwać dostęp do określonego klucza partycji logicznej itp. Jeśli masz wielu czytników, możesz użyć `ChangeFeedOptions` do dystrybucji wczytywania odczytu do różnych wątków lub różnych klientów.

## <a name="using-the-change-feed-pull-model"></a>Używanie modelu ściągania źródła zmian

[Model ściągania źródła zmian](change-feed-pull-model.md) umożliwia korzystanie z kanału informacyjnego zmiany we własnym tempie i zrównoleglanie przetwarzania zmian z FeedRanges. FeedRange obejmuje zakres wartości klucza partycji. Przy użyciu modelu ściągania źródła zmian można również łatwo przetwarzać zmiany dla określonego klucza partycji.

> [!NOTE]
> Model ściągania źródła zmian jest obecnie w [wersji zapoznawczej tylko w Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . Wersja zapoznawcza nie jest jeszcze dostępna dla innych wersji zestawu SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Źródło zmian w interfejsach API dla Cassandra i MongoDB

Funkcja zmiany kanału informacyjnego jest oferowana jako strumień zmiany w interfejsie API MongoDB i zapytanie z predykatem w interfejs API Cassandra. Aby dowiedzieć się więcej na temat szczegółów implementacji interfejsu API MongoDB, zobacz [zmiana strumieni w interfejsie api Azure Cosmos DB dla MongoDB](mongodb-change-streams.md).

Natywny program Apache Cassandra udostępnia funkcję przechwytywania zmian danych (rerzucij), mechanizm do flagi określonych tabel do archiwizacji, a także odrzucanie zapisów w tych tabelach po osiągnięciu konfigurowalnego rozmiaru na dysku dla dziennika przechwytywania zmian. Funkcja zmiany kanału informacyjnego w interfejsie Azure Cosmos DB API for Cassandra zwiększa możliwość wykonywania zapytań o zmiany przy użyciu predykatu za pośrednictwem CQL. Aby dowiedzieć się więcej na temat szczegółów implementacji, zobacz temat [Zmiana kanału informacyjnego w interfejsie API Azure Cosmos DB Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej na temat źródła zmian w następujących artykułach:

* [Przegląd źródła zmian](change-feed.md)
* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)
* [Korzystanie z biblioteki procesora kanału informacyjnego zmiany](change-feed-processor.md)
