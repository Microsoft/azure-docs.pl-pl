---
title: Rozwiązywanie ogólnych problemów z usługą Azure Percept DK i IoT Edge
description: Uzyskaj porady dotyczące rozwiązywania problemów w przypadku niektórych typowych problemów z platformą Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 826759907bfe5ec3359bf5c9125909466372c68f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608150"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Rozwiązywanie problemów z platformą Azure Percept DK (dev Kit)

Zapoznaj się z poniższymi wskazówkami, aby poznać ogólne porady dotyczące rozwiązywania problemów z platformą Azure Percept.

## <a name="general-troubleshooting-commands"></a>Ogólne polecenia rozwiązywania problemów

Aby uruchomić te polecenia, 
1. Połącz z [Wi-Fim AP zestawu deweloperów](./quickstart-percept-dk-set-up.md)
1. [Protokół SSH do zestawu deweloperskiego](./how-to-ssh-into-percept-dk.md)
1. Wprowadź polecenia w terminalu SSH

Aby przekierować wszystkie dane wyjściowe do pliku. txt w celu dalszej analizy, należy użyć następującej składni:

```console
sudo [command] > [file name].txt
```

Zmień uprawnienia pliku txt, aby można było go skopiować:

```console
sudo chmod 666 [file name].txt
```

Po przekierowaniu danych wyjściowych do pliku txt Skopiuj plik na komputer hosta za pośrednictwem punktu połączenia usługi:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` odwołuje się do lokalizacji na komputerze hosta, do której chcesz skopiować plik txt. ```[remote username]``` jest nazwą użytkownika SSH wybraną podczas [instalacji](./quickstart-percept-dk-set-up.md). Jeśli nie skonfigurowano logowania SSH w trakcie OOBE, zdalna nazwa użytkownika to ```root``` .

Dodatkowe informacje na temat poleceń Azure IoT Edge można znaleźć w [dokumentacji dotyczącej rozwiązywania problemów z urządzeniem Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

|Kategoria:         |Dotyczące                    |Funkcyjn                  |
|------------------|----------------------------|---------------------------|
|System operacyjny                |```cat /etc/os-release```         |Sprawdź wersję obrazu Mariner |
|System operacyjny                |```cat /etc/os-subrelease```      |Sprawdź wersję obrazu pochodnego |
|System operacyjny                |```cat /etc/adu-version```        |Sprawdź wersję ADU |
|Temperatura       |```cat /sys/class/thermal/thermal_zone0/temp``` |Sprawdź temperaturę Devkit |
|Wi-Fi             |```sudo journalctl -u hostapd.service``` |Sprawdź dzienniki SoftAP|
|Wi-Fi             |```sudo journalctl -u wpa_supplicant.service``` |Sprawdź dzienniki usług Wi-Fi Services |
|Wi-Fi             |```sudo journalctl -u ztpd.service```  |Sprawdź, Wi-Fi nie ma dzienników usługi aprowizacji dotykowej |
|Wi-Fi             |```sudo journalctl -u systemd-networkd``` |Sprawdź dzienniki stosu sieci Mariner |
|Wi-Fi             |```sudo cat /etc/hostapd/hostapd-wlan1.conf``` |Sprawdź szczegóły konfiguracji punktu dostępu w sieci Wi-Fi |
|OOBE              |```sudo journalctl -u oobe -b```       |Sprawdź dzienniki OOBE |
|Telemetria         |```sudo azure-device-health-id```      |Znajdowanie unikatowych danych telemetrycznych HW_ID |
|Azure IoT Edge          |```sudo iotedge check```          |Uruchamianie konfiguracji i sprawdzanie łączności w przypadku typowych problemów |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |Sprawdź dzienniki kontenerów, takie jak moduły mowy i obsługi |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |zbieranie dzienników modułów, Azure IoT Edge dzienników programu Security Manager, dzienników aparatu kontenerów, ```iotedge check``` danych wyjściowych JSON i innych przydatnych informacji debugowania w ciągu ostatniej godziny |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |Wyświetlanie dzienników programu Azure IoT Edge Security Manager |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |Uruchom ponownie demona zabezpieczeń Azure IoT Edge |
|Azure IoT Edge          |```sudo iotedge list```           |Wyświetl listę wdrożonych modułów Azure IoT Edge |
|Inne             |```df [option] [file]```          |Wyświetl informacje na temat dostępne/całkowitego miejsca w określonych systemach plików |
|Inne             |`ip route get 1.1.1.1`        |Wyświetlanie informacji o adresie IP i interfejsie urządzenia |
|Inne             |<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |Wyświetlaj tylko adres IP urządzenia |


```journalctl```Polecenia Wi-Fi można łączyć w następujące pojedyncze polecenie:

```console
sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Polecenia rozwiązywania problemów z rozwiązaniem Docker

|Dotyczące                        |Funkcyjn                  |
|--------------------------------|---------------------------|
|```sudo docker ps``` |[pokazuje, które kontenery są uruchomione](https://docs.docker.com/engine/reference/commandline/ps/) |
|```sudo docker images``` |[pokazuje, które obrazy znajdują się na urządzeniu](https://docs.docker.com/engine/reference/commandline/images/)|
|```sudo docker rmi [image id] -f``` |[usuwa obraz z urządzenia](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```sudo docker logs -f edgeAgent``` <br> ```sudo docker logs -f [module_name]``` |[Pobiera dzienniki kontenerów określonego modułu](https://docs.docker.com/engine/reference/commandline/logs/) |
|```sudo docker image prune``` |[usuwa wszystkie obrazy zawieszonego](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```sudo watch docker ps``` <br> ```watch ifconfig [interface]``` |Sprawdź stan pobierania kontenera platformy Docker |

## <a name="usb-updating"></a>Aktualizowanie USB

|Błąd:                                    |Rozwiązanie:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX podczas flash USB za pośrednictwem UUU |Ten błąd jest wynikiem błędu połączenia USB podczas aktualizowania UUU. Jeśli kabel USB nie jest prawidłowo podłączony do portów USB na komputerze lub PE-10X, wystąpi błąd tego formularza. Spróbuj odłączyć i ponownie podłączyć oba punkty końcowe kabla USB i Jiggling kabel, aby zapewnić bezpieczne połączenie. Prawie zawsze rozwiązuje ten problem. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Microsoft Azure Percept DK — Stany LED

Istnieją trzy małe diody LED na górze obudowy tablicy nośnej. Ikona w chmurze jest drukowana obok 1, a Wi-Fi ikona jest drukowana obok 2, a ikona wykrzyknika jest drukowana obok pola LED 3. Zapoznaj się z tabelą poniżej, aby uzyskać informacje o każdym stanie diody LED.

|BRANŻ             |Stan      |Opis                      |
|----------------|-----------|---------------------------------|
|Dioda LED 1 (IoT Hub) |Włączony (pełny) |Urządzenie jest połączone z IoT Hubem. |
|Dioda LED 2 (Wi-Fi)   |Wolne migotanie |Urządzenie jest gotowe do skonfigurowania za pomocą Wi-Fi łatwe łączenie i jest ogłaszane w usłudze Configurator. |
|Dioda LED 2 (Wi-Fi)   |Szybka migotanie |Uwierzytelnianie zakończyło się pomyślnie, skojarzenie urządzenia jest w toku. |
|Dioda LED 2 (Wi-Fi)   |Włączony (pełny) |Uwierzytelnianie i kojarzenie zakończyło się pomyślnie; urządzenie jest połączone z siecią Wi-Fi. |
|DIODA LED 3           |NA         |Dioda LED nie jest używana. |


