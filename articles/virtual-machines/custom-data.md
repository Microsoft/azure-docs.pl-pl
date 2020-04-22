---
title: Dane niestandardowe i maszyny wirtualne platformy Azure
description: Szczegółowe informacje na temat korzystania z niestandardowych danych i funkcji cloud-init na maszynach wirtualnych platformy Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 9497e665d024b583c261ade3e6fb5393a9322ce0
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759138"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Niestandardowe dane i funkcja cloud-init na maszynach wirtualnych platformy Azure

## <a name="what-is-custom-data"></a>Co to są dane niestandardowe?

Klienci często pytają, jak mogą wstrzyknąć skrypt lub inne metadane do maszyny wirtualnej platformy Microsoft Azure w czasie aprowizowania.  W innych chmurach ta koncepcja jest często określana jako dane użytkownika.  Na platformie Microsoft Azure mamy podobną funkcję o nazwie dane niestandardowe. 

Dane niestandardowe są udostępniane tylko maszynie wirtualnej podczas pierwszej konfiguracji rozruchowej/początkowej, nazywamy to "inicjowanie obsługi administracyjnej". Inicjowanie obsługi administracyjnej to proces, w którym maszyny Wirtualnej Tworzenie parametrów (na przykład nazwa hosta, nazwa użytkownika, hasło, certyfikaty, dane niestandardowe, klucze itp.) są udostępniane maszynie wirtualnej i agent aprowizujący przetwarza je, takie jak [Agent Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) i [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Przekazywanie danych niestandardowych do maszyny Wirtualnej
Aby użyć danych niestandardowych, należy najpierw zakodować zawartość 64 przed przekazaniem jej do interfejsu API, chyba że używasz narzędzia interfejsu wiersza polecenia, które wykonuje konwersję dla Ciebie, takiego jak AZ CLI. Rozmiar nie może przekraczać 64 KB.

W wierszu polecenia można przekazać dane niestandardowe jako plik i zostaną przekonwertowane na base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

W usłudze Azure Resource Manager (ARM) istnieje [funkcja base64](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Przetwarzanie danych niestandardowych
Agenci inicjowania obsługi administracyjnej zainstalowane na maszynach wirtualnych obsługują połączenie z platformą i umieszczenie go w systemie plików. 

### <a name="windows"></a>Windows
Dane niestandardowe są umieszczane w pliku plików binarnych *%SYSTEMDRIVE%\AzureData\CustomData.bin,* ale nie są przetwarzane. Jeśli chcesz przetworzyć ten plik, musisz utworzyć obraz niestandardowy i napisać kod do przetworzenia pliku CustomData.bin.

### <a name="linux"></a>Linux  
W systemie operacyjnym Linux dane niestandardowe są przekazywane do maszyny Wirtualnej za pośrednictwem pliku ovf-env.xml, który jest kopiowany do katalogu */var/lib/waagent* podczas inicjowania obsługi administracyjnej.  Nowsze wersje programu Microsoft Azure Linux Agent również skopiować base64 zakodowane dane do */var/lib/waagent/CustomData,* jak również dla wygody.

Platforma Azure obsługuje obecnie dwóch agentów inicjowania obsługi administracyjnej:
* Linux Agent — domyślnie agent nie będzie przetwarzać danych niestandardowych, trzeba będzie zbudować niestandardowy obraz z włączoną. Odpowiednie ustawienia, zgodnie z [dokumentacją,](https://github.com/Azure/WALinuxAgent#configuration) to:
    * Inicjowanie obsługi administracyjnej.DecodeCustomData
    * Inicjowanie obsługi administracyjnej.ExecuteCustomData

Po włączeniu danych niestandardowych i wykonać skrypt, opóźni raportowanie maszyny Wirtualnej, który jest gotowy lub że inicjowanie administracyjne powiodło się, dopóki skrypt nie zostanie ukończony. Jeśli skrypt przekracza całkowity limit czasu inicjowania obsługi administracyjnej maszyny Wirtualnej 40 minut, tworzenie maszyny Wirtualnej zakończy się niepowodzeniem. Uwaga: jeśli skrypt nie zostanie wykonany lub błędy podczas wykonywania, nie jest uważany za błąd inicjowania obsługi administracyjnej ze skutkiem śmiertelnym, należy utworzyć ścieżkę powiadomień, aby ostrzec o stanie ukończenia skryptu.

Aby rozwiązać problem z wykonywaniem danych niestandardowych, przejrzyj */var/log/waagent.log*

* cloud-init — domyślnie będzie domyślnie przetwarzać dane niestandardowe, cloud-init akceptuje [wiele formatów](https://cloudinit.readthedocs.io/en/latest/topics/format.html) danych niestandardowych, takich jak konfiguracja cloud-init, skrypty itp. Podobnie jak agent systemu Linux, gdy cloud-init przetwarza dane niestandardowe. Jeśli występują błędy podczas wykonywania przetwarzania konfiguracji lub skryptów, nie jest uważany za błąd inicjowania obsługi administracyjnej ze skutkiem śmiertelnym i należy utworzyć ścieżkę powiadomień, aby ostrzec o stanie ukończenia skryptu. Jednak inny niż agent systemu Linux, cloud-init nie czeka na konfiguracje danych niestandardowych użytkownika, aby zakończyć przed zgłoszeniem do platformy, że maszyna wirtualna jest gotowa. Aby uzyskać więcej informacji na temat cloud-init na platformie Azure, zapoznaj się z [dokumentacją](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Aby rozwiązać problem z wykonywaniem danych niestandardowych, zapoznaj się z [dokumentacją](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)rozwiązywania problemów .


## <a name="faq"></a>Często zadawane pytania
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Czy mogę zaktualizować dane niestandardowe po utworzeniu maszyny Wirtualnej?
W przypadku pojedynczych maszyn wirtualnych nie można zaktualizować danych niestandardowych w modelu maszyny Wirtualnej, ale w przypadku usług VMSS można aktualizować dane niestandardowe VMSS za pośrednictwem interfejsu API REST (nie dotyczy klientów interfejsu WIERSZA POLECENIA PS lub AZ). Podczas aktualizowania danych niestandardowych w modelu VMSS:
* Istniejące wystąpienia w VMSS nie otrzymają zaktualizowanych danych niestandardowych, dopóki nie zostaną ponownie zaimkowane.
* Istniejące wystąpienia w VMSS, które są uaktualniane, nie otrzymają zaktualizowanych danych niestandardowych.
* Nowe wystąpienia otrzymają nowe dane niestandardowe.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Czy mogę umieścić poufne wartości w danych niestandardowych?
**Zalecamy,** aby nie przechowywać poufnych danych w danych niestandardowych. Aby uzyskać więcej informacji, zobacz [Najważniejsze wskazówki dotyczące zabezpieczeń i szyfrowania platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>Czy dane niestandardowe są udostępniane w imds?
Nie, ta funkcja nie jest obecnie dostępna.
