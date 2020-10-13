---
title: Diagnostyka wydajności dla maszyn wirtualnych platformy Azure | Microsoft Docs
description: Wprowadzenie do usługi Azure Performance Diagnostics dla systemu Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 04b70e593e8b1bee8beb72ac88bc8441bc38bb9a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963248"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnostyka wydajności maszyn wirtualnych platformy Azure

Narzędzie Diagnostyka wydajności pomaga rozwiązywać problemy z wydajnością, które mogą mieć wpływ na maszynę wirtualną z systemem Windows lub Linux. Obsługiwane scenariusze rozwiązywania problemów obejmują szybkie sprawdzanie znanych problemów i najlepszych rozwiązań oraz złożone problemy, które obejmują niską wydajność maszyn wirtualnych lub duże użycie procesora CPU, miejsca na dysku lub pamięci.

Diagnostykę wydajności można uruchomić bezpośrednio z poziomu Azure Portal, w którym można także przeglądać szczegółowe informacje i raporty dotyczące różnych dzienników, bogatych konfiguracji i danych diagnostycznych. Zalecamy uruchomienie diagnostyki wydajności i przejrzenie szczegółowych informacji i danych diagnostycznych przed skontaktowaniem się z pomoc techniczna firmy Microsoft.

> [!NOTE]
> W przypadku systemu Windows Diagnostyka wydajności jest obecnie obsługiwana na maszynach wirtualnych z zainstalowanym zestawem SDK .NET w wersji 4,5 lub nowszej. Aby uzyskać instrukcje uruchamiania diagnostyki wydajności na klasycznych maszynach wirtualnych, zobacz [rozszerzenie maszyny wirtualnej diagnostyki wydajności Azure](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

### <a name="windows"></a>Windows

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 z dodatkiem R2
* Windows Server 2012
* Windows Server 2008 z dodatkiem R2
* Windows 10
* Windows 8.1
* Windows 8

### <a name="linux"></a>Linux

- Obecnie obsługiwane są następujące dystrybucje:

    | Dystrybucja               | Wersja                                         |
    |----------------------------|-------------------------------------------------|
    | Serwer Oracle Linux        | 6,10 [ `*` ], 7,3, 7,6, 7,5 |
    | CentOS                     | 6,5 [ `*` ], 7,6                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [ `*` ]                               |
    | Ubuntu                     | 14,04, 16,04, 18,04, 20,04                               |
    | Debian                     | 8, 9, 10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Zapoznaj się ze [znanymi problemami](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Instalowanie i uruchamianie diagnostyki wydajności na maszynie wirtualnej

Diagnostyka wydajności instaluje rozszerzenie maszyny wirtualnej z uruchomionym narzędziem diagnostycznym o nazwie że program perfinsights. Że program perfinsights jest dostępny dla [systemów Windows](./how-to-use-perfinsights.md) i [Linux](./how-to-use-perfinsights-linux.md). Aby zainstalować i uruchomić diagnostykę wydajności, wykonaj następujące kroki:

1. W lewej kolumnie poleceń wybierz pozycję **maszyny wirtualne**.
1. Z listy nazw maszyn wirtualnych wybierz maszynę wirtualną, na której chcesz przeprowadzić diagnostykę.
1. W prawej kolumnie poleceń wybierz pozycję **Diagnostyka wydajności**.

    ![Zrzut ekranu przedstawiający Azure Portal, z wyróżnionym przyciskiem Zainstaluj diagnostykę wydajności](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Na tym zrzucie ekranu blok nazw maszyn wirtualnych jest ukryty.
1. Wybierz konto magazynu (opcjonalnie)

    Jeśli chcesz użyć jednego konta magazynu do przechowywania wyników diagnostyki wydajności dla wielu maszyn wirtualnych, możesz wybrać konto magazynu, klikając przycisk **Ustawienia** na pasku narzędzi. Kliknij przycisk **OK** po wybraniu konta magazynu.

    Jeśli nie określisz konta magazynu, domyślnie zostanie utworzone nowe konto magazynu.

    ![Zrzut ekranu przedstawiający blok diagnostyki wydajności z wyróżnionym przyciskiem paska narzędzi ustawień](media/performance-diagnostics/settings-button.png)

    ![Zrzut ekranu przedstawiający wybór konta magazynu z bloku ustawień diagnostyki wydajności](media/performance-diagnostics/select-storage-account.png)

1. Wybierz przycisk **Zainstaluj diagnostykę wydajności** .
1. Zaznacz pole wyboru **Uruchom diagnostykę** , jeśli chcesz uruchomić diagnostykę po zakończeniu instalacji. Jeśli wybierzesz tę opcję, będzie można wybrać scenariusz analizy wydajności i powiązane opcje.

    ![Zrzut ekranu przedstawiający przycisk instalacji diagnostyki wydajności](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Wybierz scenariusz analizy do uruchomienia

Poniższe scenariusze analizy są dostępne w Azure Portal. Wybierz analizę, w zależności od występującego problemu z wydajnością. Wybierz opcje czasu trwania i śledzenia w zależności od potrzeb analizy.

* **Analiza szybkiej wydajności**  
    Sprawdza znane problemy, analizuje najlepsze rozwiązania i zbiera dane diagnostyczne. Wykonanie tej analizy trwa kilka minut. Dowiedz się więcej o [systemie Windows](./how-to-use-perfinsights.md) lub [Linux](./how-to-use-perfinsights-linux.md)

* **Analiza wydajności**  
    Obejmuje wszystkie kontrole szybkiej analizy wydajności i monitoruje duże zużycie zasobów. Ta wersja służy do rozwiązywania ogólnych problemów z wydajnością, takich jak duże użycie procesora CPU, pamięci i dysku. Ta analiza trwa 30 sekund do 15 minut, w zależności od wybranego czasu trwania. Dowiedz się więcej o [systemie Windows](./how-to-use-perfinsights.md) lub [Linux](./how-to-use-perfinsights-linux.md)

* **Zaawansowana analiza wydajności**`*`  
    Obejmuje wszystkie operacje sprawdzania w analizie wydajności i zbiera jeden lub więcej śladów, jak opisano w poniższych sekcjach. Ten scenariusz umożliwia rozwiązywanie złożonych problemów wymagających dodatkowych śladów. Uruchomienie tego scenariusza dla dłuższych okresów spowoduje zwiększenie całkowitego rozmiaru danych wyjściowych diagnostyki, w zależności od rozmiaru maszyny wirtualnej i wybranych opcji śledzenia. Wykonanie tej analizy trwa 30 sekund do 15 minut, w zależności od wybranego czasu trwania. [Dowiedz się więcej](./how-to-use-perfinsights.md)

* **Analiza Azure Files**`*`  
    Obejmuje wszystkie sprawdzenia w analizie wydajności i przechwytuje śledzenie sieci i liczniki protokołu SMB. Ten scenariusz umożliwia rozwiązywanie problemów z wydajnością usługi Azure Files. Wykonanie tej analizy trwa 30 sekund do 15 minut, w zależności od wybranego czasu trwania. [Dowiedz się więcej](./how-to-use-perfinsights.md)

>[!Note]
>[ `*` ] Te scenariusze analizy są obsługiwane tylko w systemie Windows.

![Zrzut ekranu przedstawiający okienko uruchamiania diagnostyki w bloku diagnostyki wydajności](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Podaj objawy (opcjonalnie)

Wybierz z listy wszystkie wybrane objawy lub Dodaj nowe objawy. Dzięki temu możemy ulepszyć analizę w przyszłości.

### <a name="provide-support-request-number-if-available-optional"></a>Podaj numer żądania pomocy technicznej, jeśli jest dostępny (opcjonalnie)

Jeśli pracujesz z specjalistą pomocy technicznej firmy Microsoft w ramach istniejącego biletu pomocy technicznej, podaj numer biletu pomocy technicznej.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Przejrzyj zasady ochrony prywatności i postanowienia prawne, a następnie zaznacz pole wyboru, aby potwierdzić (wymagane)

Aby uruchomić diagnostykę, należy wyrazić zgodę na postanowienia prawne i zaakceptować zasady ochrony prywatności.

### <a name="select-ok-to-run-the-diagnostics"></a>Wybierz przycisk OK, aby uruchomić diagnostykę

Zostanie wyświetlone powiadomienie z informacją o ponownym uruchomieniu diagnostyki wydajności. Po zakończeniu instalacji zostanie wyświetlone powiadomienie z informacją o pomyślnym zakończeniu instalacji. Wybrana analiza jest następnie uruchamiana przez określony czas. Jest to dobry moment, aby odtworzyć problem z wydajnością, aby dane diagnostyczne mogły być przechwytywane w odpowiednim czasie.

Po zakończeniu analizy następujące elementy są przekazywane do tabel platformy Azure i kontenera obiektów binarnych (BLOB) na określonym koncie magazynu:

* Wszystkie szczegółowe dane i powiązane informacje o przebiegu
* Plik skompresowany (zip) (o nazwie **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip** ) w systemie Windows i plik kulki pułapki (o nazwie **PerformanceDiagnostics_yyyy-mm-DD_HH-mm-SS-FFF. tar. gz** ) w systemie Linux, który zawiera pliki dziennika
* Raport HTML

Po przekazaniu nowy raport diagnostyczny zostanie wyświetlony w Azure Portal.

![Zrzut ekranu przedstawiający listę raportów diagnostycznych w bloku diagnostyki wydajności](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Jak zmienić ustawienia diagnostyki wydajności

Użyj przycisku **paska narzędzi** , aby zmienić konto magazynu, w którym można przechowywać dane diagnostyczne i dane wyjściowe. Możesz użyć tego samego konta magazynu dla wielu maszyn wirtualnych korzystających z diagnostyki wydajności. Gdy zmienisz konto magazynu, stare raporty i szczegółowe informacje nie zostaną usunięte. Nie będą jednak już wyświetlane na liście raportów diagnostycznych.

## <a name="review-insights-and-performance-diagnostics-report"></a>Przejrzyj szczegółowe informacje i Raport diagnostyki wydajności

Każdy przebieg diagnostyczny zawiera listę szczegółowych informacji i zaleceń, odpowiednich zasobów, plików dziennika i innych zebranych gromadzonych danych diagnostycznych oraz raport do wyświetlania w trybie offline. Aby uzyskać pełną listę wszystkich zebranych danych diagnostycznych, zobacz **jakiego rodzaju informacje są zbierane przez że program perfinsights?** w [systemie Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) lub [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights).

### <a name="select-a-performance-diagnostics-report"></a>Wybierz Raport diagnostyki wydajności

Możesz użyć listy raport diagnostyczny, aby znaleźć wszystkie raporty diagnostyczne, które zostały uruchomione. Lista zawiera szczegóły dotyczące analizy, która została użyta, szczegółowe informacje, które zostały znalezione i ich poziomy wpływ. Wybierz wiersz, aby wyświetlić więcej szczegółów.

![Zrzut ekranu przedstawiający Wybieranie raportu diagnostycznego z bloku Diagnostyka wydajności](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Przejrzyj raport diagnostyki wydajności

Każdy raport diagnostyki wydajności może zawierać kilka szczegółowych informacji i wskazywać poziom wpływu na wysoki, średni lub niski. Każdy wgląd zawiera również zalecenia, które pozwolą zmniejszyć istotność. Szczegółowe informacje są pogrupowane w celu ułatwienia filtrowania.

Poziomy wpływu reprezentują potencjalne problemy z wydajnością w oparciu o takie czynniki jak błędna konfiguracja, znane problemy lub problemy zgłaszane przez innych użytkowników. Być może nie wystąpił jeszcze jeden lub więcej z wymienionych problemów. Na przykład pliki dzienników SQL i pliki bazy danych mogą znajdować się na tym samym dysku danych. Ten stan ma duże znaczenie dla wąskich gardeł i innych problemów z wydajnością, jeśli użycie bazy danych jest wysokie, podczas gdy użycie jest niskie.

![Zrzut ekranu przedstawiający blok przegląd raportu diagnostyki wydajności](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Przeglądanie szczegółowych informacji i zaleceń dotyczących diagnostyki wydajności

Możesz wybrać wgląd, aby wyświetlić więcej szczegółów na temat zasobów, których dotyczy problem, sugerowane ograniczenia i linki odwołań.

![Zrzut ekranu przedstawiający szczegółowe informacje dotyczące diagnostyki wydajności](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Pobierz i Przejrzyj raport diagnostyczny pełnej wydajności

Możesz użyć przycisku **Pobierz raport** , aby pobrać raport HTML zawierający dodatkowe rozbudowane informacje diagnostyczne, takie jak Konfiguracja magazynu i sieci, liczniki wydajności, ślady, lista procesów i dzienniki. Zawartość zależy od wybranej analizy. W celu zaawansowania rozwiązywania problemów raport może zawierać dodatkowe informacje i wykresy interaktywne, które są związane z dużym użyciem procesora CPU, dużym użyciem dysku i procesami, które zużywają nadmierną ilość pamięci. Aby uzyskać więcej informacji na temat raportu diagnostyki wydajności, zobacz [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) lub [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Zarządzanie raportami diagnostyki wydajności

Można usunąć co najmniej jeden raport diagnostyki wydajności za pomocą przycisku **Usuń raport** .

## <a name="how-to-uninstall-performance-diagnostics"></a>Jak odinstalować diagnostykę wydajności

Można odinstalować diagnostykę wydajności z maszyny wirtualnej. Ta akcja spowoduje usunięcie rozszerzenia maszyny wirtualnej, ale nie ma wpływu na żadne dane diagnostyczne znajdujące się na koncie magazynu.

![Zrzut ekranu przedstawiający pasek narzędzi bloku diagnostyki wydajności z wyróżnionym przyciskiem Odinstaluj](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Gdzie są przechowywane dane diagnostyczne z mojej maszyny wirtualnej

Wszystkie szczegółowe informacje i raporty dotyczące diagnostyki wydajności są przechowywane na Twoim koncie magazynu. Szczegółowe informacje są przechowywane w tabelach platformy Azure. Skompresowany plik raportów jest przechowywany w kontenerze obiektów binarnych (BLOB) o nazwie azdiagextnresults.

Informacje o koncie magazynu można wyświetlić za pomocą przycisku Ustawienia na pasku narzędzi.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Jak mogę udostępniać te dane z obsługą klienta firmy Microsoft

Istnieje wiele sposobów udostępniania raportu diagnostyki firmie Microsoft.

**Opcja 1:** Automatycznie Udostępnij najnowszy raport  
Po otwarciu biletu pomocy technicznej w firmie Microsoft ważne jest, aby udostępnić Raport diagnostyki wydajności. Jeśli wybrano opcję udostępnienia tych informacji firmie Microsoft podczas uruchamiania diagnostyki (zaznaczając pole wyboru "**Zgadzam się na udostępnianie informacji diagnostycznych firmie Microsoft**"), firma Microsoft będzie mogła uzyskać dostęp do raportu z konta magazynu przy użyciu linku SAS do wyjściowego pliku zip przez maksymalnie 30 dni od daty uruchomienia. Tylko najnowszy raport jest dostępny dla inżyniera pomocy technicznej.

**Opcja 2:** Generuj sygnaturę dostępu współdzielonego dla skompresowanego pliku raportu diagnostyki  
Możesz udostępnić link do skompresowanego pliku raportów przy użyciu sygnatur dostępu współdzielonego. W tym celu wykonaj następujące kroki:

1. W Azure Portal przejdź do konta magazynu, w którym są przechowywane dane diagnostyczne.
1. Wybierz pozycję **obiekty blob** w sekcji **BLOB Service** .
1. Wybierz kontener **azdiagextnresults** .
1. Wybierz skompresowany plik wyjściowy diagnostyki wydajności, który chcesz udostępnić.
1. Na karcie **generowanie SAS** Wybierz kryteria udostępniania.
1. Kliknij pozycję **Generuj token SAS i adres URL obiektu BLOB**.
1. Skopiuj **adres URL sygnatury dostępu współdzielonego obiektu BLOB**i udostępnij go inżynierowi pomocy technicznej.

**Opcja 3:** Pobierz Raport z konta magazynu

Plik skompresowany Raport diagnostyki wydajności można również znaleźć w sekcji kroki 1 – 4 w opcji 2. Wybierz, aby pobrać plik, a następnie Udostępnij go za pośrednictwem poczty e-mail lub skontaktuj się z inżynierem pomocy technicznej, aby uzyskać instrukcje dotyczące przekazywania pliku.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Jak mogę przechwycić dane diagnostyczne o odpowiednim czasie

Każdy przebieg diagnostyki wydajności ma dwa etapy:

1. Zainstaluj lub zaktualizuj rozszerzenie maszyny wirtualnej diagnostyki wydajności.
1. Uruchom diagnostykę przez określony czas.

Obecnie nie istnieje łatwy sposób, aby dokładnie poznać, kiedy instalacja rozszerzenia maszyny wirtualnej została ukończona. Zwykle trwa około 45 sekund od 1 minuty, aby zainstalować rozszerzenie maszyny wirtualnej. Po zainstalowaniu rozszerzenia maszyny wirtualnej można uruchomić kroki Odtwórz, aby Diagnostyka wydajności przechwytuje poprawny zestaw danych na potrzeby rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu szczegółowych informacji i raportu dotyczącego diagnostyki wydajności, jeśli nadal nie możesz ustalić przyczyny problemu i potrzebujesz więcej pomocy, możesz otworzyć bilet pomocy technicznej w ramach pomocy technicznej firmy Microsoft.

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).