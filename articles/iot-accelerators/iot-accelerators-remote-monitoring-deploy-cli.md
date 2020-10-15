---
title: Wdrażanie rozwiązania do monitorowania zdalnego przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: W tym przewodniku opisano sposób udostępniania akceleratora rozwiązania do zdalnego monitorowania przy użyciu interfejsu wiersza polecenia.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: f9dcf19f5318021df5d9fdde777b8786942e33d8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072258"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Wdróż Akcelerator rozwiązania do monitorowania zdalnego przy użyciu interfejsu wiersza polecenia

Ten przewodnik przedstawia sposób wdrażania akceleratora rozwiązania do monitorowania zdalnego. Rozwiązanie jest wdrażane przy użyciu interfejsu wiersza polecenia. Możesz również wdrożyć rozwiązanie przy użyciu interfejsu użytkownika opartego na sieci Web w witrynie azureiotsolutions.com, aby dowiedzieć się więcej na temat tej opcji, zobacz sekcję [wdrażanie akceleratora rozwiązania do monitorowania zdalnego](quickstart-remote-monitoring-deploy.md) — Szybki Start.

## <a name="prerequisites"></a>Wymagania wstępne

Do wdrożenia akceleratora rozwiązania do monitorowania zdalnego potrzebna jest aktywna subskrypcja platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

