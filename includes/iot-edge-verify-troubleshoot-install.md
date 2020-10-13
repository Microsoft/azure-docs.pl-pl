---
title: Po zakończeniu instalacji i aprowizacji Sprawdź powodzenie i rozwiązywanie problemów
description: plik dołączany
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979722"
---
## <a name="verify-successful-setup"></a>Sprawdź, czy instalacja powiodła się

Sprawdź stan usługi IoT Edge. Powinien być wymieniony jako uruchomiony.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

Sprawdzanie dzienników usług.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

Po zakończeniu instalowania środowiska uruchomieniowego IoT Edge może zostać wyświetlona lista błędów od czasu między uruchomieniem metody **Deploy-IoTEdge** i **Initialize-IoTEdge**. Te błędy są oczekiwane, ponieważ próba uruchomienia usługi przed jej skonfigurowaniem.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

Uruchom [Narzędzie do rozwiązywania problemów](../articles/iot-edge/troubleshoot.md#run-the-check-command) , aby sprawdzić najbardziej typowe błędy konfiguracji i sieci.

```powershell
iotedge check
```

Do momentu wdrożenia pierwszego modułu do IoT Edge na urządzeniu moduł **$edgeHub** system nie zostanie wdrożony na urządzeniu. W związku z tym, automatyczne sprawdzanie zwróci błąd `Edge Hub can bind to ports on host` sprawdzania łączności. Ten błąd można zignorować, chyba że występuje po wdrożeniu modułu na urządzeniu.

Na koniec Wyświetl listę uruchomionych modułów:

```powershell
iotedge list
```

Po nowej instalacji jedynym modułem, który powinien zostać uruchomiony, jest **edgeAgent**.

## <a name="tips-and-troubleshooting"></a>Porady i rozwiązywanie problemów

W przypadku urządzeń z ograniczonym zasobem zdecydowanie zaleca się ustawienie zmiennej środowiskowej *OptimizeForPerformance* na *wartość false* zgodnie z instrukcjami w [przewodniku rozwiązywania problemów](../articles/iot-edge/troubleshoot.md).

Jeśli urządzenie nie może nawiązać połączenia z usługą IoT Hub, a sieć ma serwer proxy, wykonaj kroki opisane w [sekcji Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](../articles/iot-edge/how-to-configure-proxy-support.md).

# <a name="linux"></a>[Linux](#tab/linux)

Na urządzeniach z systemem Linux wymagane są podwyższone uprawnienia do uruchamiania `iotedge` poleceń. Po zainstalowaniu środowiska uruchomieniowego Wyloguj się z komputera i zaloguj się ponownie, aby automatycznie zaktualizować swoje uprawnienia. Do tego czasu należy używać `sudo` polecenia do uruchamiania poleceń z podniesionymi uprawnieniami.

# <a name="windows"></a>[Windows](#tab/windows)

W przypadku urządzeń z systemem Windows, na których działa kontenery systemu Windows, aparat kontenera Moby został zainstalowany w ramach IoT Edge. Aparat Moby został zaprojektowany do pracy równolegle z pulpitem Docker. Możesz użyć `docker` poleceń, jeśli chcesz bezpośrednio korzystać z kontenerów na urządzeniu. Należy jednak jawnie wskazać aparat Moby w przypadku, gdy na urządzeniu jest zainstalowany również program Docker Desktop.

Na przykład aby wyświetlić listę wszystkich obrazów platformy Docker, użyj następującego polecenia:

```powershell
docker images
```

Aby wyświetlić listę wszystkich obrazów Moby, należy zmodyfikować to samo polecenie ze wskaźnikiem do aparatu Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Identyfikator URI aparatu jest wyświetlany w danych wyjściowych skryptu instalacji lub można go znaleźć w sekcji Ustawienia środowiska uruchomieniowego kontenera dla pliku config. YAML.

![Identyfikator URI moby_runtime w pliku config. YAML](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
