---
title: Przewodnik Szybki Start dotyczący konfigurowania i wdrażania Azure Stack Edge GPU | Microsoft Docs
description: Wprowadzenie do wdrażania Azure Stack Edge procesora GPU po odebraniu urządzenia.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: quickstart
ms.date: 04/07/2021
ms.author: alkohli
ms.openlocfilehash: 43477f11effaafb0bcd6158be6843c79d19092ac
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210814"
---
# <a name="quickstart-get-started-with-azure-stack-edge-pro-with-gpu"></a>Szybki Start: Rozpoczynanie pracy z usługą Azure Stack EDGE Pro z procesorem GPU 

Ten przewodnik Szybki Start zawiera szczegółowe informacje o wymaganiach wstępnych i krokach wymaganych do wdrożenia urządzenia z procesorem GPU w programie Azure Stack Edge. Czynności opisane w przewodniku Szybki start należy wykonać w witrynie Azure Portal oraz w lokalnym internetowym interfejsie użytkownika na urządzeniu. 

Całkowita procedura powinna trwać około 1,5 godzin. Aby uzyskać szczegółowe instrukcje krok po kroku, przejdź do [samouczka: przygotowanie do wdrożenia Azure Stack Edge — procesor GPU](azure-stack-edge-gpu-deploy-prep.md#deployment-configuration-checklist). 


## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem programu upewnij się, że są spełnione następujące wymagania wstępne:

1. Urządzenie GPU w Azure Stack Edge jest dostarczane do Twojej lokacji, [rozpakowane](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) i [zamontowane w stojaku](azure-stack-edge-gpu-deploy-install.md#rack-the-device). 
1. Skonfiguruj sieć w taki sposób, aby urządzenie korzystało z [wymienionych wzorców i portów adresów URL](azure-stack-edge-gpu-system-requirements.md#networking-port-requirements). 
1. Masz uprawnienia właściciela lub współautora do subskrypcji platformy Azure.
1. W Azure Portal przejdź do **strony głównej > subskrypcji > dostawców zasobów > subskrypcji**. Wyszukaj `Microsoft.DataBoxEdge` i zarejestruj dostawcę zasobów. Powtórz tę procedurę, `Microsoft.Devices` Jeśli utworzysz zasób IoT Hub w celu wdrożenia obciążeń obliczeniowych.
1. Upewnij się, że masz co najmniej 2 bezpłatne, statyczne, ciągłe adresy IP dla węzłów Kubernetes i co najwyżej 1 statyczny adres IP dla usługi IoT Edge. Dla każdego modułu lub usługi zewnętrznej należy wdrożyć 1 dodatkowy adres IP.
1. Zapoznaj się z [listą kontrolną wdrożenia](azure-stack-edge-gpu-deploy-checklist.md) , aby uzyskać wszystko, czego potrzebujesz do konfigurowania urządzeń. 


## <a name="deployment-steps"></a>Kroki wdrażania

1. **Zainstaluj**: Podłącz port 1 do komputera klienckiego za pośrednictwem kabla skrzyżowanego lub karty Ethernet USB. Połącz co najmniej jeden inny port urządzenia dla danych, najlepiej 25 GbE, (z portu 3 do portu 6) za pośrednictwem kabli SFP + miedziowych lub użyj portu 2 z kablem protokołu RJ45. Połącz dostarczone przewody zasilające z jednostkami zasilacza i oddziel możliwości, aby wypróbować dystrybucję. Naciśnij przycisk energia na panelu przednim, aby włączyć urządzenie.  

    Zobacz [macierz zgodności z seriami Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) i [Mellanox Dual port 25g ConnectX-4 Channel karta sieciowa zgodne produkty](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) , aby uzyskać zgodne kable sieciowe i przełączniki.

    Poniżej znajduje się minimalna konfiguracja okablowania, która jest wymagana do wdrożenia urządzenia:  ![ płaszczyzna tylna urządzenia z kablem](./media/azure-stack-edge-gpu-quickstart/backplane-min-cabling-1.png)

2. **Połącz**: Skonfiguruj ustawienia protokołu IPv4 na karcie Ethernet na komputerze przy użyciu statycznego adresu IP **192.168.100.5** i podsieci **255.255.255.0**. Otwórz przeglądarkę i Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia pod adresem https://192.168.100.10 . Może to potrwać kilka minut. Przejdź do strony sieci Web, gdy zostanie wyświetlone ostrzeżenie dotyczące certyfikatu zabezpieczeń.

3. **Logowanie**: Zaloguj się do urządzenia przy użyciu domyślnego hasła *Password1*. Zmień hasło administratora urządzenia. Hasło musi zawierać od 8 do 16 znaków i 3 z wielkich, małych liter, cyfr i znaków specjalnych.

4. **Skonfiguruj sieć**: zaakceptuj domyślną konfigurację DHCP dla połączonego portu danych, jeśli w sieci istnieje serwer DHCP. W przeciwnym razie podaj adres IP, serwer DNS i bramę domyślną. Zobacz więcej informacji na temat [ustawień sieciowych](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-network).

5. **Konfigurowanie sieci obliczeniowej**: Utwórz przełącznik wirtualny, włączając port na urządzeniu. Wprowadź 2 bezpłatne, ciągłe statyczne adresy IP dla węzłów Kubernetes w tej samej sieci, w której został utworzony przełącznik. Podaj co najmniej 1 statyczny adres IP dla usługi IoT Edge Hub, aby uzyskać dostęp do modułów obliczeniowych i 1 statyczny adres IP dla każdej dodatkowej usługi lub kontenera, do którego chcesz uzyskać dostęp poza klastrem Kubernetes. 

    Kubernetes jest wymagany do wdrożenia wszystkich obciążeń kontenerowych. Zobacz więcej informacji na temat [ustawień sieci obliczeniowej](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

6. **Konfigurowanie serwera proxy sieci Web**: Jeśli używasz serwera proxy sieci Web w środowisku, wprowadź adres IP serwera proxy sieci Web w `http://<web-proxy-server-FQDN>:<port-id>` . Ustaw opcję Uwierzytelnianie na **Brak**. Zobacz więcej informacji na temat [ustawień serwera proxy sieci Web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-web-proxy).

7. **Konfigurowanie urządzenia**: Wprowadź nazwę urządzenia i domenę DNS lub zaakceptuj ustawienia domyślne. 

8. **Konfiguruj serwer aktualizacji**: Zaakceptuj domyślny serwer Microsoft Update lub określ serwer Windows Server Update Services (WSUS) i ścieżkę do serwera. 

9. **Konfigurowanie ustawień czasu**: zaakceptuj domyślne ustawienia czasu lub ustaw strefę czasową, podstawowy serwer NTP oraz pomocniczy serwer NTP w sieci lokalnej lub jako serwery publiczne.

10. **Konfigurowanie certyfikatów**: w przypadku zmiany nazwy urządzenia i/lub domeny DNS należy wygenerować certyfikaty lub dodać certyfikaty w celu aktywowania urządzenia. 

    - Aby przetestować obciążenia inne niż produkcyjne, użyj [opcji Generuj certyfikaty](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates). 
    - W przypadku przenoszenia własnych certyfikatów, w tym łańcuchów podpisywania, należy [dodać certyfikaty](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) w odpowiednim formacie. Upewnij się, że najpierw przekaż łańcuch podpisywania. Zobacz [Tworzenie certyfikatów](azure-stack-edge-gpu-create-certificates-tool.md) i [przekazywanie certyfikatów za pośrednictwem lokalnego interfejsu użytkownika](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).

11. **Aktywuj**: Aby uzyskać klucz aktywacji 

    1. W Azure Portal przejdź do **zasobu Azure Stack Edge > przegląd > Konfiguracja urządzenia > aktywuj > Wygeneruj klucz**. Skopiuj klucz. 
    1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji wprowadzenie **> Aktywuj** i Podaj klucz aktywacji. Po zastosowaniu klucza urządzenie potrwa kilka minut. Pobierz `<device-serial-number>` plik JSON po wyświetleniu monitu o bezpieczne przechowywanie kluczy urządzeń wymaganych do przyszłego odzyskiwania. 

12. **Konfigurowanie obliczeń**: w Azure Portal przejdź do **omówienia > urządzenie**. Sprawdź, czy urządzenie jest w **trybie online**. W okienku po lewej stronie przejdź do pozycji **obliczenia graniczne > wprowadzenie > Konfigurowanie obliczeń brzegowych > Oblicz**. Podaj istniejącą lub nową usługę IoT Hub i poczekaj około 20 minut na skonfigurowanie obliczeń. Zobacz więcej informacji na temat [samouczka: Konfigurowanie obliczeń na urządzeniu Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-compute.md)

Możesz przystąpić do wdrażania obciążeń obliczeniowych na urządzeniu [za pośrednictwem IoT Edge](azure-stack-edge-gpu-deploy-sample-module-marketplace.md), [za pośrednictwem `kubectl` ](azure-stack-edge-gpu-create-kubernetes-cluster.md) lub [za pośrednictwem usługi Azure Arc Kubernetes](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Jeśli podczas instalacji wystąpią jakiekolwiek problemy, przejdź do obszaru [Rozwiązywanie]()problemów z urządzeniami, [zamawianie](azure-stack-edge-gpu-troubleshoot.md)problemów, [problemy z certyfikatami](azure-stack-edge-gpu-certificate-troubleshooting.md)lub problemy z Kubernetes. 

## <a name="next-steps"></a>Następne kroki

[Zainstaluj Azure Stack Edge — procesor GPU](./azure-stack-edge-gpu-deploy-install.md)