Aby uruchomić interfejs wiersza polecenia, musisz [Node.js](https://nodejs.org/) zainstalowany na komputerze lokalnym.

## <a name="install-the-cli"></a>Instalowanie interfejsu wiersza polecenia

Aby zainstalować interfejs wiersza polecenia, uruchom następujące polecenie w środowisku z wierszem poleceń:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Logowanie do interfejsu wiersza polecenia

Aby można było wdrożyć Akcelerator rozwiązania, należy zalogować się do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia:

```cmd/sh
pcs login
```

Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby ukończyć proces logowania.

## <a name="deployment-options"></a>Opcje wdrożenia

Po wdrożeniu akceleratora rozwiązań istnieje kilka opcji konfigurowania procesu wdrażania:

| Opcja | Wartości | Opis |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | _Podstawowe_ wdrożenie jest przeznaczone do testowania i demonstracji, wdraża wszystkie mikrousługi na jednej maszynie wirtualnej. Wdrożenie _standardowe_ jest przeznaczone dla środowiska produkcyjnego, a mikrousługi są wdrażane na kilku maszynach wirtualnych. _Lokalne_ wdrożenie konfiguruje kontener platformy Docker do uruchamiania mikrousług na maszynie lokalnej i korzysta z usług Azure Cloud Services, takich jak storage i Cosmos DB. |
| Środowisko uruchomieniowe | `dotnet`, `java` | Wybiera implementację języka mikrousług. |

Aby dowiedzieć się, jak korzystać z opcji lokalnego wdrażania, zobacz [lokalne uruchamianie rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Wdrożenia podstawowe i standardowe

Ta sekcja zawiera podsumowanie najważniejszych różnic między wdrożeniem podstawowym i standardowym.

### <a name="basic"></a>Podstawowy

Można wykonać podstawowe wdrożenie z [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) lub przy użyciu interfejsu wiersza polecenia.

Podstawowe wdrożenie jest ukierunkowane na zaprezentowanie rozwiązania. Aby obniżyć koszty, wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej. To wdrożenie nie używa architektury gotowej do produkcji.

Podstawowe wdrożenie powoduje utworzenie następujących usług w ramach subskrypcji platformy Azure:

| Licznik | Zasób                       | Typ         | Używane dla |
|-------|--------------------------------|--------------|----------|
| 1     | [Maszyna wirtualna z systemem Linux](https://azure.microsoft.com/services/virtual-machines/) | Standardowa D1 v2  | Obsługa mikrousług |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 — warstwa standardowa | Zarządzanie urządzeniami i komunikacja |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standardowa        | Przechowywanie danych konfiguracji, zasad, alertów i innych chłodnych magazynów |  
| 1     | [Konto usługi Azure Storage](../storage/common/storage-introduction.md#types-of-storage-accounts)  | Standardowa        | Magazynowanie maszyn wirtualnych i punktów kontrolnych przesyłania strumieniowego |
| 1     | [Aplikacja sieci Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosting aplikacji sieci Web frontonu |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Zarządzanie tożsamościami i zabezpieczeniami użytkowników |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standardowa                | Wyświetlanie lokalizacji zasobów |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednostki              | Włączanie analizy w czasie rzeczywistym |
| 1     | [Usługa Azure Device Provisioning](../iot-dps/index.yml)        |       S1          | Inicjowanie obsługi administracyjnej urządzeń |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 — 1 jednostka              | Magazyn dla danych komunikatów i umożliwia analizę szczegółowe telemetrii |

### <a name="standard"></a>Standardowa

Wdrożenie standardowe można wykonać tylko przy użyciu interfejsu wiersza polecenia.

Wdrożenie standardowe to wdrożenie gotowe do produkcji, które deweloper może dostosować i zwiększyć. Użyj standardowej opcji wdrażania, gdy wszystko jest gotowe do dostosowania architektury gotowej do użycia w środowisku produkcyjnym, która jest przeznaczona do skalowania i rozszerzalności. Mikrousługi aplikacji są kompilowane jako kontenery platformy Docker i wdrażane za pomocą usługi Azure Kubernetes. Koordynator Kubernetes wdraża, skaluje i zarządza mikrousług.

Wdrożenie standardowe tworzy następujące usługi w ramach subskrypcji platformy Azure:

| Licznik | Zasób                                     | Jednostka SKU/rozmiar      | Używane dla |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Użyj w pełni zarządzanej usługi aranżacji kontenerów Kubernetes — domyślnie to 3 agenci|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 — warstwa standardowa | Zarządzanie urządzeniami, polecenie i kontrola |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standardowa        | Przechowywanie danych konfiguracyjnych, jak reguł, alertów i komunikatów telemetrycznych urządzeń |
| 5     | [Konta usługi Azure Storage](../storage/common/storage-introduction.md#types-of-storage-accounts)    | Standardowa        | 4 dla magazynu maszyn wirtualnych i 1 dla punktów kontrolnych przesyłania strumieniowego |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | Standardowa S1     | Brama aplikacji przez protokół TLS |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Zarządzanie tożsamościami i zabezpieczeniami użytkowników |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standardowa                | Wyświetlanie lokalizacji zasobów |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednostki              | Włączanie analizy w czasie rzeczywistym |
| 1     | [Usługa Azure Device Provisioning](../iot-dps/index.yml)        |       S1          | Inicjowanie obsługi administracyjnej urządzeń |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 — 1 jednostka              | Magazyn dla danych komunikatów i umożliwia analizę szczegółowe telemetrii |

> [!NOTE]
> Informacje o cenach dla tych usług można znaleźć pod adresem [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing) . Szczegóły dotyczące użycia i rozliczeń dla subskrypcji można znaleźć w [witrynie Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Wdrażanie akceleratora rozwiązań

Przykłady wdrożenia:

### <a name="example-deploy-net-version"></a>Przykład: Wdróż wersję .NET

Poniższy przykład pokazuje, jak wdrożyć wersję Basic, .NET akceleratora rozwiązania do monitorowania zdalnego:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Przykład: Wdróż wersję języka Java

Poniższy przykład pokazuje, jak wdrożyć standardową wersję języka Java dla akceleratora rozwiązania do monitorowania zdalnego:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Opcje poleceń komputerów

Po uruchomieniu `pcs` polecenia w celu wdrożenia rozwiązania zostanie wyświetlony monit o:

- Nazwa rozwiązania. Ta nazwa musi być unikatowa.
- Subskrypcja platformy Azure, która ma być używana.
- Lokalizacja.
- Poświadczenia dla maszyn wirtualnych, które obsługują mikrousługi. Przy użyciu tych poświadczeń można uzyskać dostęp do maszyn wirtualnych w celu rozwiązywania problemów.

Po `pcs` zakończeniu wykonywania polecenia zostanie wyświetlony adres URL nowego akceleratora rozwiązania. `pcs`Polecenie tworzy również plik `{deployment-name}-output.json` , który zawiera informacje, takie jak nazwa utworzonego IoT Hub.

Aby uzyskać więcej informacji na temat parametrów wiersza polecenia, uruchom polecenie:

```cmd/sh
pcs -h
```

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia, zobacz [jak korzystać z interfejsu wiersza polecenia](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku krok po kroku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Konfigurowanie akceleratora rozwiązań
> * Wdrażanie akceleratora rozwiązań
> * Zaloguj się do akceleratora rozwiązania

Teraz, gdy wdrożono rozwiązanie do monitorowania zdalnego, następnym krokiem jest zapoznanie się z [możliwościami pulpitu nawigacyjnego rozwiązania](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->