---
title: Zarządzanie ustawieniami modelu i uczenia — Personalizacja
description: Model uczenia maszynowego i ustawienia uczenia można wyeksportować do kopii zapasowej w ramach własnego systemu kontroli źródła.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: f82adad5273f1c5559cbeb1924f59e57c863f6b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303458"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Jak zarządzać ustawieniami modelu i uczenia

Model uczenia maszynowego i ustawienia uczenia można wyeksportować do kopii zapasowej w ramach własnego systemu kontroli źródła.

## <a name="export-the-personalizer-model"></a>Eksportowanie modelu personalizowania

W sekcji Zarządzanie zasobami dla **ustawień model i nauka**przejrzyj temat Tworzenie modelu i Data ostatniej aktualizacji oraz wyeksportuj bieżący model. Za pomocą Azure Portal lub interfejsów API personalizacji można eksportować plik modelu na potrzeby archiwizowania.

![Eksportuj bieżący model personalizacji](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Wyczyść dane dla swojej pętli szkoleniowej

1. W Azure Portal dla zasobu personalizacji na stronie **Ustawienia modelu i uczenia** wybierz pozycję **Wyczyść dane**.
1. Aby wyczyścić wszystkie dane i zresetować pętlę uczenia do oryginalnego stanu, zaznacz wszystkie 3 pola wyboru.

    ![W Azure Portal Wyczyść dane z zasobów personalizacji.](./media/settings/clear-data-from-personalizer-resource.png)

    |Wartość|Przeznaczenie|
    |--|--|
    |Zarejestrowane dane personalizacji i nagrody.|Te dane rejestrowania są używane w obliczeniach w trybie offline. Wyczyść dane, jeśli resetuje się zasób.|
    |Zresetuj model personalizowania.|Ten model zmienia się w każdym przeszkoleniu. Ta częstotliwość uczenia jest określona w polu **częstotliwość przekazywania modeli** na stronie **Konfiguracja** . |
    |Ustaw domyślne zasady uczenia.|Jeśli zasady uczenia zostały zmienione w ramach oceny w trybie offline, spowoduje to przywrócenie oryginalnych zasad nauki.|

1. Wybierz pozycję **Wyczyść wybrane dane** , aby rozpocząć proces czyszczenia. Stan jest raportowany w powiadomieniach platformy Azure w prawym górnym rogu.

## <a name="import-a-new-learning-policy"></a>Importuj nowe zasady uczenia

Ustawienia [zasad nauki](concept-active-learning.md#understand-learning-policy-settings) określają _Parametry_ szkolenia modelu. Przeprowadź [ocenę w trybie offline](how-to-offline-evaluation.md) , aby znaleźć nowe zasady nauki.

1. Otwórz [Azure Portal](https://portal.azure.com)i wybierz zasób personalizacji.
1. Wybierz pozycję **Ustawienia modelu i uczenia** w sekcji **Zarządzanie zasobami** .
1. W polu **Importuj ustawienia uczenia** wybierz utworzony powyżej plik w formacie JSON, a następnie wybierz przycisk **Przekaż** .

    Poczekaj na powiadomienie, że zasady uczenia zostały pomyślnie przekazane.

## <a name="export-a-learning-policy"></a>Eksportowanie zasad uczenia

1. Otwórz [Azure Portal](https://portal.azure.com)i wybierz zasób personalizacji.
1. Wybierz pozycję **Ustawienia modelu i uczenia** w sekcji **Zarządzanie zasobami** .
1. W polu **Importuj ustawienia uczenia** wybierz przycisk **Eksportuj ustawienia uczenia** . Spowoduje to zapisanie `json` pliku na komputerze lokalnym.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak zarządzać zasadami uczenia](how-to-manage-model.md)
