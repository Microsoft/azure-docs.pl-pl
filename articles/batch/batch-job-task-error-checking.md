---
title: Sprawdzanie pod kątem błędów w zadaniach i zadaniach podrzędnych
description: Błędy do sprawdzenia i rozwiązywania problemów z zadaniami i zadaniami
author: mscurrell
ms.topic: how-to
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: ece0f1473b3c453ca5506f06b7ef094533d146aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964332"
---
# <a name="job-and-task-error-checking"></a>Sprawdzanie błędów zadania i zadania

Istnieją różne błędy, które mogą wystąpić podczas dodawania zadań i zadań. Wykrywanie błędów dla tych operacji jest proste, ponieważ wszystkie błędy są zwracane bezpośrednio przez interfejs API, interfejsu wiersza polecenia lub interfejsu użytkownika.  Istnieją jednak błędy, które mogą wystąpić później w przypadku zaplanowania i uruchomienia zadań i zadań.

W tym artykule opisano błędy, które mogą wystąpić po przesłaniu zadań i zadań. Zawiera listę i wyjaśnia błędy, które należy sprawdzić i obsłużyć.

## <a name="jobs"></a>Stanowiska

Zadanie to grupa składająca się z jednego lub więcej zadań, zadania w rzeczywistości określają wiersze poleceń do uruchomienia.

Podczas dodawania zadania można określić następujące parametry, które mogą mieć wpływ na sposób niepowodzenia zadania:

