---
title: Opis aktywnej kolekcji dzienników na urządzeniu Azure Stack EDGE Pro
description: Opisuje sposób, w jaki aktywne jest zbieranie dzienników na urządzeniu z systemem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466960"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Aktywna kolekcja dzienników na urządzeniu z systemem Azure Stack Edge

Możesz włączyć funkcję aktywnej kolekcji dzienników na urządzeniu brzegowym Azure Stack na podstawie wskaźników kondycji systemu, aby skutecznie rozwiązywać problemy z urządzeniami. W tym artykule opisano, co to jest funkcja proaktywne zbieranie dzienników, jak włączyć ją i jak są obsługiwane dane po włączeniu aktywnej kolekcji dzienników.
   
Informacje przedstawione w tym artykule mają zastosowanie do Azure Stack brzegowych procesorów GPU, Azure Stack EDGE Pro R i Azure Stack Edge.

## <a name="about-proactive-log-collection"></a>Informacje o aktywnej kolekcji dzienników

Zespoły obsługi klienta i inżynierów firmy Microsoft używają dzienników systemu z urządzenia brzegowego Azure Stack, aby skutecznie identyfikować i rozwiązywać problemy, które mogą wystąpić podczas operacji. Proaktywne zbieranie dzienników to metoda, która powiadamia firmę Microsoft o problemie/zdarzeniu (zobacz sekcję wskaźniki aktywnego zbierania dzienników dla monitorowanych zdarzeń) została wykryta przez urządzenie Azure Stack brzegowej klienta. Dzienniki pomocy technicznej związane z problemem są automatycznie przekazywane do konta usługi Azure Storage zarządzanego i kontrolowanego przez firmę Microsoft. Pomoc techniczna firmy Microsoft i inżynierów firmy Microsoft przeglądają te dzienniki pomocy technicznej w celu ustalenia najlepszego sposobu działania w celu rozwiązania problemu z klientem.    

> [!NOTE]
> Dzienniki te są używane tylko do celów debugowania i zapewniają klientom pomoc techniczną w przypadku problemów. 


## <a name="enabling-proactive-log-collection"></a>Włączanie aktywnej kolekcji dzienników

Możesz włączyć funkcję aktywnej kolekcji dzienników podczas próby aktywowania urządzenia za pomocą lokalnego interfejsu użytkownika. 

1. W lokalnym interfejsie użytkownika sieci Web urządzenia **Przejdź na stronę wprowadzenie.**
2. Na kafelku **Aktywacja** wybierz pozycję **Aktywuj**. 

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. W okienku **Aktywuj** :
    1. Wprowadź **klucz aktywacji** uzyskany w polu [Pobierz klucz aktywacji dla Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Możesz włączyć funkcję aktywnej kolekcji dzienników, aby umożliwić firmie Microsoft zbieranie dzienników na podstawie stanu kondycji urządzenia. Dzienniki zebrane w ten sposób są przekazywane do konta usługi Azure Storage.
    
    1. Wybierz przycisk **Zastosuj**.

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>Wskaźniki aktywnego zbierania dzienników

Po włączeniu aktywnej kolekcji dzienników dzienniki są przekazywane automatycznie, gdy na urządzeniu zostanie wykryte jedno z następujących zdarzeń:  


|Alert/błąd/warunek  |Opis  |
|---------|---------|
|AcsUnhealthyCondition     |Spójne usługi platformy Azure są w złej kondycji.         |
|IOTEdgeAgentNotRunningCondition      |Agent IoT Edge nie jest uruchomiony.         |
|UpdateInstallFailedEvent | Nie można zainstalować aktualizacji.        |

 
Firma Microsoft będzie nadal dodawać nowe zdarzenia do powyższej listy. Nowe zdarzenia nie wymagają żadnej dodatkowej zgody. Zapoznaj się z tą stroną, aby dowiedzieć się więcej o najbardziej aktualnych wskaźnikach kolekcji aktywnych dzienników.    
 

## <a name="other-log-collection-methods"></a>Inne metody zbierania dzienników

Oprócz funkcji aktywnej kolekcji dzienników, która gromadzi określone dzienniki związane z określonym problemem, istnieją inne kolekcje dzienników, które mogą zapewnić znacznie dokładniejsze zrozumienie kondycji i zachowania systemu. Zwykle te inne kolekcje dzienników mogą być wykonywane w trakcie żądania obsługi lub wyzwalane przez firmę Microsoft na podstawie danych telemetrycznych dostarczanych przez urządzenie.  

## <a name="handling-data"></a>Obsługa danych

Jeśli klient włączy proaktywne zbieranie dzienników, firma Microsoft wyrazi zgodę na zbieranie dzienników z Azure Stack urządzenia brzegowego zgodnie z opisem w niniejszym artykule. Klient potwierdzi również i wyraża zgodę na przekazywanie i przechowywanie tych dzienników na koncie usługi Azure Storage zarządzanym i kontrolowanym przez firmę Microsoft.

Firma Microsoft używa tych danych do rozwiązywania problemów z kondycją i błędami systemu. Dane nie są używane do marketingu, reklamy ani żadnych innych celów komercyjnych bez zgody klienta. 

Zbieranie danych przez firmę Microsoft jest obsługiwane zgodnie z naszymi standardowymi zasadami zachowania poufności informacji. Jeśli klient zdecyduje się odwołać swoją zgodę dla aktywnej kolekcji dzienników, nie wpłynie to na wszystkie dane zebrane z zgodą przed odwołaniem.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zebrać pakiet pomocy technicznej](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).