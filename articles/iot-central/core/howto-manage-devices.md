---
title: Zarządzanie urządzeniami w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Jako operator, Dowiedz się, jak zarządzać urządzeniami w aplikacji IoT Central platformy Azure. Dowiedz się, jak zarządzać poszczególnymi urządzeniami i importować zbiorczo i eksportować urządzenia w aplikacji.
author: dominicbetts
ms.author: dobett
ms.date: 10/08/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2f0b6feea5e586c87191b22f42e3ab86e85ba7b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97032528"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Zarządzanie urządzeniami w aplikacji IoT Central platformy Azure

W tym artykule opisano, jak za pomocą operatora zarządzać urządzeniami w aplikacji IoT Central platformy Azure. Jako operator można:

- Strona **urządzenia** służy do wyświetlania, dodawania i usuwania urządzeń podłączonych do aplikacji IoT Central platformy Azure.
- Importuj i Eksportuj urządzenia zbiorczo.
- Przechowywanie aktualnych danych spisu urządzeń.
- Zadbaj o aktualność metadanych urządzenia, zmieniając wartości przechowywane we właściwościach urządzenia w widokach.
- Kontroluj zachowanie urządzeń, aktualizując ustawienia na określonym urządzeniu w widokach.

Aby dowiedzieć się, jak zarządzać grupami niestandardowymi urządzeń, zobacz [Samouczek: używanie grup urządzeń do analizowania danych telemetrycznych urządzenia](tutorial-use-device-groups.md).

## <a name="view-your-devices"></a>Wyświetlanie urządzeń

Aby wyświetlić pojedyncze urządzenie:

1. Wybierz pozycję **urządzenia** w okienku po lewej stronie. W tym miejscu zostanie wyświetlona lista wszystkich urządzeń i szablonów urządzeń.

1. Wybierz szablon urządzenia.

