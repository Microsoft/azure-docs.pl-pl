---
title: Wyświetlanie stanu zadań importu/eksportu platformy Azure | Microsoft Docs
description: Dowiedz się, jak wyświetlić stan zadań importu/eksportu platformy Azure oraz używanych dysków. Poznaj czynniki wpływające na czas przetwarzania zadania.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8ef18ea663f3a77589d61ed89c50df38f5cf0d0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176151"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Wyświetlanie stanu zadań importu/eksportu platformy Azure

Ten artykuł zawiera informacje dotyczące sposobu wyświetlania stanu dysku i zadania na potrzeby zadań importu/eksportu platformy Azure. Usługa Azure Import/Export służy do bezpiecznego transferu dużych ilości danych do obiektów blob platformy Azure i Azure Files. Usługa jest również używana do eksportowania danych z usługi Azure Blob Storage.  

## <a name="view-job-and-drive-status"></a>Wyświetlanie stanu zadania i dysku
Stan zadań importowania lub eksportowania można śledzić na karcie **Importowanie/Eksportowanie** w Azure Portal.
1. Zaloguj się do https://portal.azure.com/ .
2. Wyszukaj **zadania importowania/eksportowania**.

    ![Wyszukiwanie w zadaniach importu/eksportu](./media/storage-import-export-view-drive-status/open-import-export-tab.png)

 3. Na stronie zostanie wyświetlona lista zadań importowania/eksportowania.

    ![Wyświetl stan zadania](./media/storage-import-export-view-drive-status/job-state.png)

4. Wybierz i kliknij zadanie, aby wyświetlić szczegóły zadania.

   ![Wyświetl szczegółowy stan zadania](./media/storage-import-export-view-drive-status/job-detail.png)
  
## <a name="view-job-status"></a>Wyświetlanie stanu zadania

Zostanie wyświetlony jeden z następujących stanów zadań w zależności od miejsca, w którym znajduje się dysk.

