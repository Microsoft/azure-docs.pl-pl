---
title: Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc
description: Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2a931b1a3c3f88af1abec4fd1810aae09c849c48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940855"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Zaloguj się do kontrolera danych usługi Azure Arc

Aby można było utworzyć wystąpienie, zaloguj się do kontrolera danych usługi Azure ARC, jeśli nie jest jeszcze zalogowany.

```console
azdata login
```

Następnie zostanie wyświetlony monit o podanie nazwy użytkownika, hasła i przestrzeni nazw System.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Tworzenie wystąpienia zarządzanego Azure SQL

Aby wyświetlić dostępne opcje tworzenia forSQL wystąpienie zarządzane, użyj następującego polecenia:
```console
azdata arc sql mi create --help
```

Aby utworzyć wystąpienie zarządzane SQL, użyj następującego polecenia:

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

Przykład:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  Długość nazw musi być krótsza niż 13 znaków i być zgodna z [konwencjami nazewnictwa DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  Podczas określania alokacji pamięci i alokacji rdzeń wirtualny Użyj tej formuły, aby upewnić się, że tworzenie zakończyło się pomyślnie — dla każdego 1 rdzeń wirtualny potrzeba co najmniej 4 GB pamięci RAM pojemności dostępnej w węźle Kubernetes, w którym zostanie uruchomione wystąpienie zarządzane SQL pod.
>
>  Podczas tworzenia wystąpienia SQL nie używaj wielkich liter w nazwie, jeśli aprowizujesz na platformie Azure
>
>  Aby wyświetlić listę dostępnych klas magazynu w klastrze Kubernetes `kubectl get storageclass` 


> [!NOTE]
> Jeśli chcesz zautomatyzować tworzenie wystąpień programu SQL i uniknąć interakcyjnego monitu o hasło administratora, możesz ustawić `AZDATA_USERNAME` `AZDATA_PASSWORD` zmienne środowiskowe i dla żądanej nazwy użytkownika i hasła przed uruchomieniem `azdata arc sql mi create` polecenia.
> 
>  Jeśli kontroler danych został utworzony przy użyciu AZDATA_USERNAME i AZDATA_PASSWORD w tej samej sesji terminala, to wartości AZDATA_USERNAME i AZDATA_PASSWORD zostaną użyte do utworzenia wystąpienia zarządzanego SQL.

> [!NOTE]
> Utworzenie wystąpienia zarządzanego Azure SQL nie spowoduje zarejestrowania zasobów na platformie Azure. Kroki służące do zarejestrowania zasobu znajdują się w następujących artykułach: 
> - [Wyświetlanie dzienników i metryk przy użyciu Kibana i Grafana](monitor-grafana-kibana.md)
> - [Przekaż dane dotyczące rozliczeń na platformę Azure i Wyświetl je w Azure Portal](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Wyświetl wystąpienie w usłudze Azure Arc

Aby wyświetlić wystąpienie, użyj następującego polecenia:

```console
azdata arc sql mi list
```

Dane wyjściowe powinny wyglądać następująco:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Jeśli używasz AKS lub `kubeadm` lub OpenShift itp., możesz skopiować zewnętrzny adres IP i numer portu z tego miejsca i nawiązać z nim połączenie przy użyciu ulubionego narzędzia do łączenia się z wystąpieniem programu SQL Server/Azure SQL, takim jak Azure Data Studio lub SQL Server Management Studio. Jeśli jednak korzystasz z maszyny wirtualnej szybkiego startu, zapoznaj się z artykułem [nawiązywanie połączenia z usługą Azure ARC z włączonym wystąpieniem SQL](connect-managed-instance.md) , aby uzyskać szczegółowe instrukcje.


## <a name="next-steps"></a>Następne kroki
- [Połącz z wystąpieniem zarządzanym SQL z włączonym usługą Azure Arc](connect-managed-instance.md)
- [Zarejestruj swoje wystąpienie na platformie Azure i przekaż metryki i dzienniki dotyczące wystąpienia](upload-metrics-and-logs-to-azure-monitor.md)
- [Wdróż wystąpienie zarządzane Azure SQL przy użyciu Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)
