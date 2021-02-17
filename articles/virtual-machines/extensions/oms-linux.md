---
title: Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Linux
description: Wdróż agenta Log Analytics na maszynie wirtualnej z systemem Linux przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: 202cdc341ce31c2347552e6fbc430c679ef28d7f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580091"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Linux

## <a name="overview"></a>Omówienie

Dzienniki Azure Monitor udostępniają funkcje monitorowania, alertów i korygowania alertów w ramach zasobów w chmurze i lokalnych. Log Analytics rozszerzenie maszyny wirtualnej dla systemu Linux jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym Log Analytics obszarze roboczym. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia maszyny wirtualnej Log Analytics dla systemu Linux.

>[!NOTE]
>W ramach trwającego przejścia z usługi Microsoft Operations Management Suite (OMS) do Azure Monitor Agent pakietu OMS dla systemu Windows lub Linux będzie określany jako agent Log Analytics dla systemu Windows i agenta Log Analytics w systemie Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Aby uzyskać szczegółowe informacje o obsługiwanych dystrybucjach systemu Linux, zobacz artykuł [Omówienie agentów Azure monitor](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Wersja rozszerzenia agenta i maszyny wirtualnej
Poniższa tabela zawiera mapowanie wersji rozszerzenia maszyny wirtualnej Log Analytics i pakietu agenta Log Analytics dla każdej wersji. Dołączono link do informacji o wersji dla wersji pakietu agenta Log Analytics. Informacje o wersji zawierają szczegóły poprawek błędów i nowych funkcji dostępnych dla danej wersji agenta.  

| Wersja rozszerzenia maszyny wirtualnej z systemem Log Analytics Linux | Wersja pakietu agenta Log Analytics | 
|--------------------------------|--------------------------|
| 1.13.33 | [1.13.33](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.33-0) |
| 1.13.27 | [1.13.27](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.27-0) |
| 1.13.15 | [1.13.9-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.9-0) |
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1 — 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0 — 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4 – 210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3 – 174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2 – 125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2 — 124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0 – 45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5 – 127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5 – 127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4 — 15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automatycznie inicjuje agenta Log Analytics i łączy go z domyślnym obszarem roboczym Log Analytics utworzonym przez ASC w ramach subskrypcji platformy Azure. Jeśli używasz Azure Security Center, nie wykonuj kroków opisanych w tym dokumencie. Spowoduje to zastąpienie skonfigurowanego obszaru roboczego i przerwanie połączenia z Azure Security Center.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie agenta Log Analytics dla systemu Linux wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia agenta Log Analytics. Rozszerzenie wymaga identyfikatora obszaru roboczego i klucza obszaru roboczego z obszaru roboczego Log Analytics docelowego; te wartości można [znaleźć w obszarze roboczym log Analytics](../../azure-monitor/vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key) w Azure Portal. Ponieważ klucz obszaru roboczego powinien być traktowany jako poufne dane, powinien on być przechowywany w konfiguracji chronionego ustawienia. Dane ustawienia chronionego rozszerzenia maszyny wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że w **Identyfikator obszaru roboczego** i **workspaceKey** jest rozróżniana wielkość liter.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>W schemacie założono, że zostanie on umieszczony na poziomie głównym szablonu. Jeśli umieścisz ją wewnątrz zasobu maszyny wirtualnej w szablonie, `type` `name` należy zmienić właściwości i, zgodnie [](#template-deployment)z powyższym opisem.

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft. EnterpriseCloud. Monitoring |
| typ | OmsAgentForLinux |
| typeHandlerVersion | 1.13 |
| Identyfikator obszaru roboczego (np.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

>[!NOTE]
>Niektóre składniki rozszerzenia maszyny wirtualnej Log Analytics są również dostarczane w [rozszerzeniu maszyny wirtualnej diagnostyki](./diagnostics-linux.md). Ze względu na tę architekturę mogą wystąpić konflikty w przypadku wystąpienia obu rozszerzeń w tym samym szablonie usługi ARM. Aby uniknąć tych konfliktów w czasie instalacji, należy użyć [ `dependsOn` dyrektywy](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) , aby upewnić się, że rozszerzenia są zainstalowane sekwencyjnie. Rozszerzenia można zainstalować w dowolnej kolejności.

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Szablony są idealnym rozwiązaniem podczas wdrażania co najmniej jednej maszyny wirtualnej, która wymaga konfiguracji po wdrożeniu, takiej jak dołączanie do dzienników Azure Monitor. Przykładowy szablon Menedżer zasobów zawierający rozszerzenie maszyny wirtualnej agenta Log Analytics można znaleźć w [galerii szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Konfiguracja JSON rozszerzenia maszyny wirtualnej może być zagnieżdżona w ramach zasobu maszyny wirtualnej lub umieszczona na głównym lub najwyższego poziomu szablonu JSON Menedżer zasobów. Położenie konfiguracji JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu dla zasobów podrzędnych](../../azure-resource-manager/templates/child-resource-name-type.md). 

W poniższym przykładzie przyjęto założenie, że rozszerzenie maszyny wirtualnej jest zagnieżdżone w zasobie maszyn wirtualnych. Podczas zagnieżdżania zasobu rozszerzenia kod JSON jest umieszczany w `"resources": []` obiekcie maszyny wirtualnej.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Podczas umieszczania kodu JSON rozszerzenia w katalogu głównym szablonu nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla konfigurację zagnieżdżoną.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure można wdrożyć rozszerzenie maszyny wirtualnej Log Analytics Agent na istniejącej maszynie wirtualnej. Zastąp wartość *myWorkspaceKey* poniżej kluczem obszaru roboczego i wartość *myWorkspaceId* identyfikatorem obszaru roboczego. Te wartości można znaleźć w obszarze roboczym Log Analytics w obszarze Azure Portal *Ustawienia zaawansowane*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

| Kod błędu | Znaczenie | Możliwa akcja |
| :---: | --- | --- |
| 9 | Włącz przedwcześnie | [Zaktualizuj agenta systemu Azure Linux](./update-linux-agent.md) do najnowszej dostępnej wersji. |
| 10 | Maszyna wirtualna jest już połączona z obszarem roboczym Log Analytics | Aby połączyć maszynę wirtualną z obszarem roboczym określonym w schemacie rozszerzenia, należy ustawić stopOnMultipleConnections na false w ustawieniach publicznych lub usunąć tę właściwość. Ta maszyna wirtualna jest rozliczana raz dla każdego obszaru roboczego, z którym jest połączona. |
| 11 | Nieprawidłowa konfiguracja podana dla rozszerzenia | Postępuj zgodnie z powyższymi przykładami, aby ustawić wszystkie wartości właściwości niezbędne do wdrożenia. |
| 17 | Niepowodzenie instalacji pakietu Log Analytics | 
| 19 | Niepowodzenie instalacji pakietu OMI | 
| 20 | Niepowodzenie instalacji pakietu SCX |
| 51 | To rozszerzenie nie jest obsługiwane w systemie operacyjnym maszyny wirtualnej | |
| 52 | To rozszerzenie nie powiodło się ze względu na brakującą zależność | Sprawdź dane wyjściowe i dzienniki, aby uzyskać więcej informacji o braku zależności. |
| 53 | To rozszerzenie nie powiodło się z powodu braku lub nieprawidłowych parametrów konfiguracji | Sprawdź dane wyjściowe i dzienniki, aby uzyskać więcej informacji na temat tego, co poszło źle. Sprawdź również prawidłowość identyfikatora obszaru roboczego i sprawdź, czy komputer jest połączony z Internetem. |
| 55 | Nie można nawiązać połączenia z usługą Azure Monitor lub brakuje wymaganych pakietów lub Menedżer pakietów serwerach dpkg jest zablokowany| Sprawdź, czy system ma dostęp do Internetu lub czy podano prawidłowy serwer proxy HTTP. Sprawdź poprawność identyfikatora obszaru roboczego i sprawdź, czy zainstalowano narzędzia Zastąp i tar. |

Dodatkowe informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku rozwiązywania problemów log Analytics-Agent-for-Linux](../../azure-monitor/visualize/vmext-troubleshoot.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
