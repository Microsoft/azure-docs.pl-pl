---
title: Skrypty systemu Windows dla Azure IoT Edge | Microsoft Docs
description: Informacje referencyjne dotyczące skryptów programu IoT Edge PowerShell służących do instalowania, odinstalowywania lub aktualizowania na urządzeniach z systemem Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a082ccb62103ab5bd027bf49b9ee05bc48c63115
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979674"
---
# <a name="powershell-scripts-for-iot-edge-on-windows"></a>Skrypty programu PowerShell dla IoT Edge w systemie Windows

Zapoznaj się ze skryptami programu PowerShell, które instalują, aktualizują lub Odinstalowuje IoT Edge na urządzeniach z systemem Windows.

Polecenia opisane w tym artykule pochodzą z `IoTEdgeSecurityDaemon.ps1` pliku wydanego w każdej [IoT Edge wydania](https://github.com/Azure/azure-iotedge/releases). Najnowsza wersja skryptu jest zawsze dostępna pod adresem aka.ms/iotedge-win.

Aby uzyskać dostęp do najnowszej wersji skryptu, można uruchomić dowolne polecenie przy użyciu `Invoke-WebRequest` polecenia cmdlet. Na przykład:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

Możesz również pobrać ten skrypt lub wersję skryptu z określonej wersji, aby uruchomić polecenia. Na przykład:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

Podany skrypt jest podpisany, aby zwiększyć bezpieczeństwo. Podpis można sprawdzić, pobierając skrypt na urządzenie, a następnie uruchamiając następujące polecenie programu PowerShell:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Stan danych wyjściowych jest **prawidłowy** w przypadku zweryfikowania podpisu.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Polecenie Deploy-IoTEdge pobiera i wdraża demona zabezpieczeń IoT Edge i jej zależności. Polecenie wdrożenia akceptuje te typowe parametry między innymi. Aby uzyskać pełną listę, użyj polecenia `Get-Help Deploy-IoTEdge -full` .  

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **ContainerOs** | **System Windows** lub **Linux** | Jeśli nie określono systemu operacyjnego kontenera, system Windows jest wartością domyślną.<br><br>W przypadku kontenerów systemu Windows IoT Edge używa aparatu kontenera Moby dołączonego do instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenera przed rozpoczęciem instalacji. |
| **Proxy** | Adres URL serwera proxy | Należy uwzględnić ten parametr, jeśli urządzenie musi przejść przez serwer proxy, aby połączyć się z Internetem. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ścieżka katalogu | Jeśli ten parametr zostanie uwzględniony, Instalator sprawdzi katalog na wymienionym katalogu dla plików MSI IoT Edge cab i VC Runtime wymaganych do instalacji. Wszystkie pliki, które nie znajdują się w katalogu, są pobierane. Jeśli oba pliki znajdują się w katalogu, można zainstalować IoT Edge bez połączenia z Internetem. Można również użyć tego parametru, aby użyć określonej wersji. |
| **InvokeWebRequestParameters** | Hashtable parametrów i wartości | Podczas instalacji wykonywane jest kilka żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci Web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | brak | Ta flaga umożliwia skryptowi wdrożenia ponowne uruchomienie maszyny bez monitowania, w razie potrzeby. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge polecenie konfiguruje IoT Edge przy użyciu parametrów połączenia urządzenia i szczegółów operacyjnych. Większość informacji generowanych przez to polecenie jest następnie przechowywana w pliku iotedge\config.YAML. Polecenie inicjujące akceptuje te typowe parametry między innymi. Aby uzyskać pełną listę, użyj polecenia `Get-Help Initialize-IoTEdge -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **ManualConnectionString** | Brak | **Parametr przełącznika**. **Wartość domyślna**. Jeśli nie określono typu aprowizacji, ręczna obsługa administracyjna z parametrami połączenia jest wartością domyślną.<br><br>Deklaruje, że podajesz parametry połączenia urządzenia, aby ręcznie zainicjować obsługę administracyjną urządzenia. |
| **ManualX509** | Brak | **Parametr przełącznika**. Jeśli nie określono typu aprowizacji, ręczna obsługa administracyjna z parametrami połączenia jest wartością domyślną.<br><br>Deklaruje, że należy podać certyfikat tożsamości i klucz prywatny, aby ręcznie zainicjować obsługę administracyjną urządzenia.
| **DpsTpm** | Brak | **Parametr przełącznika**. Jeśli nie określono typu aprowizacji, ręczna obsługa administracyjna z parametrami połączenia jest wartością domyślną.<br><br>Deklaruje, że podajesz identyfikator zakresu usługi Device Provisioning Service (DPS) i Identyfikator rejestracji urządzenia, aby zapewnić obsługę administracyjną.  |
| **DpsSymmetricKey** | Brak | **Parametr przełącznika**. Jeśli nie określono typu aprowizacji, ręczna obsługa administracyjna z parametrami połączenia jest wartością domyślną.<br><br>Deklaruje, że podajesz identyfikator zakresu usługi Device Provisioning Service (DPS) i Identyfikator rejestracji urządzenia, aby umożliwić obsługę administracyjną, wraz z kluczem symetrycznym do zaświadczania. |
| **DpsX509** | Brak | **Parametr przełącznika**. Jeśli nie określono typu aprowizacji, ręczna obsługa administracyjna z parametrami połączenia jest wartością domyślną.<br><br>Deklaruje, że podajesz identyfikator zakresu usługi Device Provisioning Service (DPS) i Identyfikator rejestracji urządzenia, aby umożliwić obsługę administracyjną, wraz z certyfikatem tożsamości X. 509 i kluczem prywatnym dla zaświadczania.  |
| **DeviceConnectionString** | Parametry połączenia z urządzenia IoT Edge zarejestrowane w IoT Hub, w pojedynczym cudzysłowie | **Wymagane** do ręcznego inicjowania obsługi przy użyciu parametrów połączenia. Jeśli nie podano parametrów połączenia w parametrach skryptu, zostanie wyświetlony monit o podanie jednego z nich. |
| **IotHubHostName** | Nazwa hosta Centrum IoT, z którym łączy się urządzenie. | **Wymagane** do ręcznego inicjowania obsługi z certyfikatami X. 509. Format *{Hub Name}. Azure-Devices.NET*. |
| **Identyfikator** | Identyfikator urządzenia z zarejestrowanej tożsamości urządzenia w IoT Hub. | **Wymagane** do ręcznego inicjowania obsługi z certyfikatami X. 509. |
| **Identyfikatora zakresu** | Identyfikator zakresu z wystąpienia usługi Device Provisioning skojarzonej z IoT Hubem. | **Wymagane** do APROWIZACJI usługi DPS. Jeśli w parametrach skryptu nie podano identyfikatora zakresu, zostanie wyświetlony monit o podanie jednego z nich. |
| **Identyfikator rejestracji** | Identyfikator rejestracji generowany przez urządzenie | **Wymagane** do APROWIZACJI usługi DPS w przypadku korzystania z modułu TPM lub zaświadczania klucza symetrycznego. **Opcjonalne** w przypadku zaświadczania certyfikatu X. 509. |
| **X509IdentityCertificate** | Ścieżka identyfikatora URI do certyfikatu tożsamości urządzenia X. 509 na urządzeniu. | **Wymagane** do aprowizacji ręcznej lub DPS, jeśli używany jest zaświadczenie certyfikatu X. 509. |
| **X509IdentityPrivateKey** | Ścieżka URI klucza certyfikatu tożsamości urządzenia X. 509 na urządzeniu. | **Wymagane** do aprowizacji ręcznej lub DPS, jeśli używany jest zaświadczenie certyfikatu X. 509. |
| **SymmetricKey** | Klucz symetryczny używany do inicjowania obsługi tożsamości urządzenia IoT Edge podczas korzystania z usługi DPS | **Wymagane** do APROWIZACJI usługi DPS w przypadku korzystania z zaświadczania klucza symetrycznego. |
| **ContainerOs** | **System Windows** lub **Linux** | Jeśli nie określono systemu operacyjnego kontenera, system Windows jest wartością domyślną.<br><br>W przypadku kontenerów systemu Windows IoT Edge używa aparatu kontenera Moby dołączonego do instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenera przed rozpoczęciem instalacji. |
| **DeviceCACertificate** | Ścieżka identyfikatora URI do certyfikatu urzędu certyfikacji X. 509 na urządzeniu. | Można go również skonfigurować w `C:\ProgramData\iotedge\config.yaml` pliku. Aby uzyskać więcej informacji, zobacz [Zarządzanie certyfikatami na urządzeniu IoT Edge](how-to-manage-device-certificates.md). |
| **DeviceCAPrivateKey** | Ścieżka URI klucza prywatnego urzędu certyfikacji urządzenia X. 509 na urządzeniu. | Można go również skonfigurować w `C:\ProgramData\iotedge\config.yaml` pliku. Aby uzyskać więcej informacji, zobacz [Zarządzanie certyfikatami na urządzeniu IoT Edge](how-to-manage-device-certificates.md). |
| **InvokeWebRequestParameters** | Hashtable parametrów i wartości | Podczas instalacji wykonywane jest kilka żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci Web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | Identyfikator URI obrazu agenta IoT Edge | Domyślnie Nowa instalacja IoT Edge używa najnowszego znacznika stopniowego dla obrazu agenta IoT Edge. Użyj tego parametru, aby ustawić określony tag dla wersji obrazu lub podać własny obraz agenta. Aby uzyskać więcej informacji, zobacz [Omówienie tagów IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nazwa użytkownika** | Nazwa użytkownika rejestru kontenerów | Tego parametru należy używać tylko w przypadku ustawienia parametru-AgentImage w kontenerze w rejestrze prywatnym. Podaj nazwę użytkownika z dostępem do rejestru. |
| **Password** (Hasło) | Bezpieczny ciąg hasła | Tego parametru należy używać tylko w przypadku ustawienia parametru-AgentImage w kontenerze w rejestrze prywatnym. Podaj hasło, aby uzyskać dostęp do rejestru. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **ContainerOs** | **System Windows** lub **Linux** | Jeśli nie określono żadnego systemu operacyjnego kontenera, system Windows jest wartością domyślną. W przypadku kontenerów systemu Windows aparat kontenera zostanie uwzględniony w instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenera przed rozpoczęciem instalacji. |
| **Proxy** | Adres URL serwera proxy | Należy uwzględnić ten parametr, jeśli urządzenie musi przejść przez serwer proxy, aby połączyć się z Internetem. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hashtable parametrów i wartości | Podczas instalacji wykonywane jest kilka żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci Web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ścieżka katalogu | Jeśli ten parametr zostanie uwzględniony, Instalator sprawdzi katalog na wymienionym katalogu dla plików MSI IoT Edge cab i VC Runtime wymaganych do instalacji. Wszystkie pliki, które nie znajdują się w katalogu, są pobierane. Jeśli oba pliki znajdują się w katalogu, można zainstalować IoT Edge bez połączenia z Internetem. Można również użyć tego parametru, aby użyć określonej wersji. |
| **RestartIfNeeded** | brak | Ta flaga umożliwia skryptowi wdrożenia ponowne uruchomienie maszyny bez monitowania, w razie potrzeby. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Moc** | brak | Ta flaga wymusza dezinstalację w przypadku, gdy poprzednia próba odinstalowania zakończyła się niepowodzeniem.
| **RestartIfNeeded** | brak | Ta flaga umożliwia skryptowi dezinstalacji ponowne uruchomienie maszyny bez monitowania, w razie potrzeby. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać tych poleceń w następujących artykułach:

* [Instalowanie lub Odinstalowywanie środowiska uruchomieniowego Azure IoT Edge](how-to-install-iot-edge.md)
* [Inicjowanie obsługi administracyjnej urządzenia Azure IoT Edge z uwierzytelnianiem przy użyciu klucza symetrycznego](how-to-manual-provision-symmetric-key.md)
* [Inicjowanie obsługi administracyjnej urządzenia Azure IoT Edge przy użyciu uwierzytelniania certyfikatu X. 509](how-to-manual-provision-x509.md)
