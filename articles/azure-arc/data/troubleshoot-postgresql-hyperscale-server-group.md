---
title: Rozwiązywanie problemów z grupami serwerów PostgreSQL w hiperskali
description: Rozwiązywanie problemów z grupami serwerów PostgreSQL w hiperskali przy użyciu Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "92320212"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Rozwiązywanie problemów z grupami serwerów PostgreSQL w hiperskali
W tym artykule opisano niektóre techniki, których można użyć do rozwiązywania problemów z grupą serwerów. Oprócz tego artykułu możesz przeczytać, jak używać programu [Kibana](monitor-grafana-kibana.md) do przeszukiwania dzienników lub wizualizować metryki dotyczące grupy serwerów za pomocą programu [Grafana.](monitor-grafana-kibana.md) 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Uzyskiwanie szczegółowych informacji na temat wykonywania polecenia azdata
Możesz dodać parametr **--debug do** dowolnego wykonywanego polecenia azdata. Spowoduje to wyświetlenie w konsoli dodatkowych informacji o wykonaniu tego polecenia. Przydatne powinno być uzyskiwanie szczegółowych informacji, które pomogą Ci zrozumieć zachowanie tego polecenia.
Można na przykład uruchomić
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

lub
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Ponadto można użyć parametru --help dla dowolnego polecenia azdata, aby wyświetlić pomoc, listę parametrów dla określonego polecenia. Na przykład:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Zbieranie dzienników kontrolera danych i grup serwerów
Przeczytaj artykuł na temat [uzyskiwania dzienników dla usługi danych z obsługą usługi Azure Arc](troubleshooting-get-logs.md)



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Interaktywne rozwiązywanie problemów za pomocą notesów Jupyter w Azure Data Studio
Notesy mogą dokumentować procedury, dołączając zawartość Markdown, aby opisać, co należy zrobić i jak to zrobić. Mogą również udostępniać kod wykonywalny służący do automatyzacji procedury.  Ten wzorzec jest przydatny we wszystkim, od standardowych procedur po przewodniki po rozwiązywaniu problemów.

Na przykład rozwiążmy problem z grupą serwerów PostgreSQL w hiperskali, która może mieć problemy z używaniem Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Instalowanie narzędzi

Zainstaluj Azure Data Studio i na komputerze klienckim, za pomocą `kubectl` [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] których uruchamiasz notes w programie Azure Data Studio. Aby to zrobić, postępuj zgodnie z instrukcjami w te [tematu Instalowanie narzędzi klienckich](install-client-tools.md)

### <a name="update-the-path-environment-variable"></a>Aktualizowanie zmiennej środowiskowej PATH

Upewnij się, że te narzędzia mogą być wywoływane z dowolnego miejsca na tym komputerze klienckim. Na przykład na komputerze klienckim z systemem Windows zaktualizuj zmienną środowiskową PATH systemu i dodaj folder, w którym zainstalowano program kubectl.

### <a name="sign-in-with-azure-data-cli-azdata"></a>Zaloguj się przy użyciu [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Zaloguj się do kontrolera danych Arc z tego komputera klienckiego i przed uruchomieniem Azure Data Studio. W tym celu uruchom polecenie, takie jak:

```console
azdata login --endpoint https://<IP address>:<port>
```

Zastąp `<IP address>` adresem IP klastra Kubernetes i portem, na którym `<port>` nasłuchuje usługa Kubernetes. Zostanie wyświetlony monit o nazwę użytkownika i hasło. Aby wyświetlić więcej szczegółów, uruchom:_

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Zaloguj się do klastra Kubernetes przy użyciu usługi kubectl

W tym celu możesz użyć przykładowych poleceń podanych w [tym wpisie](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) w blogu.
Należy uruchamiać polecenia, takie jak:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>Notes rozwiązywania problemów

Uruchom Azure Data Studio i otwórz notes rozwiązywania problemów. 

Zaim implementuj kroki opisane  [w 033-manage-Postgres-with-AzureDataStudio.md,](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) aby:

1. Nawiązywanie połączenia z kontrolerem danych arc
2. Wybierz prawym przyciskiem na prawo wystąpienie usługi Postgres i wybierz **pozycję [Zarządzaj]**
3. Wybieranie pulpitu **nawigacyjnego [Diagnozowanie i rozwiązywanie problemów]**
4. Wybierz link **[Rozwiązywanie problemów]**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio — notes rozwiązywania problemów z programem Open PostgreSQL":::

Zostanie **otwarty notes TSG100 — baza danych PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc rozwiązywania** problemów: Azure Data Studio — korzystanie z notesu rozwiązywania problemów :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="z programem PostgreSQL":::

#### <a name="run-the-scripts"></a>Uruchamianie skryptów
Wybierz przycisk "Uruchom wszystko" u góry, aby wykonać notes jednocześnie, lub możesz przechodzić przez poszczególne komórki kodu i wykonywać je jedna po drugiej.

Wyświetl dane wyjściowe z wykonywania komórek kodu, aby uzyskać informacje o potencjalnych problemach.

Z czasem dodamy do notesu więcej szczegółów na temat rozpoznawania typowych problemów i ich rozwiązywania.

## <a name="next-step"></a>Następny krok
- Przeczytaj o [uzyskiwaniu dzienników dla usługi danych z obsługą usługi Azure Arc](troubleshooting-get-logs.md)
- Przeczytaj o [wyszukiwaniu dzienników za pomocą kibany](monitor-grafana-kibana.md)
- Przeczytaj o [monitorowaniu za pomocą narzędzia Grafana](monitor-grafana-kibana.md)
- Tworzenie własnych notesów
