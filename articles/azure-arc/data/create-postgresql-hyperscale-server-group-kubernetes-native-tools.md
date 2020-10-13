---
title: Tworzenie grupy serwerów PostgreSQL do skalowania przy użyciu narzędzi Kubernetes
description: Tworzenie grupy serwerów PostgreSQL do skalowania przy użyciu narzędzi Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f447c6028b1750aa96e531a97e7b0861f66a5749
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761672"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Tworzenie grupy serwerów PostgreSQL do skalowania przy użyciu narzędzi Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Powinien już zostać utworzony [kontroler danych usługi Azure Arc](./create-data-controller.md).

Aby utworzyć grupę serwerów PostgreSQL do skalowania za pomocą narzędzi Kubernetes, należy zainstalować narzędzia Kubernetes.  Przykłady w tym artykule będą używane `kubectl` , ale podobne podejścia mogą być używane z innymi narzędziami Kubernetes, takimi jak pulpit nawigacyjny Kubernetes, `oc` lub `helm` wiedząc, że narzędzia i Kubernetes YAML/JSON.

[Instalowanie narzędzia polecenia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Omówienie

Aby utworzyć grupę serwerów PostgreSQL do skalowania, należy utworzyć wpis tajny Kubernetes w celu bezpiecznego przechowywania danych logowania i hasła administratora Postgres oraz niestandardową grupę serwerów PostgreSQL ze skalowaniem na podstawie niestandardowych definicji zasobów PostgreSQL-12 lub PostgreSQL.

## <a name="create-a-yaml-file"></a>Utwórz plik YAML

Plik [YAML szablonu](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/postsgresql.yaml) można użyć jako punktu wyjścia, aby utworzyć własny plik YAML PostgreSQL grupy serwerów wieloskalowej.  Pobierz ten plik na komputer lokalny i otwórz go w edytorze tekstu.  Warto używać edytora tekstu, takiego jak [vs Code](https://code.visualstudio.com/download) , które obsługują wyróżnianie składni i zaznaczanie błędów dla plików YAML.

Jest to przykładowy plik YAML:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: example
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Dostosowywanie nazwy logowania i hasła.
Wpis tajny Kubernetes jest przechowywany jako ciąg zakodowany w formacie base64 — jeden dla nazwy użytkownika i jeden dla hasła.  Musisz zakodować w formacie base64 nazwę logowania i hasło administratora oraz umieścić je w lokalizacji zastępczej w `data.password` i `data.username` .  Nie dodawaj `<` symboli i `>` określonych w szablonie.

Możesz użyć narzędzia online do kodowania base64 żądanej nazwy użytkownika i hasła lub użyć wbudowanych narzędzi interfejsu wiersza polecenia w zależności od platformy.

Program PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>Dostosowywanie nazwy

Szablon ma wartość "example" dla atrybutu Name.  Można to zmienić, ale muszą to być znaki zgodne ze standardami nazewnictwa DNS.  Należy również zmienić nazwę wpisu tajnego na zgodne.  Jeśli na przykład zmienisz nazwę grupy serwerów PostgreSQL na "postgres1", musisz zmienić nazwę wpisu tajnego z "example-login-Secret" na "postgres1-login-Secret"

### <a name="customizing-the-engine-version"></a>Dostosowywanie wersji aparatu

Możesz zmienić wersję aparatu na PostgreSQL-11 lub PostgreSQL-12, edytując `kind` atrybut.

### <a name="customizing-the-resource-requirements"></a>Dostosowywanie wymagań dotyczących zasobów

W razie potrzeby można zmienić wymagania dotyczące zasobów — limity pamięci RAM i rdzeni oraz żądania.  

> [!NOTE]
> Więcej informacji na temat [zarządzania zasobami Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Wymagania dotyczące limitów zasobów i żądań:
- Wartość limitu rdzeni jest **wymagana** na potrzeby rozliczania.
- Pozostałe żądania i limity zasobów są opcjonalne.
- Limit rdzeni i żądanie muszą być dodatnią liczbą całkowitą, jeśli określono.
- Wymagane jest co najmniej 1 rdzeń dla żądania rdzeni, jeśli jest określony.
- Format wartości pamięci jest zgodny z notacją Kubernetes.  

### <a name="customizing-service-type"></a>Dostosowywanie typu usługi

W razie potrzeby można zmienić typ usługi na NodePort.  Zostanie przypisany losowy numer portu.

### <a name="customizing-storage"></a>Dostosowywanie magazynu

Można dostosować klasy magazynu dla magazynu, aby odpowiadały Twojemu środowisku.  Jeśli nie masz pewności, które klasy magazynu są dostępne, możesz uruchomić polecenie, `kubectl get storageclass` aby je wyświetlić.  Szablon ma domyślną wartość "default".  Oznacza to, że istnieje Klasa magazynu _o nazwie_ "default", która nie ma klasy magazynu, która _jest_ domyślna.  Opcjonalnie możesz również zmienić rozmiar magazynu.  Więcej informacji na temat [konfiguracji magazynu](./storage-configuration.md)można znaleźć w artykule.

## <a name="creating-the-postgresql-hyperscale-server-group"></a>Tworzenie grupy serwerów PostgreSQL

Teraz, po dostosowaniu pliku YAML grupy serwerów PostgreSQL, można utworzyć grupę serwerów PostgreSQL, uruchamiając następujące polecenie:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>Monitorowanie stanu tworzenia

Utworzenie grupy serwerów PostgreSQL do skalowania może potrwać kilka minut. Postęp można monitorować w innym oknie terminalu przy użyciu następujących poleceń:

> [!NOTE]
>  W przykładowych poleceniach przyjęto założenie, że utworzono grupę serwerów PostgreSQL z skalowaniem o nazwie "postgres1" i Kubernetes przestrzeń nazw o nazwie "ARC".  Jeśli użyto innej nazwy grupy serwerów przestrzeni nazw/PostgreSQL, można zastąpić "ARC" i "postgres1" nazwami.

```console
kubectl get postgresql-12/postgres1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Możesz również sprawdzić stan tworzenia dowolnego określonego obszaru pod, uruchamiając polecenie podobne do poniższego.  Jest to szczególnie przydatne w przypadku rozwiązywania problemów.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/postgres1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Rozwiązywanie problemów z tworzeniem

W przypadku wystąpienia problemów z tworzeniem należy zapoznać się z [przewodnikiem rozwiązywania problemów](troubleshoot-guide.md).