| Stan zadania | Opis |
|:--- |:--- |
| Tworzenie | Po utworzeniu zadania jego stan jest ustawiany na **Tworzenie**. Gdy zadanie jest w stanie **tworzenia** , usługa importu/eksportu zakłada, że dyski nie zostały dostarczone do centrum danych. Zadanie może pozostawać w tym stanie przez maksymalnie dwa tygodnie, po upływie którego zostanie automatycznie usunięte przez usługę. |
| Wysyłka | Po wysłaniu pakietu należy zaktualizować informacje o śledzeniu w Azure Portal.  Spowoduje to zamianę zadania w stan **wysyłki** . Zadanie pozostaje w stanie **wysyłki** przez maksymalnie dwa tygodnie. 
| Odebrano | Po odebraniu wszystkich dysków w centrum danych stan zadania jest ustawiany na **odebrane**. |
| Transferowanie | Po rozpoczęciu przetwarzania co najmniej jednego dysku stan zadania jest ustawiany na **transfer**. Aby uzyskać więcej informacji, przejdź do pozycji [dysk Stany](#view-drive-status). |
| Packaging | Po zakończeniu przetwarzania na wszystkich dyskach zadanie jest umieszczane w stanie **pakowania** , dopóki dyski nie zostaną wysłane z powrotem do użytkownika. |
| Ukończone | Po wysłaniu wszystkich dysków z powrotem do użytkownika, jeśli zadanie zostało ukończone bez błędów, to zadanie jest ustawione na **ukończone**. Zadanie jest automatycznie usuwane po 90 dniach w stanie **ukończone** . |
| Zamknięty | Po wysłaniu wszystkich dysków z powrotem do użytkownika, jeśli wystąpią błędy podczas przetwarzania zadania, zadanie jest ustawione na **zamknięte**. Zadanie jest automatycznie usuwane po 90 dniach w stanie **zamkniętym** . |

## <a name="view-drive-status"></a>Wyświetlanie stanu dysku

W poniższej tabeli opisano cykl życia poszczególnych stacji podczas przejścia przez zadanie importowania lub eksportowania. Bieżący stan każdego dysku w zadaniu jest widoczny w Azure Portal.

W poniższej tabeli opisano każdy stan, w którym można przechodzić poszczególne dyski w zadaniu.

| Stan dysku | Opis |
|:--- |:--- |
| Wyszczególnion | W przypadku zadania importowania, gdy zadanie jest tworzone na podstawie Azure Portal, zostanie **określony** stan początkowy dla dysku. W przypadku zadania eksportu, ponieważ nie określono dysku podczas tworzenia zadania, zostanie **odebrany** stan dysku początkowego. |
| Odebrano | Dysk przechodzi do stanu **otrzymanego** , gdy usługa importu/eksportu przetworzyła dyski, które zostały odebrane przez firmę wysyłkową dla zadania importu. W przypadku zadania eksportu stan początkowej stacji jest stan **odebrany** . |
| NeverReceived | Dysk przechodzi do stanu **NeverReceived** , gdy pakiet dla zadania dociera, ale pakiet nie zawiera dysku. Dysk jest również przenoszony do tego stanu, jeśli centrum danych jeszcze nie otrzymało pakietu, a usługa otrzymała informacje o wysyłce co najmniej dwa tygodnie temu. |
| Transferowanie | Dysk przechodzi do stanu **transferu** , gdy usługa zaczyna transferować dane z dysku do usługi Azure Storage. |
| Ukończone | Dysk przechodzi do stanu **ukończenia** , gdy usługa pomyślnie przekazała wszystkie dane bez błędów.
| CompletedMoreInfo | Dysk przechodzi do stanu **CompletedMoreInfo** , gdy usługa napotkała problemy podczas kopiowania danych z lub do dysku. Informacje mogą zawierać błędy, ostrzeżenia i komunikaty informacyjne dotyczące zastępowania obiektów BLOB.
| ShippedBack | Dysk przechodzi do stanu **ShippedBack** , gdy został wysłany z centrum danych z powrotem do adresu zwrotnego. |

Ten obraz z Azure Portal zawiera stan dysku przykładowego zadania:

![Wyświetl stan dysku](./media/storage-import-export-view-drive-status/drive-state.png)

W poniższej tabeli opisano Stany awarii stacji i akcje podejmowane dla każdego stanu.

| Stan dysku | Zdarzenie | Rozwiązanie/następny krok |
|:--- |:--- |:--- |
| Nigdy nie odebrano | Dysk oznaczony jako **NeverReceived** (ponieważ nie został odebrany jako część wysyłki zadania) dociera do innej wysyłki. | Zespół operacyjny przenosi dysk do **odbierania**. |
| Nie dotyczy | Dysk, który nie jest częścią żadnego zadania, dociera do centrum danych jako część innego zadania. | Dysk jest oznaczony jako dodatkowy dysk. Jest on zwracany po zakończeniu zadania skojarzonego z oryginalnym pakietem. |

## <a name="time-to-process-job"></a>Czas przetwarzania zadania
Czas przetwarzania zadania importu/eksportu zależy od wielu czynników, takich jak:

-  Czas dostawy
-  Ładowanie w centrum danych
-  Typ zadania i rozmiar kopiowanych danych
-  Liczba dysków w zadaniu. 

Usługa Import/Export nie ma umowy SLA, ale usługa dokłada starań, aby zakończyć kopię w ciągu 7 – 10 dni od momentu odebrania dysków. Oprócz stanu opublikowanego w Azure Portal można używać interfejsów API REST do śledzenia postępów zadań. Użyj parametru% Complete w wywołaniu interfejsu API operacji [listy zadań](/previous-versions/azure/dn529083(v=azure.100)) , aby wyświetlić procentowy postęp kopiowania.


## <a name="next-steps"></a>Następne kroki

* [Transferowanie danych za pomocą narzędzia wiersza polecenia AzCopy](../storage/common/storage-use-azcopy-v10.md)
* [Przykład interfejsu API REST usługi Azure Import Export](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)