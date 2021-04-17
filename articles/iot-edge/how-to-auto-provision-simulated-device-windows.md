---
title: Automatyczne aprowizuj urządzenia z systemem Windows za pomocą usługi DPS — Azure IoT Edge | Microsoft Docs
description: Używanie symulowanego urządzenia na maszynie z systemem Windows do testowania automatycznego aprowizowania urządzeń Azure IoT Edge przy użyciu usługi Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bd027773a5759caee19228f56ba4b3dfe8c2cf
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482011"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Tworzenie i aprowizować symulowane urządzenie IoT Edge wirtualnego modułu TPM w systemie Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge mogą być automatycznie aprowowane przy użyciu usługi [Device Provisioning,](../iot-dps/index.yml) podobnie jak urządzenia, które nie są włączone na urządzeniach brzegowych. Jeśli nie masz informacji na temat procesu automatycznego aprowizowania, przed kontynuowaniem zapoznaj się z omówieniem aprowacji. [](../iot-dps/about-iot-dps.md#provisioning-process)

Program DPS obsługuje zaświadczenia klucza symetrycznego dla urządzeń IoT Edge rejestracji indywidualnej i grupowej. W przypadku rejestracji grupowej w przypadku sprawdzenia, czy opcja "jest urządzeniem IoT Edge" ma wartość true w przypadku zaświadczenia klucza symetrycznego, wszystkie urządzenia zarejestrowane w tej grupie rejestracji zostaną oznaczone jako IoT Edge rejestracji.

W tym artykule pokazano, jak przetestować automatyczną aprowizowanie na symulowanym IoT Edge, aby wykonać następujące czynności:

* Utwórz wystąpienie usługi IoT Hub Device Provisioning Service (DPS).
* Utwórz symulowane urządzenie na maszynie z systemem Windows przy użyciu symulowanego Trusted Platform Module (TPM) w celu zabezpieczenia sprzętu.
* Utwórz rejestrację indywidualną dla urządzenia.
* Zainstaluj środowisko IoT Edge uruchomieniowe i połącz urządzenie z IoT Hub.

> [!TIP]
> W tym artykule opisano testowanie automatycznego aprowizowania przy użyciu zaświadczenia modułu TPM na urządzeniach wirtualnych, ale większość z nich ma zastosowanie również w przypadku korzystania z fizycznego sprzętu TPM.

## <a name="prerequisites"></a>Wymagania wstępne

* Maszyna dewelopera z systemem Windows. W tym artykule są używane Windows 10.
* Aktywny IoT Hub.

> [!NOTE]
> Moduł TPM 2.0 jest wymagany w przypadku korzystania z zaświadczenia modułu TPM z usługą DPS i może służyć tylko do tworzenia rejestracji indywidualnych, a nie grupowych.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie usługi IoT Hub Device Provisioning Service platformie Azure i połącz je z centrum IoT. Możesz wykonać instrukcje z [tematu Konfigurowanie IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)

Po uruchomieniu usługi Device Provisioning Service skopiuj wartość **Zakres** identyfikatorów ze strony przeglądu. Ta wartość jest wartością używaną podczas konfigurowania IoT Edge uruchomieniowego.

