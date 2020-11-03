---
title: Tworzenie wystąpienia zarządzanego SQL przy użyciu narzędzi Kubernetes
description: Tworzenie wystąpienia zarządzanego SQL przy użyciu narzędzi Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2902274cfff11ac256459abd4fe0378146ee067b
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280191"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Tworzenie wystąpienia zarządzanego Azure SQL przy użyciu narzędzi Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Powinien już zostać utworzony [kontroler danych usługi Azure Arc](./create-data-controller.md).

Aby utworzyć wystąpienie zarządzane SQL przy użyciu narzędzi Kubernetes, należy zainstalować narzędzia Kubernetes.  Przykłady w tym artykule będą używane `kubectl` , ale podobne podejścia mogą być używane z innymi narzędziami Kubernetes, takimi jak pulpit nawigacyjny Kubernetes, `oc` lub `helm` wiedząc, że narzędzia i Kubernetes YAML/JSON.

[Instalowanie narzędzia polecenia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Omówienie

Aby utworzyć wystąpienie zarządzane SQL, należy utworzyć wpis tajny Kubernetes w celu bezpiecznego przechowywania identyfikatora logowania i hasła administratora systemu oraz niestandardowego zasobu wystąpienia zarządzanego SQL na podstawie definicji niestandardowego zasobu sqlmanagedinstance.

## <a name="create-a-yaml-file"></a>Utwórz plik YAML

Plik [YAML szablonu](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/sqlmi.yaml) można użyć jako punktu wyjścia do utworzenia własnego niestandardowego pliku YAML wystąpienia zarządzanego SQL.  Pobierz ten plik na komputer lokalny i otwórz go w edytorze tekstu.  Warto używać edytora tekstu, takiego jak [vs Code](https://code.visualstudio.com/download) , które obsługują wyróżnianie składni i zaznaczanie błędów dla plików YAML.

Jest to przykładowy plik YAML:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: example
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    datalogs:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Dostosowywanie nazwy logowania i hasła

Wpis tajny Kubernetes jest przechowywany jako ciąg zakodowany w formacie base64 — jeden dla nazwy użytkownika i jeden dla hasła.  Musisz zakodować w formacie base64 identyfikator logowania i hasło administratora systemu i umieścić je w lokalizacji zastępczej w `data.password` i `data.username` .  Nie dodawaj `<` symboli i `>` określonych w szablonie.

> [!NOTE]
> W celu uzyskania optymalnego poziomu zabezpieczeń użycie wartości "sa" jest niedozwolone w przypadku logowania.
> Postępuj zgodnie z [zasadami złożoności haseł](/sql/relational-databases/security/password-policy#password-complexity).

Możesz użyć narzędzia online do kodowania base64 żądanej nazwy użytkownika i hasła lub użyć wbudowanych narzędzi interfejsu wiersza polecenia w zależności od platformy.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>Dostosowywanie nazwy

Szablon ma wartość "example" dla atrybutu Name.  Można to zmienić, ale muszą to być znaki zgodne ze standardami nazewnictwa DNS.  Należy również zmienić nazwę wpisu tajnego na zgodne.  Na przykład jeśli zmienisz nazwę wystąpienia zarządzanego SQL na "SQL1", musisz zmienić nazwę wpisu tajnego z "example-login-Secret" na "SQL1-login-Secret"

### <a name="customizing-the-resource-requirements"></a>Dostosowywanie wymagań dotyczących zasobów

W razie potrzeby można zmienić wymagania dotyczące zasobów — limity pamięci RAM i rdzeni oraz żądania.  

> [!NOTE]
> Więcej informacji na temat [zarządzania zasobami Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Wymagania dotyczące limitów zasobów i żądań:
- Wartość limitu rdzeni jest **wymagana** na potrzeby rozliczania.
- Pozostałe żądania i limity zasobów są opcjonalne.
- Limit rdzeni i żądanie muszą być dodatnią liczbą całkowitą, jeśli określono.
- Co najmniej 2 rdzenie są wymagane dla żądania rdzeni, jeśli zostały określone.
- Format wartości pamięci jest zgodny z notacją Kubernetes.  
- Co najmniej 2Gi jest wymagany dla żądania pamięci, jeśli jest określony.
- Ogólnie rzecz biorąc, należy mieć 4 GB pamięci RAM dla każdego 1 rdzenia do produkcji.

### <a name="customizing-service-type"></a>Dostosowywanie typu usługi

W razie potrzeby można zmienić typ usługi na NodePort.  Zostanie przypisany losowy numer portu.

### <a name="customizing-storage"></a>Dostosowywanie magazynu

Można dostosować klasy magazynu dla magazynu, aby odpowiadały Twojemu środowisku.  Jeśli nie masz pewności, które klasy magazynu są dostępne, możesz uruchomić polecenie, `kubectl get storageclass` aby je wyświetlić.  Szablon ma domyślną wartość "default".  Oznacza to, że istnieje Klasa magazynu _o nazwie_ "default", która nie ma klasy magazynu, która _jest_ domyślna.  Opcjonalnie możesz również zmienić rozmiar magazynu.  Więcej informacji na temat [konfiguracji magazynu](./storage-configuration.md)można znaleźć w artykule.

## <a name="creating-the-sql-managed-instance"></a>Tworzenie wystąpienia zarządzanego SQL

Teraz, gdy plik YAML wystąpienia zarządzanego SQL został dostosowany, można utworzyć wystąpienie zarządzane SQL, uruchamiając następujące polecenie:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>Monitorowanie stanu tworzenia

Tworzenie wystąpienia zarządzanego SQL potrwa kilka minut. Postęp można monitorować w innym oknie terminalu przy użyciu następujących poleceń:

> [!NOTE]
>  W przykładowych poleceniach przyjęto założenie, że utworzono wystąpienie zarządzane SQL o nazwie "SQL1" i przestrzeń nazw Kubernetes o nazwie "ARC".  Jeśli użyto innej przestrzeni nazw lub nazwy wystąpienia zarządzanego SQL, można zastąpić "ARC" i "sqlmi" nazwami.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Możesz również sprawdzić stan tworzenia dowolnego określonego obszaru pod, uruchamiając polecenie podobne do poniższego.  Jest to szczególnie przydatne w przypadku rozwiązywania problemów.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Rozwiązywanie problemów z tworzeniem

W przypadku wystąpienia problemów z tworzeniem należy zapoznać się z [przewodnikiem rozwiązywania problemów](troubleshoot-guide.md).

## <a name="next-steps"></a>Następne kroki

[Połącz z wystąpieniem zarządzanym SQL z włączonym usługą Azure Arc](connect-managed-instance.md)
