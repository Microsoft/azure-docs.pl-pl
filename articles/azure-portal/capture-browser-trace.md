---
title: Przechwyć śledzenie przeglądarki w celu rozwiązywania problemów | Microsoft Docs
description: Przechwyć informacje o sieci z śledzenia przeglądarki, aby pomóc w rozwiązywaniu problemów z Azure Portal.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76310700"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Przechwytywanie danych śledzenia przeglądarki na potrzeby rozwiązywania problemów

W przypadku rozwiązywania problemów z Azure Portal i konieczności skontaktowania się z pomocą techniczną firmy Microsoft zalecamy najpierw przechwycenie śledzenia przeglądarki i pewnych dodatkowych informacji. Zbierane informacje mogą dostarczyć ważnych informacji o portalu w momencie wystąpienia problemu. Wykonaj kroki opisane w tym artykule dla narzędzi deweloperskich w używanej przeglądarce: Google Chrome lub Microsoft Edge (chrom), Microsoft Edge (EdgeHTML) lub Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome i Microsoft Edge (chrom)

Google Chrome i Microsoft Edge (chrom) są zarówno oparte na [projekcie "Open Source](https://www.chromium.org/Home)" w formacie chrom. Poniższe kroki pokazują, jak używać narzędzi deweloperskich, które są bardzo podobne w obu przeglądarkach. Aby uzyskać więcej informacji, zobacz [Chrome devtools](https://developers.google.com/web/tools/chrome-devtools) i [Microsoft Edge (chrom) narzędzia deweloperskie](/microsoft-edge/devtools-guide-chromium).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Ważne jest, aby zalogować się _przed_ rozpoczęciem śledzenia, tak aby ślad nie zawierał informacji poufnych związanych z logowaniem. 

1. Rozpocznij rejestrowanie kroków, które należy wykonać w portalu, przy użyciu [rejestratora kroków](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. W portalu przejdź do kroku tuż przed wystąpieniem problemu.

1. Naciśnij klawisz F12 lub ![wybierz zrzut ekranu ikony ustawienia](media/capture-browser-trace/chromium-icon-settings.png) > przeglądarki ikona**więcej narzędzi** > **deweloperskich**.

1. Domyślnie przeglądarka przechowuje informacje o śledzeniu tylko dla aktualnie załadowanej strony. Ustaw poniższe opcje, aby przeglądarka zachował wszystkie informacje o śledzeniu, nawet jeśli Odtwórz wymaga przechodzenia do więcej niż jednej strony:

    1. Wybierz kartę **Sieć** , a następnie wybierz pozycję **Zachowaj dziennik**.

          ![Zrzut ekranu przedstawiający "zachowywanie dziennika"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Wybierz kartę **konsola** , wybierz pozycję **Ustawienia konsoli**, a następnie wybierz pozycję **Zachowaj dziennik**. Ponownie wybierz **Ustawienia konsoli** , aby zamknąć okienko ustawienia.

          ![Zrzut ekranu przedstawiający "zachowywanie dziennika"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Wybierz kartę **Sieć** , a następnie wybierz pozycję **Zatrzymaj rejestrowanie dziennika sieci** i **Wyczyść pole wyboru**.

    ![Zrzut ekranu przedstawiający "Zatrzymaj rejestrowanie dziennika sieciowego" i "Wyczyść"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Wybierz pozycję **zarejestruj dziennik sieci**, a następnie Odtwórz problem w portalu.

    ![Zrzut ekranu przedstawiający "Uruchom sesję profilowania"](media/capture-browser-trace/chromium-start-session.png)

    Zostaną wyświetlone dane wyjściowe sesji podobne do poniższej ilustracji.

    ![Zrzut ekranu przedstawiający wyniki śledzenia przeglądarki](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Po ponownym utworzeniu nieoczekiwanego zachowania portalu wybierz pozycję **Zatrzymaj rejestrowanie dziennika sieci**, a następnie wybierz pozycję **Eksportuj Har** i Zapisz plik.

    ![Zrzut ekranu przedstawiający "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Zatrzymaj Rejestrator kroków i Zapisz plik.

1. W okienku narzędzia deweloperskie przeglądarki wybierz kartę **konsola** . Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Zapisz jako...** i Zapisz dane wyjściowe konsoli w pliku tekstowym.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli](media/capture-browser-trace/chromium-console-select.png)

1. Pakuj plik HAR, dane wyjściowe konsoli i nagrywanie ekranu w skompresowanym formacie, na przykład. zip, i udostępnij go z pomocą techniczną firmy Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

Poniższe kroki pokazują, jak używać narzędzi deweloperskich w przeglądarce Microsoft Edge (EdgeHTML). Aby uzyskać więcej informacji, zobacz [Microsoft Edge (EdgeHTML) narzędzia deweloperskie](/microsoft-edge/devtools-guide).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Ważne jest, aby zalogować się _przed_ rozpoczęciem śledzenia, tak aby ślad nie zawierał informacji poufnych związanych z logowaniem. 

1. Rozpocznij rejestrowanie kroków, które należy wykonać w portalu, przy użyciu [rejestratora kroków](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. W portalu przejdź do kroku tuż przed wystąpieniem problemu.

1. Naciśnij klawisz F12 lub ![wybierz zrzut ekranu ikony ustawienia](media/capture-browser-trace/edge-icon-settings.png) > przeglądarki ikona**więcej narzędzi** > **deweloperskich**.

1. Domyślnie przeglądarka przechowuje informacje o śledzeniu tylko dla aktualnie załadowanej strony. Ustaw poniższe opcje, aby przeglądarka zachował wszystkie informacje o śledzeniu, nawet jeśli Odtwórz wymaga przechodzenia do więcej niż jednej strony:

    1. Wybierz kartę **Sieć** , a następnie usuń zaznaczenie pola wyboru **Wyczyść wpisy podczas nawigowania**.

          ![Zrzut ekranu przedstawiający "Czyszczenie wpisów przy nawigacji"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Wybierz kartę **konsola** , a następnie wybierz pozycję **Zachowaj dziennik**.

          ![Zrzut ekranu przedstawiający "zachowywanie dziennika"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Wybierz kartę **Sieć** , a następnie wybierz pozycję **Zatrzymaj sesję profilowania** i **Wyczyść sesję**.

    ![Zrzut ekranu przedstawiający "Zatrzymaj sesję profilowania" i "Wyczyść sesję"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Wybierz pozycję **Rozpocznij sesję profilowania**, a następnie Odtwórz problem w portalu.

    ![Zrzut ekranu przedstawiający "Uruchom sesję profilowania"](media/capture-browser-trace/edge-start-session.png)

    Zostaną wyświetlone dane wyjściowe sesji podobne do poniższej ilustracji.

    ![Zrzut ekranu przedstawiający wyniki śledzenia przeglądarki](media/capture-browser-trace/edge-browser-trace-results.png)

1. Po ponownym utworzeniu nieoczekiwanego zachowania portalu wybierz pozycję **Zatrzymaj sesję profilowania**, a następnie wybierz pozycję **Eksportuj jako Har** i Zapisz plik.

    ![Zrzut ekranu przedstawiający "Export as HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Zatrzymaj Rejestrator kroków i Zapisz plik.

1. W okienku narzędzia deweloperskie przeglądarki wybierz kartę **konsola** i rozwiń okno. Umieść kursor na początku danych wyjściowych w konsoli, a następnie przeciągnij i zaznacz całą zawartość danych wyjściowych. Kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Kopiuj**i Zapisz dane wyjściowe konsoli w pliku tekstowym.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli](media/capture-browser-trace/edge-console-select.png)

1. Pakuj plik HAR, dane wyjściowe konsoli i nagrywanie ekranu w skompresowanym formacie, na przykład. zip, i udostępnij go z pomocą techniczną firmy Microsoft.

## <a name="apple-safari"></a>Apple Safari

Poniższe kroki pokazują, jak używać narzędzi deweloperskich w programie Apple Safari. Aby uzyskać więcej informacji, zobacz [omówienie narzędzia deweloperskie Safari](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Włącz narzędzia deweloperskie w przeglądarce Safari firmy Apple:

    1. Wybierz opcję **Safari**, a następnie wybierz pozycję **Preferences (Preferencje**).

        ![Zrzut ekranu preferencji Safari](media/capture-browser-trace/safari-preferences.png)

    1. Wybierz kartę **Zaawansowane** , a następnie wybierz pozycję **Pokaż menu rozwijania na pasku menu**.

        ![Zrzut ekranu przedstawiający zaawansowane Preferencje programu Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Ważne jest, aby zalogować się _przed_ rozpoczęciem śledzenia, tak aby ślad nie zawierał informacji poufnych związanych z logowaniem. 

1. Rozpocznij rejestrowanie kroków, które należy wykonać w portalu. Aby uzyskać więcej informacji, zobacz [jak zarejestrować ekran na komputerze Mac](https://support.apple.com/HT208721).

1. W portalu przejdź do kroku tuż przed wystąpieniem problemu.

1. Wybierz pozycję **programowanie**, a następnie wybierz pozycję **Pokaż inspektora sieci Web**.

    ![Zrzut ekranu przedstawiający "Pokaż inspektora sieci Web"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Domyślnie przeglądarka przechowuje informacje o śledzeniu tylko dla aktualnie załadowanej strony. Ustaw poniższe opcje, aby przeglądarka zachował wszystkie informacje o śledzeniu, nawet jeśli Odtwórz wymaga przechodzenia do więcej niż jednej strony:

    1. Wybierz kartę **Sieć** , a następnie wybierz pozycję **Zachowaj dziennik**.

          ![Zrzut ekranu przedstawiający "zachowywanie dziennika"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Wybierz kartę **konsola** , a następnie wybierz pozycję **Zachowaj dziennik**.

          ![Zrzut ekranu przedstawiający "zachowywanie dziennika"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Wybierz kartę **Sieć** , a następnie wybierz pozycję **Wyczyść elementy sieciowe**.

    ![Zrzut ekranu przedstawiający "Czyszczenie elementów sieci"](media/capture-browser-trace/safari-clear-session.png)

1. Odtwórz problem w portalu. Zostaną wyświetlone dane wyjściowe sesji podobne do poniższej ilustracji.

    ![Zrzut ekranu przedstawiający wyniki śledzenia przeglądarki](media/capture-browser-trace/safari-browser-trace-results.png)

1. Po ponownym utworzeniu nieoczekiwanego zachowania portalu wybierz pozycję **Eksportuj** i Zapisz plik.

    ![Zrzut ekranu przedstawiający "Export"](media/capture-browser-trace/safari-network-export-har.png)

1. Zatrzymaj rejestrator ekranu i Zapisz plik.

1. W okienku narzędzia deweloperskie przeglądarki wybierz kartę **konsola** i rozwiń okno. Umieść kursor na początku danych wyjściowych w konsoli, a następnie przeciągnij i zaznacz całą zawartość danych wyjściowych. Użyj polecenia-C, aby skopiować dane wyjściowe i zapisać je w pliku tekstowym.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli](media/capture-browser-trace/safari-console-select.png)

1. Pakuj plik HAR, dane wyjściowe konsoli i nagrywanie ekranu w skompresowanym formacie, na przykład. zip, i udostępnij go z pomocą techniczną firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

[Azure Portal — omówienie](azure-portal-overview.md)