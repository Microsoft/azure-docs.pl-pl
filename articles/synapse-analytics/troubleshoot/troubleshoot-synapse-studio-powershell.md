---
title: Rozwiązywanie problemów z łącznością w programie Synapse Studio
description: Rozwiązywanie problemów Azure Synapse Studio przy użyciu programu PowerShell
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 2bbdaef9268239005cdf5ea7fbee6734dadc8113
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566255"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>Rozwiązywanie problemów Synapse Studio łącznością przy użyciu programu PowerShell

Azure Synapse Studio do prawidłowego działania zależy od zestawu punktów końcowych internetowego interfejsu API. Ten przewodnik pomoże Ci zidentyfikować przyczyny problemów z łącznością, gdy:
- konfigurowanie sieci lokalnej (takiej jak sieć za firmową zaporą) w celu uzyskiwania dostępu do Azure Synapse Studio.
- występują problemy z łącznością przy użyciu programu Azure Synapse Studio.

## <a name="prerequisite"></a>Wymaganie wstępne

* Program PowerShell 5.0 lub nowsza wersja w systemie Windows lub
* PowerShell Core 6.0 lub nowszą w systemie Windows lub Linux.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Kliknij prawym przyciskiem myszy następujący link i wybierz pozycję "Zapisz element docelowy jako":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Alternatywnie możesz otworzyć link bezpośrednio i zapisać otwarty plik skryptu. Nie zapisuj adresu powyższego linku, ponieważ może on ulec zmianie w przyszłości.

W Eksploratorze plików kliknij prawym przyciskiem myszy pobrany plik skryptu i wybierz polecenie "Uruchom za pomocą programu PowerShell".

![Uruchamianie pobranego pliku skryptu przy użyciu programu PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Po wyświetleniu monitu wprowadź Azure Synapse obszaru roboczego, w którym obecnie występuje problem lub który chcesz przetestować pod celu połączenia, a następnie naciśnij klawisz Enter.

![Wprowadź nazwę obszaru roboczego](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Sesja diagnostyczna zostanie uruchomiona. Poczekaj na jego ukończenie.

![Oczekiwanie na zakończenie diagnostyki](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Na końcu zostanie wyświetlone podsumowanie diagnostyki. Jeśli komputer nie może nawiązać połączenia z co najmniej jednym punktem końcowym, w sekcji "Podsumowanie" zostaną wyświetlane sugestie.

![Przegląd podsumowania diagnostycznego](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Ponadto plik dziennika diagnostycznego dla tej sesji zostanie wygenerowany w tym samym folderze co skrypt rozwiązywania problemów. Jego lokalizacja jest wyświetlana w sekcji "Porady ogólne" ( `D:\TestAzureSynapse_2020....log` ). W razie potrzeby możesz wysłać ten plik do pomocy technicznej.

Jeśli jesteś administratorem sieci i dostrajasz konfigurację zapory dla programu Azure Synapse Studio, pomocne mogą być szczegóły techniczne przedstawione powyżej sekcji "Podsumowanie".

* Wszystkie elementy testowe (żądania) oznaczone znakiem "Passed" oznaczają, że przeszły testy łączności, niezależnie od kodu stanu HTTP.
 W przypadku żądań, które zakończyły się niepowodzeniem, przyczyna jest wyświetlana w kolorze żółtym, takim `NamedResolutionFailure` jak lub `ConnectFailure` . Z tych powodów można ustalić, czy występują błędy konfiguracji środowiska sieciowego.


## <a name="next-steps"></a>Następne kroki
Jeśli poprzednie kroki nie pomogą rozwiązać problemu, utwórz [bilet pomocy technicznej](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).