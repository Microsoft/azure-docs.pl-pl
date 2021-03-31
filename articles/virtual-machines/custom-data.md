---
title: Dane niestandardowe i Virtual Machines platformy Azure
description: Szczegółowe informacje na temat używania niestandardowych danych i Cloud-Init na platformie Azure Virtual Machines
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87284918"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Dane niestandardowe i Cloud-Init na platformie Azure Virtual Machines

Może być konieczne wstrzyknięcie skryptu lub innych metadanych do Microsoft Azure maszyny wirtualnej w czasie aprowizacji.  W innych chmurach pojęcie to jest często określane jako dane użytkownika.  W Microsoft Azure mamy podobną funkcję o nazwie dane niestandardowe. 

Dane niestandardowe są dostępne tylko dla maszyny wirtualnej podczas pierwszej konfiguracji rozruchu/początkowej, nazywamy to "Inicjowanie obsługi". Inicjowanie obsługi jest procesem, w którym maszyny wirtualne tworzą parametry (na przykład nazwa hosta, nazwa użytkownika, hasło, certyfikaty, dane niestandardowe, klucze itp.), są dostępne dla maszyny wirtualnej, a agent inicjowania obsługi administracyjnej przetwarza je, takie jak [Agent systemu Linux](./extensions/agent-linux.md) i usługa [Cloud-init](./linux/using-cloud-init.md#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Przekazywanie danych niestandardowych do maszyny wirtualnej
Aby użyć danych niestandardowych, należy najpierw zakodować zawartość Base64 przed przekazaniem jej do interfejsu API, chyba że używasz narzędzia interfejsu wiersza polecenia, które wykonuje konwersję dla użytkownika, takiego jak AZ CLI. Rozmiar nie może przekraczać 64 KB.

W interfejsie wiersza polecenia można przekazać dane niestandardowe jako plik i zostanie on przekonwertowany na format Base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

W Azure Resource Manager (ARM) istnieje [Funkcja Base64](../azure-resource-manager/templates/template-functions-string.md#base64).

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
Agenci aprowizacji zainstalowani na maszynach wirtualnych współdziałają z platformą i umieszczają ją w systemie plików. 

### <a name="windows"></a>Windows
Dane niestandardowe są umieszczane w *%SYSTEMDRIVE%\AzureData\CustomData.bin* jako plik binarny, ale nie są przetwarzane. Jeśli chcesz przetworzyć ten plik, musisz utworzyć obraz niestandardowy i napisać kod, aby przetworzyć CustomData. bin.

### <a name="linux"></a>Linux  
W systemie operacyjnym Linux dane niestandardowe są przesyłane do maszyny wirtualnej za pośrednictwem pliku ovf-env.xml, który jest kopiowany do katalogu */var/lib/waagent* podczas aprowizacji.  Nowsze wersje agenta Microsoft Azure Linux również skopiują dane zakodowane algorytmem Base64 do */var/lib/waagent/CustomData* i wygodę.

Obecnie platforma Azure obsługuje dwóch agentów aprowizacji:
* Agent systemu Linux — domyślnie Agent nie będzie przetwarzać danych niestandardowych, należy utworzyć niestandardowy obraz z włączonym. Odpowiednie ustawienia, zgodnie z [dokumentacją](https://github.com/Azure/WALinuxAgent#configuration) :
    * Inicjowanie obsługi administracyjnej. DecodeCustomData
    * Provisioning.ExecuteCustomData

Po włączeniu danych niestandardowych i wykonaniu skryptu zostanie opóźnione raportowanie maszyn wirtualnych, które jest gotowe lub że inicjowanie obsługi zakończyło się powodzeniem do momentu zakończenia skryptu. Jeśli skrypt przekracza łączny czas udostępniania maszyny wirtualnej wynoszący 40 minut, Tworzenie maszyny wirtualnej zakończy się niepowodzeniem. Uwaga Jeśli wykonanie skryptu lub błędy podczas wykonywania nie powiedzie się, nie jest uznawany za krytyczny błąd aprowizacji, należy utworzyć ścieżkę powiadomienia, aby poinformować użytkownika o stanie ukończenia skryptu.

Aby rozwiązać problem z niestandardowym wykonywaniem danych, przejrzyj */var/log/waagent.log*

* Cloud-init-domyślnie domyślnie przetwarza dane niestandardowe. Usługa Cloud-init akceptuje [wiele formatów](https://cloudinit.readthedocs.io/en/latest/topics/format.html) danych niestandardowych, takich jak konfiguracja usługi Cloud-init, skrypty itp. Podobnie jak w przypadku agenta systemu Linux, gdy usługa Cloud-init przetwarza dane niestandardowe. Jeśli występują błędy podczas wykonywania operacji przetwarzania lub tworzenia konfiguracji, nie jest uznawany za krytyczny błąd aprowizacji i należy utworzyć ścieżkę powiadomienia, aby poinformować użytkownika o stanie ukończenia skryptu. Jednak inne dla agenta systemu Linux Usługa Cloud-init nie czeka na ukończenie niestandardowych konfiguracji danych użytkownika przed rozpoczęciem raportowania do platformy, która jest gotowa do użycia przez maszynę wirtualną. Aby uzyskać więcej informacji o usłudze Cloud-init na platformie Azure, zapoznaj się z [dokumentacją](./linux/using-cloud-init.md).


Aby rozwiązać problem z wykonywaniem danych niestandardowych, zapoznaj się z [dokumentacją](./linux/using-cloud-init.md#troubleshooting-cloud-init)rozwiązywania problemów.


## <a name="faq"></a>Często zadawane pytania
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Czy mogę zaktualizować dane niestandardowe po utworzeniu maszyny wirtualnej?
W przypadku pojedynczych maszyn wirtualnych nie można zaktualizować danych niestandardowych w modelu maszyny wirtualnej, ale dla VMSS można zaktualizować dane niestandardowe VMSS za pośrednictwem [interfejsu API REST](/rest/api/compute/virtualmachinescalesets/update) (nie dotyczy to klientów PS i AZ CLI). Gdy aktualizujesz dane niestandardowe w modelu VMSS:
* Istniejące wystąpienia w VMSS nie otrzymają zaktualizowanych danych niestandardowych tylko do momentu ich odłączenia.
* Istniejące wystąpienia w uaktualnianym VMSS nie będą otrzymywać zaktualizowanych danych niestandardowych.
* Nowe wystąpienia otrzymają nowe dane niestandardowe.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Czy mogę umieścić poufne wartości w danych niestandardowych?
Firma Microsoft zaleca, aby **nie** przechowywać poufnych danych w danych niestandardowych. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące zabezpieczeń i szyfrowania na platformie Azure](../security/fundamentals/data-encryption-best-practices.md).


### <a name="is-custom-data-made-available-in-imds"></a>Czy dane niestandardowe są dostępne w IMDS?
Nie. Ta funkcja nie jest obecnie dostępna.
