---
title: Znane problemy z usługą Azure Percept
description: Dowiedz się więcej o znanych problemach i obejść usługi Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: 49ac497505930d82a3ab8e90fb3f386cc1741dc7
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605063"
---
# <a name="known-issues"></a>Znane problemy

Jeśli wystąpi którykolwiek z tych problemów, otwarcie usterki nie jest konieczne. Jeśli masz problemy z jakimkolwiek obejściem, Otwórz problem.

|Warstwowy|Opis problemu|Obejście|
|-------|---------|---------|
| Środowisko dołączania | Nie można ukończyć pracy podczas dołączania, jeśli nie skonfigurowano Wi-Fi urządzenia (logowanie na platformie Azure nie powiedzie się). | 1. [SSH do platformy Azure PERCEPT DK](./how-to-ssh-into-percept-dk.md). <br> 2. zidentyfikuj i skopiuj adres IP sieci Ethernet urządzenia. <br> 3. Nawiąż połączenie z usługą dołączania przy użyciu adresu URL opartego na protokole IP Ethernet. |
| Środowisko dołączania | Kliknięcie linku w umowie licencyjnej (Umowa licencyjna) czasami nie powoduje otwarcia nowej strony sieci Web. | Skopiuj link i otwórz go w osobnym oknie przeglądarki. |
| Środowisko dołączania | Nie można wykonać czynności podczas dołączania po nawiązaniu połączenia z Wi-Fiem hotspotu mobilnego. | Połącz urządzenie bezpośrednio z usługą SoftAP, siecią Wi-Fi lub siecią za pośrednictwem sieci Ethernet. |
| Wi-Fi | SoftAP może czasami odłączać lub znikać. | Badamy.  Ponowne uruchomienie urządzenia zwykle spowoduje jego przywrócenie. |
| Wi-Fi | Przycisk sprzętowy, który włącza i wyłącza Wi-Fi SoftAP, czasami nie działa. | Kontynuuj, naciskając przycisk lub ponownie uruchom urządzenie. |
| Wi-Fi | Po nawiązaniu połączenia z siecią Wi-Fi użytkownicy mogą zobaczyć komunikat: <br> "Ta Wi-Fi sieć używa starszego standardu zabezpieczeń". | SoftAP Devkit używa algorytmu szyfrowania WEP. |
| Wi-Fi | Nie można nawiązać połączenia z usługą SoftAP z komputera z systemem Windows 10 z następującym komunikatem o błędzie: <br> "Nie można nawiązać połączenia z tą siecią" | Uruchom ponownie zarówno Devkit, jak i komputer. |
| Aktualizacja urządzenia | Kontenery nie są uruchamiane po aktualizacji OTA. | Za pomocą tego polecenia Użyj protokołu SSH do urządzenia i ponownie uruchom kontener IoT Edge: `systemctl restart iotedge` . Spowoduje to ponowne uruchomienie wszystkich kontenerów. |
| Aktualizacja urządzenia | Użytkownicy mogą otrzymać komunikat informujący, że aktualizacja nie powiodła się, nawet jeśli zakończyła się pomyślnie. | Potwierdź aktualizację, przechodząc do devkitego urządzenia w IoT Hub i sprawdzając wartość `swVersion` . Ta wartość jest rozwiązywana po pierwszej aktualizacji. |
| Aktualizacja urządzenia | Po pierwszej aktualizacji użytkownicy mogą utracić ustawienia połączenia Wi-Fi. | Wykonaj kroki dołączania po aktualizacji w celu skonfigurowania połączenia Wi-Fi. Ta wartość jest rozwiązywana po pierwszej aktualizacji. |
| Aktualizacja urządzenia | Po przeprowadzeniu aktualizacji OTA użytkownicy nie będą mogli logować się za pośrednictwem protokołu SSH przy użyciu wcześniej utworzonych kont użytkowników i nie można tworzyć nowych użytkowników SSH za pośrednictwem środowiska dołączania. Ten problem ma wpływ na systemy wykonujące aktualizacje OTA z następujących wstępnie zainstalowanych wersji obrazu: 2020.110.114.105 i 2020.109.101.105. | Aby odzyskać profile użytkowników, wykonaj następujące kroki po aktualizacji OTA: <br> Użyj opcji "root" jako nazwy użytkownika w [Devkit](./how-to-ssh-into-percept-dk.md) . Jeśli wyłączono Logowanie użytkownika "root" protokołu SSH za pośrednictwem środowiska dołączania, należy je ponownie włączyć. Uruchom to polecenie po pomyślnym nawiązaniu połączenia: <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> Aby odzyskać poprzednie dane główne użytkownika, uruchom następujące polecenie: <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Aktualizacja urządzenia | Po wprowadzeniu aktualizacji OTA grupy aktualizacji zostaną utracone. | Zaktualizuj tag urządzenia, wykonując [te instrukcje](./how-to-update-over-the-air.md#create-a-device-update-group). |
| Instalator pakietu narzędzi deweloperskich | Opcjonalna instalacja Caffe może zakończyć się niepowodzeniem, jeśli platforma Docker nie działa prawidłowo w systemie. | Upewnij się, że platforma Docker jest zainstalowana i uruchomiona, a następnie ponów próbę instalacji Caffe. |
| Instalator pakietu narzędzi deweloperskich | Opcjonalna instalacja CUDA kończy się niepowodzeniem w systemach niezgodnych. | Przed uruchomieniem Instalatora Sprawdź zgodność systemu z programem CUDA. |
| Docker, Sieć, IoT Edge | Jeśli w sieci wewnętrznej używane są 172. x. x. x, kontenery platformy Docker nie będą mogły nawiązywać połączeń z IoT Edge. | Dodaj specjalną sekcję BIP do/etc/Docker/daemon.jsw pliku w następujący sposób: `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Linki "Wyświetl strumień" w usłudze Azure Percept Studio nie otwierają nowego okna przedstawiającego strumień sieci Web urządzenia. | 1. Otwórz [Azure Portal](https://portal.azure.com) i wybierz pozycję **IoT Hub**. <br> 2. Kliknij IoT Hub, do którego urządzenie jest połączone. <br> 3. Wybierz pozycję **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami** na stronie IoT Hub. <br> 4. Wybierz urządzenie z listy. <br> 5. Wybierz pozycję **Ustaw moduły** w górnej części strony urządzenia. <br> 6. Kliknij ikonę kosza na śmieci obok pozycji **HostIpModule** , aby usunąć moduł. <br> 7. Aby potwierdzić działanie, kliknij przycisk **Przegląd + Utwórz** , a następnie **Utwórz**. <br> 8. Otwórz [usługę Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) , a następnie kliknij pozycję **urządzenia** w panelu menu po lewej stronie. <br> 9. Wybierz urządzenie z listy. <br> 10. na karcie **wizja** wybierz pozycję **Wyświetl strumień urządzenia**. Urządzenie pobierze nową wersję programu HostIpModule i otworzy kartę przeglądarki ze strumieniem sieci Web urządzenia. |