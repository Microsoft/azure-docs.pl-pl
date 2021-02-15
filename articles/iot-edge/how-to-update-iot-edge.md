---
title: Aktualizacja IoT Edge wersji na urządzeniach — Azure IoT Edge | Microsoft Docs
description: Jak zaktualizować IoT Edge urządzeń w celu uruchamiania najnowszych wersji demona zabezpieczeń i środowiska uruchomieniowego IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/20/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0adcbf49ff2128fdbe623121838058c5ed89dce2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378030"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizowanie demona zabezpieczeń i środowiska uruchomieniowego usługi IoT Edge

Ponieważ usługa IoT Edge zwalnia nowe wersje, należy zaktualizować urządzenia IoT Edge pod kątem najnowszych funkcji i ulepszeń zabezpieczeń. Ten artykuł zawiera informacje dotyczące sposobu aktualizowania urządzeń IoT Edge, gdy jest dostępna nowa wersja.

Jeśli chcesz przejść do nowszej wersji, należy zaktualizować dwa składniki urządzenia IoT Edge. Pierwszy to demon zabezpieczeń, który działa na urządzeniu i uruchamia moduły środowiska uruchomieniowego, gdy urządzenie zostanie uruchomione. Obecnie demon zabezpieczeń można aktualizować tylko na urządzeniu. Drugi składnik to środowisko uruchomieniowe składające się z centrów IoT Edge i IoT Edge agentów. W zależności od sposobu tworzenia struktury wdrożenia środowisko uruchomieniowe można zaktualizować z urządzenia lub zdalnie.

