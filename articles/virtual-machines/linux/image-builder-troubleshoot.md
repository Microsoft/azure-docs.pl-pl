---
title: Rozwiązywanie problemów z usługą Azure Image Builder
description: Rozwiązywanie typowych problemów i błędów podczas korzystania z usługi Azure VM Image Builder
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: f76c3e6c739ae4dd13355d350a01b878e4d4f360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666214"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Rozwiązywanie problemów z usługą Azure Image Builder

Ten artykuł pomaga w rozwiązywaniu problemów i rozwiązywaniu typowych problemów, które mogą wystąpić podczas korzystania z usługi Azure Image Builder.

Awarie AIB mogą wystąpić w 2 obszarach:
- Przesyłanie szablonu obrazu
- Kompilacja obrazu

## <a name="troubleshoot-image-template-submission-errors"></a>Rozwiązywanie problemów z błędami przesłania szablonu obrazu

Błędy przesłania szablonu obrazu są zwracane tylko w przypadku przesłania. Dziennik błędów nie zawiera błędów przesłania szablonu obrazu. Jeśli wystąpił błąd podczas przekazywania, można zwrócić błąd poprzez sprawdzenie stanu szablonu, a w tym celu przejrzenie `ProvisioningState` i `ProvisioningErrorMessage` / `provisioningError` .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> W przypadku programu PowerShell konieczne będzie zainstalowanie [modułów programu PowerShell dla narzędzia Azure Image Builder](../windows/image-builder-powershell.md#prerequisites).

Poniższe sekcje zawierają wskazówki dotyczące rozwiązywania problemów z typowymi błędami przesłania szablonu obrazu.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>Aktualizacja/uaktualnienie szablonów obrazów nie jest obecnie obsługiwane

#### <a name="error"></a>Błąd

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Przyczyna

Szablon już istnieje.

#### <a name="solution"></a>Rozwiązanie

Jeśli przesyłasz szablon konfiguracji obrazu i przesyłanie nie powiedzie się, nadal istnieje artefakt szablonu zakończony niepowodzeniem. Usuń szablon zakończony niepowodzeniem.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>Operacja zasobu została ukończona z stanem inicjowania obsługi terminalu "Niepowodzenie"

#### <a name="error"></a>Błąd

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Przyczyna

W większości przypadków błąd niepowodzenia wdrożenia zasobu występuje ze względu na brakujące uprawnienia.

#### <a name="solution"></a>Rozwiązanie

W zależności od scenariusza Konstruktor obrazów platformy Azure może potrzebować uprawnień do:
- Grupa zasobów obrazu źródłowego lub udostępnionej galerii obrazów
- Obraz dystrybucji lub zasób galerii udostępnionego obrazu
- Konto magazynu, kontener lub obiekt BLOB, do którego uzyskuje dostęp. 

Aby uzyskać więcej informacji na temat konfigurowania uprawnień, zobacz [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu interfejsu wiersza polecenia platformy Azure](image-builder-permissions-cli.md) lub [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu programu PowerShell](image-builder-permissions-powershell.md) .

### <a name="error-getting-managed-image"></a>Błąd podczas pobierania obrazu zarządzanego

#### <a name="error"></a>Błąd

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Przyczyna

Brakujące uprawnienia.

#### <a name="solution"></a>Rozwiązanie

W zależności od scenariusza Konstruktor obrazów platformy Azure może potrzebować uprawnień do:
* Grupa zasobów obrazu źródłowego lub udostępnionej galerii obrazów
* Obraz dystrybucji lub zasób galerii udostępnionego obrazu
* Konto magazynu, kontener lub obiekt BLOB, do którego uzyskuje dostęp. 

Aby uzyskać więcej informacji na temat konfigurowania uprawnień, zobacz [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu interfejsu wiersza polecenia platformy Azure](image-builder-permissions-cli.md) lub [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu programu PowerShell](image-builder-permissions-powershell.md) .

### <a name="build--step-failed-for-image-version"></a>Nie można wykonać kroku kompilacji dla wersji obrazu

#### <a name="error"></a>Błąd
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Przyczyna

Konstruktor obrazów platformy Azure nie może zlokalizować obrazu źródłowego.

#### <a name="solution"></a>Rozwiązanie

Upewnij się, że obraz źródłowy jest prawidłowy i istnieje w lokalizacji usługi Azure Image Builder.

### <a name="downloading-external-file-to-local-file"></a>Pobieranie pliku zewnętrznego do pliku lokalnego

#### <a name="error"></a>Błąd

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Przyczyna

Nazwa lub lokalizacja pliku jest nieprawidłowa lub lokalizacja jest nieosiągalna.

#### <a name="solution"></a>Rozwiązanie

Upewnij się, że plik jest osiągalny. Sprawdź, czy nazwa i lokalizacja są poprawne.

## <a name="troubleshoot-build-failures"></a>Rozwiązywanie problemów z błędami kompilacji

W przypadku błędów kompilacji obrazu można pobrać błąd z `lastrunstatus` , a następnie przejrzeć szczegóły w temacie Customization. log.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Dziennik dostosowania

Gdy kompilacja obrazu jest uruchomiona, dzienniki są tworzone i przechowywane na koncie magazynu. Usługa Azure Image Builder tworzy konto magazynu w tymczasowej grupie zasobów podczas tworzenia artefaktu szablonu obrazu.

Nazwa konta magazynu używa następującego wzorca: **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

Na przykład *IT_aibmdi_helloImageTemplateLinux01*.

Możesz wyświetlić polecenie Dostosowywanie. log na koncie magazynu w grupie zasobów, wybierając pozycję   >  **obiekty blob** konta magazynu  >  `packerlogs` .  Następnie wybierz pozycję **katalog > Dostosowywanie. log**.


### <a name="understanding-the-customization-log"></a>Informacje o dzienniku dostosowania

Dziennik jest pełny. Obejmuje to kompilację obrazu, w tym wszelkie problemy z dystrybucją obrazów, takie jak replikacja udostępnionej galerii obrazów. Te błędy są podane w komunikacie o błędzie stanu szablonu obrazu.

Dostosowanie. log obejmuje następujące etapy:

1. Wdróż maszynę wirtualną i zależności kompilacji przy użyciu szablonów usługi ARM na etapie IT_ grupy zasobów tymczasowych. Ten etap obejmuje wiele wpisów do dostawcy zasobów programu Azure Image Builder:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Stan etapu wdrożenia. Ten etap obejmuje stan poszczególnych wdrożeń zasobów:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Nawiąż połączenie z etapem kompilowania maszyny wirtualnej.

    W przypadku systemu Windows usługa Azure Image Builder nawiązuje połączenie za pomocą usługi WinRM:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    W przypadku systemu Linux usługa Azure Image Builder będzie łączyć się przy użyciu protokołu SSH:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Uruchom etap dostosowywania. Po uruchomieniu dostosowań można je zidentyfikować, przeglądając temat Dostosowywanie. log. Wyszukaj frazę *(telemetrię)*.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. Etap cofania aprowizacji. Konstruktor obrazów platformy Azure dodaje ukryty element dostosowujący. Ten krok cofania obsługi jest odpowiedzialny za przygotowanie maszyny wirtualnej do nieaprowizacji. Uruchamia narzędzie Windows Sysprep (przy użyciu programu c:\DeprovisioningScript.ps1) lub waagent z systemem Linux (przy użyciu/tmp/DeprovisioningScript.sh). 

    Na przykład:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Oczyść etap. Po zakończeniu kompilacji zasoby programu Azure Image Builder zostaną usunięte.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Wskazówki dotyczące rozwiązywania problemów ze skryptem/dostosowaniem wbudowanym
- Przetestuj kod przed dostarczeniem go do konstruktora obrazów
- Upewnij się, że Azure Policy i zapory umożliwiają łączność z zasobami zdalnymi.
- Komentarze wyjściowe do konsoli programu, takie jak użycie `Write-Host` lub `echo` , umożliwiają przeszukanie dziennika Customization. log.

## <a name="troubleshoot-common-build-errors"></a>Rozwiązywanie typowych błędów kompilacji

### <a name="packer-build-command-failure"></a>Błąd polecenia kompilacji pakietu Packer

#### <a name="error"></a>Błąd

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Przyczyna

Niepowodzenie dostosowywania.

#### <a name="solution"></a>Rozwiązanie

Przejrzyj dziennik, aby zlokalizować niepowodzenia konfiguratorów. Wyszukaj frazę *(telemetrię)*. 

Na przykład:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Przekroczono limit czasu

#### <a name="error"></a>Błąd

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Przyczyna

Kompilacja przekroczyła limit czasu kompilacji. Ten błąd jest widoczny w "LastRunStatus".

#### <a name="solution"></a>Rozwiązanie

1. Przejrzyj temat Customization. log. Zidentyfikuj ostatni dostosowany do uruchomienia. Wyszukaj `(telemetry)` od dołu dziennika. 

2. Sprawdź dostosowania skryptu. Dostosowania mogą nie pomijać interakcji użytkownika z poleceniami, takimi jak `quiet` Opcje. Na przykład `apt-get install -y` wyniki wykonywane przez skrypt oczekują na interakcję z użytkownikiem.

3. Jeśli używasz narzędzia `File` dostosowywania do pobierania artefaktów o rozmiarze większym niż 20 MB, zobacz sekcję obejścia.

4. Przejrzyj błędy i zależności w skrypcie, które mogą spowodować oczekiwania skryptu.

5. Jeśli spodziewasz się, że dostosowania będą potrzebować więcej czasu, zwiększ [buildTimeoutInMinutes](image-builder-json.md). Wartość domyślna to cztery godziny.

6. W przypadku akcji intensywnie korzystających z zasobów, takich jak pobieranie gigabajtów plików, należy wziąć pod uwagę rozmiar bazowej kompilacji maszyny wirtualnej. Usługa używa maszyny wirtualnej Standard_D1_v2. Maszyna wirtualna ma 1 vCPU i 3,5 GB pamięci. Jeśli pobierasz 50 GB, prawdopodobnie spowoduje to wyczerpanie zasobów maszyny wirtualnej i spowodowanie niepowodzeń komunikacji między usługą Azure Image Builder a kompilacją maszyny wirtualnej. Ponów próbę kompilacji przy użyciu większej ilości pamięci maszyny wirtualnej, ustawiając [VM_Size](image-builder-json.md#vmprofile).

### <a name="long-file-download-time"></a>Długi czas pobierania pliku

#### <a name="error"></a>Błąd
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Przyczyna 

Konfigurator plików pobiera duży plik.

#### <a name="solution"></a>Rozwiązanie

Konfigurator plików jest odpowiedni dla małych plików do pobrania poniżej 20 MB. Aby zwiększyć liczbę pobrań plików, Użyj skryptu lub polecenia wbudowanego. Na przykład w systemie Linux można użyć `wget` lub `curl` . W systemie Windows można użyć `Invoke-WebRequest` .

### <a name="error-waiting-on-shared-image-gallery"></a>Wystąpił błąd podczas oczekiwania na galerię obrazów udostępnionych

#### <a name="error"></a>Błąd

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Przyczyna

Podczas oczekiwania na dodanie obrazu do galerii obrazów udostępnionych (SIG) upłynął limit czasu dla konstruktora obrazu. Jeśli obraz jest wstrzykiwany do SIG, można założyć, że kompilacja obrazu zakończyła się pomyślnie. Jednak ogólny proces nie powiódł się, ponieważ Konstruktor obrazów czeka na udostępnioną galerię obrazów, aby zakończyć replikację. Mimo że kompilacja zakończyła się niepowodzeniem, replikacja jest kontynuowana. Właściwości wersji obrazu można uzyskać, sprawdzając *runOutput* dystrybucji.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Rozwiązanie

Zwiększ **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Niskie zdarzenia informacji o zasobach systemu Windows

#### <a name="error"></a>Błąd

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Przyczyna

Wyczerpanie zasobów. Ten problem jest często widoczny dla Windows Update uruchomiony przy użyciu domyślnego rozmiaru maszyny wirtualnej kompilacji D1_V2.

#### <a name="solution"></a>Rozwiązanie

Zwiększ rozmiar maszyny wirtualnej kompilacji.

### <a name="builds-finished-but-no-artifacts-were-created"></a>Kompilacje zakończone, ale nie utworzono żadnych artefaktów

#### <a name="error"></a>Błąd

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Przyczyna

Przekroczono limit czasu podczas oczekiwania na utworzenie wymaganych zasobów platformy Azure.

#### <a name="solution"></a>Rozwiązanie

Uruchom ponownie kompilację, aby spróbować ponownie.

### <a name="resource-not-found"></a>Nie znaleziono zasobu

#### <a name="error"></a>Błąd

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Przyczyna

Brakujące uprawnienia.

#### <a name="solution"></a>Rozwiązanie

Ponownie sprawdź, czy Konstruktor obrazów Azure ma wszystkie wymagane uprawnienia. 

Aby uzyskać więcej informacji na temat konfigurowania uprawnień, zobacz [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu interfejsu wiersza polecenia platformy Azure](image-builder-permissions-cli.md) lub [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu programu PowerShell](image-builder-permissions-powershell.md) .

### <a name="sysprep-timing"></a>Chronometraż programu Sysprep

#### <a name="error"></a>Błąd

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Przyczyna

Przyczyną może być problem z chronometrażem spowodowany rozmiarem maszyny wirtualnej D1_V2. Jeśli dostosowania są ograniczone i wykonywane w czasie krótszym niż trzy sekundy, polecenia Sysprep są uruchamiane przez program Azure Image Builder w celu cofnięcia aprowizacji. Gdy program Azure Image Builder anuluje postanowienia, polecenie Sysprep sprawdza *WindowsAzureGuestAgent*, co może nie być w pełni zainstalowane, powodując problem z chronometrażem. 

#### <a name="solution"></a>Rozwiązanie

Zwiększ rozmiar maszyny wirtualnej. Można też dodać 60-sekundowe dostosowanie środowiska PowerShell, aby uniknąć problemów z chronometrażem.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Anulowanie konstruktora po anulowaniu kontekstu anulowania kontekstu

#### <a name="error"></a>Błąd
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Przyczyna
Usługa Image Builder używa portu 22 (Linux) lub 5986 (Windows) do nawiązania połączenia z maszyną wirtualną kompilacji, dzieje się tak, gdy usługa jest odłączona od maszyny wirtualnej kompilacji podczas kompilacji obrazu. Przyczyny rozłączenia mogą się różnić, ale włączenie lub skonfigurowanie zapór w skrypcie może zablokować powyższe porty.

#### <a name="solution"></a>Rozwiązanie
Przejrzyj skrypty pod kątem zmian/włączenia zapory lub zmiany w usłudze SSH lub WinRM oraz upewnij się, że wszystkie zmiany umożliwiają stałą łączność między usługą i Tworzenie maszyny wirtualnej na powyższych portach. Aby uzyskać więcej informacji na temat sieci programu Image Builder, zapoznaj się z [wymaganiami](./image-builder-networking.md).

## <a name="devops-task"></a>Zadanie metodyki DevOps 

### <a name="troubleshooting-the-task"></a>Rozwiązywanie problemów z zadaniem
Zadanie będzie kończyć się niepowodzeniem w przypadku wystąpienia błędu podczas dostosowywania, gdy zostanie to zrobione, że zadanie zgłosi błąd i opuści grupę zasobów tymczasowych z dziennikami, dzięki czemu można zidentyfikować problem. 

Aby zlokalizować dziennik, należy znać nazwę szablonu, przejść do potoku > kompilacja nie powiodła się > przechodzenie do szczegółów zadania DevOps AIB, a następnie zobaczysz nazwę dziennika i szablonu:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Przejdź do portalu, wyszukaj nazwę szablonu w grupie zasobów, a następnie wyszukaj grupę zasobów przy użyciu IT_ *.
Przejdź do konta magazynu > obiekty blob > kontenery > dzienników.

### <a name="troubleshooting-successful-builds"></a>Rozwiązywanie problemów dotyczących kompilacji zakończonych powodzeniem
Mogą jednak wystąpić sytuacje, w których należy zbadać pomyślne kompilacje i chcieć przejrzeć dziennik. Jak wspomniano, jeśli kompilacja obrazu zakończy się pomyślnie, Grupa zasobów tymczasowych zawierająca dzienniki zostanie usunięta w ramach czyszczenia. Można jednak wykonać uśpienie po poleceniu wbudowanym, a następnie pobrać dzienniki, gdy kompilacja jest wstrzymana. W tym celu wykonaj następujące czynności:
 
1. Zaktualizuj polecenie wbudowane i Dodaj: Write-Host/Echo "uśpienie" — umożliwi to wyszukiwanie w dzienniku
2. Dodaj uśpienie dla co najmniej 10mins, możesz użyć polecenia [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep)lub `Sleep` Linux.
3. Użyj powyższej metody, aby zidentyfikować lokalizację dziennika, a następnie kontynuować pobieranie/sprawdzanie dziennika do momentu, aż do uśpienia.


### <a name="operation-was-canceled"></a>Operacja została anulowana

#### <a name="error"></a>Błąd

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Przyczyna

Jeśli kompilacja nie została anulowana przez użytkownika, została anulowana przez agenta użytkownika usługi Azure DevOps. Najprawdopodobniej upłynął limit czasu wynoszący 1 godzinę z powodu możliwości usługi Azure DevOps. Jeśli używasz prywatnego projektu i agenta, otrzymujesz 60 minut czasu kompilacji. Jeśli kompilacja przekracza limit czasu, DevOps anuluje uruchomione zadanie.

Aby uzyskać więcej informacji na temat możliwości i ograniczeń usługi Azure DevOps, zobacz [Agenci hostowani przez firmę Microsoft](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations) .
 
#### <a name="solution"></a>Rozwiązanie

Możesz obsługiwać własnych agentów DevOps lub przyjrzeć się skróceniu czasu kompilacji. Na przykład w przypadku dystrybuowania do galerii obrazów udostępnionych należy przeprowadzić replikację do jednego regionu. Jeśli chcesz replikować asynchronicznie. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Wolne logowanie do systemu Windows: "Czekaj na Instalator modułów systemu Windows"

#### <a name="error"></a>Błąd
Po utworzeniu obrazu systemu Windows 10 za pomocą programu Image Builder Utwórz maszynę wirtualną na podstawie obrazu, przy użyciu protokołu RDP i musisz poczekać minuty przy pierwszym logowaniu, zobaczyc niebieski ekran z komunikatem:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Rozwiązanie
Najpierw sprawdź, czy nie ma żadnych zaległych ponownych uruchomień wymaganych przez dodanie dostosowywania ponownego uruchomienia systemu Windows jako ostatniego dostosowania oraz ukończenie instalacji całego oprogramowania. Na koniec Dodaj opcję [/Mode: VM](/windows-hardware/manufacture/desktop/sysprep-command-line-options) do domyślnego programu Sysprep używanego przez AIB, zobacz poniżej "maszyny wirtualne utworzone na podstawie obrazów AIB nie zostały pomyślnie utworzone" > "przesłaniające polecenia"  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>Maszyny wirtualne utworzone na podstawie obrazów AIB nie zostały pomyślnie utworzone

Domyślnie program Azure Image *Builder uruchamia kod* cofania aprowizacji na końcu każdej fazy dostosowywania obrazu w celu *uogólnienia* obrazu. Uogólnianie to proces, w którym obraz jest skonfigurowany do ponownego użycia w celu utworzenia wielu maszyn wirtualnych i można przekazać ustawienia maszyny wirtualnej, takie jak nazwa hosta, nazwa użytkownika itd. W przypadku systemu Windows usługa Azure Image Builder wykonuje program *Sysprep* i dla programu Linux Azure Image Builder `waagent -deprovision` . 

W przypadku systemu Windows usługa Azure Image Builder używa ogólnych poleceń Sysprep. Jednak może to nie być odpowiednie dla każdego pomyślnego uogólnienia systemu Windows. Usługa Azure Image Builder umożliwia dostosowanie polecenia Sysprep. Uwaga Azure Image Builder to narzędzie do automatyzacji obrazów. Jest on odpowiedzialny za pomyślne uruchomienie polecenia Sysprep. Można jednak potrzebować innych poleceń programu Sysprep do ponownego użycia obrazu. W przypadku systemu Linux usługa Azure Image Builder używa `waagent -deprovision+user` polecenia generycznego. Aby uzyskać więcej informacji, zobacz [dokumentację agenta Microsoft Azure systemu Linux](https://github.com/Azure/WALinuxAgent#command-line-options).

W przypadku migrowania istniejącego dostosowania i używania innych poleceń Sysprep/waagent można wypróbować polecenia ogólne konstruktora obrazu. Jeśli Tworzenie maszyny wirtualnej nie powiedzie się, użyj poprzednich poleceń Sysprep/waagent.

> [!NOTE]
> Jeśli AIB pomyślnie tworzy obraz niestandardowy systemu Windows, a następnie utworzysz maszynę wirtualną, nie zostanie ona pomyślnie utworzona (na przykład polecenie tworzenia maszyny wirtualnej nie zostanie ukończone/przekroczenie limitu czasu), należy przejrzeć dokumentację programu Sysprep dla systemu Windows Server. Możesz też zgłosić żądanie pomocy technicznej z zespołem pomocy technicznej dla programu Windows Server Sysprep, który może rozwiązać problemy i zalecić poprawne polecenie Sysprep.

### <a name="command-locations-and-filenames"></a>Lokalizacje poleceń i nazwy plików

W systemie Windows:

```
c:\DeprovisioningScript.ps1
```

W systemie Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Polecenie Sysprep: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>Polecenie anulowania aprowizacji: system Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>Zastępowanie poleceń

Aby zastąpić polecenia, należy użyć programu PowerShell lub obsługi skryptów powłoki, aby utworzyć pliki poleceń z dokładną nazwą pliku i umieścić je w wymienionych wcześniej katalogach. Konstruktor obrazów platformy Azure odczytuje te polecenia i dane wyjściowe są zapisywane w *dzienniku Customization. log*.

## <a name="getting-support"></a>Uzyskiwanie pomocy technicznej
Jeśli zostały określone wskazówki i nadal nie można rozwiązać problemu, możesz otworzyć zgłoszenie do pomocy technicznej. W takim przypadku wybierz odpowiedni produkt i pomoc techniczną, co spowoduje włączenie zespołu pomocy technicznej konstruktora obrazów maszyn wirtualnych platformy Azure.

Wybieranie produktu przypadku:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Image Builder](../image-builder-overview.md).
