---
title: Odczytywanie zestawienia zmian w usłudze Azure Cosmos DB
description: W tym artykule opisano różne opcje dostępne do odczytu i dostępu do źródła zmian w Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/27/2020
ms.reviewer: sngun
ms.openlocfilehash: 0252af90a6afb5b2a59620afaa61702f208991e7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785259"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Odczytywanie zestawienia zmian w usłudze Azure Cosmos DB

Możesz współpracować ze źródłem zmian Azure Cosmos DB, korzystając z modelu wypychania lub modelu ściągania. W przypadku modelu wypychania procesor kanału informacyjnego zmiany wypycha zadania do klienta, który ma logikę biznesową na potrzeby przetwarzania tej pracy. Jednak złożoność sprawdzania pracy i przechowywania stanu dla ostatnio przetworzonej pracy jest obsługiwana w ramach procesora źródła zmian.

W przypadku modelu ściągania klient musi ściągnąć swoją służbę z serwera. Klient w tym przypadku nie tylko ma logikę biznesową do przetwarzania pracy, ale również zapisuje stan dla ostatnio przetworzonej pracy, obsługując Równoważenie obciążenia między wieloma klientami i przetwarza błędy.

Podczas odczytywania ze źródła zmian Azure Cosmos DB zwykle zalecamy korzystanie z modelu wypychania, ponieważ nie trzeba martwić się o:

- Sondowanie źródła zmian pod kątem przyszłych zmian.
- Przechowywanie stanu dla ostatnio przetworzonej zmiany. Podczas odczytywania ze źródła zmian jest on automatycznie przechowywany w [kontenerze dzierżawy](change-feed-processor.md#components-of-the-change-feed-processor).
- Równoważenie obciążenia na wielu klientach korzystających ze zmian. Na przykład jeśli jeden klient nie może zachować zmian w przetwarzaniu, a drugi ma dostępną pojemność.
- [Obsługa błędów](change-feed-processor.md#error-handling). Na przykład automatyczne ponawianie nieudanych zmian, które zostały nieprawidłowo przetworzone po nieobsługiwanym wyjątku w kodzie lub przejściowym problemie z siecią.

Większość scenariuszy korzystających ze źródła zmian Azure Cosmos DB będzie korzystać z jednej z opcji modelu wypychania. Istnieje jednak kilka scenariuszy, w których można chcieć uzyskać dodatkową kontrolę nad modelem na niskim poziomie. Należą do nich:

- Odczytywanie zmian z określonego klucza partycji
- Kontrolowanie tempa, w którym klient otrzymuje zmiany w celu przetworzenia
- Jednorazowe odczytywanie istniejących danych ze źródła zmian (na przykład w celu przeprowadzenia migracji danych)

## <a name="reading-change-feed-with-a-push-model"></a>Odczytywanie źródła zmian z modelem wypychania

Korzystanie z modelu wypychania to najprostszy sposób odczytywania ze źródła zmian. Istnieją dwa sposoby odczytywania ze źródła zmian przy użyciu modelu wypychania: [Azure Functions Cosmos DB wyzwalacze](change-feed-functions.md) i [Biblioteka procesora źródła zmian](change-feed-processor.md). Azure Functions korzysta ze współdziałania procesora źródła zmian w tle, dlatego są one bardzo podobne do odczytu źródła zmian. Należy wziąć pod uwagę Azure Functions jak po prostu platformą hostingu procesora, a nie zupełnie innym sposobem odczytywania źródła zmian.

### <a name="azure-functions"></a>Azure Functions

Azure Functions jest najprostszą opcją, jeśli dopiero zaczynasz korzystać ze źródła zmian. Ze względu na prostotę jest to również zalecana opcja dla większości przypadków użycia źródła zmian. Po utworzeniu wyzwalacza Azure Functions dla Azure Cosmos DB, należy wybrać kontener do połączenia, a funkcja platformy Azure zostanie wyzwolona za każdym razem, gdy istnieje zmiana w kontenerze. Ponieważ Azure Functions używa procesora danych zmiany w tle, automatycznie parallelizes przetwarzanie zmian w [partycjach](partitioning-overview.md)kontenera.

Programowanie przy użyciu Azure Functions jest prostym doświadczeniem i może być szybsze niż wdrożenie procesora źródła zmian. Wyzwalacze można tworzyć przy użyciu portalu Azure Functions lub programowo przy użyciu zestawów SDK. Program Visual Studio i VS Code zapewniają pomoc techniczną do pisania Azure Functions i można nawet użyć interfejsu wiersza polecenia Azure Functions do tworzenia aplikacji dla wielu platform. Można napisać i debugować kod na pulpicie, a następnie wdrożyć funkcję jednym kliknięciem. Aby dowiedzieć się więcej, zobacz temat [Przetwarzanie baz danych bez użycia serwera przy użyciu Azure Functions](serverless-computing-database.md) i [Używanie kanału informacyjnego zmian z](change-feed-functions.md) artykułami Azure Functions.

### <a name="change-feed-processor-library"></a>Zmień bibliotekę procesora kanału informacyjnego

Procesor ze źródłem zmian zapewnia większą kontrolę nad źródłem zmian i nadal ukrywa większość złożoności. Biblioteka procesora kanału informacyjnego zmiany jest zgodna ze wzorcem obserwatora, gdzie funkcja przetwarzania jest wywoływana przez bibliotekę. Biblioteka procesora kanału informacyjnego zmiany automatycznie sprawdzi zmiany i, jeśli zostaną znalezione zmiany, wypchnij je do klienta. W przypadku kanału informacyjnego o wysokiej przepływności można utworzyć wystąpienie wielu klientów w celu odczytania źródła zmian. Biblioteka procesora kanału informacyjnego zmiany automatycznie dzieli obciążenie między różne komputery klienckie. Nie trzeba implementować żadnej logiki do równoważenia obciążenia na wielu klientach ani żadnej logiki w celu utrzymania stanu dzierżawy.

Biblioteka procesora kanału informacyjnego zmiany gwarantuje dostarczenie wszystkich zmian w "co najmniej raz". Innymi słowy, jeśli używasz biblioteki procesora źródła zmian, funkcja przetwarzania zostanie wywołana pomyślnie dla każdego elementu w strumieniu zmian. Jeśli w logice biznesowej w funkcji przetwarzania wystąpi nieobsługiwany wyjątek, nieudane zmiany zostaną ponowione, dopóki nie zostaną pomyślnie przetworzone. Aby zapobiec nieudanej próbie wykonania tych samych zmian przez procesor kanału informacyjnego zmiany, Dodaj logikę w funkcji przetwarzania, aby napisać dokumenty, po wyjątku do kolejki utraconych wiadomości. Dowiedz się więcej o [obsłudze błędów](change-feed-processor.md#error-handling).

W Azure Functions zalecenia dotyczące obsługi błędów są takie same. Nadal należy dodać logikę w kodzie delegata do zapisu dokumentów, po wyjątku, do kolejki utraconych wiadomości. Jeśli jednak w funkcji platformy Azure wystąpi nieobsługiwany wyjątek, zmiana, która wygenerowała wyjątek nie zostanie automatycznie ponowiona. Jeśli wystąpił nieobsługiwany wyjątek w logice biznesowej, funkcja platformy Azure przejdzie w celu przetworzenia następnej zmiany. Funkcja platformy Azure nie ponowi żadnej próby. ta sama zmiana nie powiodła się.

Podobnie jak Azure Functions, Programowanie przy użyciu biblioteki procesora źródła zmian jest proste. Jednak użytkownik jest odpowiedzialny za wdrożenie co najmniej jednego hosta dla procesora źródła zmian. Host jest wystąpieniem aplikacji korzystającym z procesora źródła zmian do nasłuchiwania zmian. Chociaż Azure Functions ma możliwości skalowania automatycznego, użytkownik jest odpowiedzialny za skalowanie hostów. Aby dowiedzieć się więcej, zobacz [Korzystanie z procesora źródła zmian](change-feed-processor.md#dynamic-scaling). Biblioteka procesora kanału informacyjnego zmian jest częścią [zestawu Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="reading-change-feed-with-a-pull-model"></a>Odczytywanie źródła zmian z modelem ściągania

[Model ściągania źródła zmian](change-feed-pull-model.md) umożliwia korzystanie z kanału informacyjnego zmiany we własnym tempie. Klient musi zażądać zmian i nie ma automatycznego sondowania dla zmian. Jeśli chcesz trwale "zakładkę" ostatnią przetworzoną zmianę (podobną do kontenera dzierżawy modelu wypychania), musisz [zapisać token kontynuacji](change-feed-pull-model.md#saving-continuation-tokens).

Przy użyciu modelu ściągania danych o zmianach można uzyskać większą kontrolę nad źródłem zmian. Podczas odczytywania kanału informacyjnego zmiany przy użyciu modelu ściągania są dostępne trzy opcje:

- Odczytaj zmiany dla całego kontenera
- Odczytaj zmiany dotyczące określonego [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization)
- Odczytaj zmiany dotyczące określonej wartości klucza partycji

Można zrównoleglanie przetwarzanie zmian między wieloma klientami, podobnie jak w przypadku procesora źródła zmian. Jednak model ściągania nie obsługuje automatycznie równoważenia obciążenia wśród klientów. W przypadku używania modelu ściągania do zrównoleglanie przetwarzania źródła zmian należy najpierw uzyskać listę FeedRanges. FeedRange obejmuje zakres wartości klucza partycji. Musisz mieć proces programu Orchestrator, który uzyskuje FeedRanges i dystrybuuje je między maszynami. Następnie można użyć tych FeedRanges, aby wiele maszyn odczytało Źródło zmian równolegle.

Nie ma żadnej wbudowanej gwarancji dostarczania "co najmniej raz" z modelem ściągania. Model ściągania zapewnia kontrolę niskiego poziomu, aby zdecydować, w jaki sposób chcesz obsłużyć błędy.

> [!NOTE]
> Model ściągania źródła zmian jest obecnie w [wersji zapoznawczej tylko w Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview) . Wersja zapoznawcza nie jest jeszcze dostępna dla innych wersji zestawu SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Źródło zmian w interfejsach API dla Cassandra i MongoDB

Funkcja zmiany kanału informacyjnego jest oferowana jako strumienie zmian w interfejsie API MongoDB i zapytania z predykatem w interfejs API Cassandra. Aby dowiedzieć się więcej na temat szczegółów implementacji interfejsu API MongoDB, zobacz [zmiana strumieni w interfejsie api Azure Cosmos DB dla MongoDB](mongodb-change-streams.md).

Natywny program Apache Cassandra udostępnia funkcję przechwytywania zmian danych (rerzucij), mechanizm do flagi określonych tabel do archiwizacji, a także odrzucanie zapisów w tych tabelach po osiągnięciu konfigurowalnego rozmiaru na dysku dla dziennika przechwytywania zmian. Funkcja zmiany kanału informacyjnego w interfejsie Azure Cosmos DB API for Cassandra zwiększa możliwość wykonywania zapytań o zmiany przy użyciu predykatu za pośrednictwem CQL. Aby dowiedzieć się więcej na temat szczegółów implementacji, zobacz temat [Zmiana kanału informacyjnego w interfejsie API Azure Cosmos DB Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Następne kroki

Teraz możesz dalej dowiedzieć się więcej na temat źródła zmian w następujących artykułach:

* [Przegląd źródła zmian](change-feed.md)
* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)
* [Korzystanie z biblioteki procesora kanału informacyjnego zmiany](change-feed-processor.md)
