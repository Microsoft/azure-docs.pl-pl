---
title: Skrypty programu PowerShell dla Azure IoT Edge dla systemu Linux w systemie Windows | Microsoft Docs
description: Informacje referencyjne dotyczące Azure IoT Edge dla systemu Linux w skryptach środowiska Windows PowerShell do wdrażania, aprowizacji i IoT Edge stanu dla systemu Linux na maszynach wirtualnych z systemem Windows.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0b49cf4fe28e2a9b4d15f889d227b443777b1efa
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889250"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>Skrypty programu PowerShell dla IoT Edge dla systemu Linux w systemie Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Zapoznaj się ze skryptami programu PowerShell, które wdrażają, inicjują i pobierają stan IoT Edge dla systemu Linux na maszynie wirtualnej z systemem Windows.

Polecenia opisane w tym artykule pochodzą z `AzureEFLOW.psm1` pliku, który można znaleźć w Twoim systemie w `WindowsPowerShell` katalogu `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

Polecenie **Deploy-eFlow** to główna Metoda wdrażania. Polecenie wdrożenia tworzy maszynę wirtualną, Inicjuje obsługę plików i wdraża moduł agenta IoT Edge. Chociaż żaden z parametrów nie jest wymagany, można go użyć do aprowizacji urządzenia IoT Edge podczas wdrażania i modyfikowania ustawień dla maszyny wirtualnej podczas jej tworzenia. Aby zapoznać się z pełną listą, użyj polecenia `Get-Help Deploy-Eflow -full` .  

>[!NOTE]
>W przypadku typu aprowizacji obecnie **x509** odnosi się wyłącznie do aprowizacji x509 przy użyciu [IoT Hub Device Provisioning Service platformy Azure](../iot-dps/about-iot-dps.md). Ręczna metoda inicjowania obsługi x509 nie jest obecnie obsługiwana.

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| eflowVhdxDir | Ścieżka katalogu | Katalog, w którym wdrożenie zapisuje plik VHDX dla maszyny wirtualnej. |
| provisioningType | **Ręczna**, **TPM**, **x509** lub **symetryczny** |  Definiuje typ aprowizacji, który ma być używany przez urządzenie IoT Edge. |
| devConnString | Parametry połączenia urządzenia istniejącego urządzenia IoT Edge | Parametry połączenia urządzenia do ręcznego inicjowania obsługi urządzenia IoT Edge (**Ręczne**). |
| symmKey | Klucz podstawowy istniejącej rejestracji usługi DPS lub klucz podstawowy istniejącego urządzenia IoT Edge zarejestrowane przy użyciu kluczy symetrycznych | Klucz symetryczny do aprowizacji urządzenia IoT Edge (**x509** lub **symetryczny**). |
| Identyfikatora zakresu | Identyfikator zakresu istniejącego wystąpienia usługi DPS. | Identyfikator zakresu do aprowizacji urządzenia IoT Edge (**x509** lub **symetryczny**). |
| Identyfikator rejestracji | Identyfikator rejestracji istniejącego urządzenia IoT Edge | Identyfikator rejestracji na potrzeby aprowizacji urządzenia IoT Edge (**x509** lub **symetryczny**). |
| identityCertLocVm | Ścieżka katalogu; musi znajdować się w folderze, który może należeć do `iotedge` usługi | Bezwzględna ścieżka docelowa certyfikatu tożsamości na maszynie wirtualnej w celu aprowizacji urządzenia IoT Edge (**x509** lub **symetryczny**). |
| identityCertLocWin | Ścieżka katalogu | Bezwzględna ścieżka źródła certyfikatu tożsamości w systemie Windows do aprowizacji urządzenia IoT Edge (**x509** lub **symetryczny**). |
| identityPkLocVm |  | Ścieżka katalogu; musi znajdować się w folderze, który może należeć do `iotedge` usługi | Bezwzględna ścieżka docelowa prywatnego klucza tożsamości na maszynie wirtualnej na potrzeby aprowizacji urządzenia IoT Edge (**x509** lub **symetryczny**). |
| identityPkLocWin | Ścieżka katalogu | Bezwzględna ścieżka źródłowa klucza prywatnego tożsamości w systemie Windows do aprowizacji urządzenia IoT Edge (**x509** lub **symetryczny**). |
| vmSizeDefintion | Nie więcej niż 30 znaków | Definicja liczby rdzeni i dostępnej pamięci RAM dla maszyny wirtualnej. **Wartość domyślna**: Standard_K8S_v1. |
| vmDiskSize | Od 8 GB do 256 GB | Maksymalny rozmiar dysku dynamicznego powiększający się wirtualny dysk twardy. **Wartość domyślna**: 16 GB. |
| vmUser | Nie więcej niż 30 znaków | Nazwa użytkownika do logowania się na maszynie wirtualnej. |
| vnetType | **Transparent** lub **ICS** | Typ przełącznika wirtualnego. **Wartość domyślna**: transparent. |
| vnetName | Nie więcej niż 64 znaków | Nazwa przełącznika wirtualnego. **Wartość domyślna**: external. |
| enableVtpm | Brak | **Parametr przełącznika**. Utwórz maszynę wirtualną z włączonym lub wyłączonym modułem TPM. |
| mobyPackageVersion | Nie więcej niż 30 znaków |  Wersja pakietu Moby do zweryfikowania lub zainstalowania na maszynie wirtualnej.  **Wartość domyślna:** 19.03.11. |
| iotedgePackageVersion | Nie więcej niż 30 znaków | Wersja pakietu IoT Edge do zweryfikowania lub zainstalowania na maszynie wirtualnej. **Wartość domyślna:** 1.1.0. |
| installPackages | Brak | **Parametr przełącznika**. Po przełączeniu skrypt podejmie próbę zainstalowania pakietów Moby i IoT Edge, a nie tylko sprawdzenia, czy pakiety są obecne. |

## <a name="verify-eflowvm"></a>Verify-EflowVm

Polecenie **verify-EflowVm** jest uwidocznioną funkcją, aby sprawdzić, czy utworzono IoT Edge dla systemu Linux na maszynie wirtualnej z systemem Windows. Przyjmuje tylko typowe parametry i zwróci **wartość true** , jeśli maszyna wirtualna została utworzona, i **wartość false** , jeśli nie. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Verify-EflowVm -full` .

