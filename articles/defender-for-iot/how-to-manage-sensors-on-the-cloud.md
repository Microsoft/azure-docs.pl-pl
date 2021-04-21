---
title: Zarządzanie czujnikami w portalu usługi Defender dla IoT
description: Dowiedz się, jak dołączać i wyświetlać czujniki oraz zarządzać nimi w portalu usługi Defender dla IoT.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f407a65f60a1b969f17ebe00be39a888a09ec83d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752718"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>Zarządzanie czujnikami w portalu usługi Defender dla IoT

W tym artykule opisano sposób dołączania i wyświetlania czujników oraz zarządzania nimi w portalu [usługi Defender dla IoT.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

## <a name="onboard-sensors"></a>Dołączanie czujników

Czujnik jest dołączany przez zarejestrowanie go w Azure Defender dla IoT i pobranie pliku aktywacji czujnika.

### <a name="register-the-sensor"></a>Rejestrowanie czujnika

W celu przeprowadzenia rejestracji:

1. Przejdź do strony **powitaowej** w portalu [usługi Defender dla IoT.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)
1. Wybierz **pozycję Dołączanie czujnika**.
1. Utwórz nazwę czujnika. Zalecamy dołącznie adresu IP zainstalowanego czujnika jako części nazwy lub użycie łatwej do zidentyfikowania nazwy. Zapewni to łatwiejsze śledzenie i spójne nazewnictwo między nazwą rejestracji w portalu usługi Azure [Defender dla IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) a adresem IP wdrożonego czujnika wyświetlanym w konsoli czujnika.
1. Skojarz czujnik z subskrypcją platformy Azure.
1. Wybierz tryb połączenia czujnika przy użyciu **przełącznika Połączone z chmurą.** Jeśli przełącznik jest włączona, czujnik jest połączony z chmurą. Jeśli przełącznik jest wyłączony, czujnik jest zarządzany lokalnie.

   - **Czujniki połączone z chmurą:** informacje wykrywane przez czujnik są wyświetlane w konsoli czujników. Informacje o alertach są dostarczane za pośrednictwem centrum IoT i mogą być udostępniane innym usługom platformy Azure, takim jak Azure Sentinel. Ponadto pakiety analizy zagrożeń można wypychać z portalu Azure Defender dla IoT do czujników. Z drugiej strony, gdy czujnik nie jest połączony z chmurą, należy pobrać pakiety analizy zagrożeń, a następnie przekazać je do czujników przedsiębiorstwa. Aby zezwolić u programowi Defender dla IoT na wypychanie pakietów do czujników, włącz przełącznik **Automatyczne aktualizacje** analizy zagrożeń. Aby uzyskać więcej informacji, zobacz [Analizy zagrożeń i pakiety](how-to-work-with-threat-intelligence-packages.md).
   Wybierz centrum IoT Hub, które będzie służyć jako brama między tym czujnikiem a Azure Defender dla IoT portalem. Zdefiniuj nazwę i strefę lokacji. Można również dodać tagi opisowe. Nazwa witryny, strefa i tagi są opisowymi wpisami na [stronie Witryny i czujniki.](#view-onboarded-sensors)

   - **Czujniki zarządzane lokalnie:** informacje wykrywane przez czujniki są wyświetlane w konsoli czujników. Jeśli pracujesz w bezprzewodowej sieci i chcesz uzyskać ujednolicony widok wszystkich informacji wykrytych przez wiele lokalnie zarządzanych czujników, pracuj z lokalną konsolą zarządzania.

   W przypadku czujników połączonych z chmurą nazwa zdefiniowana podczas dołączania to nazwa wyświetlana w konsoli czujników. Nie można zmienić tej nazwy bezpośrednio z konsoli. W przypadku lokalnie zarządzanych czujników nazwa stosowana podczas dołączania będzie przechowywana na platformie Azure, ale można ją zaktualizować w konsoli czujników.

### <a name="download-the-sensor-activation-file"></a>Pobieranie pliku aktywacji czujnika

Plik aktywacji czujnika zawiera instrukcje dotyczące trybu zarządzania czujnikiem. Należy pobrać unikatowy plik aktywacji dla każdego wdrażanych czujników. Użytkownik, który po raz pierwszy korzysta z konsoli czujnika, przesyła plik aktywacji do czujnika.

Aby pobrać plik aktywacji:

1. Na stronie **Dołączanie czujnika** wybierz pozycję **Pobierz plik aktywacji.**
1. Udostępnij plik użytkownikowi, który loguje się do konsoli czujnika po raz pierwszy.

## <a name="view-onboarded-sensors"></a>Wyświetlanie dołączanych czujników

W portalu [usługi Defender dla IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)można wyświetlić ważne informacje operacyjne dotyczące dołączanych czujników.

1. Wybierz **pozycję Witryny i czujniki.** Na stronie przedstawiono liczbę czujników, które zostały przyłączone, liczbę czujników połączonych z chmurą i zarządzanych lokalnie, a także:

- nazwa czujnika przypisana podczas dołączania.
- typ połączenia (połączony z chmurą lub zarządzany lokalnie)
- strefa skojarzona z czujnikiem.
- Zainstalowana wersja czujnika
- Stan połączenia czujnika z chmurą.
- Czas ostatniego wykrycia czujnika podczas nawiązywania połączenia z chmurą.

## <a name="manage-onboarded-sensors"></a>Zarządzanie czujnikami dołączanych

Użyj portalu [usługi Defender dla IoT do](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) wykonywania zadań zarządzania związanych z czujnikami.

Dołączane czujniki można wyświetlić na stronie **Witryny i czujniki.** Na tej stronie można również edytować informacje z czujników.

### <a name="export-sensor-details"></a>Eksportowanie szczegółów czujnika

Aby wyeksportować informacje o dołączanych czujnikach, wybierz ikonę **Eksportuj** w górnej części strony **Witryny i** czujniki.

### <a name="edit-sensor-zone-details"></a>Edytowanie szczegółów strefy czujnika

Użyj opcji **edycji Lokacje i Czujniki,** aby edytować nazwę i strefę czujnika.

Aby edytować:

1. Wybierz **wielokropek** (**...**) dla czujnika, który chcesz edytować.
1. Wybierz pozycję **Edytuj**.
1. Zaktualizuj strefę czujnika lub utwórz nową strefę.

### <a name="delete-a-sensor"></a>Usuwanie czujnika

Jeśli usuniesz czujnik połączony z chmurą, informacje nie będą wysyłane do centrum IoT. Usuń lokalnie połączone czujniki, gdy nie pracujesz już z nimi.

Aby usunąć czujnik:

1. Wybierz wielokropek (**...**) dla czujnika, który chcesz usunąć.
1. Potwierdź usunięcie.

### <a name="reactivate-a-sensor"></a>Ponowne uaktywnij czujnik 

Może być konieczne ponowne uaktywninie czujnika, ponieważ chcesz:

- **Praca w trybie** połączonym z chmurą zamiast w trybie zarządzanym lokalnie: po ponownej aktywacji wykrywanie czujników jest wyświetlane w czujniku, a nowo wykryte informacje o alertach są dostarczane za pośrednictwem centrum IoT. Te informacje można udostępniać innym usługom platformy Azure, takim jak Azure Sentinel.

- **Praca w trybie zarządzanym lokalnie zamiast** w trybie połączonym z chmurą: po ponownej aktywacji informacje dotyczące wykrywania czujnika są wyświetlane tylko w czujniku.

- **Skojarz** czujnik z nowym centrum IoT: W tym celu zarejestruj ponownie czujnik w nowym centrum, a następnie pobierz nowy plik aktywacji.

Aby ponownie uaktywnić czujnik:

1. Przejdź do **strony Witryny i czujniki** w portalu usługi Defender dla [IoT.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

2. Wybierz czujnik, dla którego chcesz przekazać nowy plik aktywacji.

3. Usuń czujnik.

4. Ponownie dołączaj czujnik w nowym trybie lub do nowego  centrum IoT, wybierając pozycję Dołączanie czujnika na Wprowadzenie internetowej.

5. Pobierz plik aktywacji.

1. Zaloguj się do konsoli czujnika usługi Defender dla IoT.

7. W konsoli czujnika wybierz pozycję **Ustawienia systemu,** a następnie wybierz **pozycję Uaktywnij ponownie.**

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Przekaż plik aktywacji, aby ponownie uaktywnić czujnik.":::

8. Wybierz **pozycję** Przekaż i wybierz plik zapisany na stronie Dołączanie czujnika.

9. Wybierz **pozycję Aktywuj.**

## <a name="next-steps"></a>Następne kroki

[Aktywowanie i konfigurowanie czujnika](how-to-activate-and-set-up-your-sensor.md)
