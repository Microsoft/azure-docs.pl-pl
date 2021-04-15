---
title: Zarządzanie urządzeniami w aplikacji Azure IoT Central zarządzania | Microsoft Docs
description: Jako operator dowiedz się, jak zarządzać urządzeniami w Azure IoT Central aplikacji. Dowiedz się, jak zarządzać poszczególnymi urządzeniami oraz importować zbiorczo i eksportować urządzenia w aplikacji.
author: dominicbetts
ms.author: dobett
ms.date: 10/08/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5bab4a7a90101d3749571e0f2d4179f0fce14296
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378639"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Zarządzanie urządzeniami w Azure IoT Central aplikacji

W tym artykule opisano, jak operator zarządza urządzeniami w aplikacji Azure IoT Central aplikacji. Operator może:

- Strona **Urządzenia umożliwia** wyświetlanie, dodawanie i usuwanie urządzeń połączonych z Azure IoT Central aplikacji.
- Zbiorcze importowanie i eksportowanie urządzeń.
- Utrzymywanie aktualnego spisu urządzeń.
- Metadane urządzenia są aktualne, zmieniając wartości przechowywane we właściwościach urządzenia z widoków.
- Kontroluj zachowanie urządzeń, aktualizując ustawienie na określonym urządzeniu z widoków.

Aby dowiedzieć się, jak zarządzać niestandardowymi grupami urządzeń, zobacz [Samouczek: używanie grup urządzeń do analizowania telemetrii urządzeń.](tutorial-use-device-groups.md)

## <a name="view-your-devices"></a>Wyświetlanie urządzeń

Aby wyświetlić pojedyncze urządzenie:

1. Wybierz **pozycję Urządzenia** w okienku po lewej stronie. W tym miejscu zostanie wyświetlona lista wszystkich urządzeń i szablonów urządzeń.

1. Wybierz szablon urządzenia.

