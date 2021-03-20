---
title: Usuwanie serwerów i wyłączanie ochrony | Microsoft Docs
description: W tym artykule opisano, jak wyrejestrować serwery z magazynu Site Recovery i wyłączyć ochronę maszyn wirtualnych i serwerów fizycznych.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sharrai
ms.openlocfilehash: 1356deabd13db1dd2f29ac1b1f088db2120353fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738858"
---
# <a name="remove-servers-and-disable-protection"></a>Usuwanie serwerów i wyłączanie ochrony

W tym artykule opisano, jak wyrejestrować serwery z magazynu Recovery Services i jak wyłączyć ochronę dla maszyn chronionych przez Site Recovery.


## <a name="unregister-a--configuration-server"></a>Wyrejestrowywanie serwera konfiguracji

Jeśli replikujesz maszyny wirtualne VMware lub serwery fizyczne z systemem Windows/Linux do platformy Azure, możesz wyrejestrować niepołączony serwer konfiguracji z magazynu w następujący sposób:

1. [Wyłącz ochronę maszyn wirtualnych](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Usuń skojarzenie lub usunąć](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) zasady replikacji.
3. [Usuń serwer konfiguracji](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Wyrejestrowywanie serwera programu VMM

1. Zatrzymaj replikację maszyn wirtualnych w chmurach na serwerze programu VMM, który chcesz usunąć.
2. Usuń wszystkie mapowania sieci używane przez chmury na serwerze programu VMM, który chcesz usunąć. W obszarze **Site Recovery infrastruktura**  >  **dla mapowania sieci programu System Center VMM**  >  kliknij prawym przyciskiem myszy mapowanie sieci > **Usuń**.
3. Zanotuj identyfikator serwera programu VMM.
4. Usuń skojarzenie zasad replikacji z chmur na serwerze programu VMM, który chcesz usunąć.  W **Site Recovery infrastruktura**  >  **dla zasad replikacji programu System Center VMM**  >   kliknij dwukrotnie skojarzone zasady. Kliknij prawym przyciskiem myszy > w chmurze, aby **usunąć skojarzenie**.
5. Usuń serwer programu VMM lub aktywny węzeł. W **Site Recovery infrastruktura**  >  **dla serwerów programu System Center VMM**  >  **VMM** kliknij prawym przyciskiem myszy serwer > **Usuń**.
6. Jeśli serwer programu VMM był w stanie odłączonym, Pobierz i uruchom [skrypt oczyszczania](/samples/browse/?redirectedfrom=TechNet-Gallery) na serwerze programu VMM. Otwórz program PowerShell z opcją **Uruchom jako administrator** , aby zmienić zasady wykonywania dla domyślnego zakresu (LocalMachine). W skrypcie Określ identyfikator serwera programu VMM, który chcesz usunąć. Skrypt usuwa z serwera informacje o rejestracji i parowania z chmurą.
5. Uruchom skrypt oczyszczania na dowolnym pomocniczym serwerze programu VMM.
6. Uruchom skrypt czyszczący dla wszystkich innych pasywnych węzłów klastra programu VMM, na których zainstalowano dostawcę.
7. Odinstaluj dostawcę ręcznie na serwerze programu VMM. Jeśli masz klaster, Usuń ze wszystkich węzłów.
8. Jeśli maszyny wirtualne zostały replikowane do platformy Azure, należy odinstalować agenta Recovery Services firmy Microsoft z hostów funkcji Hyper-V w usuniętej chmurze.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Wyrejestrowywanie hosta funkcji Hyper-V w lokacji funkcji Hyper-V

Hosty funkcji Hyper-V, które nie są zarządzane przez program VMM, są zbierane do lokacji funkcji Hyper-V. Usuń hosta w lokacji funkcji Hyper-V w następujący sposób:

1. Wyłącz replikację dla maszyn wirtualnych funkcji Hyper-V znajdujących się na hoście.
2. Usuń skojarzenia zasad dla lokacji funkcji Hyper-V. W **Site Recovery infrastrukturze**  >  **dla zasad replikacji lokacji funkcji Hyper-V**  >   kliknij dwukrotnie skojarzone zasady. Kliknij prawym przyciskiem myszy witrynę > **Usuń skojarzenie**.
3. Usuń hosty funkcji Hyper-V. W **infrastrukturze usługi Site Recovery** dla hostów funkcji Hyper-v w lokacji z systemem  >    >  , kliknij prawym przyciskiem myszy serwer > **Usuń**.
4. Usuń lokację funkcji Hyper-V po usunięciu z niej wszystkich hostów. W **Site Recovery infrastrukturze** dla lokacji funkcji Hyper-v w lokacjach funkcji Hyper-v  >    >  kliknij prawym przyciskiem myszy lokację > **Usuń**.
5. Jeśli host funkcji Hyper-V był w stanie **odłączonym** , uruchom następujący skrypt na każdym usuniętym hoście funkcji Hyper-v. Skrypt czyści ustawienia na serwerze i wyrejestrowuje go z magazynu.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Wyłącz ochronę maszyny wirtualnej VMware lub serwera fizycznego (VMware do platformy Azure)

1. W obszarze **chronione elementy**  >  **zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
2. Na stronie **Wyłącz replikację** wybierz jedną z następujących opcji:
    - **Wyłącz replikację i Usuń (zalecane)** — ta opcja powoduje usunięcie zreplikowanego elementu z Azure Site Recovery a replikacja dla maszyny zostanie zatrzymana. Konfiguracja replikacji na serwerze konfiguracji jest czyszczona, a Site Recovery rozliczeń dla tego chronionego serwera zostanie zatrzymana. Tej opcji można używać tylko wtedy, gdy serwer konfiguracji jest w stanie połączonym.
    - **Usuń** — ta opcja powinna być używana tylko wtedy, gdy środowisko źródłowe zostało usunięte lub jest niedostępne (niepołączone). Spowoduje to usunięcie zreplikowanego elementu z Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na serwerze konfiguracji **nie zostanie** wyczyszczona. 

> [!NOTE]
> W ramach obu opcji usługa mobilności nie zostanie odinstalowana z chronionych serwerów, należy odinstalować ją ręcznie. Jeśli planujesz ponownie chronić serwer przy użyciu tego samego serwera konfiguracji, możesz pominąć Odinstalowywanie usługi mobilności.

> [!NOTE]
> Jeśli maszyna wirtualna została już przełączona w tryb failover i działa na platformie Azure, należy pamiętać, że wyłączenie ochrony nie powoduje usunięcia/wpływu na maszynę wirtualną w trybie failover.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Wyłączanie ochrony maszyny wirtualnej platformy Azure (Azure na platformę Azure)

-  W obszarze **chronione elementy**  >  **zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
> [!NOTE]
> usługa mobilności nie zostanie odinstalowana z chronionych serwerów, należy odinstalować ją ręcznie. Jeśli planujesz ponownie chronić serwer, możesz pominąć Odinstalowywanie usługi mobilności.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Wyłączanie ochrony maszyny wirtualnej funkcji Hyper-V (funkcja Hyper-V do platformy Azure)

> [!NOTE]
> Tej procedury należy użyć w przypadku replikowania maszyn wirtualnych funkcji Hyper-V na platformę Azure bez serwera programu VMM. Jeśli replikujesz maszyny wirtualne za pomocą scenariusza programu **System Center VMM do platformy Azure** , postępuj zgodnie z instrukcjami dotyczącymi wyłączania ochrony maszyny wirtualnej funkcji Hyper-V za pomocą scenariusza programu System Center VMM do platformy Azure

1. W obszarze **chronione elementy**  >  **zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
2. W obszarze **Wyłącz replikację** można wybrać następujące opcje:
   - **Wyłącz replikację i Usuń (zalecane)** — ta opcja powoduje usunięcie zreplikowanego elementu z Azure Site Recovery a replikacja dla maszyny zostanie zatrzymana. Konfiguracja replikacji na lokalnej maszynie wirtualnej zostanie oczyszczona, a Site Recovery rozliczenia dla tego chronionego serwera zostanie zatrzymana.
   - **Usuń** — ta opcja powinna być używana tylko wtedy, gdy środowisko źródłowe zostało usunięte lub jest niedostępne (niepołączone). Spowoduje to usunięcie zreplikowanego elementu z Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na lokalnej maszynie wirtualnej **nie zostanie** wyczyszczona. 

    > [!NOTE]
    > W przypadku wybrania opcji **Usuń** uruchom następujący zestaw skryptów, aby wyczyścić ustawienia replikacji lokalnego serwera funkcji Hyper-V.

    > [!NOTE]
    > Jeśli maszyna wirtualna została już przełączona w tryb failover i działa na platformie Azure, należy pamiętać, że wyłączenie ochrony nie powoduje usunięcia/wpływu na maszynę wirtualną w trybie failover.

1. Aby usunąć replikację maszyny wirtualnej na źródłowym serwerze hosta funkcji Hyper-V. Zastąp SQLVM1 nazwą swojej maszyny wirtualnej i uruchom skrypt z administracyjnego programu PowerShell

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Wyłącz ochronę maszyny wirtualnej funkcji Hyper-V replikowanie na platformę Azure za pomocą scenariusza programu System Center VMM do platformy Azure

1. W obszarze **chronione elementy**  >  **zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
2. W obszarze **Wyłącz replikację** wybierz jedną z następujących opcji:

   - **Wyłącz replikację i Usuń (zalecane)** — ta opcja powoduje usunięcie zreplikowanego elementu z Azure Site Recovery a replikacja dla maszyny zostanie zatrzymana. Konfiguracja replikacji na lokalnej maszynie wirtualnej jest czyszczona, a Site Recovery rozliczeń dla tego chronionego serwera zostanie zatrzymana.
   - **Usuń** — ta opcja powinna być używana tylko wtedy, gdy środowisko źródłowe zostało usunięte lub jest niedostępne (niepołączone). Spowoduje to usunięcie zreplikowanego elementu z Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na lokalnej maszynie wirtualnej **nie zostanie** wyczyszczona. 

     > [!NOTE]
     > W przypadku wybrania opcji **Usuń** należy TUN następujące skrypty, aby wyczyścić ustawienia replikacji lokalnego serwera programu VMM.
3. Uruchom ten skrypt na źródłowym serwerze programu VMM, używając programu PowerShell (wymagane są uprawnienia administratora) z konsoli programu VMM. Zastąp symbol zastępczy **SQLVM1** nazwą swojej maszyny wirtualnej.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. Powyższe kroki wyczyściją ustawienia replikacji na serwerze programu VMM. Aby zatrzymać replikację maszyny wirtualnej uruchomionej na serwerze hosta funkcji Hyper-V, Uruchom ten skrypt. Zastąp SQLVM1 nazwą swojej maszyny wirtualnej i host01.contoso.com nazwą serwera hosta funkcji Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Wyłącz ochronę maszyny wirtualnej funkcji Hyper-V replikowanie na pomocniczy serwer programu VMM za pomocą scenariusza programu System Center VMM do programu VMM

1. W obszarze **chronione elementy**  >  **zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
2. W obszarze **Wyłącz replikację** wybierz jedną z następujących opcji:

   - **Wyłącz replikację i Usuń (zalecane)** — ta opcja powoduje usunięcie zreplikowanego elementu z Azure Site Recovery a replikacja dla maszyny zostanie zatrzymana. Konfiguracja replikacji na lokalnej maszynie wirtualnej jest czyszczona, a Site Recovery rozliczeń dla tego chronionego serwera zostanie zatrzymana.
   - **Usuń** — ta opcja powinna być używana tylko wtedy, gdy środowisko źródłowe zostało usunięte lub jest niedostępne (niepołączone). Spowoduje to usunięcie zreplikowanego elementu z Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na lokalnej maszynie wirtualnej **nie zostanie** wyczyszczona. Uruchom następujący zestaw skryptów, aby wyczyścić ustawienia replikacji lokalnych maszyn wirtualnych.
     > [!NOTE]
     > W przypadku wybrania opcji **Usuń** należy TUN następujące skrypty, aby wyczyścić ustawienia replikacji lokalnego serwera programu VMM.

3. Uruchom ten skrypt na źródłowym serwerze programu VMM, używając programu PowerShell (wymagane są uprawnienia administratora) z konsoli programu VMM. Zastąp symbol zastępczy **SQLVM1** nazwą swojej maszyny wirtualnej.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. Na pomocniczym serwerze programu VMM Uruchom ten skrypt, aby wyczyścić ustawienia dla pomocniczej maszyny wirtualnej:

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Remove-SCVirtualMachine -VM $vm -Force
    ```

5. Na serwerze pomocniczym programu VMM Odśwież maszyny wirtualne na serwerze hosta funkcji Hyper-V, aby ponownie wykryć dodatkową maszynę wirtualną w konsoli programu VMM.
6. Powyższe kroki wyczyściją ustawienia replikacji na serwerze programu VMM. Jeśli chcesz zatrzymać replikację maszyny wirtualnej, uruchom poniższy skrypt jako podstawową i pomocniczą maszynę wirtualną. Zastąp SQLVM1 nazwą swojej maszyny wirtualnej.

    ```powershell
    Remove-VMReplication –VMName "SQLVM1"
    ```