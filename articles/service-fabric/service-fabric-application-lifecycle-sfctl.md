---
title: Zarządzanie aplikacjami Service Fabric platformy Azure przy użyciu usługi sfctl
description: Dowiedz się, jak wdrażać i usuwać aplikacje z klastra Service Fabric platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure Service Fabric
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84711038"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Zarządzanie aplikacją Service Fabric platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure Service Fabric (sfctl)

Dowiedz się, jak tworzyć i usuwać aplikacje działające w klastrze Service Fabric platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj interfejs wiersza polecenia Service Fabric. Następnie wybierz klaster Service Fabric. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z interfejsem wiersza polecenia Service Fabric](service-fabric-cli.md).

* Przygotuj pakiet aplikacji Service Fabric gotowy do wdrożenia. Aby uzyskać więcej informacji na temat sposobu tworzenia i pakowania aplikacji, Przeczytaj o [modelu aplikacji Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Omówienie

Aby wdrożyć nową aplikację, wykonaj następujące kroki:

1. Przekaż pakiet aplikacji do magazynu obrazów Service Fabric.
2. Zainicjuj obsługę administracyjną typu aplikacji.
3. Usuń zawartość z magazynu obrazów.
4. Określ i Utwórz aplikację.
5. Określ i Utwórz usługi.

Aby usunąć istniejącą aplikację, wykonaj następujące kroki:

1. Usuń aplikację.
2. Cofanie aprowizacji skojarzonego typu aplikacji.

## <a name="deploy-a-new-application"></a>Wdróż nową aplikację

Aby wdrożyć nową aplikację, wykonaj następujące zadania:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Przekaż nowy pakiet aplikacji do magazynu obrazów

Przed utworzeniem aplikacji Przekaż pakiet aplikacji do magazynu obrazów Service Fabric.

Na przykład jeśli pakiet aplikacji znajduje się w `app_package_dir` katalogu, użyj następujących poleceń, aby przekazać katalog:

```shell
sfctl application upload --path ~/app_package_dir
```

W przypadku dużych pakietów aplikacji można określić `--show-progress` opcję wyświetlania postępu przekazywania.

### <a name="provision-the-application-type"></a>Inicjowanie obsługi administracyjnej typu aplikacji

Po zakończeniu przekazywania Zainicjuj obsługę administracyjną aplikacji. Aby zainicjować obsługę administracyjną aplikacji, użyj następującego polecenia:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

Wartość dla `application-type-build-path` to nazwa katalogu, do którego został przekazany pakiet aplikacji.

### <a name="delete-the-application-package"></a>Usuń pakiet aplikacji

Zaleca się usunięcie pakietu aplikacji po pomyślnym zarejestrowaniu aplikacji.  Usuwanie pakietów aplikacji z magazynu obrazów zwalnia zasoby systemowe.  Utrzymywanie nieużywanych pakietów aplikacji zużywa magazyn dyskowy i prowadzi do problemów z wydajnością aplikacji. 

Aby usunąć pakiet aplikacji z magazynu obrazów, użyj następującego polecenia:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path` musi być nazwą katalogu, który został przekazany podczas tworzenia aplikacji.

### <a name="create-an-application-from-an-application-type"></a>Tworzenie aplikacji na podstawie typu aplikacji

Po udostępnieniu aplikacji użyj następującego polecenia, aby nawiązać nazwę i utworzyć aplikację:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` jest nazwą, która ma być używana dla wystąpienia aplikacji. Możesz uzyskać dodatkowe parametry z wcześniej zainicjowanego manifestu aplikacji.

Nazwa aplikacji musi zaczynać się od prefiksu `fabric:/` .

### <a name="create-services-for-the-new-application"></a>Tworzenie usług dla nowej aplikacji

Po utworzeniu aplikacji Utwórz usługi z aplikacji. W poniższym przykładzie tworzymy nową usługę bezstanową z naszej aplikacji. Usługi, które można utworzyć na podstawie aplikacji, są definiowane w manifeście usługi w wcześniej zainicjowanym pakiecie aplikacji.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Weryfikowanie wdrożenia i kondycji aplikacji

Aby sprawdzić, czy wszystko jest w dobrej kondycji, użyj następujących poleceń dotyczących kondycji:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Aby sprawdzić, czy usługa jest w dobrej kondycji, użyj podobnych poleceń, aby pobrać kondycję zarówno usługi, jak i aplikacji:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Usługi i aplikacje w dobrej kondycji mają `HealthState` wartość `Ok` .

## <a name="remove-an-existing-application"></a>Usuń istniejącą aplikację

Aby usunąć aplikację, należy wykonać następujące zadania:

### <a name="delete-the-application"></a>Usuń aplikację

Aby usunąć aplikację, użyj następującego polecenia:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Cofanie aprowizacji typu aplikacji

Po usunięciu aplikacji można anulować obsługę administracyjną typu aplikacji, jeśli nie jest już potrzebna. Aby anulować obsługę administracyjną typu aplikacji, użyj następującego polecenia:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Nazwa typu i wersja typu muszą być zgodne z nazwą i wersją w zainicjowanym wcześniej manifeście aplikacji.

## <a name="upgrade-application"></a>Uaktualnianie aplikacji

Po utworzeniu aplikacji można powtórzyć ten sam zestaw kroków, aby udostępnić drugą wersję aplikacji. Następnie za pomocą uaktualnienia aplikacji Service Fabric można przejść do uruchamiania drugiej wersji aplikacji. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [Service Fabric uaktualnień aplikacji](service-fabric-application-upgrade.md).

Aby przeprowadzić uaktualnienie, najpierw Zainicjuj obsługę następnej wersji aplikacji przy użyciu tych samych poleceń jak wcześniej:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Zalecane jest, aby przeprowadzić monitorowane automatyczne uaktualnianie, a następnie uruchomić uaktualnienie, uruchamiając następujące polecenie:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Uaktualnienia przesłaniają istniejące parametry z dowolnym zestawem. Parametry aplikacji powinny być przekazane jako argumenty do polecenia upgrade, w razie potrzeby. Parametry aplikacji powinny być kodowane jako obiekt JSON.

Aby pobrać wcześniej określone parametry, można użyć `sfctl application info` polecenia.

Gdy uaktualnienie aplikacji jest w toku, stan można pobrać przy użyciu `sfctl application upgrade-status` polecenia.

Na koniec Jeśli uaktualnienie jest w toku i wymaga anulowania, można użyć programu `sfctl application upgrade-rollback` do wycofania uaktualnienia.

## <a name="next-steps"></a>Następne kroki

* [Podstawy interfejsu wiersza polecenia Service Fabric](service-fabric-cli.md)
* [Wprowadzenie do Service Fabric w systemie Linux](service-fabric-get-started-linux.md)
* [Uruchamianie uaktualnienia aplikacji Service Fabric](service-fabric-application-upgrade.md)
