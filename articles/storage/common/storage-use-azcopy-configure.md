---
title: Znajdowanie błędów podczas & zadań za pomocą dzienników w programie AzCopy (Azure Storage) | Microsoft Docs
description: Dowiedz się, jak używać dzienników do diagnozowania błędów i wznawiania zadań wstrzymanych przy użyciu plików planu.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d3b956803e9a796c49288f90873e88c3b69f1c7b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502899"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>Znajdowanie błędów i wznawianie zadań przy użyciu plików dziennika i planu w programie AzCopy

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. Ten artykuł ułatwia używanie dzienników do diagnozowania błędów, a następnie wznawianie zadań przy użyciu plików planu. W tym artykule pokazano również, jak skonfigurować pliki dziennika i planu, zmieniając ich poziom szczegółowości oraz domyślną lokalizację, w której są przechowywane.

> [!NOTE]
> Jeśli szukasz zawartości, która pomoże Ci rozpocząć pracę z platformą AzCopy, zobacz Get started with AzCopy (Wprowadzenie [do programu AzCopy).](storage-use-azcopy-v10.md)

## <a name="log-and-plan-files"></a>Pliki dziennika i planu

Program AzCopy *tworzy pliki* dziennika *i* planu dla każdego zadania. Za pomocą tych dzienników można badać i rozwiązywać potencjalne problemy. 

Dzienniki będą zawierać stan błędu ( , i ), pełną ścieżkę i `UPLOADFAILED` `COPYFAILED` `DOWNLOADFAILED` przyczynę błędu.

Domyślnie pliki dziennika i planu znajdują się w katalogu w systemie Windows lub w katalogu na komputerach Mac i w systemie `%USERPROFILE%\.azcopy` `$HOME$\.azcopy` Linux, ale można zmienić lokalizację. 

Odpowiedni błąd nie musi być pierwszym błędem, który pojawia się w pliku. W przypadku błędów, takich jak błędy sieci, limity czasu i błędy zajętości serwera, program AzCopy ponowi próbę maksymalnie 20 razy i zwykle proces ponawiania zakończy się powodzeniem.  Pierwszy wyświetlony błąd może być czymś niegroźnym, który został pomyślnie ponoszony.  Dlatego zamiast przyglądać się pierwszeowi błędowi w pliku, poszukaj błędów, które znajdują się w pobliżu `UPLOADFAILED` `COPYFAILED` , lub `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Podczas przesyłania żądania do Pomoc techniczna Microsoft (lub rozwiązywania problemu związanego z dowolną stroną trzecią) udostępnij z redagowanie wersję polecenia, które chcesz wykonać. Dzięki temu sygnatura dostępu współdzielonego nie zostanie przypadkowo udostępniona nikomu. Z redagowana wersja można znaleźć na początku pliku dziennika.

## <a name="review-the-logs-for-errors"></a>Przejrzyj dzienniki pod celu sprawdzenia, czy występują błędy

Następujące polecenie pobierze wszystkie błędy `UPLOADFAILED` ze stanem z `04dc9ca9-158f-7945-5933-564021086c79` dziennika:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>Wyświetlanie i wznawianie zadań

Każda operacja transferu spowoduje utworzenie zadania programu AzCopy. Użyj następującego polecenia, aby wyświetlić historię zadań:

```
azcopy jobs list
```

Aby wyświetlić statystyki zadania, użyj następującego polecenia:

```
azcopy jobs show <job-id>
```

Aby filtrować transfery według stanu, użyj następującego polecenia:

```
azcopy jobs show <job-id> --with-status=Failed
```

Użyj następującego polecenia, aby wznowić zadanie, które zakończyło się niepowodzeniem lub anulowano. To polecenie używa swojego identyfikatora wraz z tokenem SAS, ponieważ nie jest trwały ze względów bezpieczeństwa:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Argumenty ścieżki, takie jak token SAS, należy ująć w cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

Po wznowieniu zadania program AzCopy przejmuje plik planu zadania. Plik planu zawiera listę wszystkich plików, które zostały zidentyfikowane do przetworzenia podczas pierwszego utworzenia zadania. Po wznowieniu zadania program AzCopy podejmie próbę przeniesienia wszystkich plików wymienionych w pliku planu, które nie zostały jeszcze przeniesione.

## <a name="change-the-location-of-plan-files"></a>Zmienianie lokalizacji plików planu

Użyj dowolnego z tych poleceń.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> W wierszu polecenia użyj: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Użyj funkcji `azcopy env` , aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, pliki planu są zapisywane w lokalizacji domyślnej.

## <a name="change-the-location-of-log-files"></a>Zmienianie lokalizacji plików dziennika

Użyj dowolnego z tych poleceń.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> W wierszu polecenia użyj polecenia: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Użyj funkcji `azcopy env` , aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, dzienniki są zapisywane w lokalizacji domyślnej.

## <a name="change-the-default-log-level"></a>Zmienianie domyślnego poziomu dziennika

Domyślnie poziom dziennika programu AzCopy jest ustawiony na `INFO` wartość . Jeśli chcesz zmniejszyć szczegółowość dziennika, aby zaoszczędzić miejsce na dysku, zastąp to ustawienie przy użyciu ``--log-level`` opcji . 

Dostępne poziomy dziennika to: `NONE` , , , , , i `DEBUG` `INFO` `WARNING` `ERROR` `PANIC` `FATAL` .

## <a name="remove-plan-and-log-files"></a>Usuwanie plików planu i dziennika

Jeśli chcesz usunąć wszystkie pliki planu i dziennika z komputera lokalnego, aby zaoszczędzić miejsce na dysku, użyj `azcopy jobs clean` polecenia .

Aby usunąć pliki planu i dziennika skojarzone tylko z jednym zadaniem, użyj programu `azcopy jobs rm <job-id>` . Zastąp symbol `<job-id>` zastępczy w tym przykładzie identyfikatorem zadania.

## <a name="see-also"></a>Zobacz też

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
