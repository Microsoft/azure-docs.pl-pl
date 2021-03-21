---
title: Lista kontrolna preinstalacji w celu wdrożenia urządzenia z urządzeniem mini R Azure Stack Edge
description: W tym artykule opisano informacje, które można zebrać przed wdrożeniem urządzenia z systemem Azure Stack Edge mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/24/2021
ms.author: alkohli
ms.openlocfilehash: c6e0e6c18803d24f7aa41350d64de77f92c5c0c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730596"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-mini-r-device"></a>Lista kontrolna wdrożenia dla urządzenia z urządzeniem mini R Azure Stack Edge  

W tym artykule opisano informacje, które można zbierać przed rzeczywistym wdrożeniem urządzenia z systemem Azure Stack Edge mini R. 

Skorzystaj z poniższej listy kontrolnej, aby upewnić się, że masz te informacje po złożeniu zamówienia dla urządzenia Azure Stack Edge mini R i przed odebraniem urządzenia. 

## <a name="deployment-checklist"></a>Lista kontrolna wdrożenia 

| Etap                             | Parametr                                                                                                                                                                                                                           | Szczegóły                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Zarządzanie urządzeniami               | <li>Subskrypcja platformy Azure</li><li>Zarejestrowani dostawcy zasobów</li><li>Konto usługi Azure Storage</li>|<li>Włączone dla Azure Stack Edge mini R/Data Box Gateway, właściciela lub dostępu współautora.</li><li>W Azure Portal przejdź do **strony głównej > subskrypcji > dostawców zasobów > subskrypcji**. Wyszukaj `Microsoft.DataBoxEdge` i zarejestruj. Powtórz tę czynność w `Microsoft.Devices` przypadku wdrażania obciążeń IoT.</li><li>Wymagane poświadczenia dostępu</li> |
| Instalacja urządzenia               | Kable zasilające pakietu. <br>Dla nas kabel SVE 18/3 sklasyfikowany dla 125 V i 15 amper z NEMA 5-15P do C13 (dane wejściowe do wyjścia) jest dostarczany. | Aby uzyskać więcej informacji, zobacz listę [obsługiwanych przewodów zasilających według kraju](azure-stack-edge-technical-specifications-power-cords-regional.md)  |
|                                   | <li>Co najmniej 1 X 1 GbE kabla sieciowego RJ-45 dla portu 1  </li><li> Co najmniej 1 X 25-GbE SFP + kabel miedziowy dla portu 3, port 4, port 5 lub port 6</li>| Klient musi kupić te kable.<br>Aby zapoznać się z pełną listą obsługiwanych kabli sieciowych, przełączników i urządzeń nadawczych dla kart sieciowych urządzeń, zobacz [macierz zgodności serii Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) i [karta Mellanox Dual port 25g ConnectX-4](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Połączenie urządzenia pierwszego czasu      | <li>Laptop, którego ustawienia IPv4 można zmienić. Ten laptop nawiązuje połączenie z portem 1 za pośrednictwem przełącznika lub adaptera USB do sieci Ethernet.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Logowanie do urządzenia                      | Hasło administratora urządzenia, od 8 do 16 znaków, w tym trzy z następujących typów znaków: wielkie litery, małe litery, cyfry i znaki specjalne.                                            | Domyślne hasło to *Password1*, które wygasa podczas pierwszego logowania.                                                     |
| Ustawienia sieciowe                  | Urządzenie zawiera 2 x 1 GbE, 4 x 25-GbE portów sieciowych. <li>Port 1 służy do konfigurowania tylko ustawień zarządzania. Można połączyć i skonfigurować co najmniej jeden port danych. </li><li> Co najmniej jeden interfejs sieciowy danych między portem 2-port 6 musi być połączony z Internetem (z łącznością z platformą Azure).</li><li> Obsługiwana jest konfiguracja protokołu DHCP i statycznego adresu IPv4. | Statyczna konfiguracja IPv4 wymaga adresu IP, serwera DNS i bramy domyślnej.   |
| Ustawienia sieci obliczeniowej     | <li>Wymagane są 2 bezpłatne, statyczne, ciągłe adresy IP dla węzłów Kubernetes oraz 1 statyczny adres IP dla usługi IoT Edge.</li><li>Wymagany jest 1 dodatkowy adres IP dla każdej dodatkowej usługi lub modułu, który zostanie wdrożony.</li>| Obsługiwana jest tylko statyczna konfiguracja IPv4.|
| Obowiązkowe Ustawienia serwera proxy sieci Web     | <li>Adres IP/nazwa FQDN serwera proxy sieci Web, port </li><li>Nazwa użytkownika serwera proxy sieci Web, hasło</li> |  |
| Ustawienia zapory i portu        | Jeśli jest używana Zapora, upewnij się, że na [liście adresów URL są dozwolone wzorce i porty](azure-stack-edge-system-requirements.md#networking-port-requirements) adresów IP urządzeń. |  |
| Rekomendowane Ustawienia czasu       | Konfigurowanie strefy czasowej, podstawowego serwera NTP, pomocniczego serwera NTP. | Skonfiguruj podstawowy i pomocniczy serwer NTP w sieci lokalnej.<br>Jeśli serwer lokalny jest niedostępny, można skonfigurować publiczne serwery NTP.                                                    |
| Obowiązkowe Aktualizowanie ustawień serwera | <li>Wymagaj adresu IP serwera aktualizacji w sieci lokalnej, ścieżki do serwera WSUS. </li> | Domyślnie jest używany publiczny serwer Windows Update.|
| Ustawienia urządzenia | <li>W pełni kwalifikowana nazwa domeny urządzenia (FQDN) </li><li>Domena DNS</li> | |
| Obowiązkowe Przystawki  | Aby przetestować obciążenia inne niż produkcyjne, użyj [opcji Generuj certyfikaty](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates) <br><br> W przypadku przenoszenia własnych certyfikatów, w tym łańcuchów podpisywania, należy [dodać certyfikaty](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) w odpowiednim formacie.| Skonfiguruj certyfikaty tylko w przypadku zmiany nazwy urządzenia i/lub domeny DNS. |
| Uaktywnienie  | Wymagaj klucza aktywacji z zasobu Azure Stack Edge.    | Po wygenerowaniu klucz wygasa za 3 dni. |

<!--
| (Optional) MAC Address            | If MAC address needs to be approved, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>Następne kroki

Przygotuj się do wdrożenia [urządzenia z usługą Azure Stack Edge mini R](azure-stack-edge-gpu-deploy-prep.md).
