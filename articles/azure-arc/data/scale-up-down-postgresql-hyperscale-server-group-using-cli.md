---
title: Skalowanie w górę i w dół grupy serwerów z Azure Database for PostgreSQLm skalowaniem przy użyciu interfejsu wiersza polecenia (azdata lub polecenia kubectl)
description: Skalowanie w górę i w dół grupy serwerów z Azure Database for PostgreSQLm skalowaniem przy użyciu interfejsu wiersza polecenia (azdata lub polecenia kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4461fb6904d51ee8d740b633a2d0028658ac2ced
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687553"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>Skalowanie w górę i w dół grupy serwerów z Azure Database for PostgreSQLm skalowaniem przy użyciu interfejsu wiersza polecenia (azdata lub polecenia kubectl)



Czasami może zajść potrzeba zmiany charakterystyki lub definicji grupy serwerów. Na przykład:

- Skalowanie w górę lub w dół liczby rdzeni wirtualnych, które są używane przez każdego koordynatora lub węzły procesu roboczego
- Skalowanie w górę lub w dół pamięci używanej przez każdy koordynator lub węzły procesu roboczego

W tym przewodniku wyjaśniono, jak skalować rdzeń wirtualny i/lub pamięć.

Skalowanie w górę lub w dół ustawień rdzeń wirtualny lub pamięci grupy serwerów oznacza, że istnieje możliwość ustawienia minimum i/lub maksimum dla każdego ustawienia rdzeń wirtualny i pamięci. Aby skonfigurować grupę serwerów tak, aby korzystała z określonej liczby rdzeń wirtualny lub określonej ilości pamięci, należy ustawić ustawienia minimalne równe wartości maksymalnej.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>Pokaż bieżącą definicję grupy serwerów

Aby wyświetlić bieżącą definicję grupy serwerów i zobaczyć, jakie są bieżące ustawienia rdzeń wirtualny i pamięci, Uruchom jedno z następujących poleceń:

### <a name="cli-with-azdata"></a>Interfejs wiersza polecenia z azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>Interfejs wiersza polecenia z polecenia kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> Jeśli utworzono grupę serwerów PostgreSQL w wersji 11, należy `kubectl describe postgresql-11/<server group name>` zamiast tego uruchomić polecenie.

Zwraca konfigurację grupy serwerów. Jeśli utworzono grupę serwerów z ustawieniami domyślnymi, powinna zostać wyświetlona Definicja w następujący sposób:

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>Interpretuj definicję grupy serwerów

W definicji grupy serwerów Sekcja zawierająca ustawienia min/max rdzeń wirtualny na węzeł i minimalna/maksymalna ilość pamięci na węzeł to sekcja **"Planowanie"** . W tej sekcji maksymalne ustawienia zostaną utrwalone w podsekcji o nazwie **"limity"** , a ustawienia minimalne są utrwalane w podsekcji o nazwie **"requests"**.

Jeśli ustawisz minimalne ustawienia, które różnią się od ustawień maksymalnych, konfiguracja gwarantuje, że Grupa serwerów zostanie przypisana do żądanych zasobów, jeśli jest potrzebna. Nie przekroczy ustawionych limitów.

Zasoby (rdzeni wirtualnych i pamięć), które będą używane przez grupę serwerów, są maksymalnie maksymalne i zależą od obciążeń i zasobów dostępnych w klastrze. Jeśli ustawienia nie zostaną skonfigurowane z maksymalną liczbą, Grupa serwerów może używać do wszystkich zasobów, które są przydzielane przez klaster Kubernetes do węzłów Kubernetes, na których zaplanowano grupę serwerów.

Te ustawienia rdzeń wirtualny i pamięci mają zastosowanie do każdego z węzłów PostgreSQL (koordynatora i węzłów procesu roboczego). Nie jest jeszcze obsługiwane Ustawianie definicji węzła koordynatora i węzłów procesu roboczego osobno.

W domyślnej konfiguracji tylko minimalna ilość pamięci jest ustawiana na 256Mi, ponieważ jest to minimalny rozmiar pamięci zalecanej do uruchomienia funkcji PostgreSQL.

> [!NOTE]
> Ustawienie minimum nie oznacza, że Grupa serwerów będzie koniecznie używała tego minimum. Oznacza to, że jeśli Grupa serwerów jest dla niego potrzebna, gwarantowaną gwarancję będzie przydzielenie co najmniej tego minimum. Rozważmy na przykład, że ustawimy `--minCpu 2` . Nie oznacza to, że Grupa serwerów będzie korzystać z co najmniej 2 rdzeni wirtualnych przez cały czas. Zamiast tego oznacza, że Grupa serwerów może rozpoczynać się przy użyciu mniej niż 2 rdzeni wirtualnych, jeśli nie jest to potrzebne, i jest gwarantowane, że zostanie przypisana co najmniej 2 rdzeni wirtualnych, jeśli będzie ona potrzebna później. Oznacza to, że klaster Kubernetes przydziela zasoby do innych obciążeń w taki sposób, że może przydzielić 2 rdzeni wirtualnych do grupy serwerów, jeśli kiedykolwiek ich potrzebują.

>[!NOTE]
>Przed zmodyfikowaniem konfiguracji systemu należy zapoznać się z modelem zasobów Kubernetes [tutaj](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities) .

## <a name="scale-up-the-server-group"></a>Skalowanie w górę grupy serwerów

Ustawienia, które należy skonfigurować, muszą być uwzględniane w konfiguracji ustawionej dla klastra Kubernetes. Upewnij się, że nie są ustawione wartości, które nie będą mogły być spełnione przez klaster Kubernetes. Może to prowadzić do błędów lub nieprzewidywalne zachowanie. Jeśli na przykład stan grupy serwerów pozostaje w trakcie _aktualizacji_ przez długi czas po zmianie konfiguracji, może to oznaczać, że następujące parametry są ustawiane na wartości, których nie może spełnić klaster Kubernetes. W takim przypadku Przywróć zmianę lub Odczytaj _troubleshooting_section.

Załóżmy na przykład, że chcesz skalować w górę definicję grupy serwerów, aby:

- Min rdzeń wirtualny = 2
- Maksymalna rdzeń wirtualny = 4
- Minimalna ilość pamięci = 512 MB
- Maksymalna ilość pamięci = 1 GB

Należy użyć jednej z następujących metod:

### <a name="cli-with-azdata"></a>Interfejs wiersza polecenia z azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> Poniżej przedstawiono przykład przedstawiający sposób użycia polecenia. Przed wykonaniem polecenia Edit upewnij się, że parametry są ustawione na wartości, które może obsłużyć klaster Kubernetes.

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

Polecenie zostanie wykonane pomyślnie, gdy zostanie wyświetlone:

```console
<name of your server group> is Ready
```

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat tych parametrów, uruchom polecenie `azdata arc postgres server edit --help` .

### <a name="cli-with-kubectl"></a>Interfejs wiersza polecenia z polecenia kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

Spowoduje to przejście do edytora VI, w którym można nawigować i zmieniać konfigurację. Aby zmapować odpowiednie ustawienie na nazwę pola w specyfikacji, użyj następujących elementów:

> [!CAUTION]
> Poniżej przedstawiono przykład przedstawiający sposób edytowania konfiguracji. Przed aktualizacją konfiguracji należy ustawić parametry na wartości, które może obsłużyć klaster Kubernetes.

Na przykład:
- Min rdzeń wirtualny = 2-> scheduling\default\resources\requests\cpu
- Max rdzeń wirtualny = 4-> scheduling\default\resources\limits\cpu
- Minimalna pamięć = 512 MB-> scheduling\default\resources\requests\cpu
- Maksymalna ilość pamięci = 1 GB-> scheduling\default\resources\limits\cpu

Jeśli nie znasz edytora VI, zobacz opis poleceń, które mogą być potrzebne w [tym miejscu](https://www.computerhope.com/unix/uvi.htm):
- tryb edycji: `i`
- poruszanie się za pomocą strzałek
- _stop edytowanie: `esc`
- _exit bez zapisywania: `:qa!`
- _exit po zapisaniu: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>Pokaż definicję skalowania grupy serwerów

Uruchom ponownie polecenie, aby wyświetlić definicję grupy serwerów i sprawdzić, czy jest ona ustawiona na życzenie:

### <a name="cli-with-azdata"></a>Interfejs wiersza polecenia z azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>Interfejs wiersza polecenia z polecenia kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> Jeśli utworzono grupę serwerów PostgreSQL w wersji 11, należy `kubectl describe postgresql-11/<server group name>` zamiast tego uruchomić polecenie.


Zostanie wyświetlona nowa definicja grupy serwerów:

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>Skalowanie w dół grupy serwerów

Aby skalować w dół grupę serwerów, należy wykonać to samo polecenie, ale ustawić mniejsze wartości dla ustawień, które mają być skalowane w dół. Aby usunąć żądania i/lub limity, określ jej wartość jako pusty ciąg.

## <a name="reset-to-default-values"></a>Przywróć wartości domyślne
Aby zresetować limity rdzeni/pamięci/żądania do ich wartości domyślnych, należy je edytować i przekazać pusty ciąg zamiast wartości rzeczywistej. Na przykład jeśli chcesz zresetować limit rdzenia (CL), uruchom następujące polecenia:
- na kliencie z systemem Linux:

```console
    azdata arc postgres server edit -n <servergroup name> -cl ""
```

- na kliencie systemu Windows: 
 
```console
    azdata arc postgres server edit -n <servergroup name> -cl '""'
```


## <a name="next-steps"></a>Następne kroki

- [Skalowanie w poziomie grupy serwerów usługi Azure Database for PostgreSQL — hiperskala](scale-out-postgresql-hyperscale-server-group.md)
- [Konfiguracja magazynu i pojęcia dotyczące magazynu Kubernetes](storage-configuration.md)
- [Model zasobów Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
