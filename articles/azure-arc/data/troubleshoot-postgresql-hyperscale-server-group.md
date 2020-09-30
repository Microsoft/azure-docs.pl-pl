---
title: Rozwiązywanie problemów z grupami serwerów PostgreSQL
description: Rozwiązywanie problemów z grupami serwerów PostgreSQL ze skalowaniem za pomocą Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8d1c9027b6a9a7b295ce83e26281832beca1bc33
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531959"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Rozwiązywanie problemów z grupami serwerów PostgreSQL
W tym artykule opisano niektóre techniki, których można użyć do rozwiązywania problemów z grupą serwerów. Oprócz tego artykułu warto zapoznać się z tematem jak używać [Kibana](monitor-grafana-kibana.md) do wyszukiwanie dzienników lub użyć [Grafana](monitor-grafana-kibana.md) do wizualizacji metryk dotyczących grupy serwerów. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Więcej szczegółowych informacji na temat wykonywania polecenia azdata
Można dodać parametr **--Debug** do dowolnego polecenia azdata, które wykonujesz. Spowoduje to wyświetlenie w konsoli dodatkowych informacji na temat wykonywania tego polecenia. Warto uzyskać szczegółowe informacje ułatwiające zrozumienie zachowania tego polecenia.
Na przykład można uruchomić
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

lub
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Ponadto można użyć parametru--Help na dowolnym azdata polecenie, aby wyświetlić pomoc, listę parametrów dla konkretnego polecenia. Na przykład:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Zbieranie dzienników kontrolera danych i grup serwerów
Przeczytaj artykuł dotyczący [pobierania dzienników dla usług danych z włączonym usługą Azure Arc](troubleshooting-get-logs.md)



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Interaktywne rozwiązywanie problemów z notesami Jupyter w Azure Data Studio
Notesy mogą dokumentować procedury, dołączając zawartość Markdown, aby opisać, co należy zrobić i jak to zrobić. Mogą również udostępniać kod wykonywalny służący do automatyzacji procedury.  Ten wzorzec jest przydatny we wszystkim, od standardowych procedur po przewodniki po rozwiązywaniu problemów.

Na przykład Rozwiązywanie problemów z grupą serwerów PostgreSQL w ramach skalowania, które mogą powodować problemy przy użyciu Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Instalowanie narzędzi

Zainstaluj Azure Data Studio `kubectl` i `azdata` na komputerze klienckim, którego używasz do uruchamiania notesu w programie Azure Data Studio. Aby to zrobić, postępuj zgodnie z instrukcjami podanymi w tematach [Instalowanie narzędzi klienckich](install-client-tools.md) .

### <a name="update-the-path-environment-variable"></a>Aktualizowanie zmiennej środowiskowej PATH

Upewnij się, że te narzędzia mogą być wywoływane z dowolnego miejsca na tej maszynie klienckiej. Na przykład na komputerze klienckim z systemem Windows zaktualizuj zmienną środowiskową PATH systemowej i Dodaj folder, w którym zainstalowano polecenia kubectl.

### <a name="sign-in-with-azdata"></a>Zaloguj się przy użyciu `azdata`

Przed uruchomieniem Azure Data Studio Zaloguj się do kontrolera danych ARC z tego komputera klienckiego. W tym celu należy uruchomić polecenie takie jak:

```console
azdata login --endpoint https://<IP address>:<port>
```

Zastąp `<IP address>` adres IP klastra Kubernetes i `<port>` port, na którym nasłuchuje Kubernetes. Zostanie wyświetlony monit o podanie nazwy użytkownika i hasła. Aby wyświetlić więcej szczegółów, uruchom polecenie: _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Zaloguj się do klastra Kubernetes za pomocą polecenia kubectl

W tym celu warto użyć przykładowych poleceń dostępnych w [tym](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) wpisie w blogu.
Można uruchamiać polecenia takie jak:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>Notes rozwiązywania problemów

Uruchom Azure Data Studio i Otwórz Notes rozwiązywania problemów. 

Zaimplementuj kroki opisane w  [033-Manage-Postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) , aby:

1. Nawiązywanie połączenia z kontrolerem danych Arc
2. Prawym przyciskiem myszy wybierz wystąpienie Postgres i wybierz pozycję **[Zarządzaj]**
3. Wybierz **pulpit nawigacyjny [diagnozowanie i rozwiązywanie problemów]**
4. Wybierz **łącze [Rozwiązywanie problemów]**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio Otwórz Notes rozwiązywania problemów z programem PostgreSQL":::

**TSG100 — Azure Data Studio Notes platformy Azure z włączonym narzędziem do rozwiązywania problemów z PostgreSQLem** :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio Otwórz Notes rozwiązywania problemów z programem PostgreSQL":::

#### <a name="run-the-scripts"></a>Uruchamianie skryptów
Wybierz przycisk "Uruchom wszystko" w górnej części, aby wykonać w notesie wszystkie naraz lub możesz wykonać krokowo każdą komórkę kodu.

Wyświetlanie danych wyjściowych z wykonywania komórek kodu dla potencjalnych problemów.

Dodaliśmy więcej szczegółów do notesu wraz z upływem czasu na temat sposobu rozpoznawania typowych problemów i sposobu ich rozwiązywania.

## <a name="next-step"></a>Następny krok
- Przeczytaj o [uzyskiwaniu dzienników dla usług danych z włączonym usługą Azure Arc](troubleshooting-get-logs.md)
- Przeczytaj o [wyszukiwaniu dzienników za pomocą Kibana](monitor-grafana-kibana.md)
- Przeczytaj o [monitorowaniu za pomocą Grafana](monitor-grafana-kibana.md)
- Tworzenie własnych notesów