1. W okienku po prawej stronie **urządzenia** zostanie wyświetlona lista urządzeń utworzonych na podstawie tego szablonu. Wybierz pojedyncze urządzenie, aby wyświetlić stronę szczegółów urządzenia dla tego urządzenia:

    ![Strona szczegółów urządzenia](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Dodawanie urządzenia

Aby dodać urządzenie do aplikacji usługi Azure IoT Central:

1. Wybierz pozycję **urządzenia** w okienku po lewej stronie.

1. Wybierz szablon urządzenia, z którego chcesz utworzyć urządzenie.

1. Wybierz pozycję + **Nowy**.

1. Zmień przełącznik **symulowany** na **włączony** lub **wyłączony**. Rzeczywiste urządzenie dotyczy urządzenia fizycznego połączonego z aplikacją Azure IoT Central. Urządzenie symulowane ma przykładowe dane wygenerowane przez usługę Azure IoT Central.

1. Wybierz przycisk **Utwórz**.

1. To urządzenie jest teraz widoczne na liście urządzeń dla tego szablonu. Wybierz urządzenie, aby wyświetlić stronę szczegóły urządzenia, która zawiera wszystkie widoki dla urządzenia.

## <a name="import-devices"></a>Importuj urządzenia

Aby połączyć dużą liczbę urządzeń z aplikacją, można zbiorczo importować urządzenia z pliku CSV. Plik CSV powinien zawierać następujące kolumny i nagłówki:

* **IOTC_DeviceID** — identyfikator urządzenia może zawierać litery, cyfry i `-` znak.
* **IOTC_DeviceName** — ta kolumna jest opcjonalna.

Aby przeprowadzić zbiorczą rejestrację urządzeń w aplikacji:

1. Wybierz pozycję **urządzenia** w okienku po lewej stronie.

1. Na panelu po lewej stronie wybierz szablon urządzenia, dla którego chcesz utworzyć zbiorczo urządzenia.

    > [!NOTE]
    > Jeśli nie masz jeszcze szablonu urządzenia, możesz zaimportować urządzenia ze **wszystkich urządzeń** i zarejestrować je bez szablonu. Po zaimportowaniu urządzeń można migrować je do szablonu.

1. Wybierz pozycję **Importuj**.

    ![Importuj akcję](./media/howto-manage-devices/bulkimport1a.png)


1. Wybierz plik CSV, który zawiera listę identyfikatorów urządzeń do zaimportowania.

1. Importowanie urządzenia rozpocznie się po przekazaniu pliku. Stan importowania można śledzić w panelu Operacje na urządzeniu. Ten panel jest wyświetlany automatycznie po rozpoczęciu importowania lub dostęp do niego za pomocą ikony dzwonka w prawym górnym rogu.

1. Po zakończeniu importowania w panelu Operacje urządzenia zostanie wyświetlony komunikat o powodzeniu.

    ![Powodzenie importowania](./media/howto-manage-devices/bulkimport3a.png)

Jeśli operacja importowania urządzenia nie powiedzie się, zostanie wyświetlony komunikat o błędzie w panelu Operacje na urządzeniu. Zostanie wygenerowany plik dziennika, który przechwytuje wszystkie błędy, które można pobrać.

## <a name="migrate-devices-to-a-template"></a>Migrowanie urządzeń do szablonu

W przypadku rejestrowania urządzeń przez uruchomienie importowania w obszarze **wszystkie urządzenia** urządzenia są tworzone bez skojarzenia szablonu urządzenia. Urządzenia muszą być skojarzone z szablonem, aby eksplorować dane oraz inne szczegóły dotyczące urządzenia. Wykonaj następujące kroki, aby skojarzyć urządzenia z szablonem:

1. Wybierz pozycję **urządzenia** w okienku po lewej stronie.

1. Na panelu po lewej stronie wybierz pozycję **wszystkie urządzenia**:

    ![Nieskojarzone urządzenia](./media/howto-manage-devices/unassociateddevices1a.png)

1. Użyj filtru na siatce, aby określić, czy wartość w kolumnie **szablon urządzenia** nie jest **skojarzona** z żadnym z urządzeń.

1. Wybierz urządzenia, które chcesz skojarzyć z szablonem:

1. Wybierz pozycję **Migruj**:

    ![Skojarz urządzenia](./media/howto-manage-devices/unassociateddevices2a.png)

1. Wybierz szablon z listy dostępnych szablonów i wybierz pozycję **Migruj**.

1. Wybrane urządzenia są skojarzone z wybranym szablonem urządzenia.

## <a name="export-devices"></a>Eksportuj urządzenia

Aby podłączyć rzeczywiste urządzenie do IoT Central, potrzebne są jego parametry połączenia. Aby uzyskać informacje potrzebne do tworzenia parametrów połączenia urządzenia, można zbiorczo wyeksportować szczegóły dotyczące urządzeń. Proces eksportowania tworzy plik CSV z tożsamością urządzenia, nazwą urządzenia i kluczami dla wszystkich wybranych urządzeń.

Aby wyeksportować zbiorczo urządzenia z aplikacji:

1. Wybierz pozycję **urządzenia** w okienku po lewej stronie.

1. W lewym okienku wybierz szablon urządzenia, z którego mają zostać wyeksportowane urządzenia.

1. Wybierz urządzenia, które chcesz wyeksportować, a następnie wybierz akcję **Eksportuj** .

    ![Eksportowanie](./media/howto-manage-devices/export1a.png)

1. Rozpocznie się proces eksportowania. Stan można śledzić za pomocą panelu Operacje na urządzeniu.

1. Po zakończeniu eksportowania zostanie wyświetlony komunikat o powodzeniu wraz z linkiem umożliwiającym pobranie wygenerowanego pliku.

1. Wybierz link **Pobierz plik** , aby pobrać plik do folderu lokalnego na dysku.

    ![Powodzenie eksportowania](./media/howto-manage-devices/export2a.png)

1. Wyeksportowany plik CSV zawiera następujące kolumny: Identyfikator urządzenia, nazwa urządzenia, klucze urządzeń i odciski palca certyfikatu x509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Aby uzyskać więcej informacji na temat parametrów połączenia i łączenia rzeczywistych urządzeń z aplikacją IoT Central, zobacz [łączność urządzenia w usłudze Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Usuwanie urządzenia

Aby usunąć urządzenie prawdziwe lub symulowane z aplikacji IoT Central platformy Azure:

1. Wybierz pozycję **urządzenia** w okienku po lewej stronie.

1. Wybierz szablon urządzenia, które chcesz usunąć.

1. Użyj narzędzi filtru do filtrowania i wyszukiwania urządzeń. Zaznacz pole wyboru obok pozycji urządzenia do usunięcia.

1. Wybierz przycisk **Usuń**. Stan tego usunięcia można śledzić w panelu Operacje na urządzeniu.

## <a name="change-a-property"></a>Zmiana właściwości

Właściwości chmury to metadane urządzenia skojarzone z urządzeniem, takie jak miasto i numer seryjny. Właściwości chmury istnieją tylko w aplikacji IoT Central i nie są synchronizowane z urządzeniami. Właściwości do zapisu kontrolują zachowanie urządzenia i umożliwiają zdalne Ustawianie stanu urządzenia, na przykład przez ustawienie temperatury docelowej dla termostatu.  Właściwości urządzenia są ustawiane przez urządzenie i są tylko do odczytu w ramach IoT Central. Można wyświetlać i aktualizować właściwości w widokach **szczegóły urządzenia** dla urządzenia.

1. Wybierz pozycję **urządzenia** w okienku po lewej stronie.

1. Wybierz szablon urządzenia urządzenia, którego właściwości chcesz zmienić, a następnie wybierz urządzenie docelowe.

1. Wybierz widok, który zawiera właściwości urządzenia, ten widok umożliwia wprowadzanie wartości i wybranie opcji **Zapisz** w górnej części strony. Tutaj zobaczysz właściwości urządzenia i ich bieżące wartości. Właściwości chmury i zapisywalne właściwości mają pola edytowalne, podczas gdy właściwości urządzenia są tylko do odczytu. Dla właściwości do zapisu można zobaczyć stan synchronizacji u dołu pola. 

1. Zmodyfikuj właściwości do potrzebnych wartości. Można modyfikować wiele właściwości jednocześnie i aktualizować je wszystkie w tym samym czasie.

1. Wybierz polecenie **Zapisz**. Jeśli Zapisano właściwości do zapisu, wartości są wysyłane do urządzenia. Gdy urządzenie potwierdzi zmianę właściwości zapisywalnej, stan zwraca z powrotem do **synchronizowane**. Jeśli Zapisano Właściwość chmury, wartość jest aktualizowana.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać urządzeniami w aplikacji IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem[Konfigurowanie reguł](howto-configure-rules.md) dla urządzeń.
