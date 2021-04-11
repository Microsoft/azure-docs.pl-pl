---
title: Jak uzyskać nazwy hostów węzłów klastra usługi Azure HDInsight
description: Dowiedz się więcej na temat uzyskiwania nazw hostów i nazw FQDN węzłów klastra usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111467"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>Znajdowanie nazw hostów węzłów klastra

Klaster usługi HDInsight jest tworzony za pomocą publicznego clustername.azurehdinsight.net DNS. W przypadku protokołu SSH do poszczególnych węzłów lub ustawiania połączenia z węzłami klastra w tej samej niestandardowej sieci wirtualnej należy użyć nazwy hosta lub w pełni kwalifikowanych nazw domen (FQDN) węzłów klastra.

W tym artykule dowiesz się, jak uzyskać nazwy hostów węzłów klastra. Można to zrobić ręcznie za poorednictwem internetowego interfejsu użytkownika Ambari lub automatycznie za poorednictwem interfejsu API REST Ambari.

> [!WARNING]
> Aby pobrać nazwy hostów węzłów klastra, należy użyć następujących zalecanych metod. Liczby w nazwie hosta nie są gwarantowane w kolejności, a Usługa HDInsight może zmienić format nazwy hosta tak, aby był wyrównany do maszyn wirtualnych z odświeżaniem wydania. Nie należy podejmować zależności od żadnej z określonych konwencji nazewnictwa, która już istnieje. 
>

Nazwy hostów można uzyskać za pomocą interfejsu Ambari UI lub interfejsu API REST Ambari. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Pobierz nazwy hostów z interfejsu użytkownika sieci Web Ambari
Przy użyciu interfejsu użytkownika sieci Web Ambari można pobrać nazwy hostów podczas korzystania z protokołu SSH do węzła. Widok hosty interfejsu użytkownika sieci Web Ambari jest dostępny w klastrze usługi HDInsight w lokalizacji `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` , gdzie `CLUSTERNAME` jest nazwą klastra.

![Get-Host-names-in-Ambari-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Pobieranie nazw hostów z interfejsu API REST usługi Ambari
Podczas kompilowania skryptów automatyzacji można użyć interfejsu API REST Ambari w celu uzyskania nazw hostów przed nawiązywania połączeń z hostami. Liczby w nazwie hosta nie są gwarantowane w kolejności, a Usługa HDInsight może zmienić format nazwy hosta tak, aby był wyrównany do maszyn wirtualnych z odświeżaniem wydania. Nie należy podejmować zależności od żadnej z określonych konwencji nazewnictwa, która już istnieje. 

Poniżej przedstawiono kilka przykładów sposobu pobierania nazwy FQDN dla węzłów w klastrze. Aby uzyskać więcej informacji na temat interfejsu API REST usługi Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu API REST usługi Apache Ambari](.\hdinsight-hadoop-manage-ambari-rest-api.md)

Poniższy przykład używa [JQ](https://stedolan.github.io/jq/) lub [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) do analizy dokumentu odpowiedzi JSON i wyświetlania tylko nazw hostów.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```