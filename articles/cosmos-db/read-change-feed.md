---
title: Uzyskiwanie dostępu do źródła zmian w Azure Cosmos DB Azure Cosmos DB
description: W tym artykule opisano różne opcje dostępne do odczytu i dostępu do źródła zmian w Azure Cosmos DB Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74688127"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Odczytywanie zestawienia zmian w usłudze Azure Cosmos DB

Ze źródłem zmian Azure Cosmos DB można korzystać z jednej z następujących opcji:

* Korzystanie z usługi Azure Functions
* Korzystanie z biblioteki procesora zestawienia zmian
* Korzystanie z zestawu SDK interfejsu API SQL usługi Azure Cosmos DB

## <a name="using-azure-functions"></a>Korzystanie z usługi Azure Functions

Azure Functions jest najprostszą i zalecaną opcją. Po utworzeniu wyzwalacza Azure Functions dla Cosmos DB, można wybrać kontener do połączenia, a funkcja platformy Azure zostanie wyzwolona za każdym razem, gdy nastąpi zmiana do kontenera. Wyzwalacze można tworzyć przy użyciu portalu Azure Functions, portalu Azure Cosmos DB lub programowo przy użyciu zestawów SDK. Program Visual Studio i VS Code zapewniają pomoc techniczną do pisania Azure Functions i można nawet użyć interfejsu wiersza polecenia Azure Functions do tworzenia aplikacji dla wielu platform. Można napisać i debugować kod na pulpicie, a następnie wdrożyć funkcję jednym kliknięciem. Aby dowiedzieć się więcej, zobacz artykuł [Przetwarzanie baz danych bez serwera przy użyciu Azure Functions](serverless-computing-database.md) i [Używanie kanału informacyjnego zmian z Azure Functions](change-feed-functions.md)).

## <a name="using-the-change-feed-processor-library"></a>Korzystanie z biblioteki procesora zestawienia zmian

Biblioteka procesora kanału informacyjnego zmian powoduje ukrycie złożoności i zapewnia pełną kontrolę nad źródłem zmian. Biblioteka jest zgodna ze wzorcem obserwatora, gdzie funkcja przetwarzania jest wywoływana przez bibliotekę. W przypadku kanału informacyjnego o wysokiej przepływności można utworzyć wystąpienie wielu klientów w celu odczytania źródła zmian. Ze względu na to, że korzystasz z biblioteki procesora źródła zmian, będzie ona automatycznie dzielić obciążenie między różnymi klientami, bez konieczności implementowania tej logiki. Cała złożoność jest obsługiwana przez bibliotekę. Jeśli chcesz mieć własny moduł równoważenia obciążenia, możesz wdrożyć `IPartitionLoadBalancingStrategy` dla niestandardowej strategii partycji, aby przetwarzać Źródło zmian. Aby dowiedzieć się więcej, zobacz [Korzystanie z biblioteki procesora źródła zmian](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Korzystanie z zestawu SDK interfejsu API SQL usługi Azure Cosmos DB

Zestaw SDK otrzymuje kontrolę nad źródłem zmian. Punkt kontrolny można zarządzać, uzyskiwać dostęp do określonego klucza partycji logicznej itp. Jeśli masz wielu czytników, możesz użyć `ChangeFeedOptions` do dystrybucji wczytywania odczytu do różnych wątków lub różnych klientów.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Źródło zmian w interfejsach API dla Cassandra i MongoDB

Funkcja zmiany kanału informacyjnego jest oferowana jako strumień zmiany w interfejsie API MongoDB i zapytanie z predykatem w interfejs API Cassandra. Aby dowiedzieć się więcej na temat szczegółów implementacji interfejsu API MongoDB, zobacz [zmiana strumieni w interfejsie api Azure Cosmos DB dla MongoDB](mongodb-change-streams.md).

Natywny program Apache Cassandra udostępnia funkcję przechwytywania zmian danych (rerzucij), mechanizm do flagi określonych tabel do archiwizacji, a także odrzucanie zapisów w tych tabelach po osiągnięciu konfigurowalnego rozmiaru na dysku dla dziennika przechwytywania zmian. Funkcja zmiany kanału informacyjnego w interfejsie Azure Cosmos DB API for Cassandra zwiększa możliwość wykonywania zapytań o zmiany przy użyciu predykatu za pośrednictwem CQL. Aby dowiedzieć się więcej na temat szczegółów implementacji, zobacz temat [Zmiana kanału informacyjnego w interfejsie API Azure Cosmos DB Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej na temat źródła zmian w następujących artykułach:

* [Przegląd źródła zmian](change-feed.md)
* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)
* [Korzystanie z biblioteki procesora kanału informacyjnego zmiany](change-feed-processor.md)