## <a name="provision-eflowvm"></a>Provision-EflowVm

Polecenie **EflowVm —** umożliwia dodanie informacji o aprowizacji urządzenia IoT Edge do pliku IoT Edge maszyny wirtualnej `config.yaml` . Inicjowanie obsługi można także wykonać w fazie wdrażania przez ustawienie parametrów w poleceniu **Deploy-eFlow** . Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Provision-EflowVm -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| vmUser | Nie więcej niż 30 znaków | Nazwa użytkownika do logowania się na maszynie wirtualnej. |
| provisioningType | **Ręczna**, **TPM**, **x509** lub **symetryczny** |  Definiuje typ aprowizacji, który ma być używany przez urządzenie IoT Edge. |
| devConnString | Parametry połączenia urządzenia istniejącego urządzenia IoT Edge | Parametry połączenia urządzenia do ręcznego inicjowania obsługi urządzenia IoT Edge (**Ręczne**). |
| symmKey | Klucz podstawowy istniejącej rejestracji usługi DPS lub klucz podstawowy istniejącego urządzenia IoT Edge zarejestrowane przy użyciu kluczy symetrycznych | Klucz symetryczny do aprowizacji urządzenia IoT Edge (**DPS**, **symetryczny**). |
| Identyfikatora zakresu | Identyfikator zakresu istniejącego wystąpienia usługi DPS. | Identyfikator zakresu dla aprowizacji urządzenia IoT Edgeowego (**DPS**). |
| Identyfikator rejestracji | Identyfikator rejestracji istniejącego urządzenia IoT Edge | Identyfikator rejestracji na potrzeby aprowizacji urządzenia IoT Edgeowego (**DPS**). |
| identityCertLocVm | Ścieżka katalogu; musi znajdować się w folderze, który może należeć do `iotedge` usługi | Bezwzględna ścieżka docelowa certyfikatu tożsamości na maszynie wirtualnej na potrzeby aprowizacji urządzenia IoT Edge (**DPS**, **x509**). |
| identityCertLocWin | Ścieżka katalogu | Bezwzględna ścieżka źródła certyfikatu tożsamości w systemie Windows do aprowizacji urządzenia IoT Edge (**DPS**, **x509**). |
| identityPkLocVm |  | Ścieżka katalogu; musi znajdować się w folderze, który może należeć do `iotedge` usługi | Bezwzględna ścieżka docelowa prywatnego klucza tożsamości na maszynie wirtualnej na potrzeby aprowizacji urządzenia IoT Edge (**DPS**, **x509**). |
| identityPkLocWin | Ścieżka katalogu | Bezwzględna ścieżka źródłowa klucza prywatnego tożsamości w systemie Windows do aprowizacji urządzenia IoT Edge (**DPS**, **x509**). |

