---
title: Monitorowanie i rozwiązywanie problemów aplikacji magazynu w chmurze na platformie Azure | Microsoft Docs
description: Korzystanie z narzędzi diagnostycznych, metryk i alertów w celu rozwiązywania problemów i monitorowania aplikacji w chmurze.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 6c1d9b813db15900521968865c1036d53ad53f41
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544789"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorowanie i rozwiązywanie problemów aplikacji magazynu w chmurze

Ten samouczek to czwarta i ostatnia część serii. Zawiera instrukcje monitorowania aplikacji magazynu w chmurze i rozwiązywania problemów.

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie rejestrowania i metryk
> * Włączanie alertów dotyczących błędów autoryzacji
> * Uruchamianie testowego ruchu z nieprawidłowymi tokenami SAS
> * Pobieranie i analizowanie dzienników

[Usługa Azure Storage Analytics](../common/storage-analytics.md) zapewnia dane rejestrowania i metryk dla konta magazynu. Te dane informują o kondycji konta magazynu. Aby zbierać dane z usługi Azure Storage Analytics, możesz skonfigurować rejestrowanie, metryki i alerty. Ten proces polega na włączeniu rejestrowania, skonfigurowaniu metryk i włączeniu alertów.

Rejestrowanie i metryki dotyczące konta magazynu można włączyć na karcie **Diagnostyka** w witrynie Azure Portal. Rejestrowanie danych magazynu umożliwia zarejestrowanie szczegółów dotyczących żądań zakończonych zarówno powodzeniem, jak i niepowodzeniem na koncie magazynu. Te dzienniki przedstawiają szczegółowe informacje o operacjach odczytu, zapisu i usuwania, wykonanych na tabelach, kolejkach i obiektach blob na platformie Azure. Zawierają także przyczyny niepowodzeń żądań, na przykład przekroczenia limitu czasu, ograniczenia i błędy autoryzacji.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do [Azure Portal](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Włączanie rejestrowania i metryk

W menu po lewej stronie wybierz kolejno pozycje **Grupy zasobów** i **myResourceGroup**, a następnie wybierz konto magazynu na liście zasobów.

W obszarze **Ustawienia diagnostyki (klasyczne)** zmień ustawienie **Stan** na **Włączone**. Upewnij się, że wszystkie opcje w obszarze **Właściwości obiektu blob** są włączone.

Po zakończeniu kliknij przycisk **Zapisz** .

![Zrzut ekranu, który podświetla sekcję zawierającą ustawienia konfiguracji służące do włączania rejestrowania i metryk.](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Włączanie alertów

Alerty umożliwiają wysłanie wiadomości e-mail do administratora lub wyzwolenie elementu webhook w momencie naruszenia progu przez metrykę. W tym przykładzie włączony zostanie alert dotyczący metryki `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Przechodzenie do konta magazynu w witrynie Azure Portal

W sekcji **Monitorowanie** wybierz pozycję **Alerty (klasyczne)**.

Wybierz polecenie **Dodaj alert dotyczący metryki (klasyczne)** i ukończ formularz **Dodawanie reguły**, podając wymagane informacje. Z listy rozwijanej **Metryki** wybierz pozycję `SASClientOtherError`. Aby umożliwić wyzwolenie alertu po pierwszym błędzie, z listy rozwijanej **Warunek** wybierz pozycję **Większe niż lub równe**.

![Okienko diagnostyki](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Symulowanie błędu

Aby zasymulować prawidłowy alert, można spróbować zażądać nieistniejącego obiektu blob z konta magazynu. Następujące polecenie wymaga nazwy kontenera magazynu. Można użyć nazwy istniejącego kontenera lub utworzyć nowy na potrzeby tego przykładu.

Zastąp symbole zastępcze rzeczywistymi wartościami (upewnij się, że element `<INCORRECT_BLOB_NAME>` jest ustawiony na wartość, która nie istnieje) i uruchom polecenie.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

Poniższa ilustracja przedstawia przykładowy alert utworzony na podstawie symulowanego błędu wywołanego w poprzednim przykładzie.

 ![Alert przykładowy](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Pobieranie i wyświetlanie dzienników

Dane dzienników magazynu są przechowywane w zestawie obiektów blob w kontenerze obiektów blob o nazwie **$logs** na koncie magazynu. Ten kontener nie jest wyświetlany na liście wszystkich kontenerów obiektów blob na koncie, ale można wyświetlić jego zawartość po uzyskaniu do niego bezpośredniego dostępu.

W tym scenariuszu należy użyć narzędzia [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide) do interakcji z kontem usługi Azure Storage.

### <a name="download-microsoft-message-analyzer"></a>Pobieranie narzędzia Microsoft Message Analyzer

Pobierz narzędzie [Microsoft Message Analyzer](/message-analyzer/installing-and-upgrading-message-analyzer) i zainstaluj aplikację.

Uruchom aplikację i wybierz polecenie **plik**  >  **otwarty**  >  **z innych źródeł plików**.

W oknie dialogowym **File Selector** (Selektor plików) wybierz opcję **+ Add Azure Connection** (+ Dodaj połączenie Azure). Uzupełnij pola **Storage account name** (Nazwa konta magazynu) i **Account key** (Klucz konta), a następnie kliknij przycisk **OK**.

![Narzędzie Microsoft Message Analyzer — Okno dialogowe Dodawanie połączenia z usługą Azure Storage](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Po nawiązaniu połączenia rozwiń kontenery w widoku drzewa magazynu, aby wyświetlić obiekty blob dziennika. Wybierz najnowszy dziennik i kliknij przycisk **OK**.

![Zrzut ekranu pokazujący Analizator komunikatów firmy Microsoft i wyróżniony wybrany plik dziennika.](media/storage-monitor-troubleshoot-storage-application/figure4.png)

W oknie dialogowym **New Session** (Nowa sesja) kliknij przycisk **Start** (Uruchom), aby wyświetlić dziennik.

Po otwarciu dziennika można wyświetlać zdarzenia magazynu. Jak widać na poniższej ilustracji, na koncie magazynu wystąpiło zdarzenie `SASClientOtherError`. Aby uzyskać dodatkowe informacje na temat rejestrowania danych magazynu, odwiedź stronę [Storage Analytics (Analityka magazynu)](../common/storage-analytics.md).

![Narzędzie Microsoft Message Analyzer — wyświetlanie zdarzeń](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) to kolejne narzędzie, które może służyć do interakcji z kontami magazynu, w tym z kontenerem **$logs** i dziennikami, które są w nim zawarte.

## <a name="next-steps"></a>Następne kroki

W czwartej i ostatniej części serii przedstawiono sposób monitorowania konta magazynu i rozwiązywania problemów, w tym następujące czynności:

> [!div class="checklist"]
> * Włączanie rejestrowania i metryk
> * Włączanie alertów dotyczących błędów autoryzacji
> * Uruchamianie testowego ruchu z nieprawidłowymi tokenami SAS
> * Pobieranie i analizowanie dzienników

Kliknij ten link, aby wyświetlić wstępnie skompilowane przykłady magazynu.

> [!div class="nextstepaction"]
> [Azure storage script samples (Przykładowe skrypty dla usługi Azure Storage)](storage-samples-blobs-cli.md)