---
title: Lista kontrolna preinstalacji umożliwiająca wdrożenie Azure Stack urządzeniu brzegowym | Microsoft Docs
description: W tym artykule opisano informacje, które można zebrać przed wdrożeniem urządzenia Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: e1233e17dfe2a56e6ea995190b243ed9c64b8945
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181361"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-device"></a>Lista kontrolna wdrożenia urządzenia brzegowego Azure Stack  

W tym artykule opisano informacje, które można zbierać przed rzeczywistym wdrożeniem urządzenia Azure Stack Edge. 

Użyj poniższej listy kontrolnej, aby upewnić się, że masz te informacje po złożeniu zamówienia dla urządzenia brzegowego Azure Stack i przed odebraniem urządzenia. 

## <a name="deployment-checklist"></a>Lista kontrolna wdrożenia 

| Etap                             | Parametr                                                                                                                                                                                                                           | Szczegóły                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Zarządzanie urządzeniami               | <li>Subskrypcja Microsoft Azure</li><li>Interfejs API programu Graph usługi Azure Active Directory</li><li>Konto Microsoft Azure Storage</li>|<li>Włączone dla Azure Stack Edge/Data Box Gateway, uprawnienia współautora</li><li>Zapewnianie dostępu administratora lub użytkownika</li><li>Wymagane poświadczenia dostępu</li> |
| Instalacja urządzenia               | Kable zasilające pakietu. <br>Dla nas kabel SVE 18/3 sklasyfikowany dla 125 V i 15 amper z NEMA 5-15P do C13 (dane wejściowe do wyjścia) jest dostarczany.                                                                                                                                                                                                          | Dostarczone z urządzeniem.<br>Aby uzyskać więcej informacji, zobacz listę [obsługiwanych przewodów zasilających według kraju](azure-stack-edge-technical-specifications-power-cords-regional.md)                                                                                        |
|                                   | <li>Co najmniej 1 X 1 GbE kabla sieciowego RJ-45 dla portu 1  </li><li> Co najmniej 1 X 25-GbE SFP + kabel miedziowy dla portu 3, port 4, port 5 lub port 6</li>| Klient musi kupić te kable.<br>Aby zapoznać się z pełną listą obsługiwanych kabli sieciowych, przełączników i urządzeń nadawczych dla kart sieciowych urządzeń, zobacz [macierz zgodności serii Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) i [karta Mellanox Dual port 25g ConnectX-4](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Połączenie urządzenia po raz pierwszy      | Port 1 ma stały adres IP (192.168.100.10/24) dla połączenia początkowego. <li>Wymagaj komputera przenośnego do bezpośredniego połączenia z portem 1 z adresem IP w sieci 192.168.100.0/24.</li><li> Użyj lokalnego interfejsu użytkownika urządzenia pod adresem: `https://192.168.100.10` w celu dalszej konfiguracji.</li><li> Po zakończeniu wstępnej instalacji należy użyć co najmniej 1 GbE przełącznika. Lokalny interfejs użytkownika sieci Web nie będzie dostępny, jeśli przełącznik podłączony nie ma co najmniej 1 GbE.</li>|                                                                                                                   |
| Logowanie do urządzenia                      | Hasło administratora urządzenia musi mieć od 8 do 16 znaków. <br>Musi zawierać trzy z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne.                                            | Domyślne hasło to *Password1* , które wygasa podczas pierwszego logowania.                                                     |
| Ustawienia sieciowe                  | Urządzenie zawiera 2 x 1 GbE, 4 x 25 portów sieciowych. <li>Port 1 służy do konfigurowania tylko ustawień zarządzania. Można połączyć i skonfigurować co najmniej jeden port danych. </li><li> Co najmniej jeden interfejs sieciowy danych między portem 2-port 6 musi być połączony z Internetem (z łącznością z platformą Azure).</li><li> Ustawienia protokołu IP obsługują konfigurację protokołu DHCP/statycznego adresu IPv4. | Statyczna konfiguracja IPv4 wymaga adresu IP, serwera DNS i bramy domyślnej.                                                                                                                  |
| Ustawienia sieci obliczeniowej     | <li>Wymagaj 2 statycznych publicznych adresów IP dla węzłów Kubernetes oraz co najmniej 1 statyczny adres IP dla usługi Azure Stack Edge Hub, aby uzyskać dostęp do modułów obliczeniowych.</li><li>Wymagany jest jeden adres IP dla każdej dodatkowej usługi lub kontenera, do którego należy uzyskać dostęp zewnętrznie spoza klastra Kubernetes.</li>                                                                                                                       | Obsługiwana jest tylko statyczna konfiguracja IPv4.                                                                      |
| Obowiązkowe Ustawienia serwera proxy sieci Web     | <li>Adres IP/nazwa FQDN serwera proxy sieci Web, port </li><li>Nazwa użytkownika serwera proxy sieci Web, hasło</li>                                                                                                                                                                                                    | Konfiguracja obliczeń nie jest obecnie obsługiwana.                                                                     |
| Ustawienia zapory i portu        | Użyj [wymienionych poniżej wzorców adresów URL i portów](azure-stack-edge-system-requirements.md#networking-port-requirements) , aby zezwolić na adresy IP urządzeń.                                                                                                                                                  |                                                                                                                   |
| Obowiązkowe Adres MAC            | Jeśli adres MAC musi być listy dozwolonych, należy uzyskać adres połączonego portu z lokalnego interfejsu użytkownika urządzenia. |                                                                                                                   |
| Obowiązkowe Port przełącznika sieci    | Urządzenie obsługuje maszyny wirtualne funkcji Hyper-V na potrzeby obliczeń. Niektóre konfiguracje portów przełącznika sieciowego domyślnie nie obsługują tych ustawień.                                                                                                        |                                                                                                                   |
| Rekomendowane Ustawienia czasu       | Konfigurowanie strefy czasowej, podstawowego serwera NTP, pomocniczego serwera NTP.                                                                                                                                                                    | Skonfiguruj podstawowy i pomocniczy serwer NTP w sieci lokalnej.<br>Jeśli serwer lokalny jest niedostępny, można skonfigurować publiczne serwery NTP.                                                    |
| Obowiązkowe Aktualizowanie ustawień serwera | <li>Wymagaj adresu IP serwera aktualizacji w sieci lokalnej, ścieżki do serwera WSUS. </li> | Domyślnie jest używany publiczny serwer Windows Update.|
| Ustawienia urządzenia                   | <li>Nazwa urządzenia zarejestrowana w organizacji DNS </li><li>Domena DNS</li> |                                                                                                                   |
| Obowiązkowe Przystawki                      | Użyj certyfikatów wygenerowanych przez urządzenie <br><br> W przypadku przenoszenia własnych certyfikatów potrzebne są: <li>Zaufany główny certyfikat podpisywania w formacie DER z *. cer* </li><li>Certyfikaty punktów końcowych w formacie *PFX*</li>|Certyfikaty punktów końcowych obejmują Azure Resource Manager, usługi BLOB Storage, lokalny interfejs użytkownika sieci Web.                                                                                                                   |
| Aktywacja                        | Wymagaj klucza aktywacji z zasobu Azure Stack Edge/Data Box Gateway.                                                                                                                                                       | Po wygenerowaniu klucz wygasa za 3 dni.                                                                        |


## <a name="next-steps"></a>Następne kroki

Przygotuj się do wdrożenia [urządzenia Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).