## <a name="get-eflowvmname"></a>Get-EflowVmName

Polecenie **Get-EflowVmName** służy do wykonywania zapytań względem bieżącej nazwy hosta maszyny wirtualnej. To polecenie istnieje, aby uwzględnić fakt, że nazwa hosta systemu Windows może ulec zmianie z upływem czasu. Przyjmuje tylko typowe parametry i zwróci bieżącą nazwę hosta maszyny wirtualnej. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowVmName -full` .

## <a name="get-eflowlogs"></a>Get-EflowLogs

Polecenie **Get-EflowLogs** służy do zbierania i gromadzenia dzienników z IoT Edge dla systemu Linux w systemie Windows. Generuje ona powiązane dzienniki w postaci `.zip` folderu. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowLogs -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| vmUser | Nie więcej niż 30 znaków | Nazwa użytkownika do logowania się na maszynie wirtualnej. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

Polecenie **Get-EflowVmTpmProvisioningInfo** służy do zbierania i wyświetlania informacji o aprowizacji vTPM maszyny wirtualnej. Jeśli maszyna wirtualna została utworzona bez vTPM, polecenie zwróci informację o tym, że nie można odnaleźć informacji o aprowizacji modułu TPM. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowVmTpmProvisioningInfo -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| vmUser | Nie więcej niż 30 znaków | Nazwa użytkownika do logowania się na maszynie wirtualnej. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

Polecenie **Get-EflowVmAddr** służy do znajdowania i wyświetlania adresów IP i Mac maszyny wirtualnej. Przyjmuje tylko typowe parametry. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowVmAddr -full` .

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

Polecenie **Get-EflowVmSystemInformation** służy do zbierania i wyświetlania informacji o systemie z maszyny wirtualnej, takich jak pamięć i użycie magazynu. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowVmSystemInformation -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| vmUser | Nie więcej niż 30 znaków | Nazwa użytkownika do logowania się na maszynie wirtualnej. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

Polecenie **Get-EflowVmEdgeInformation** służy do zbierania i wyświetlania IoT Edge informacji z maszyny wirtualnej, takich jak wersja IoT Edge maszyna wirtualna jest uruchomiona. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowVmEdgeInformation -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| vmUser | Nie więcej niż 30 znaków | Nazwa użytkownika do logowania się na maszynie wirtualnej. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

Polecenie **Get-EflowVmEdgeModuleList** służy do wykonywania zapytań i wyświetlania listy modułów IoT Edge uruchomionych na maszynie wirtualnej. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowVmEdgeModuleList -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| vmUser | Nie więcej niż 30 znaków | Nazwa użytkownika do logowania się na maszynie wirtualnej. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

Polecenie **Get-EflowVmEdgeStatus** służy do wykonywania zapytań i wyświetlania stanu środowiska uruchomieniowego IoT Edge na maszynie wirtualnej. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowVmEdgeStatus -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| vmUser | Nie więcej niż 30 znaków | Nazwa użytkownika do logowania się na maszynie wirtualnej. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

Polecenie **Get-EflowVmUserName** służy do wykonywania zapytań i wyświetlania nazwy użytkownika maszyny wirtualnej, która została użyta do utworzenia maszyny wirtualnej z rejestru. Przyjmuje tylko typowe parametry. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowVmUserName -full` .

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

Polecenie **Get-EflowVmSshKey** służy do wykonywania zapytań i wyświetlania klucza SSH używanego przez maszynę wirtualną. Przyjmuje tylko typowe parametry. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Get-EflowVmSshKey -full` .

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

Do maszyny wirtualnej jest używane polecenie **SSH-EflowVm** . Jedynym kontem dozwolonym dla protokołu SSH do maszyny wirtualnej jest użytkownik, który go utworzył. Aby uzyskać dodatkowe informacje, użyj polecenia `Get-Help Ssh-EflowVm -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| vmUser | Nie więcej niż 30 znaków | Nazwa użytkownika do logowania się na maszynie wirtualnej. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać tych poleceń w następującym artykule:

* [Zainstaluj Azure IoT Edge dla systemu Linux w systemie Windows](how-to-install-iot-edge-windows.md)

* Zapoznaj się z informacjami o [skrypcie IoT Edge dla systemu Linux w programie Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) , aby uzyskać wszystkie polecenia dostępne za pomocą programu PowerShell.
