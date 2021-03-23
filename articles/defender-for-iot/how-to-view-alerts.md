---
title: Filtrowanie alertów i zarządzanie nimi na stronie alertów
description: Wyświetlanie alertów zgodnie z różnymi kategoriami oraz korzystanie z funkcji wyszukiwania w celu ułatwienia wyszukiwania interesujących alertów.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/22/2021
ms.topic: how-to
ms.openlocfilehash: 391d1872124c7332bdaa6008a244490b47df4bf7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781589"
---
# <a name="filter-and-manage-alerts-from-the-alerts-page"></a>Filtrowanie alertów i zarządzanie nimi na stronie alertów 

W tym artykule opisano sposób wyświetlania alertów wyzwalanych przez czujnik i zarządzania nimi za pomocą narzędzi alertów.

Alerty można wyświetlać na podstawie różnych kategorii, takich jak alerty zarchiwizowane lub przypięte. Możesz również wyszukać alerty, na przykład alerty oparte na adresie IP lub MAC.  

Możesz również wyświetlać alerty z poziomu pulpitu nawigacyjnego czujnika.

Aby wyświetlić alerty:

- Z menu po lewej stronie wybierz pozycję **alerty** . W oknie alerty są wyświetlane alerty wykryte przez czujnik.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="Widok ekranu alertów.":::

## <a name="view-alerts-by-category"></a>Wyświetl alerty według kategorii

Alerty można wyświetlać zgodnie z różnymi kategoriami w widoku głównym **alertów** . Wybierz Alert, aby przejrzeć szczegóły i zarządzać wydarzeniem.

| Sortuj według typu | Opis |
|--|--|
| **Ważne alerty** | Alerty sortowane według ważności. |
| **Przypięte alerty** | Alerty, które użytkownik przypięto do dalszej analizy. Przypięte alerty nie są archiwizowane i są przechowywane przez 14 dni w przypiętym folderze. |
| **Ostatnie alerty** | Alerty sortowane według czasu. |
| **Potwierdzone alerty** | Alerty, które zostały potwierdzone i nieobsłużone, lub które zostały wyciszone i wywyciszone. |
| **Zarchiwizowane alerty** | Alerty automatycznie zarchiwizowane przez system. Tylko administrator może uzyskiwać do nich dostęp. |

## <a name="search-for-alerts-of-interest"></a>Wyszukaj alerty interesujące

Widok główny alertów zawiera różne funkcje wyszukiwania, które ułatwiają znalezienie alertów.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="Zrzut ekranu uczenia się.":::

### <a name="text-search"></a>Wyszukiwanie tekstu

Użyj opcji bezpłatne wyszukiwanie, aby wyszukać alerty według tekstu, cyfr lub znaków.

Aby wyszukać:

- Wpisz wymagany tekst w polu bezpłatne wyszukiwanie i naciśnij klawisz Enter na klawiaturze.

Aby wyczyścić wyszukiwanie:

- Usuń tekst w polu bezpłatne wyszukiwanie i naciśnij klawisz Enter na klawiaturze.

### <a name="device-group-or-device-ip-address-search"></a>Wyszukiwanie w grupie urządzeń lub adresie IP urządzenia

Wyszukaj alerty odwołujące się do określonych adresów IP lub grup urządzeń. Grupy urządzeń są tworzone na mapie urządzeń.

Aby użyć filtrów zaawansowanych:

1. Wybierz pozycję **filtry zaawansowane** w widoku głównym **alertów** .

2. Wybierz grupę urządzeń lub urządzenie.

3. Wybierz pozycję **Potwierdź**.

4. Aby wyczyścić wyszukiwanie, wybierz pozycję **Wyczyść wszystko**.

### <a name="security-versus-operational-alert-search"></a>Zabezpieczenia i wyszukiwanie alertów operacyjnych

Przełączenie między wyświetlaniem alertów operacyjnych i zabezpieczeń:

- Alerty **zabezpieczeń** reprezentują potencjalny ruch złośliwego oprogramowania, anomalie sieci, naruszenia zasad i naruszenia protokołów.

- Alerty **operacyjne** przedstawiają anomalie sieci, naruszenia zasad i naruszenia protokołu.

Gdy żadna z opcji nie zostanie wybrana, wyświetlane są wszystkie alerty.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="Zabezpieczenia na ekranie alertów.":::

## <a name="alert-page-options"></a>Opcje strony alertu

Komunikaty alertów udostępniają następujące akcje:

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: , aby potwierdzić alert.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: , aby usunąć potwierdzenie alertu.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: , aby przypiąć alert.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: , aby odpiąć alert.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: , aby potwierdzić wszystkie alerty.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: , aby poznać i potwierdzić wszystkie alerty.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: , aby wyeksportować informacje o alercie do pliku CSV. Użyj opcji **eksport alertów rozszerzonych** , aby wyeksportować informacje o alercie w oddzielnych wierszach dla każdego alertu obejmującego wiele urządzeń.

## <a name="alert-pop-up-window-options"></a>Opcje okna podręcznego alertu

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: ikonę, aby pobrać raport o alercie jako plik PDF.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: ikonę, aby pobrać plik PCAP. Plik można przeglądać za pomocą programu Wireshark i bezpłatnej analizatora protokołów sieciowych.

- Wybierz, :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: Aby pobrać filtrowany plik PCAP, który zawiera tylko odpowiednie pakiety alertów, co zmniejsza rozmiar pliku wyjściowego i pozwala na bardziej ukierunkowaną analizę. Plik można wyświetlić za pomocą programu Wireshark.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: ikonę, aby wyświetlić alert na osi czasu zdarzeń.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: ikonę, aby przypiąć alert.

- Wybierz :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: ikonę, aby odpiąć alert.

- Wybierz, :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: Aby zatwierdzić ruch (tylko analitycy zabezpieczeń i Administratorzy).

- Wybierz urządzenie, aby wyświetlić je na mapie urządzeń.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie zdarzeniem alertu](how-to-manage-the-alert-event.md)

[Przyspiesz przepływy pracy alertów](how-to-accelerate-alert-incident-response.md)
