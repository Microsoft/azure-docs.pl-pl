---
title: 'Tworzenie pierwszej aplikacji usługi Azure Service Fabric w systemie Linux przy użyciu języka C #'
description: Dowiedz się, jak utworzyć i wdrożyć aplikację Service Fabric przy użyciu języka C# i programu .NET Core 2,0.
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: 556d8dee4efd492fa98755f1ffd1cdc1c9887856
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82193653"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Tworzenie pierwszej aplikacji usługi Azure Service Fabric
> [!div class="op_single_selector"]
> * [Java — Linux (wersja zapoznawcza)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# — Linux (wersja zapoznawcza)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Usługa Service Fabric udostępnia zestawy SDK do kompilowania usług w systemie Linux przy użyciu platform .NET Core i Java. W tym samouczku przedstawiony zostanie sposób tworzenia aplikacji dla systemu Linux i kompilowania usługi w języku C# w programie .NET Core 2.0.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że masz [skonfigurowane środowisko programowania systemu Linux](service-fabric-get-started-linux.md). Jeśli używasz systemu Mac OS X, możesz [skonfigurować jednopunktowe środowisko systemu Linux na maszynie wirtualnej za pomocą narzędzia Vagrant](service-fabric-get-started-mac.md).

Trzeba również zainstalować [interfejs wiersza polecenia Service Fabric](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>Instalowanie i konfigurowanie generatorów dla języka C#
Usługa Service Fabric udostępnia narzędzia do tworzenia szkieletów, które ułatwiają tworzenie aplikacji usługi Service Fabric z poziomu terminalu przy użyciu generatora szablonów narzędzia Yeoman. Wykonaj poniższe kroki, aby skonfigurować generatory szablonów narzędzia Yeoman w usłudze Service Fabric dla języka C#:

1. Zainstaluj środowisko NodeJs i menedżera NPM na swojej maszynie

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. Zainstaluj Generator szablonów [Narzędzia Yeoman](https://yeoman.io/) na maszynie z poziomu usługi npm

   ```bash
   npm install -g yo
   ```
3. Zainstaluj generator aplikacji C# Yeoman usługi Service Fabric z poziomu menedżera NPM

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Tworzenie aplikacji
Aplikacja usługi Service Fabric może zawierać jedną lub więcej usług, a każda z nich pełni określoną rolę w dostarczaniu funkcjonalności aplikacji. Generator [Yeoman](https://yeoman.io/) dla języka C# usługi Service Fabric, który został zainstalowany w ostatnim kroku, ułatwia utworzenie pierwszej usługi i dodawanie kolejnych w przyszłości. Utwórzmy zatem aplikację z jedną usługą za pomocą generatora Yeoman.

1. W terminalu wpisz następujące polecenie i rozpocznij tworzenie szkieletu: `yo azuresfcsharp`
2. Nadaj nazwę aplikacji.
3. Wybierz typ pierwszej usługi i nadaj jej nazwę. Na potrzeby tego samouczka wybierzmy usługę Reliable Actor Service.

   ![Generator Yeoman usługi Service Fabric dla platformy C#][sf-yeoman]

> [!NOTE]
> Aby uzyskać więcej informacji o opcjach, zobacz [Service Fabric programming model overview](service-fabric-choose-framework.md) (Omówienie modelu programowania usługi Service Fabric).
>
>

## <a name="build-the-application"></a>Kompilowanie aplikacji
Szablony generatora Yeoman usługi Service Fabric obejmują skrypt kompilacji, którego można użyć do skompilowania aplikacji z poziomu terminala (po przejściu do folderu aplikacji).

  ```sh
 cd myapp
 ./build.sh
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Uruchamianie klienta testowego i przechodzenie w tryb failover
Projekty aktora nie działają samodzielnie. Wymagają one wysyłania im komunikatów przez inną usługę lub klienta. Szablon aktora zawiera prosty skrypt testowy, którego można użyć do interakcji z usługą aktora.

1. Uruchom skrypt za pomocą narzędzia kontrolnego, aby wyświetlić dane wyjściowe usługi aktora.

   W przypadku systemu MAC OS X należy skopiować folder myactorsvcTestClient do tej samej lokalizacji wewnątrz kontenera, uruchamiając następujące polecenia dodatkowe.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. W narzędziu Service Fabric Explorer zlokalizuj węzeł, w którym znajduje się replika podstawowa usługi aktora. Na poniższym zrzucie ekranu jest to węzeł 3.

    ![Znajdowanie repliki podstawowej w narzędziu Service Fabric Explorer][sfx-primary]
3. Kliknij węzeł znaleziony w poprzednim kroku, a następnie wybierz pozycję **Dezaktywuj (uruchom ponownie)** z menu Akcje. Spowoduje to ponowne uruchomienie jednego węzła w klastrze lokalnym i wymuszenie przejścia w tryb failover do repliki pomocniczej uruchomionej na innym węźle. Podczas wykonywania tej czynności należy zwrócić uwagę na dane wyjściowe z klienta testowego oraz to, że w liczniku będzie nadal występować przyrost niezależnie od trybu failover.

## <a name="adding-more-services-to-an-existing-application"></a>Dodawanie kolejnych usług do istniejącej aplikacji

Aby dodać kolejną usługę do aplikacji utworzonej już przy użyciu polecenia `yo`, wykonaj następujące czynności:
1. Zmień katalog na katalog główny istniejącej aplikacji.  Na przykład wpisz polecenie `cd ~/YeomanSamples/MyApplication`, jeśli aplikacja `MyApplication` to aplikacja utworzona przez narzędzie Yeoman.
2. Uruchom polecenie `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>Następne kroki

* [Interacting with Service Fabric clusters using the Service Fabric CLI (Interakcja z klastrami usługi Service Fabric przy użyciu interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)
* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
