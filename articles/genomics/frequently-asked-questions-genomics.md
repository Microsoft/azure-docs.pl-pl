---
title: Często zadawane pytania — często zadawane pytania
titleSuffix: Microsoft Genomics
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Microsoft Genomics, w tym informacje techniczne, umowę SLA i rozliczenia.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: 40fff49aa3ed46417cdd9a6190f928707e41accb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608616"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: typowe pytania

W tym artykule wymieniono najważniejsze zapytania, które mogą być powiązane z Microsoft Genomics. Aby uzyskać więcej informacji na temat usługi Microsoft Genomics, zobacz [co to jest Microsoft Genomics?](overview-what-is-genomics.md). Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz nasze [wskazówki dotyczące rozwiązywania problemów](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Jak mogę uruchomić przepływy pracy GATK4 na Microsoft Genomics?
W pliku config.txt usługi Microsoft Genomics Określ process_name `gatk4` . Pamiętaj, że opłaty są naliczane według zwykłych stawek za rozliczanie.

## <a name="how-do-i-enable-output-compression"></a>Jak mogę włączyć kompresji wyjściowej?
Można skompresować dane wyjściowe VCF lub gvcf za pomocą opcjonalnego argumentu kompresji danych wyjściowych. Jest to równoznaczne z uruchomieniem `-bgzip` , a następnie `-tabix` na danych wyjściowych VCF lub gvcf, do produkcji `.gz` (bgzip Output) i `.tbi` (Tabix Output). `bgzip` kompresuje plik VCF lub gvcf i `tabix` tworzy indeks dla skompresowanego pliku. Argument jest wartością logiczną, która jest `false` domyślnie ustawiona dla danych wyjściowych VCF i `true` Domyślnie dla danych wyjściowych gcvf. Aby użyć w wierszu polecenia, określ `-bz` lub `--bgzip-output` AS `true` (Run bgzip and Tabix) lub `false` . Aby użyć tego argumentu w pliku config.txt, Dodaj `bgzip_output: true` plik lub `bgzip_output: false` do pliku.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Co to jest umowa SLA dla Microsoft Genomics?
Gwarantujemy, że przez 99,9% czasu usługa Microsoft Genomics będzie dostępna do odbierania żądań interfejsu API przepływu pracy. Aby uzyskać więcej informacji, zobacz [Umowa SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Jak użycie Microsoft Genomics jest widoczne na rachunku?
Microsoft Genomics weksle na podstawie liczby przetworzonych gigabaz na przepływ pracy. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Gdzie mogę znaleźć listę wszystkich możliwych poleceń i argumentów dla `msgen` klienta?
Pełną listę dostępnych poleceń i argumentów można uzyskać, uruchamiając polecenie `msgen help` . Jeśli nie podano żadnych dalszych argumentów, zostanie wyświetlona lista dostępnych sekcji pomocy, po jednej dla każdego z `submit` ,, `list` `cancel` i `status` . Aby uzyskać pomoc dotyczącą określonego polecenia, wpisz polecenie, `msgen help command` na przykład `msgen help submit` wyświetla listę wszystkich opcji przesyłania.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Jakie są najczęściej używane polecenia `msgen` klienta programu?
Najczęściej używane polecenia są argumentami dla `msgen` klienta: 

 |**Polecenie**          |  **Opis pola** |
 |:--------------------|:-------------         |
 |`list`               |Zwraca listę przesłanych zadań. Dla argumentów, zobacz `msgen help list` .  |
 |`submit`             |Przesyła żądanie przepływu pracy do usługi. Dla argumentów, zobacz `msgen help submit` .|
 |`status`             |Zwraca stan przepływu pracy określonego przez `--workflow-id` . Zobacz też `msgen help status`. |
 |`cancel`             |Wysyła żądanie anulowania przetwarzania przepływu pracy określonego przez `--workflow-id` . Zobacz też `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Gdzie można uzyskać wartość `--api-url-base` ?
Przejdź do Azure Portal i Otwórz stronę konta genomika. W obszarze nagłówek **zarządzania** wybierz pozycję **klucze dostępu**. W tym miejscu znajduje się adres URL interfejsu API i klucze dostępu.

## <a name="where-do-i-get-the-value-for---access-key"></a>Gdzie można uzyskać wartość `--access-key` ?
Przejdź do Azure Portal i Otwórz stronę konta genomika. W obszarze nagłówek **zarządzania** wybierz pozycję **klucze dostępu**. W tym miejscu znajduje się adres URL interfejsu API i klucze dostępu.

## <a name="why-do-i-need-two-access-keys"></a>Dlaczego są potrzebne dwa klucze dostępu?
Wymagane są dwa klucze dostępu, jeśli chcesz je zaktualizować (wygenerować ponownie) bez przerywania korzystania z usługi. Na przykład jeśli chcesz zaktualizować pierwszy klucz, wszystkie Nowe przepływy pracy powinny używać drugiego klucza. Następnie poczekaj na zakończenie wszystkich przepływów pracy przy użyciu pierwszego klucza przed zaktualizowaniem pierwszego klucza.

## <a name="do-you-save-my-storage-account-keys"></a>Czy chcesz zapisać klucze konta magazynu?
Klucz konta magazynu służy do tworzenia krótkoterminowych tokenów dostępu dla usługi Microsoft Genomics do odczytywania plików wejściowych i zapisywania plików wyjściowych. Domyślny czas trwania tokenu to 48 godzin. Czas trwania tokenu można zmienić za pomocą `-sas/--sas-duration` opcji przesyłania polecenia; wartość jest w godzinach.

## <a name="does-microsoft-genomics-store-customer-data"></a>Czy Microsoft Genomics przechowywać dane klienta?

Nie. Microsoft Genomics nie przechowuje żadnych danych klienta.

## <a name="what-genome-references-can-i-use"></a>Których odwołań genomów mogę używać?

Te odwołania są obsługiwane:

 |Odwołanie              | Wartość `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (bez analizy Alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Jak mogę formatowanie moich argumentów wiersza polecenia jako pliku konfiguracji? 

msgen rozumie pliki konfiguracyjne w następującym formacie:
* Wszystkie opcje są podane jako pary klucz-wartość z wartościami oddzielonymi od kluczy przez dwukropek.
  Biały znak jest ignorowany.
* Wiersze zaczynające `#` się od są ignorowane.
* Każdy argument wiersza polecenia w długim formacie można przekonwertować na klucz, oddzielając jego wiodące myślniki i zastępując łączniki między wyrazami znakami podkreślenia. Poniżej przedstawiono kilka przykładów konwersji:

  |Argument wiersza polecenia            | Wiersz pliku konfiguracji |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key: klucz*              |      
  |`-pa/--process-args R=B37m1`     | *process_args: R-b37m1*        |  

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Microsoft Genomics, Skorzystaj z następujących zasobów:
- Zacznij od uruchomienia pierwszego przepływu pracy za pomocą usługi Microsoft Genomics. [uruchamianie przepływu pracy za pośrednictwem usługi Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Prześlij własne dane do przetwarzania przez usługę Microsoft Genomics: [sparowany FASTQ](quickstart-input-pair-FASTQ.md)  |  [BAM](quickstart-input-BAM.md)z  |  [wieloma FASTQ lub BAM](quickstart-input-multiple.md) 

