---
title: Rozwiązywanie problemów z łącznością w programie Synapse Studio
description: Rozwiązywanie problemów z łącznością usługi Azure Synapse Studio przy użyciu programu PowerShell
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 6ff3d985bb24ec852bb5c6cfaedf295fd79a8247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120363"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>Rozwiązywanie problemów z łącznością Synapse Studio z programem PowerShell

Usługa Azure Synapse Studio zależy od zestawu punktów końcowych interfejsu API sieci Web do prawidłowego działania. Ten przewodnik pomoże Ci zidentyfikować przyczyny problemów z łącznością, które są następujące:
- Konfigurowanie sieci lokalnej (na przykład sieci za zaporą firmową) na potrzeby uzyskiwania dostępu do usługi Azure Synapse Studio.
- występują problemy z łącznością przy użyciu usługi Azure Synapse Studio.

## <a name="prerequisite"></a>Wymaganie wstępne

* Program PowerShell w wersji 5,0 lub nowszej w systemie Windows lub
* Program PowerShell w wersji Core 6,0 lub nowszej w systemie Windows lub Linux.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Kliknij prawym przyciskiem myszy poniższy link, a następnie wybierz pozycję "Zapisz element docelowy jako":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Alternatywnie możesz otworzyć link bezpośrednio, a następnie zapisać otwarty plik skryptu. Nie zapisuj adresu powyższego linku, ponieważ może on ulec zmianie w przyszłości.

W Eksploratorze plików kliknij prawym przyciskiem myszy pobrany plik skryptu, a następnie wybierz polecenie "Uruchom za pomocą programu PowerShell".

![Uruchamianie pobranego pliku skryptu za pomocą programu PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Po wyświetleniu monitu wprowadź nazwę obszaru roboczego usługi Azure Synapse, w którym występuje problem, lub przetestowanie pod kątem łączności, a następnie naciśnij klawisz ENTER.

![Wprowadź nazwę obszaru roboczego](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Sesja diagnostyczna zostanie uruchomiona. Zaczekaj na jego zakończenie.

![Poczekaj na zakończenie diagnostyki](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Na końcu zostanie wyświetlona podsumowanie diagnostyki. Jeśli komputer nie może nawiązać połączenia z co najmniej jednym z punktów końcowych, zobaczy kilka sugestii w sekcji "Podsumowanie".

![Przejrzyj podsumowanie diagnostyki](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Ponadto plik dziennika diagnostycznego dla tej sesji zostanie wygenerowany w tym samym folderze co skrypt rozwiązywania problemów. Jego lokalizacja jest pokazana w sekcji "porady ogólne" ( `D:\TestAzureSynapse_2020....log` ). W razie potrzeby można wysłać ten plik do pomocy technicznej.

Jeśli jesteś administratorem sieci i dostrajasz konfigurację zapory dla usługi Azure Synapse Studio, mogą pomóc szczegóły techniczne widoczne powyżej sekcji "Podsumowanie".

* Wszystkie elementy testowe (żądania) oznaczone jako "Passed" oznaczają, że przechodzą testy łączności, niezależnie od kodu stanu HTTP.
 W przypadku żądań zakończonych niepowodzeniem przyczyna jest wyświetlana na żółto, na przykład `NamedResolutionFailure` lub `ConnectFailure` . Te przyczyny mogą pomóc ustalić, czy istnieją pewne konfiguracje w środowisku sieciowym.


## <a name="next-steps"></a>Następne kroki
Jeśli poprzednie kroki nie pomogą rozwiązać problemu, [Utwórz bilet pomocy technicznej](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).