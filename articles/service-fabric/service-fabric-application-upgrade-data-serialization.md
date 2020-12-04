---
title: 'Uaktualnianie aplikacji: serializacja danych'
description: Najlepsze rozwiązania dotyczące serializacji danych i ich wpływu na uaktualnienia aplikacji.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 9474a0bf8041ac541389f782f60bf1220d690cde
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575639"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Jak Serializacja danych ma wpływ na uaktualnienie aplikacji
Podczas [stopniowego uaktualniania aplikacji](service-fabric-application-upgrade.md)uaktualnienie jest stosowane do podzbioru węzłów, jednej domeny uaktualnienia w danym momencie. W trakcie tego procesu niektóre domeny uaktualnienia znajdują się w nowszej wersji aplikacji, a niektóre domeny uaktualnień znajdują się w starszej wersji aplikacji. Podczas wdrażania Nowa wersja aplikacji musi mieć możliwość odczytywania starej wersji danych, a stara wersja aplikacji musi mieć możliwość odczytywania nowej wersji danych. Jeśli format danych nie jest zgodny z poprzednimi wersjami, uaktualnienie może się nie powieść lub gorszenie może spowodować utratę lub uszkodzenie danych. W tym artykule omówiono sposób działania formatu danych i przedstawiono najlepsze rozwiązania w zakresie zapewniania, że dane są zgodne z poprzednimi wersjami.

## <a name="what-makes-up-your-data-format"></a>Co robi format danych?
Na platformie Azure Service Fabric dane, które są utrwalane i replikowane, pochodzą z klas języka C#. W przypadku aplikacji korzystających z [niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md)te dane są obiektami w niezawodnych słownikach i kolejkach. W przypadku aplikacji, które używają [Reliable Actors](service-fabric-reliable-actors-introduction.md), to jest stan zapasowy aktora. Aby można było utrwalić i zreplikować te klasy języka C#, należy je serializować. W związku z tym format danych jest definiowany przez pola i właściwości, które są serializowane, a także jak są serializowane. Na przykład, w `IReliableDictionary<int, MyClass>` danych jest serializowana `int` i serializowana `MyClass` .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Zmiany w kodzie powodujące zmianę formatu danych
Ponieważ format danych jest określany przez klasy języka C#, zmiany w klasach mogą spowodować zmianę formatu danych. Należy zadbać o to, aby uaktualnienie stopniowe obsługiwało zmianę formatu danych. Przykłady, które mogą spowodować zmiany w formacie danych:

* Dodawanie lub usuwanie pól lub właściwości
* Zmiana nazwy pól lub właściwości
* Zmiana typów pól lub właściwości
* Zmiana nazwy klasy lub przestrzeni nazw

### <a name="data-contract-as-the-default-serializer"></a>Kontrakt danych jako serializator domyślny
Serializator jest zazwyczaj odpowiedzialny za odczytywanie danych i deserializacja go w bieżącej wersji, nawet jeśli dane są w starszej lub *nowszej* wersji. Domyślny serializator to [serializator kontraktu danych](/dotnet/framework/wcf/feature-details/using-data-contracts), który ma dobrze zdefiniowane reguły przechowywania wersji. Niezawodne Kolekcje umożliwiają przesłanianie serializatora, ale Reliable Actors obecnie nie. Serializator danych odgrywa ważną rolę w włączaniu uaktualnień stopniowych. Serializator kontraktu danych jest serializatorem zalecanym dla Service Fabric aplikacji.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Jak format danych ma wpływ na uaktualnienie stopniowe
Podczas uaktualniania stopniowego istnieją dwa główne scenariusze, w których serializator może napotkać starszą lub *nowszą* wersję danych:

1. Gdy węzeł zostanie uaktualniony i rozpocznie tworzenie kopii zapasowej, nowy serializator załaduje dane, które zostały utrwalone na dysku przez starą wersję.
2. Podczas uaktualniania stopniowego klaster będzie zawierać kombinację starych i nowych wersji kodu. Ponieważ repliki mogą być umieszczane w różnych domenach uaktualnienia, a repliki przesyłają dane ze sobą, Nowa i/lub stara wersja danych może zostać napotkana przez nową lub starą wersję programu szeregującego.

> [!NOTE]
> "Nowa wersja" i "stara wersja" odnoszą się do używanej wersji kodu. "Nowy serializator" odnosi się do kodu serializatora, który jest wykonywany w nowej wersji aplikacji. "Nowe dane" odwołuje się do serializowanej klasy C# z nowej wersji aplikacji.
> 
> 

Dwie wersje kodu i formatu danych muszą być zarówno do przodu, jak i wstecz. Jeśli nie są zgodne, uaktualnienie stopniowe może zakończyć się niepowodzeniem lub dane mogą zostać utracone. Uaktualnianie stopniowe może się nie powieść, ponieważ kod lub serializator może zgłosić wyjątki lub błąd podczas napotkania innej wersji. Dane mogą zostać utracone, jeśli na przykład została dodana nowa właściwość, ale stary serializator odrzuci go podczas deserializacji.

Kontrakt danych jest zalecanym rozwiązaniem w celu zapewnienia, że dane są zgodne. Ma dobrze zdefiniowane reguły obsługi wersji umożliwiające dodawanie, usuwanie i zmienianie pól. Ma również obsługę w przypadku nieznanych pól, przełączając się do procesu serializacji i deserializacji oraz zajmując się dziedziczeniem klas. Aby uzyskać więcej informacji, zobacz [Korzystanie z kontraktu danych](/dotnet/framework/wcf/feature-details/using-data-contracts).

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [zaawansowanych tematów](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów dotyczących uaktualnień aplikacji, w odniesieniu do kroków w [temacie Troubleshooting Upgrades Applications](service-fabric-application-upgrade-troubleshooting.md).