1. W okienku po prawej stronie **Urządzenia** zostanie wyświetlona lista urządzeń utworzonych na podstawie tego szablonu urządzenia. Wybierz pojedyncze urządzenie, aby wyświetlić stronę szczegółów tego urządzenia:

    ![Strona szczegółów urządzenia](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Dodawanie urządzenia

Aby dodać urządzenie do aplikacji Azure IoT Central aplikacji:

1. Wybierz **pozycję Urządzenia** w okienku po lewej stronie.

1. Wybierz szablon urządzenia, na podstawie którego chcesz utworzyć urządzenie.

1. Wybierz pozycję + **Nowy.**

1. Włącz **lub wyłącz** przełącznik  **Symulowane.** Rzeczywiste urządzenie jest urządzeniem fizycznym, które łączysz z aplikacją Azure IoT Central fizycznej. Symulowane urządzenie ma przykładowe dane wygenerowane przez Azure IoT Central.

1. Wybierz przycisk **Utwórz**.

1. To urządzenie zostanie teraz wyświetlone na liście urządzeń dla tego szablonu. Wybierz urządzenie, aby wyświetlić stronę szczegółów urządzenia zawierającą wszystkie widoki dla urządzenia.

## <a name="import-devices"></a>Importowanie urządzeń

Aby połączyć dużą liczbę urządzeń z aplikacją, możesz zbiorczo zaimportować urządzenia z pliku CSV. Plik CSV powinien mieć następujące nagłówki kolumn:

| Kolumna | Opis 
| - | - | 
| IOTC_DEVICEID | Identyfikator urządzenia jest unikatowym identyfikatorem zidentyfikowanym przez to urządzenie do nawiązania połączenia. Identyfikator urządzenia może zawierać litery, cyfry i `-` znak bez spacji. |
| IOTC_DEVICENAME | Opcjonalny. Nazwa urządzenia jest przyjazną nazwą, która będzie wyświetlana w całej aplikacji. Jeśli nie zostanie określony, będzie on taki sam jak identyfikator urządzenia.   |

Aby zbiorczo zarejestrować urządzenia w aplikacji:

1. Wybierz **pozycję Urządzenia** w okienku po lewej stronie.

1. Na panelu po lewej stronie wybierz szablon urządzenia, dla którego chcesz zbiorczo utworzyć urządzenia.

    > [!NOTE]
    > Jeśli nie masz jeszcze szablonu urządzenia, możesz zaimportować urządzenia w obszarze **Wszystkie** urządzenia i zarejestrować je bez szablonu. Po zaimportowaniu urządzeń można przeprowadzić ich migrację do szablonu.

1. Wybierz pozycję **Importuj**.

    ![Akcja importu](./media/howto-manage-devices/bulkimport1a.png)


1. Wybierz plik CSV zawierający listę identyfikatorów urządzeń do zaimportowania.

1. Importowanie urządzenia rozpoczyna się po przesłaniu pliku. Stan importu można śledzić w panelu Operacje na urządzeniach. Ten panel jest wyświetlany automatycznie po rozpoczęciem importowania lub można uzyskać do niego dostęp za pomocą ikony dzwonka w prawym górnym rogu.

1. Po zakończeniu importowania w panelu Operacje na urządzeniach zostanie wyświetlony komunikat o sukcesie.

    ![Powodzenie importowania](./media/howto-manage-devices/bulkimport3a.png)

Jeśli operacja importowania urządzenia nie powiedzie się, zostanie wyświetlony komunikat o błędzie na panelu Operacje na urządzeniach. Zostanie wygenerowany plik dziennika przechwytując wszystkie błędy, który można pobrać.

## <a name="migrate-devices-to-a-template"></a>Migrowanie urządzeń do szablonu

Jeśli zarejestrujesz urządzenia, uruchamiając import w obszarze **Wszystkie urządzenia,** urządzenia zostaną utworzone bez żadnych skojarzenia szablonu urządzenia. Urządzenia muszą być skojarzone z szablonem, aby eksplorować dane i inne szczegóły dotyczące urządzenia. Wykonaj następujące kroki, aby skojarzyć urządzenia z szablonem:

1. Wybierz **pozycję Urządzenia** w okienku po lewej stronie.

1. Na panelu po lewej stronie wybierz **pozycję Wszystkie urządzenia:**

    ![Urządzenia nieskojarzonych](./media/howto-manage-devices/unassociateddevices1a.png)

1. Użyj filtru na siatce, aby określić, czy wartość w kolumnie **Szablon** urządzenia ma wartość **Nieskojarzona** dla żadnego z urządzeń.

1. Wybierz urządzenia, które chcesz skojarzyć z szablonem:

1. Wybierz pozycję **Migruj:**

    ![Kojarzenie urządzeń](./media/howto-manage-devices/unassociateddevices2a.png)

1. Wybierz szablon z listy dostępnych szablonów, a następnie wybierz pozycję **Migruj.**

1. Wybrane urządzenia są skojarzone z wybranym szablonem urządzenia.

## <a name="export-devices"></a>Eksportowanie urządzeń

Aby połączyć rzeczywiste urządzenie z IoT Central, potrzebne są jego ciągi połączenia. Szczegóły urządzenia można wyeksportować zbiorczo, aby uzyskać informacje potrzebne do utworzenia parametry połączenia urządzenia. Proces eksportowania tworzy plik CSV z tożsamością urządzenia, nazwą urządzenia i kluczami dla wszystkich wybranych urządzeń.

Aby zbiorczo wyeksportować urządzenia z aplikacji:

1. Wybierz **pozycję Urządzenia** w okienku po lewej stronie.

1. W okienku po lewej stronie wybierz szablon urządzenia, z którego chcesz wyeksportować urządzenia.

1. Wybierz urządzenia, które chcesz wyeksportować, a następnie wybierz **akcję** Eksportuj.

    ![Eksportowanie](./media/howto-manage-devices/export1a.png)

1. Rozpocznie się proces eksportowania. Stan można śledzić przy użyciu panelu Operacje na urządzeniach.

1. Po zakończeniu eksportowania zostanie wyświetlony komunikat o sukcesie wraz z linkiem do pobrania wygenerowanego pliku.

1. Wybierz link **Pobierz** plik, aby pobrać plik do folderu lokalnego na dysku.

    ![Powodzenie eksportu](./media/howto-manage-devices/export2a.png)

1. Wyeksportowany plik CSV zawiera następujące kolumny: identyfikator urządzenia, nazwa urządzenia, klucze urządzeń i odciski palca certyfikatu X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Aby uzyskać więcej informacji na temat ciągów połączenia i łączenia rzeczywistych urządzeń z aplikacją IoT Central, zobacz [Łączność urządzenia w Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Usuwanie urządzenia

Aby usunąć rzeczywiste lub symulowane urządzenie z Azure IoT Central aplikacji:

1. Wybierz **pozycję Urządzenia** w okienku po lewej stronie.

1. Wybierz szablon urządzenia, które chcesz usunąć.

1. Użyj narzędzi filtrowania, aby filtrować i wyszukiwać urządzenia. Zaznacz pole wyboru obok urządzeń do usunięcia.

1. Wybierz przycisk **Usuń**. Stan tego usunięcia można śledzić w panelu Operacje na urządzeniach.

## <a name="change-a-property"></a>Zmienianie właściwości

Właściwości chmury to metadane urządzenia skojarzone z urządzeniem, takie jak miasto i numer seryjny. Właściwości chmury istnieją tylko IoT Central aplikacji i nie są synchronizowane z urządzeniami. Właściwości zapisywalne kontrolują zachowanie urządzenia i umożliwiają zdalne ustawianie stanu urządzenia, na przykład przez ustawienie temperatury docelowej urządzenia termostatu.  Właściwości urządzenia są ustawiane przez urządzenie i są tylko do odczytu w IoT Central. Właściwości można wyświetlać i aktualizować w **widokach Szczegóły** urządzenia dla urządzenia.

1. Wybierz **pozycję Urządzenia** w okienku po lewej stronie.

1. Wybierz szablon urządzenia, którego właściwości chcesz zmienić, i wybierz urządzenie docelowe.

1. Wybierz widok, który zawiera właściwości urządzenia. Ten widok umożliwia wprowadzanie wartości i wybranie przycisku **Zapisz** w górnej części strony. W tym miejscu zobaczysz właściwości urządzenia i ich bieżące wartości. Właściwości chmury i właściwości zapisywalne mają pola edytowalne, a właściwości urządzenia są tylko do odczytu. W przypadku właściwości zapisywalnych stan synchronizacji można zobaczyć w dolnej części pola. 

1. Zmodyfikuj właściwości, aby wartości, których potrzebujesz. Można modyfikować wiele właściwości jednocześnie i aktualizować je wszystkie w tym samym czasie.

1. Wybierz polecenie **Zapisz**. Jeśli zapisano właściwości zapisywalne, wartości są wysyłane do urządzenia. Gdy urządzenie potwierdzi zmianę właściwości zapisywalne, stan powraca do **zsynchronizowanej wartości**. Jeśli zapisano właściwość w chmurze, wartość zostanie zaktualizowana.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zarządzać urządzeniami w aplikacji Azure IoT Central, sugerowanym następnym krokiem jest skonfigurowanie reguł[dla](howto-configure-rules.md) urządzeń.
