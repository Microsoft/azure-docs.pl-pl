---
title: Monitorowanie kopii zapasowych za pomocą Eksploratora kopii zapasowych
description: W tym artykule opisano, jak używać Eksploratora kopii zapasowych do wykonywania monitorowania kopii zapasowych w czasie rzeczywistym w ramach magazynów, subskrypcji, regionów i dzierżawców.
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 78d3117d363059b6a298740f7b5daa31e10751c8
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294063"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorowanie kopii zapasowych za pomocą Eksploratora kopii zapasowych

Organizacje tworzące kopie zapasowe kolejnych i więcej maszyn w chmurze stają się coraz bardziej ważne do wydajnego monitorowania tych kopii zapasowych. Najlepszym sposobem na rozpoczęcie pracy jest użycie centralnej lokalizacji na potrzeby wyświetlania informacji operacyjnych w ramach dużej ilości danych.

Eksplorator kopii zapasowych jest wbudowanym Azure Monitor skoroszytem, który zapewnia Azure Backup klientom tę pojedynczą, centralną lokalizację. Eksplorator kopii zapasowych ułatwia monitorowanie działań operacyjnych w całej kopii zapasowej na platformie Azure, obejmującej dzierżawców, lokalizacje, subskrypcje, grupy zasobów i magazyny. W szerokim zakresie narzędzia do tworzenia kopii zapasowych zapewniają następujące możliwości:

* **Perspektywa w skali**: Uzyskaj Zagregowany widok elementów kopii zapasowych, zadań, alertów, zasad i zasobów, które nie zostały jeszcze skonfigurowane do tworzenia kopii zapasowych w całej nieruchomości.
* **Analiza przechodzenia do szczegółów**: Wyświetla szczegółowe informacje o poszczególnych zadaniach, alertach, zasadach i elementach kopii zapasowych, a wszystko to w jednym miejscu.
* **Interfejsy** z możliwością działania: po zidentyfikowaniu problemu można go rozwiązać, przechodząc do odpowiedniego elementu kopii zapasowej lub zasobu platformy Azure.

Te możliwości są udostępniane przez natywną integrację z usługą Azure Resource Graph i skoroszytami Azure Monitor.

> [!NOTE]
>
> * Eksplorator kopii zapasowych jest obecnie dostępny tylko dla danych maszyn wirtualnych platformy Azure.
> * Eksplorator kopii zapasowych jest przeznaczony dla działającego pulpitu nawigacyjnego do wyświetlania informacji o kopiach zapasowych w ciągu ostatnich 7 dni (maksimum).
> * Eksplorator kopii zapasowych nie jest obecnie obsługiwany w chmurach krajowych.
> * Obecnie Dostosowywanie szablonu Eksploratora kopii zapasowych nie jest obsługiwane.
> * Nie zalecamy pisania niestandardowych automatyzacji w danych grafu zasobów platformy Azure.
> * Obecnie Eksplorator kopii zapasowych umożliwia monitorowanie kopii zapasowych w ramach maksymalnie 1000 subskrypcji (między dzierżawcami).

## <a name="get-started"></a>Rozpoczęcie pracy

Dostęp do Eksploratora usługi Backup można uzyskać, przechodząc do dowolnego magazynu Recovery Services i wybierając link **Eksplorator kopii zapasowych** w okienku **Przegląd** .