> [!TIP]
> Jeśli używasz fizycznego urządzenia TPM, musisz określić klucz poręczenia **,** który jest unikatowy dla każdego mikroukładu modułu TPM i jest uzyskiwany od producenta mikroukładu modułu TPM skojarzonego z nim. Możesz uzyskać unikatowy **identyfikator** rejestracji dla urządzenia TPM, na przykład tworząc skrót SHA-256 klucza poręczenia.
>
> Postępuj zgodnie z instrukcjami w artykule Jak zarządzać rejestracjami urządzeń w witrynie [Azure Portal,](../iot-dps/how-to-manage-enrollments.md) aby utworzyć rejestrację w usłudze DPS, a następnie przejdź do sekcji Instalowanie środowiska [uruchomieniowego](#install-the-iot-edge-runtime) usługi IoT Edge w tym artykule, aby kontynuować.

## <a name="simulate-a-tpm-device"></a>Symulowanie urządzenia TPM

Utwórz symulowane urządzenie TPM na maszynie dewelopera z systemem Windows. Pobierz identyfikator **rejestracji i** klucz **poręczenia** dla swojego urządzenia, a następnie użyj ich do utworzenia wpisu rejestracji indywidualnej w uściślicie DPS.

Podczas tworzenia rejestracji w uciekierach dps masz możliwość zadeklarowania początkowego stanu bliźniaczej **reprezentacji urządzenia.** W bliźniaczej reprezentacji urządzenia można ustawić tagi, aby grupować urządzenia według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Te tagi są używane do tworzenia [wdrożeń automatycznych.](how-to-deploy-at-scale.md)

Wybierz język zestawu SDK, którego chcesz użyć do utworzenia symulowanego urządzenia, i postępuj zgodnie z instrukcjami do momentu utworzenia rejestracji indywidualnej.

Podczas tworzenia rejestracji indywidualnej wybierz opcję **Prawda,** aby zadeklarować, że symulowane urządzenie TPM na maszynie dewelopera z systemem Windows **jest urządzeniem IoT Edge systemem .**

> [!TIP]
> W interfejsie wiersza polecenia [](/cli/azure/iot/dps/enrollment) platformy Azure [](/cli/azure/iot/dps/enrollment-group) możesz utworzyć rejestrację lub grupę rejestracji i użyć **flagi** z obsługą krawędzi, aby określić, że urządzenie lub grupa urządzeń jest urządzeniem IoT Edge brzegowym.

Przewodniki dotyczące symulowanego urządzenia i rejestracji indywidualnej:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po utworzeniu rejestracji indywidualnej zapisz wartość **identyfikatora rejestracji**. Ta wartość jest wartością używaną podczas konfigurowania IoT Edge uruchomieniowego.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska uruchomieniowego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i umożliwiają wdrażanie dodatkowych kontenerów na urządzeniu, dzięki czemu można uruchamiać kod na brzegu sieci. Zainstaluj środowisko IoT Edge uruchomieniowe na urządzeniu z uruchomionym symulowanym modułem TPM.

Wykonaj kroki opisane w [artykule Instalowanie Azure IoT Edge uruchomieniowego,](how-to-install-iot-edge.md)a następnie wróć do tego artykułu, aby aprowizować urządzenie.

> [!TIP]
> Nie otwieraj okna z uruchomionym symulatorem modułu TPM podczas instalacji i testowania.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurowanie urządzenia z informacjami o aprowiwizowania

Po zainstalowaniu środowiska uruchomieniowego na urządzeniu skonfiguruj je przy użyciu informacji, których używa do nawiązywania połączenia z usługą Device Provisioning i IoT Hub.

1. Poznaj zakres identyfikatorów **dps** i identyfikator **rejestracji urządzenia,** które zostały zebrane w poprzednich sekcjach.

1. Otwórz okno programu PowerShell w trybie administratora. Pamiętaj, aby użyć sesji AMD64 programu PowerShell podczas instalowania programu IoT Edge, a nie programu PowerShell (x86).

1. Polecenie **Deploy-IoTEdge** sprawdza, czy maszyna z systemem Windows jest w obsługiwanej wersji, włącza funkcję kontenerów, a następnie pobiera środowisko uruchomieniowe moby i IoT Edge uruchomieniowe. Domyślnie polecenie używa kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. W tym momencie urządzenia IoT Core mogą zostać automatycznie uruchomione ponownie. Windows 10 lub Windows Server może zostać wyświetlony monit o ponowne uruchomienie. Jeśli tak, uruchom ponownie urządzenie teraz. Gdy urządzenie będzie gotowe, ponownie uruchom program PowerShell jako administrator.

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko IoT Edge uruchomieniowe na maszynie. Domyślnie polecenie to aprowizowanie ręczne przy użyciu kontenerów systemu Windows. Użyj flagi , aby zamiast aprowizowania ręcznego używać `-Dps` usługi Device Provisioning Service.

   Zastąp wartości symboli zastępczych `{scope_id}` dla i `{registration_id}` danymi zebranymi wcześniej.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Weryfikowanie pomyślnej instalacji

Jeśli środowisko uruchomieniowe zostało uruchomione pomyślnie, możesz przejść do swojego IoT Hub i rozpocząć wdrażanie IoT Edge modułów na urządzeniu. Użyj następujących poleceń na urządzeniu, aby sprawdzić, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i uruchomione.  

Sprawdź stan usługi IoT Edge.

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usług z ostatnich 5 minut.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista uruchomionych modułów.

```powershell
iotedge list
```

## <a name="next-steps"></a>Następne kroki

Proces rejestracji w usłudze Device Provisioning Service umożliwia ustawienie identyfikatora urządzenia i tagów bliźniaczej reprezentacji urządzenia w tym samym czasie, gdy aprowizuje się nowe urządzenie. Możesz użyć tych wartości, aby kierować je do poszczególnych urządzeń lub grup urządzeń przy użyciu automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na dużą](how-to-deploy-at-scale.md) skalę przy użyciu interfejsu Azure Portal lub [interfejsu wiersza polecenia platformy Azure](how-to-deploy-cli-at-scale.md)
