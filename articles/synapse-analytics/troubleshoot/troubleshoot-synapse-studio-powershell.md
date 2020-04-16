---
title: Rozwiązywanie problemów z łącznością programu PowerShell w usłudze Azure Synapse Studio (wersja zapoznawcza)
description: Rozwiązywanie problemów z łącznością usługi Azure Synapse Studio przy użyciu programu PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431477"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnozowanie problemów z łącznością usługi Azure Synapse Studio (wersja zapoznawcza) za pomocą skryptu programu PowerShell

Usługa Azure Synapse Studio (wersja zapoznawcza) zależy od zestawu punktów końcowych interfejsu API sieci Web do poprawnego działania. Ten przewodnik pomoże Ci zidentyfikować przyczyny problemów z łącznością, gdy jesteś:
- konfigurowanie sieci lokalnej (takiej jak sieć za zaporą firmową) w celu uzyskania dostępu do usługi Azure Synapse Studio.
- problemy z łącznością przy użyciu usługi Azure Synapse Studio.

## <a name="prerequisite"></a>Wymagania wstępne

* Program PowerShell 5.0 lub nowsza w systemie Windows lub
* Program PowerShell Core 6.0 lub nowsza wersja w systemie Windows lub Linux.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Kliknij prawym przyciskiem myszy poniższy link i kliknij "Zapisz cel jako":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Alternatywnie można otworzyć łącze bezpośrednio i zapisać otwarty plik skryptu. Nie zapisuj adresu powyższego linku, ponieważ może się on zmienić w przyszłości.

W Eksploratorze plików kliknij prawym przyciskiem myszy pobrany plik skryptu i kliknij polecenie "Uruchom z programem PowerShell".

![Uruchamianie pobranego pliku skryptu za pomocą programu PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Po wyświetleniu monitu wprowadź nazwę obszaru roboczego Usługi Azure Synapse, w której występuje obecnie problem lub który chcesz przetestować pod kątem łączności, a następnie naciśnij klawisz Enter.

![Wprowadź nazwę obszaru roboczego](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Sesja diagnostyczna zostanie uruchomiona. Poczekaj na jego zakończenie.

![Poczekaj na zakończenie diagnostyki](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Na koniec zostanie wyświetlone podsumowanie diagnozy. Jeśli komputer nie może połączyć się z co najmniej jednym punktem końcowym, wyświetli kilka sugestii w sekcji "Podsumowanie".

![Przejrzyj podsumowanie diagnostyczne](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Ponadto plik dziennika diagnostycznego dla tej sesji zostanie wygenerowany w tym samym folderze co skrypt rozwiązywania problemów. Jego lokalizacja jest pokazana w`D:\TestAzureSynapse_2020....log`sekcji "Ogólne wskazówki" ( ). W razie potrzeby możesz wysłać ten plik do pomocy technicznej.

Jeśli jesteś administratorem sieci i dostrajanie konfiguracji zapory dla usługi Azure Synapse Studio, szczegóły techniczne pokazane powyżej sekcji "Podsumowanie" może pomóc.

* Wszystkie elementy testowe (żądania) oznaczone "Przekazane" oznaczają, że przeszły testy łączności, niezależnie od kodu stanu HTTP.
 W przypadku żądań nieudanych przyczyna jest `NamedResolutionFailure` wyświetlana na żółto, na przykład lub `ConnectFailure`. Przyczyny te mogą pomóc w znalezieniu, czy istnieją błędy konfiguracji ze środowiskiem sieciowym.


## <a name="next-steps"></a>Następne kroki
Jeśli poprzednie kroki nie pomagają rozwiązać problem [Utwórz bilet pomocy technicznej](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
