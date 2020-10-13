---
title: Tworzenie pierwszej niezawodnej usługi w języku Java
description: Wprowadzenie do tworzenia aplikacji Microsoft Azure Service Fabric przy użyciu usług bezstanowych i stanowych w języku Java.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-javai
ms.openlocfilehash: f67957d711958febdb01dfad0b3c44a92cb0bcfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535240"
---
# <a name="get-started-with-reliable-services-in-java"></a>Wprowadzenie do Reliable Services w języku Java
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-quick-start.md)
> * [Java w systemie Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

W tym artykule wyjaśniono podstawowe informacje dotyczące usługi Azure Service Fabric Reliable Services i przedstawiono tworzenie i wdrażanie prostej niezawodnej aplikacji usługi, która jest zapisywana w języku Java. 

## <a name="installation-and-setup"></a>Instalacja i konfiguracja
Przed rozpoczęciem upewnij się, że na komputerze jest skonfigurowane środowisko deweloperskie Service Fabric.
Jeśli musisz ją skonfigurować, przejdź do pozycji [wprowadzenie na komputerze Mac](service-fabric-get-started-mac.md) lub wprowadzenie do systemu [Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Podstawowe pojęcia
Aby rozpocząć pracę z Reliable Services, musisz zrozumieć tylko kilka podstawowych pojęć:

* **Typ usługi**: to jest implementacja usługi. Jest on definiowany przez zapisanie klasy, która rozszerza `StatelessService` i wszelkie inne kod lub zależności używane w tym dokumencie wraz z nazwą i numerem wersji.
* **Nazwane wystąpienie usługi**: Aby uruchomić usługę, należy utworzyć nazwane wystąpienia typu usługi, podobnie jak w przypadku tworzenia wystąpień obiektów typu klasy. Wystąpienia usługi są w wystąpieniach obiektów faktów klasy usługi, którą napiszesz.
* **Host usługi**: utworzone wystąpienia usługi nazwane muszą zostać uruchomione na hoście. Host usługi to tylko proces, w którym można uruchamiać wystąpienia usługi.
* **Rejestracja usługi**: Rejestracja umożliwia wszystko. Typ usługi musi być zarejestrowany w środowisku uruchomieniowym Service Fabric na hoście usługi, aby umożliwić Service Fabric tworzenia wystąpień do uruchomienia.  

## <a name="create-a-stateless-service"></a>Tworzenie usługi bezstanowej
Zacznij od utworzenia aplikacji Service Fabric. Zestaw Service Fabric SDK dla systemu Linux zawiera generator narzędzia Yeoman, który umożliwia tworzenie szkieletów dla aplikacji Service Fabric za pomocą usługi bezstanowej. Zacznij od uruchomienia następującego polecenia narzędzia Yeoman:

```bash
$ yo azuresfjava
```

Postępuj zgodnie z instrukcjami, aby utworzyć **niezawodną usługę bezstanową**. W tym samouczku Nazwij aplikację "HelloWorldApplication" i usługę "HelloWorld". Wynik zawiera katalogi dla `HelloWorldApplication` i `HelloWorld` .

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Rejestracja usługi
Typy usług muszą być zarejestrowane w środowisku uruchomieniowym Service Fabric. Typ usługi jest zdefiniowany w `ServiceManifest.xml` klasie i klasy usługi implementującej `StatelessService` . Rejestracja usługi jest przeprowadzana w głównym punkcie wejścia procesu. W tym przykładzie głównym punktem wejścia procesu jest `HelloWorldServiceHost.java` :

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Implementowanie usługi

Otwórz plik **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService. Java**. Ta klasa definiuje typ usługi i może uruchamiać dowolny kod. Interfejs API usługi udostępnia dwa punkty wejścia dla kodu:

* Metoda punktu wejścia Open-zakończony, wywoływana `runAsync()` , gdzie można rozpocząć wykonywanie obciążeń, w tym długotrwałe obciążenia obliczeniowe.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Punkt wejścia komunikacji, w którym można podłączyć wybrany stos komunikacji. Jest to miejsce, w którym można zacząć otrzymywać żądania od użytkowników i innych usług.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Ten samouczek koncentruje się na `runAsync()` metodzie punktu wejścia. Jest to miejsce, w którym można od razu rozpocząć uruchamianie kodu.

### <a name="runasync"></a>RunAsync
Platforma wywołuje tę metodę, gdy wystąpienie usługi jest umieszczane i gotowe do wykonania. W przypadku usługi bezstanowej oznacza to, że jest otwierane wystąpienie usługi. Token anulowania jest dostarczany w celu koordynowania, gdy wystąpienie usługi musi zostać zamknięte. W Service Fabric ten cykl otwierania/zamykania wystąpienia usługi może wystąpić wiele razy w okresie istnienia usługi jako całości. Może się to zdarzyć z różnych powodów, takich jak:

* System przenosi wystąpienia usługi na potrzeby równoważenia zasobów.
* Błędy występują w kodzie.
* Aplikacja lub system zostały uaktualnione.
* Podstawowy sprzęt napotyka awarię.

Ta aranżacja jest zarządzana przez Service Fabric w celu zapewnienia wysokiej dostępności i prawidłowego zrównoważenia usługi.

`runAsync()` nie powinien blokować synchronicznie. Implementacja runAsync powinna zwrócić CompletableFuture, aby umożliwić kontynuowanie działania środowiska uruchomieniowego. Jeśli obciążenie wymaga zaimplementowania długotrwałego zadania, które należy wykonać w CompletableFuture.

#### <a name="cancellation"></a>Anulowanie
Anulowanie obciążenia jest wysiłkiem w ramach współpracy zorganizowanej przy użyciu podanego tokenu anulowania. System czeka na zakończenie zadania (po pomyślnym ukończeniu, anulowania lub błędu) przed przekazaniem. Ważne jest, aby honorować token anulowania, zakończyć pracę i zakończyć działanie `runAsync()` tak szybko, jak to możliwe, gdy system żąda anulowania. W poniższym przykładzie pokazano, jak obsłużyć zdarzenie anulowania:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

W tym przykładzie usługi bezstanowej liczba jest przechowywana w zmiennej lokalnej. Jednak ponieważ jest to usługa bezstanowa, przechowywana wartość istnieje tylko dla bieżącego cyklu życia wystąpienia usługi. Po przeniesieniu lub ponownym uruchomieniu usługi wartość zostanie utracona.

## <a name="create-a-stateful-service"></a>Tworzenie usługi stanowej
W Service Fabric wprowadzono nowy rodzaj usługi, która jest stanowa. Usługa stanowa może niezawodnie zachować stan w ramach samej usługi, w której znajduje się kod, który go używa. Stan zapewnia wysoką dostępność przez Service Fabric bez konieczności utrwalania stanu w sklepie zewnętrznym.

Do konwersji wartości licznika z bezstanowego na wysoko dostępne i trwałe, nawet gdy usługa jest przenoszona lub ponownie uruchamiana, potrzebna jest usługa stanowa.

W tym samym katalogu, w którym znajduje się aplikacja HelloWorld, możesz dodać nową usługę, uruchamiając `yo azuresfjava:AddService` polecenie. Wybierz "niezawodne usługi stanowe" dla swojej struktury i nazwij usługę "HelloWorldStateful". 

Aplikacja powinna mieć teraz dwie usługi: usługa bezstanowa HelloWorld i usługa stanowa HelloWorldStateful.

Usługa stanowa ma te same punkty wejścia co usługa bezstanowa. Główną różnicą jest dostępność dostawcy stanu, który może niezawodnie przechowywać stan. Service Fabric zawiera implementację dostawcy stanu o nazwie niezawodne kolekcje, które umożliwiają tworzenie replikowanych struktur danych za pomocą Menedżera niezawodnego stanu. Usługa bezstanowa domyślnie używa tego dostawcy stanu.

Otwórz HelloWorldStateful. Java w **HelloWorldStateful-> src**, który zawiera następującą metodę RunAsync:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` działa podobnie do usług stanowych i bezstanowych. Jednak w usłudze stanowej platforma wykonuje dodatkową prace w Twoim imieniu, zanim zostanie wykonana `RunAsync()` . To działanie może obejmować upewnienie się, że niezawodny Menedżer stanu i niezawodne kolekcje są gotowe do użycia.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Niezawodne kolekcje i Menedżer niezawodnego stanu
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](/java/api/microsoft.servicefabric.data.collections.reliablehashmap) to implementacja słownika, której można użyć do niezawodnego przechowywania stanu usługi. Dzięki Service Fabric i niezawodnej HashMaps można przechowywać dane bezpośrednio w usłudze bez potrzeby zewnętrznego magazynu trwałego. Niezawodne HashMaps zapewnia wysoką dostępność danych. Service Fabric to osiągnąć przez utworzenie wielu *replik* usługi i zarządzanie nimi. Udostępnia również interfejs API, który stanowi streszczenie złożoności zarządzania tymi replikami i ich przejścia stanu.

Niezawodne kolekcje mogą przechowywać dowolny typ Java, łącznie z typami niestandardowymi, z kilkoma zastrzeżeniami:

* Service Fabric zapewnia wysoką dostępność stanu przez *replikowanie* stanu między węzłami, a niezawodne HashMap przechowuje dane na dysku lokalnym na każdej replice. Oznacza to, że wszystkie elementy, które są przechowywane w niezawodnej HashMaps, muszą być *serializowane*. 
* Obiekty są replikowane w celu zapewnienia wysokiej dostępności podczas zatwierdzania transakcji w niezawodnym HashMaps. Obiekty przechowywane w niezawodnej HashMaps są przechowywane w pamięci lokalnej w usłudze. Oznacza to, że masz lokalne odwołanie do obiektu.
  
   Należy pamiętać, że nie można zmodyfikować wystąpień lokalnych tych obiektów bez wykonywania operacji aktualizacji dla niezawodnej kolekcji transakcji. Dzieje się tak dlatego, że zmiany lokalnych wystąpień obiektów nie będą replikowane automatycznie. Należy ponownie wstawić obiekt do słownika lub użyć jednej z metod *aktualizacji* w słowniku.

Niezawodny Menedżer stanu zarządza niezawodnym HashMaps. Można polecić niezawodnego menedżera stanu dla niezawodnej kolekcji według nazwy w dowolnym momencie i w dowolnym miejscu usługi. Niezawodny Menedżer stanu zapewnia odwołanie do odwołania. Nie zalecamy zapisywania odwołań do niezawodnych wystąpień kolekcji w zmiennych lub właściwościach składowych klasy. Należy zwrócić szczególną uwagę, aby upewnić się, że odwołanie jest ustawione na wystąpienie przez cały czas w cyklu życia usługi. Niezawodny Menedżer stanu obsługuje tę czynność i jest zoptymalizowany pod kątem powtarzających się wizyt.


### <a name="transactional-and-asynchronous-operations"></a>Operacje transakcyjne i asynchroniczne
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Operacje na niezawodnej HashMaps są asynchroniczne. Dzieje się tak dlatego, że operacje zapisu z niezawodnymi kolekcjami wykonują operacje we/wy umożliwiające replikację i utrwalanie danych na dysku.

Niezawodne operacje HashMap są *transakcyjne*, dzięki czemu można zachować spójność stanu przez wiele niezawodnych HashMaps i operacji. Na przykład można uzyskać element roboczy z jednego niezawodnego słownika, wykonać na nim operację i zapisać wynik w innej niezawodnej HashMap, w ramach jednej transakcji. Jest to traktowane jako operacja niepodzielna i gwarantuje, że cała operacja zakończy się powodzeniem lub cała operacja zostanie wycofana. Jeśli wystąpi błąd po cofnięciu kolejki elementu, ale przed zapisaniem wyniku, cała transakcja zostanie wycofana, a element pozostaje w kolejce do przetworzenia.


## <a name="build-the-application"></a>Kompilowanie aplikacji

Tworzenie szkieletu narzędzia Yeoman obejmuje skrypt Gradle do kompilowania aplikacji i skryptów bash w celu wdrażania i usuwania aplikacji. Aby uruchomić aplikację, najpierw skompiluj aplikację przy użyciu Gradle:

```bash
$ gradle
```

Spowoduje to utworzenie pakietu aplikacji Service Fabric, który można wdrożyć za pomocą interfejsu wiersza polecenia Service Fabric.

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Skompilowaną aplikację można wdrożyć w klastrze lokalnym.

1. Połącz się z lokalnym klastrem usługi Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Uruchom skrypt instalacji udostępniony w szablonie, aby skopiować pakiet aplikacji do magazynu obrazów klastra, zarejestrować typ aplikacji i utworzyć wystąpienie aplikacji.

    ```bash
    ./install.sh
    ```

Wdrażanie skompilowanej aplikacji przebiega tak samo jak w przypadku innych aplikacji usługi Service Fabric. Szczegółowe instrukcje są dostępne w dokumentacji dotyczącej [zarządzania aplikacją usługi Service Fabric za pomocą interfejsu wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md).

Parametry tych poleceń można znaleźć w manifestach wygenerowanych w pakiecie aplikacji.

Po wdrożeniu aplikacji otwórz przeglądarkę i przejdź do narzędzia [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pod adresem `http://localhost:19080/Explorer`. Następnie rozwiń węzeł **Aplikacje** i zwróć uwagę, że istnieje teraz wpis dla danego typu aplikacji i inny wpis dla pierwszego wystąpienia tego typu.

> [!IMPORTANT]
> Aby wdrożyć aplikację do bezpiecznego klastra z systemem Linux na platformie Azure, musisz skonfigurować certyfikat w celu weryfikacji aplikacji przy użyciu środowiska uruchomieniowego Service Fabric. Dzięki temu usługi Reliable Services mogą komunikować się z podstawowymi interfejsami API środowiska uruchomieniowego Service Fabric. Aby dowiedzieć się więcej, zobacz [Konfigurowanie aplikacji Reliable Services do uruchamiania w klastrach systemu Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Następne kroki

* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