Aby znaleźć najnowszą wersję Azure IoT Edge, zobacz [wersje Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aktualizowanie demona zabezpieczeń

Demon Security IoT Edge to składnik macierzysty, który należy zaktualizować za pomocą Menedżera pakietów na urządzeniu IoT Edge.

Sprawdź wersję demona zabezpieczeń działającą na urządzeniu za pomocą polecenia `iotedge version` .

# <a name="linux"></a>[Linux](#tab/linux)

Na urządzeniach z systemem Linux x64 Użyj apt-get lub odpowiedniego Menedżera pakietów, aby zaktualizować demona zabezpieczeń do najnowszej wersji.

Pobierz najnowszą konfigurację repozytorium od firmy Microsoft:

* **Ubuntu Server 18,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Rozciąganie systemu operacyjnego Raspberry Pi**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Skopiuj wygenerowaną listę.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Zainstaluj klucz publiczny programu Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Aktualizacja apt.

   ```bash
   sudo apt-get update
   ```

Zaznacz, aby zobaczyć, które wersje IoT Edge są dostępne.

   ```bash
   apt list -a iotedge
   ```

Jeśli chcesz zaktualizować do najnowszej wersji demona zabezpieczeń, użyj następującego polecenia, które powoduje także aktualizację **libiothsm-STD** do najnowszej wersji:

   ```bash
   sudo apt-get install iotedge
   ```

Jeśli chcesz zaktualizować do określonej wersji demona zabezpieczeń, określ wersję z listy apt dane wyjściowe. Za każdym razem, gdy **iotedge** jest aktualizowany, automatycznie próbuje zaktualizować pakiet **libiothsm-STD** do najnowszej wersji, co może spowodować konflikt zależności. Jeśli nie chcesz przejść do najnowszej wersji, pamiętaj, aby oba pakiety były przeznaczone dla tej samej wersji. Na przykład następujące polecenie instaluje określoną wersję wersji 1.0.9:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Jeśli wersja, którą chcesz zainstalować, nie jest dostępna za pośrednictwem apt-get, możesz użyć zwinięciea, aby docelowa była dowolna wersja z repozytorium [IoT Edge wydania](https://github.com/Azure/azure-iotedge/releases) . Dla każdej wersji, którą chcesz zainstalować, Znajdź odpowiednie pliki **libiothsm-STD** i **iotedge** dla urządzenia. Dla każdego pliku kliknij prawym przyciskiem myszy łącze plik i skopiuj adres łącza. Użyj adresu linku, aby zainstalować określone wersje tych składników:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

W przypadku IoT Edge dla systemu Linux w systemie Windows IoT Edge działa na maszynie wirtualnej z systemem Linux hostowanej na urządzeniu z systemem Windows. Ta maszyna wirtualna jest wstępnie zainstalowana z usługą IoT Edge i jest zarządzana za pomocą Microsoft Update, aby zapewnić aktualność składników. Obecnie nie ma dostępnych aktualizacji.

::: moniker-end

W przypadku IoT Edge dla systemu Windows IoT Edge działa bezpośrednio na urządzeniu z systemem Windows. Aby uzyskać instrukcje dotyczące aktualizacji przy użyciu skryptów programu PowerShell, zobacz temat [Instalowanie i zarządzanie Azure IoT Edge dla systemu Windows](how-to-install-iot-edge-windows-on-windows.md).

---

## <a name="update-the-runtime-containers"></a>Aktualizowanie kontenerów środowiska uruchomieniowego

Sposób aktualizowania kontenerów IoT Edge i kontenera Centrum IoT Edge zależy od tego, czy używane są Tagi walcowe (na przykład 1,0) czy konkretne Tagi (takie jak 1.0.7).

Sprawdź wersję agenta IoT Edge i IoT Edge modułów centrów dostępnych obecnie na urządzeniu przy użyciu poleceń `iotedge logs edgeAgent` lub `iotedge logs edgeHub` .

  ![Znajdź wersję kontenera w dziennikach](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Objaśnienie tagów IoT Edge

Obraz IoT Edge i centrum IoT Edge są oznaczone za pomocą IoT Edge wersji, z którą są skojarzone. Istnieją dwa różne sposoby używania tagów z obrazami środowiska uruchomieniowego:

* **Tagi walcowe** — Użyj tylko dwóch pierwszych wartości numeru wersji, aby uzyskać najnowszy obraz, który jest zgodny z tymi cyframi. Na przykład aktualizacja 1,0 jest aktualizowana za każdym razem, gdy istnieje nowe wydanie wskazujące najnowszą wersję 1.0. x. Jeśli środowisko uruchomieniowe kontenera na urządzeniu IoT Edge ponownie pobierze obraz, moduły środowiska uruchomieniowego zostaną zaktualizowane do najnowszej wersji. To podejście jest sugerowane w celach deweloperskich. Wdrożenia z Azure Portal domyślne do stopniowego znacznika.

* **Określone Tagi** — Użyj wszystkich trzech wartości numeru wersji, aby jawnie ustawić wersję obrazu. Na przykład 1.0.7 nie zmieni się po początkowej wersji. W manifeście wdrożenia można zadeklarować nowy numer wersji, gdy wszystko jest gotowe do aktualizacji. To podejście jest sugerowane w celach produkcyjnych.

### <a name="update-a-rolling-tag-image"></a>Aktualizowanie obrazu tagu walcowego

Jeśli używasz znaczników stopniowych we wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1,0**), musisz wymusić na urządzeniu środowisko uruchomieniowe kontenera, aby ściągnąć najnowszą wersję obrazu.

Usuń lokalną wersję obrazu z urządzenia IoT Edge. Na maszynach z systemem Windows Dezinstalacja demona zabezpieczeń usuwa również obrazy środowiska uruchomieniowego, więc nie trzeba ponownie wykonać tego kroku.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Aby usunąć obrazy, może być konieczne użycie `-f` flagi Force.

Usługa IoT Edge pobierze najnowsze wersje obrazów środowiska uruchomieniowego i automatycznie uruchomi je na urządzeniu.

### <a name="update-a-specific-tag-image"></a>Aktualizowanie określonego obrazu tagu

Jeśli używasz określonych tagów we wdrożeniu (na przykład mcr.microsoft.com/azureiotedge-hub:**1.0.8**), wystarczy zaktualizować tag w manifeście wdrożenia i zastosować zmiany na urządzeniu.

1. W IoT Hub w Azure Portal wybierz urządzenie IoT Edge, a następnie wybierz pozycję **Ustaw moduły**.

1. W sekcji **IoT Edge modułów** wybierz pozycję **Ustawienia środowiska uruchomieniowego**.

   ![Konfigurowanie ustawień środowiska uruchomieniowego](./media/how-to-update-iot-edge/configure-runtime.png)

1. W obszarze **Ustawienia środowiska uruchomieniowego** zaktualizuj wartość **obrazu** dla **centrum brzegowego** z odpowiednią wersją. Nie wybieraj jeszcze opcji **Zapisz** .

   ![Aktualizuj wersję obrazu centrum brzegowego](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Zwiń ustawienia **centrum Edge** lub przewiń w dół i zaktualizuj wartość **obrazu** dla **agenta usługi Edge** z tą samą odpowiednią wersją.

   ![Aktualizuj wersję agenta centrum brzegowego](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Przegląd + Utwórz**, przejrzyj wdrożenie i wybierz pozycję **Utwórz**.

## <a name="update-to-a-release-candidate-version"></a>Aktualizacja do wersji Release Candidate

Azure IoT Edge regularnie zwalnia nowe wersje usługi IoT Edge. Przed każdą stabilną wersją jest dostępna co najmniej jedna wersja Release Candidate (RC). Wersje RC obejmują wszystkie planowane funkcje wydania, ale nadal przechodzą przez testowanie i weryfikację. Jeśli chcesz przetestować nową funkcję wcześniej, możesz zainstalować wersję RC i przekazać opinię za pomocą usługi GitHub.

Wersje Release Candidate są zgodne z tą samą konwencją numerowania wydań, ale mają **-RC** Plus numer przyrostowy dołączony do końca. Kandydatów wersji można zobaczyć na tej samej liście wersji [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) , co w przypadku stabilnych wersji. Na przykład Znajdź **1.0.9-RC5** i **1.0.9-RC6**, dwa z kandydatów wydania, które zostały dostarczone przed **1.0.9**. Możesz również sprawdzić, czy wersje RC są oznaczone etykietami **wersji wstępnej** .

Agent IoT Edge i moduły centralne mają wersje RC oznaczone tą samą konwencją. Na przykład **MCR.Microsoft.com/azureiotedge-Hub:1.0.9-RC6**.

W ramach wersji zapoznawczych wersje Release Candidate nie są uwzględniane w najnowszej wersji docelowej zwykłych instalatorów. Zamiast tego należy ręcznie określić zasoby dla wersji RC, która ma zostać przetestowana. W większości przypadków Instalowanie lub aktualizowanie wersji RC jest takie samo, jak w przypadku każdej innej konkretnej wersji programu IoT Edge.

Skorzystaj z sekcji w tym artykule, aby dowiedzieć się, jak zaktualizować urządzenie IoT Edge do określonej wersji modułów demonów zabezpieczeń lub środowiska uruchomieniowego.

Jeśli instalujesz IoT Edge, a nie uaktualniasz istniejącej instalacji, wykonaj czynności opisane w sekcji [Instalacja w trybie offline lub w określonej wersji](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z najnowszymi [wersjami Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Bądź na bieżąco z najnowszymi aktualizacjami i ogłoszeniami w [blogu Internet rzeczy](https://azure.microsoft.com/blog/topics/internet-of-things/)