![Szybki link do magazynu](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Po wybraniu linku zostanie otwarty Eksplorator kopii zapasowych, który zapewnia Zagregowany widok dla wszystkich magazynów i subskrypcji, do których masz dostęp. Jeśli używasz konta usługi Azure Lighthouse, możesz wyświetlać dane między wszystkimi dzierżawcami, do których masz dostęp. Aby uzyskać więcej informacji, zobacz sekcję "widoki obejmujące wiele dzierżawców" na końcu tego artykułu.

![Strona docelowa Eksploratora kopii zapasowych](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Przypadki użycia Eksploratora kopii zapasowych

Eksplorator kopii zapasowych wyświetla wiele kart, z których każda zawiera szczegółowe informacje dotyczące określonego artefaktu kopii zapasowej (na przykład elementu kopii zapasowej, zadania lub zasad). Ta sekcja zawiera krótkie omówienie poszczególnych kart. Filmy wideo zapewniają przykładowe przypadki użycia dla każdego artefaktu kopii zapasowej wraz z opisami dostępnych kontrolek.

### <a name="the-summary-tab"></a>Karta Podsumowanie

Karta **Podsumowanie** zapewnia szybki przegląd z ogólnym warunkiem tworzenia kopii zapasowej. Można na przykład wyświetlić liczbę chronionych elementów, liczbę elementów, dla których ochrona nie została włączona lub ile zadań zakończyło się pomyślnie w ciągu ostatnich 24 godzin.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Karta elementy kopii zapasowej

Można filtrować i wyświetlać poszczególne elementy kopii zapasowej według subskrypcji, magazynu i innych właściwości. Wybierając nazwę elementu kopii zapasowej, można otworzyć okienko platformy Azure dla tego elementu. Na przykład, z tabeli, można zauważyć, że Ostatnia kopia zapasowa nie powiodła się dla elementu *X*. Zaznaczając pozycję *X*, można otworzyć okienko **kopia zapasowa** elementu, w którym można wyzwolić operację tworzenia kopii zapasowej na żądanie.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Karta zadania

Wybierz kartę **zadania** , aby wyświetlić szczegóły wszystkich zadań wyzwalanych w ciągu ostatnich 7 dni. Tutaj można filtrować według *operacji zadania*, *stanu zadania* i *kodu błędu* (dla zadań zakończonych niepowodzeniem).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Karta alerty

Wybierz kartę **alerty** , aby wyświetlić szczegóły wszystkich alertów, które zostały wygenerowane w Twoich magazynach w ciągu ostatnich 7 dni. Alerty można filtrować według typu (*Niepowodzenie kopii zapasowej* lub *Niepowodzenie przywracania*), bieżącego stanu (*aktywnego* lub *rozwiązanego*) i ważności (*krytyczne*, *Ostrzeżenie* lub *informacje*). Możesz również wybrać link, aby przejść do maszyny wirtualnej platformy Azure i podjąć wszelkie niezbędne działania.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Karta zasady

Możesz wybrać kartę **zasady** , aby wyświetlić najważniejsze informacje dotyczące wszystkich zasad tworzenia kopii zapasowych, które zostały utworzone w ramach danej kopii zapasowej. Można wyświetlić liczbę elementów skojarzonych z poszczególnymi zasadami oraz zakres przechowywania i częstotliwość tworzenia kopii zapasowych określonych przez zasady.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Karta kopia zapasowa nie jest włączona

Kopia zapasowa powinna być włączona dla wszystkich komputerów, które wymagają ochrony. Za pomocą Eksploratora kopii zapasowych Administratorzy programu Backup mogą szybko identyfikować, które maszyny w organizacji nie są jeszcze chronione przez kopię zapasową. Aby wyświetlić te informacje, wybierz kartę **kopia zapasowa niewłączona** .

W okienku **niewłączona kopia zapasowa** jest wyświetlana tabela z listą maszyn niechronionych. Organizacja może przypisywać różne znaczniki do maszyn produkcyjnych i maszyn testowych lub do maszyn, które obsługują różne funkcje. Ponieważ każda klasa maszyn wymaga oddzielnych zasad tworzenia kopii zapasowych, filtrowanie według tagów ułatwia przeglądanie informacji, które są specyficzne dla każdego z nich. Wybranie nazwy dowolnego komputera przekierowuje do okienka **Konfigurowanie kopii zapasowej** tego komputera, w którym można wybrać zastosowanie odpowiednich zasad tworzenia kopii zapasowych.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Eksportuj do programu Excel

Zawartość dowolnej tabeli lub wykresu można wyeksportować jako arkusz kalkulacyjny programu Excel. Zawartość zostanie wyeksportowana w postaci, w której zastosowano istniejące filtry. Aby wyeksportować dodatkowe wiersze tabeli, można zwiększyć liczbę wierszy, które mają być wyświetlane na stronie, za pomocą listy rozwijanej **wiersze na stronę** w górnej części każdej karty.

## <a name="pin-to-the-dashboard"></a>Przypnij do pulpitu nawigacyjnego

Możesz wybrać ikonę "PIN" w górnej części każdej tabeli lub wykresu, aby przypiąć ją do pulpitu nawigacyjnego Azure Portal. Przypinanie tych informacji ułatwia utworzenie dostosowanego pulpitu nawigacyjnego, który jest dostosowany do wyświetlania najważniejszych informacji.

## <a name="cross-tenant-views"></a>Widoki między dzierżawcami

Jeśli jesteś użytkownikiem usługi Azure Lighthouse z delegowanym dostępem do subskrypcji w wielu środowiskach dzierżawców, możesz użyć domyślnego filtru subskrypcji. Możesz wyświetlić subskrypcje, dla których chcesz wyświetlić dane, wybierając ikonę "filtr" w prawym górnym rogu Azure Portal. W przypadku korzystania z tej funkcji Program Backup Explorer agreguje informacje o wszystkich magazynach w wybranych subskrypcjach. Aby dowiedzieć się więcej, zobacz [co to jest usługa Azure Lighthouse?](../lighthouse/overview.md).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak używać Azure Monitor do uzyskiwania wglądu w dane kopii zapasowej](./backup-azure-monitoring-use-azuremonitor.md)
