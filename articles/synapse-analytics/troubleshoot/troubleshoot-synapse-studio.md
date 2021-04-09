---
title: Rozwiązywanie problemów z programem Synapse Studio
description: Rozwiązywanie problemów z usługą Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7d91001024ad547e52fe48ee30749fee9a4fb4a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116181"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure Synapse Studio

Ten przewodnik rozwiązywania problemów zawiera instrukcje dotyczące tego, jakie informacje należy podać podczas otwierania biletu pomocy technicznej dotyczącej problemów z łącznością sieciową. Mając odpowiednie informacje, możemy szybko rozwiązać ten problem.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Problem z łącznością usługi puli SQL bezserwerowej

### <a name="symptom-1"></a>Objaw 1

Opcja "bezserwerowa Pula SQL" jest wyszarzona na liście rozwijanej "Połącz z".

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Objaw 2

Uruchomienie zapytania z opcją "bezserwerowa Pula SQL" powoduje wyświetlenie komunikatu o błędzie "nie można nawiązać połączenia z serwerem".

![Objaw 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

> [!NOTE] 
>    Poniższe kroki rozwiązywania problemów dotyczą programu chrom Edge i przeglądarki Chrome. Możesz użyć innych przeglądarek (takich jak FireFox) z tymi samymi krokami rozwiązywania problemów, ale okno "Narzędzie programistyczne" może mieć inny układ od zrzutów ekranu w tym TSG. Jeśli to możliwe, nie używaj klasycznej krawędzi do rozwiązywania problemów, ponieważ w niektórych sytuacjach może być widoczne niedokładne informacje.

Otwórz panel "informacje diagnostyczne", wybierz przycisk "Pobierz diagnostykę". Zachowaj pobrane informacje na potrzeby raportowania błędów. Zamiast tego możesz skopiować "Identyfikator sesji" i dołączyć go podczas otwierania biletu pomocy technicznej.

![Diagnostyka — informacje](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Aby rozpocząć rozwiązywanie problemów, ponów próbę wykonania operacji w usłudze Azure Synapse Studio.

- W przypadku symptomu 1 Wybierz przycisk "Odśwież" z prawej strony listy rozwijanej "Użyj bazy danych" na karcie "skrypt SQL" i sprawdź, czy widzisz polecenie "bezserwerowa Pula SQL".
- W przypadku symptomu 2 spróbuj ponownie uruchomić zapytanie, aby sprawdzić, czy zostało wykonane pomyślnie.

Jeśli problem nadal istnieje, naciśnij klawisz F12 w przeglądarce, aby otworzyć okno "Narzędzia deweloperskie" (DevTools).

W oknie "Narzędzia deweloperskie" przejdź do panelu "Sieć". W razie potrzeby wybierz przycisk "Wyczyść" na pasku narzędzi w panelu "Sieć".
Upewnij się, że opcja "Wyłącz pamięć podręczną" w panelu "Sieć" została sprawdzona.

Spróbuj ponownie wykonać operację wykonywaną w usłudze Azure Synapse Studio. Na liście "Sieć" w obszarze "Narzędzia deweloperskie" mogą pojawić się nowe elementy. Zanotuj bieżącą godzinę systemową, która ma zostać zadana w ramach biletu pomocy technicznej.

![Panel sieci 1](media/troubleshooting-synapse-studio/network-panel.png)

Znajdź element, którego kolumna URL pasuje do następującego wzorca:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Gdzie [*A*] jest nazwą obszaru roboczego, a "-OnDemand" może mieć wartość "-sqlod", gdzie [*B*] powinna być nazwą bazy danych, taką jak "Master". Powinien istnieć co najwyżej dwa elementy o tej samej wartości adresu URL, ale różne wartości metod; Opcje i POST. Sprawdź, czy te dwa elementy mają wartość "200" lub "20x" w kolumnie Stan, gdzie "x" może być dowolną pojedynczą cyfrą.

Jeśli jeden z nich ma coś innego niż "20x" i:

- Stan rozpoczyna się od "(niepowodzenie)", poszerzyć kolumnę "status" (stan) lub umieść wskaźnik myszy nad tekstem stanu, aby zobaczyć kompletny tekst. Podczas otwierania biletu pomocy technicznej Dołącz tekst i/lub zrzut ekranu.

    ![tekst stanu](media/troubleshooting-synapse-studio/status-text.png)

    - Jeśli zobaczysz ERR_NAME_NOT_RESOLVED i utworzysz obszar roboczy w ciągu 10 minut, odczekaj 10 minut, a następnie ponów próbę sprawdzenia, czy problem nadal istnieje.
    - Jeśli widzisz ERR_INTERNET_DISCONNECTED lub ERR_NETWORK_CHANGED, może to oznaczać, że połączenie z siecią komputera ma problemy. Sprawdź połączenie sieciowe i spróbuj ponownie wykonać operację.
    - Jeśli widzisz ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR lub inne kody błędów zawierające "SSL", może to oznaczać, że konfiguracja lokalnego protokołu SSL ma problemy, lub administrator sieci zablokował dostęp do serwera puli SQL bez serwera. Otwórz bilet pomocy technicznej i Dołącz kod błędu w opisie.
    - Jeśli zobaczysz ERR_NETWORK_ACCESS_DENIED, może być konieczne skontaktowanie się z administratorem w sprawie tego, czy lokalne zasady zapory mają zablokowany dostęp do domeny *. database.windows.net, czy portu zdalnego 1443.
    - Opcjonalnie spróbuj natychmiast wykonać tę samą operację na innym komputerze i/lub w środowisku sieciowym, aby wykluczyć problem z konfiguracją sieci na komputerze.

- Stan to "40x", "50x" lub inne liczby, wybierz elementy, aby wyświetlić szczegóły. Szczegóły elementu powinny być widoczne po prawej stronie. Znajdź sekcję "nagłówek odpowiedzi"; następnie sprawdź, czy istnieje element o nazwie "Access-Control-Allow-Origin". Jeśli tak, sprawdź, czy ma jedną z następujących wartości:

    - `*` (pojedyncza gwiazdka)
    - https://web.azuresynapse.net/ (lub inna wartość, z której zaczyna się tekst na pasku adresu przeglądarki).

Jeśli nagłówek odpowiedzi zawiera jedną z powyższych wartości, firma Microsoft powinna już zebrać informacje o niepowodzeniu. W razie konieczności można otworzyć bilet pomocy technicznej i opcjonalnie dołączyć zrzut ekranu przedstawiający szczegóły elementu.

Jeśli nie widzisz nagłówka lub nagłówek nie ma jednej z wartości wymienionych powyżej, Dołącz zrzut ekranu przedstawiający szczegóły elementu, gdy otworzysz bilet.

 
![Szczegóły elementu](media/troubleshooting-synapse-studio/item-details.png)
 
Jeśli powyższe kroki nie rozwiązują problemu, może być konieczne otwarcie biletu pomocy technicznej. Podczas przesyłania biletu pomocy technicznej należy uwzględnić "Identyfikator sesji" lub "informacje diagnostyczne" pobrane na początku tego przewodnika.

Podczas zgłaszania problemu możesz opcjonalnie wykonać zrzut ekranu karty "konsola" w "Narzędzia deweloperskie" i dołączyć go do biletu pomocy technicznej. Przewiń zawartość i w razie potrzeby podejmij więcej niż jeden zrzut ekranu, aby przechwycić cały komunikat.

![Konsola narzędzi dla deweloperów](media/troubleshooting-synapse-studio/developer-tool-console.png)

W przypadku dołączania zrzutów ekranu podaj czas (lub przewidywany zakres czasu) w przypadku wypróbowania zrzutów ekranu. Pomożemy nam podczas wyszukiwania problemu.

Niektóre przeglądarki obsługują wyświetlanie sygnatur czasowych na karcie "konsola". W przypadku opcji chrom Edge/Chrome Otwórz okno dialogowe "Ustawienia" w obszarze "Narzędzia deweloperskie" i sprawdź pozycję "Pokaż sygnatury czasowe" na karcie "Preferencje".

![Ustawienia konsoli narzędzia deweloperskiego](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![Pokaż sygnaturę czasową](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Następne kroki
Jeśli poprzednie kroki nie pomogą rozwiązać problemu [, Utwórz bilet pomocy technicznej](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)