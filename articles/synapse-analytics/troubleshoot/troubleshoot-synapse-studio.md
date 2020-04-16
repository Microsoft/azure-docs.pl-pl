---
title: Rozwiązywanie problemów z programem Azure Synapse Studio (wersja zapoznawcza)
description: Rozwiązywanie problemów z programem Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431321"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Rozwiązywanie problemów z programem Azure Synapse Studio (wersja zapoznawcza)

Ten przewodnik rozwiązywania problemów zawiera instrukcje dotyczące informacji, jakie należy podać podczas otwierania biletu pomocy technicznej w przypadku problemów z łącznością sieciową. Dzięki odpowiednim informacjom możemy szybciej rozwiązać problem.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>Problem z łącznością usługi SQL na żądanie (wersja zapoznawcza)

### <a name="symptom-1"></a>Objaw 1

Opcja "SQL na żądanie" jest wyszarzona w rozwijaniu "Połącz się z".

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Objaw 2

Uruchomienie kwerendy z "SQL na żądanie" daje komunikat o błędzie "Nie można ustanowić połączenia z serwerem".

![symptom2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

> [!NOTE] 
>    Poniższe kroki rozwiązywania problemów są dla Chromium Edge i Chrome. Możesz użyć innych przeglądarek (takich jak FireFox) z tymi samymi krokami rozwiązywania problemów, ale okno "Narzędzie deweloperskie" może mieć inny układ niż zrzuty ekranu w tym TSG. Jeśli to możliwe, NIE używaj klasycznej krawędzi do rozwiązywania problemów, ponieważ może to pokazać niedokładne informacje w określonej sytuacji.

Otwórz panel "Informacje diagnostyczne", wybierz przycisk "Pobierz diagnostykę". Zachowaj pobrane informacje dotyczące raportowania błędów. Zamiast tego możesz skopiować "Identyfikator sesji" i dołączyć go podczas otwierania biletu pomocy technicznej.

![diagnostyka-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Aby rozpocząć rozwiązywanie problemów, ponów próbę wykonania operacji w usłudze Azure Synapse Studio.

- W przypadku symptomu 1 wybierz przycisk "Odśwież" po prawej stronie listy rozwijanej "Użyj bazy danych" na karcie "Skrypt SQL" i sprawdź, czy widzisz "SQL on-demand".
- W przypadku symptomu 2 spróbuj ponownie uruchomić kwerendę, aby sprawdzić, czy została pomyślnie wykonana.

Jeśli problem nadal występuje, naciśnij klawisz F12 w przeglądarce, aby otworzyć "Narzędzia programistyczne" (DevTools).

W oknie "Narzędzia programistyczne" przełącz się do panelu "Sieć". W razie potrzeby wybierz przycisk "Wyczyść" na pasku narzędzi w panelu "Sieć".
Upewnij się, że w panelu "Sieć" jest zaznaczona opcja "Wyłącz pamięć podręczną".

Ponów próbę wykonania operacji w usłudze Azure Synapse Studio. Nowe elementy mogą być wyświetlane na liście "Sieć" w "Narzędzia programistyczne". Zanotuj bieżący czas systemowy, aby zapewnić w bilecie pomocy technicznej.

![panel sieciowy](media/troubleshooting-synapse-studio/network-panel.png)

Znajdź element, którego kolumna Url pasuje do następującego wzorca:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Gdzie [*A*] jest nazwą obszaru roboczego, a "-ondemand" może być "-sqlod" i gdzie [*B*] powinna być nazwą bazy danych, taką jak "master". Powinny istnieć co najwyżej dwa elementy o tej samej wartości adresu URL, ale różne wartości metody; OPCJE i POST. Sprawdź, czy te dwa elementy mają "200" lub "20x" w kolumnie stanu, gdzie "x" może być dowolną pojedynczą cyfrą.

Jeśli któryś z nich ma coś innego niż "20x" i:

- stan zaczyna się od "(nie powiodło się)", albo poszerzyć kolumnę "Stan" lub najedź wskaźnikem na tekst stanu, aby zobaczyć pełny tekst. Dołącz tekst i/lub zrzut ekranu podczas otwierania biletu pomocy technicznej.

    ![tekst stanu](media/troubleshooting-synapse-studio/status-text.png)

    - Jeśli widzisz ERR_NAME_NOT_RESOLVED i utworzono obszar roboczy w ciągu 10 minut, odczekaj 10 minut i ponów próbę, aby zobaczyć, czy problem nadal istnieje.
    - Jeśli widzisz ERR_INTERNET_DISCONNECTED lub ERR_NETWORK_CHANGED, może to oznaczać, że połączenie z siecią komputera ma problemy. Sprawdź połączenie sieciowe i ponów próbę wykonania operacji.
    - Jeśli widzisz ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR lub inne kody błędów zawierające "SSL", może to oznaczać, że lokalna konfiguracja SSL ma problemy lub administrator sieci zablokował dostęp do serwera SQL na żądanie. Otwórz bilet pomocy technicznej i dołącz kod błędu w opisie.
    - Jeśli widzisz ERR_NETWORK_ACCESS_DENIED, może być konieczne sprawdzenie u administratora, czy lokalne zasady zapory zablokowały dostęp do domeny *.database.windows.net, czy do portu zdalnego 1443.
    - Opcjonalnie spróbuj natychmiast wykonać tę samą operację na innym komputerze i/lub środowisku sieciowym, aby wykluczyć problem z konfiguracją sieci na komputerze.

- stan to "40x", "50x" lub inne numery, wybierz na pozycji(-ach), aby zobaczyć szczegóły. Szczegóły elementu powinny być widoczne po prawej stronie. Znajdź sekcję "Nagłówek odpowiedzi"; następnie sprawdź, czy element o nazwie "access-control-allow-origin" istnieje. Jeśli tak, sprawdź, czy ma jedną z następujących wartości:

    - `*`(pojedyncza gwiazdka)
    - https://web.azuresynapse.net/(lub inną wartość, od którą zaczyna się tekst na pasku adresu przeglądarki)

Jeśli nagłówek odpowiedzi zawiera jedną z powyższych wartości, oznacza to, że powinniśmy już zebrać informacje o niepowodzeniu. W razie potrzeby można otworzyć bilet pomocy technicznej i opcjonalnie dołączyć zrzut ekranu szczegółów elementu.

Jeśli nie widzisz nagłówka lub nagłówek nie ma jednej z wartości wymienionych powyżej, dołącz zrzut ekranu szczegółów elementu podczas otwierania biletu.

![szczegóły przedmiotu](media/troubleshooting-synapse-studio/item-details.png)

Jeśli powyższe kroki nie rozwiążą problemu, może być konieczne otwarcie biletu pomocy technicznej. Przesyłając bilet pomocy technicznej, dołącz "Identyfikator sesji" lub "Informacje diagnostyczne" pobrane na początku tego przewodnika.

Podczas zgłaszania problemu możesz opcjonalnie zrobić zrzut ekranu karty "Konsola" w "Narzędziach programistycznych" i dołączyć go do biletu pomocy technicznej. Przewiń zawartość i zrób więcej niż jeden zrzut ekranu, jeśli to konieczne, aby przechwycić całą wiadomość.

![developer-tool-console](media/troubleshooting-synapse-studio/developer-tool-console.png)

Jeśli dołączasz zrzuty ekranu, podaj czas (lub szacowany zakres czasu) czasu, kiedy zrobiłeś zrzuty ekranu. Pomoże nam to w przyjrzeniu się problemowi.

Niektóre przeglądarki obsługują wyświetlanie znaczników czasu na karcie "Konsola". W przypadku chromium Edge/Chrome otwórz okno dialogowe "Ustawienia" w "Narzędzia programistyczne" i zaznacz "Pokaż znaczniki czasu" w zakładce "Preferencje".

![ustawienia konsoli dewelopera-narzędzia](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![sygnatura czasowa pokazu](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Następne kroki
Jeśli poprzednie kroki nie pomagają rozwiązać problem [Tworzenie biletu pomocy technicznej](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
