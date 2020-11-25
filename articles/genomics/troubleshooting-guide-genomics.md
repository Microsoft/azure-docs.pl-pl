---
title: Przewodnik rozwiązywania problemów
titleSuffix: Microsoft Genomics
description: Dowiedz się więcej na temat strategii rozwiązywania problemów dotyczących używania Microsoft Genomics, w tym komunikatów o błędach i sposobu ich rozwiązywania.
keywords: Rozwiązywanie problemów, błąd, debugowanie
services: genomics
author: ruchir
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: c508c10d619cde1a16d89b446c5cfd1a3ce81daf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023827"
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Poniżej przedstawiono kilka wskazówek dotyczących rozwiązywania problemów związanych z typowymi problemami, które można napotkać podczas korzystania z usługi Microsoft Genomics, MSGEN.

 Często zadawane pytania nie związane z rozwiązywaniem problemów — zobacz [często zadawane pytania](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Krok 1. lokalizowanie kodów błędów skojarzonych z przepływem pracy

Komunikaty o błędach skojarzone z przepływem pracy można zlokalizować przez:

1. Korzystanie z wiersza polecenia i wpisywanie  `msgen status`
2. Badanie zawartości standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Korzystanie z wiersza polecenia `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Istnieją trzy wymagane argumenty:

* URL — podstawowy identyfikator URI dla interfejsu API
* KLUCZ — klucz dostępu dla konta genomiki
    * Aby znaleźć adres URL i klucz, przejdź do Azure Portal i Otwórz stronę konta Microsoft Genomics. W obszarze nagłówek **zarządzania** wybierz pozycję **klucze dostępu**. W tym miejscu znajduje się adres URL interfejsu API i klucze dostępu.

  
* Identyfikator — identyfikator przepływu pracy
    * Aby znaleźć Typ identyfikatora przepływu pracy w `msgen list` poleceniu. Zakładając, że plik konfiguracyjny zawiera adres URL i klucze dostępu, a znajduje się w tej samej lokalizacji co program msgen exe, polecenie będzie wyglądać następująco: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Dane wyjściowe tego polecenia będą wyglądać następująco:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Alternatywnie możesz dołączyć ścieżkę do pliku konfiguracji, zamiast bezpośrednio wprowadzić adres URL i klucz. Jeśli te argumenty zostaną uwzględnione w wierszu polecenia, a także w pliku konfiguracji, pierwszeństwo mają argumenty wiersza polecenia.  

W przypadku przepływu pracy o IDENTYFIKATORze 1001 i pliku config.txt umieszczonego w tej samej ścieżce co plik wykonywalny msgen polecenie będzie wyglądać następująco:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Przeanalizuj zawartość standardoutput.txt 
Znajdź kontener danych wyjściowych dla danego przepływu pracy. MSGEN tworzy folder,   `[workflowfilename].logs.zip` po każdym wykonaniu przepływu pracy. Rozpakuj folder, aby wyświetlić jego zawartość:

* outputFileList.txt — lista plików wyjściowych tworzonych w ramach przepływu pracy
* standarderror.txt — ten plik jest pusty.
* standardoutput.txt — rejestruje wszystkie komunikaty o stanie najwyższego poziomu, w tym błędy, które wystąpiły podczas uruchamiania przepływu pracy.
* Pliki dziennika GATK — wszystkie pozostałe pliki w `logs` folderze

W celu rozwiązywania problemów sprawdź zawartość standardoutput.txt i zanotuj wszystkie wyświetlane komunikaty o błędach.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Krok 2. Wypróbuj zalecane kroki dla typowych błędów

W tej sekcji przedstawiono krótkie informacje o typowych błędach danych wyjściowych przez usługę Microsoft Genomics (msgen) oraz strategie, których można użyć do ich rozwiązania. 

Usługa Microsoft Genomics (msgen) może zgłosić następujące dwa rodzaje błędów:

1. Błędy usługi wewnętrznej: Błędy, które są wewnętrzne w usłudze, które mogą nie zostać rozpoznane przez naprawianie parametrów lub plików wejściowych. Czasami ponowne przesłanie przepływu pracy może rozwiązać te błędy.
2. Błędy wejściowe: Błędy, które można rozwiązać za pomocą prawidłowych argumentów lub naprawianie formatów plików.

### <a name="1-internal-service-errors"></a>1. błędy usługi wewnętrznej

Wewnętrzny błąd usługi nie jest akcją użytkownika. Możesz ponownie przesłać przepływ pracy, ale jeśli to nie zadziała, skontaktuj się z pomocą techniczną Microsoft Genomics

| Komunikat o błędzie                                                                                                                            | Zalecane kroki rozwiązywania problemów                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wystąpił błąd wewnętrzny. Spróbuj ponownie przesłać przepływ pracy. Jeśli ten błąd zostanie wyświetlony ponownie, skontaktuj się z pomocą techniczną Microsoft Genomics w celu uzyskania pomocy | Prześlij ponownie przepływ pracy. Skontaktuj się z pomocą techniczną Microsoft Genomics, aby uzyskać pomoc, jeśli problem będzie się powtarzać, tworząc [bilet](file-support-ticket-genomics.md )pomocy technicznej. |

### <a name="2-input-errors"></a>2. Błędy wejściowe

Te błędy są czynnościami użytkownika. W oparciu o typ pliku i kod błędu, Microsoft Genomics usługa wyprowadza odrębne kody błędów. Postępuj zgodnie z zalecanymi krokami rozwiązywania problemów wymienionymi poniżej.

| Typ pliku | Kod błędu | Komunikat o błędzie                                                                           | Zalecane kroki rozwiązywania problemów                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Dowolne          | 701        | Odczyt [readId] ma podstawowe typy [numberOfBases], ale limit wynosi [maxReadLength]           | Najczęstszym powodem tego błędu jest uszkodzenie pliku, co prowadzi do łączenia dwóch odczytów. Sprawdź pliki wejściowe. |
| BAM          | 200        |   Nie można odczytać pliku "[yourFileName]".                                                                                       | Sprawdź format pliku BAM. Prześlij ponownie przepływ pracy z poprawnie sformatowanym plikiem.                                                                           |
| BAM          | 201        |  Nie można odczytać pliku BAM [File_name].                                                                                      |Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 202        | Nie można odczytać pliku BAM [File_name]. Plik jest zbyt mały i brakuje nagłówka.                                                                                        | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 203        |   Nie można odczytać pliku BAM [File_name]. Nagłówek pliku został uszkodzony.                                                                                      |Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                           |
| BAM          | 204        |    Nie można odczytać pliku BAM [File_name]. Nagłówek pliku został uszkodzony.                                                                                     | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                           |
| BAM          | 205        |    Nie można odczytać pliku BAM [File_name]. Nagłówek pliku został uszkodzony.                                                                                     | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 206        |   Nie można odczytać pliku BAM [File_name]. Nagłówek pliku został uszkodzony.                                                                                      | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 207        |  Nie można odczytać pliku BAM [File_name]. Plik został obcięty blisko przesunięcia [offset].                                                                                       | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 208        |   Nieprawidłowy plik BAM. ReadID [Read_Id] nie ma sekwencji w pliku [File_name].                                                                                      | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                             |
| FASTQ        | 300        |  Nie można odczytać pliku FASTQ. [File_name] nie kończy się znakiem nowego wiersza.                                                                                     | Popraw format pliku FASTQ i ponownie prześlij przepływ pracy.                                                                           |
| FASTQ        | 301        |   Nie można odczytać pliku FASTQ [File_name]. Rekord FASTQ jest większy niż rozmiar buforu przy przesunięciu: [_offset]                                                                                      | Popraw format pliku FASTQ i ponownie prześlij przepływ pracy.                                                                         |
| FASTQ        | 302        |     Błąd składniowy FASTQ. Plik [File_name] ma pusty wiersz.                                                                                    | Popraw format pliku FASTQ i ponownie prześlij przepływ pracy.                                                                         |
| FASTQ        | 303        |       Błąd składniowy FASTQ. Plik [File_name] ma nieprawidłowy znak początkowy przy przesunięciu: [_offset], typ wiersza: [line_type], znak: [_char]                                                                                  | Popraw format pliku FASTQ i ponownie prześlij przepływ pracy.                                                                         |
| FASTQ        | 304      |  Błąd składniowy FASTQ w readID [_ReadID].  Pierwszy odczyt partii nie ma readID kończących się na/1 w pliku [File_name]                                                                                       | Popraw format pliku FASTQ i ponownie prześlij przepływ pracy.                                                                         |
| FASTQ        | 305        |  Błąd składniowy FASTQ w readID [_readID]. Druga odczytana Partia zadań nie ma readID kończących się w/2 w pliku [File_name]                                                                                      | Popraw format pliku FASTQ i ponownie prześlij przepływ pracy.                                                                          |
| FASTQ        | 306        |  Błąd składniowy FASTQ w readID [_ReadID]. Pierwszy odczyt pary nie ma identyfikatora kończącego się na/1 w pliku [File_name]                                                                                       | Popraw format pliku FASTQ i ponownie prześlij przepływ pracy.                                                                          |
| FASTQ        | 307        |   Błąd składniowy FASTQ w readID [_ReadID]. ReadID nie kończy się na/1 lub/2. Nie można użyć pliku [File_name] jako sparowanego pliku FASTQ.                                                                                      |Popraw format pliku FASTQ i ponownie prześlij przepływ pracy.                                                                          |
| FASTQ        | 308        |  Błąd odczytu FASTQ. Odczyty obu punktów końcowych odpowiedziały inaczej. Czy wybrano poprawne pliki FASTQ?                                                                                       | Popraw format pliku FASTQ i ponownie prześlij przepływ pracy.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Krok 3. kontakt z pomocą techniczną Microsoft Genomics

Jeśli nadal występują błędy zadań lub masz inne pytania, skontaktuj się z pomocą techniczną Microsoft Genomics z Azure Portal. Dodatkowe informacje na temat przesyłania żądania pomocy technicznej można znaleźć [tutaj](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rozwiązywania problemów i rozwiązywania typowych problemów z usługą Microsoft Genomics. Aby uzyskać więcej informacji i bardziej ogólnych często zadawanych pytań, zobacz [często zadawane pytania](frequently-asked-questions-genomics.md). 