- [Ograniczenia zadania](/rest/api/batchservice/job/add#jobconstraints)
  - `maxWallClockTime`Opcjonalnie można określić właściwość, aby ustawić maksymalną ilość czasu, przez jaki zadanie może być aktywne lub uruchomione. W przypadku przekroczenia tego zadania zostanie zakończone z `terminateReason` właściwością ustawioną w [executionInfo](/rest/api/batchservice/job/get#cloudjob) dla tego zadania.
- [Zadanie przygotowania zadania](/rest/api/batchservice/job/add#jobpreparationtask)
  - Jeśli ta wartość jest określona, zadanie przygotowania zadania jest uruchamiane przy pierwszym uruchomieniu zadania dla zadania w węźle. Zadanie przygotowania zadania może zakończyć się niepowodzeniem, co spowoduje, że zadanie nie zostanie uruchomione, a zadanie nie zostanie ukończone.
- [Zadanie zwolnienia zadania](/rest/api/batchservice/job/add#jobreleasetask)
  - Zadanie zwolnienia zadania można określić tylko w przypadku skonfigurowania zadania przygotowania zadania. Gdy zadanie jest przerywane, zadanie zwolnienia zadania jest uruchamiane w każdym węźle puli, w którym uruchomiono zadanie przygotowania zadania. Zadanie zwolnienia zadania może zakończyć się niepowodzeniem, ale zadanie będzie nadal przenoszone do `completed` stanu.

### <a name="job-properties"></a>Właściwości zadania

Następujące właściwości zadania powinny zostać sprawdzone pod kątem błędów:

- "[executionInfo](/rest/api/batchservice/job/get#jobexecutioninformation)":
  - `terminateReason`Właściwość może mieć wartości, aby wskazać, że `maxWallClockTime` , określone w ograniczeniach zadania, zostało przekroczone i w związku z tym zadanie zostało zakończone. Można również ustawić, aby wskazać, że zadanie nie powiodło się, jeśli `onTaskFailure` Właściwość zadania została odpowiednio ustawiona.
  - Właściwość [schedulingError](/rest/api/batchservice/job/get#jobschedulingerror) jest ustawiona, jeśli wystąpił błąd planowania.
 
### <a name="job-preparation-tasks"></a>Zadania przygotowania zadania

Jeśli zadanie przygotowania zadania jest określone dla zadania, wystąpienie tego zadania zostanie uruchomione po raz pierwszy zadanie dla zadania zostanie uruchomione w węźle. Zadanie przygotowania zadania skonfigurowane w tym zadaniu może być przemyślane jako szablon zadania, z uruchomionym wieloma wystąpieniami zadań przygotowania zadania, do liczby węzłów w puli.

Należy sprawdzić wystąpienia zadania przygotowania zadania, aby określić, czy wystąpiły błędy:
- Gdy zadanie przygotowania zadania zostanie uruchomione, zadanie wyzwalające zadanie przygotowania zadania przejdzie do [stanu](/rest/api/batchservice/task/get#taskstate) `preparing` . Jeśli zadanie przygotowania zadania zakończy się niepowodzeniem, zadanie wyzwalania zostanie przywrócone do `active` stanu i nie zostanie uruchomione.  
- Wszystkie wystąpienia zadania przygotowania zadania, które zostały uruchomione, można uzyskać z zadania za pomocą interfejsu API [listy stan zadania przygotowania i zwolnienia](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Podobnie jak w przypadku każdego zadania, dostępne są [Informacje o wykonywaniu](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) z właściwościami, takimi jak `failureInfo` , `exitCode` i `result` .
- W przypadku niepowodzenia zadań związanych z przygotowaniem zadania zadania wyzwalania nie zostaną uruchomione, zadanie nie zostanie ukończone i zostanie zablokowane. Pula może być niedostępna, jeśli nie ma żadnych innych zadań z zadaniami, które można zaplanować.

### <a name="job-release-tasks"></a>Zadania zwolnienia zadań

Jeśli zadanie zwolnienia zadania jest określone dla zadania, wtedy, gdy zadanie jest przerywane, wystąpienie zadania zwolnienia zadania jest uruchamiane na wszystkich węzłach puli, w których uruchomiono zadanie przygotowania zadania.  Należy sprawdzić wystąpienia zadania wydania zadania, aby określić, czy wystąpiły błędy:
- Wszystkie wystąpienia uruchomionego zadania zwolnienia zadania mogą zostać uzyskane z zadania przy użyciu [stanu zadania przygotowania i zwolnienia listy](/rest/api/batchservice/job/listpreparationandreleasetaskstatus)interfejsów API. Podobnie jak w przypadku każdego zadania, dostępne są [Informacje o wykonywaniu](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) z właściwościami, takimi jak `failureInfo` , `exitCode` i `result` .
- Jeśli co najmniej jedno zadanie zwolnienia zadania zakończy się niepowodzeniem, zadanie będzie nadal kończone i przechodzi do `completed` stanu.

## <a name="tasks"></a>Zadania

Zadania zadań mogą się nie powieść z wielu powodów:

- Wiersz polecenia zadania kończy się niepowodzeniem, zwracając niezerowy kod zakończenia.
- `resourceFiles`Określono dla zadania, ale wystąpił błąd, co oznacza, że co najmniej jeden plik nie został pobrany.
- `outputFiles`Określono dla zadania, ale wystąpił błąd, co oznacza, że co najmniej jeden plik nie został przekazany.
- Przekroczono czas trwania zadania określony przez `maxWallClockTime` Właściwość w [ograniczeniach](/rest/api/batchservice/task/add#taskconstraints)zadań.

We wszystkich przypadkach następujące właściwości muszą być sprawdzane pod kątem błędów i informacji o błędach:
- Właściwość [executionInfo](/rest/api/batchservice/task/get#taskexecutioninformation) zadań zawiera wiele właściwości, które zawierają informacje o błędzie. [wynik](/rest/api/batchservice/task/get#taskexecutionresult) wskazuje, czy zadanie nie powiodło się z jakiegokolwiek powodu, `exitCode` i `failureInfo` dostarcza więcej informacji o błędzie.
- Zadanie będzie zawsze przenoszone do `completed` [stanu](/rest/api/batchservice/task/get#taskstate)niezależnie od tego, czy zakończyło się powodzeniem, czy niepowodzeniem.

Należy rozważyć wpływ niepowodzeń zadań na zadanie i wszelkie zależności zadań.  Właściwość [exitConditions](/rest/api/batchservice/task/add#exitconditions) może być określona dla zadania w celu skonfigurowania akcji dla zależności i zadania.
- W przypadku zależności, [DependencyAction](/rest/api/batchservice/task/add#dependencyaction) kontroluje, czy zadania zależne od zadania zakończonego niepowodzeniem są blokowane czy uruchamiane.
- Dla zadania [JobAction](/rest/api/batchservice/task/add#jobaction) kontroluje, czy zadanie zakończone niepowodzeniem prowadzi do zadania, które zostało wyłączone, przerwane lub pozostawione bez zmian.

### <a name="task-command-line-failures"></a>Błędy wiersza polecenia zadania

Po uruchomieniu wiersza polecenia zadania, dane wyjściowe są zapisywane do `stderr.txt` i `stdout.txt` . Ponadto aplikacja może zapisywać w plikach dziennika specyficznych dla aplikacji.

Jeśli węzeł puli, na którym zadanie zostało uruchomione nadal istnieje, można uzyskać i wyświetlić pliki dziennika. Na przykład Azure Portal list i może wyświetlać pliki dzienników dla danego zadania lub węzła puli. Wiele interfejsów API umożliwia również wyświetlanie i uzyskiwanie plików zadań, takich jak [pobieranie z zadania](/rest/api/batchservice/file/getfromtask).

Ze względu na to, że pule i węzły puli często są trwałe, a węzły są ciągle dodawane i usuwane, zaleca się zachowywanie plików dziennika. [Pliki wyjściowe zadania](./batch-task-output-files.md) są wygodnym sposobem zapisywania plików dziennika w usłudze Azure Storage.

### <a name="output-file-failures"></a>Błędy plików wyjściowych
W przypadku każdego przekazywania plików Batch zapisuje dwa pliki dziennika w węźle COMPUTE `fileuploadout.txt` i `fileuploaderr.txt` . Możesz sprawdzić te pliki dziennika, aby dowiedzieć się więcej na temat konkretnego błędu. W przypadkach, gdy nigdy nie podjęto próby przekazania pliku, na przykład ponieważ nie można uruchomić samego zadania, te pliki dziennika nie będą istnieć.  

## <a name="next-steps"></a>Następne kroki

Sprawdź, czy aplikacja implementuje kompleksowe sprawdzanie błędów; może to być krytyczne, aby szybko wykrywać i diagnozować problemy.
