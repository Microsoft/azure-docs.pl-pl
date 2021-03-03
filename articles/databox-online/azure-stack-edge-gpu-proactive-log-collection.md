---
title: Opis aktywnej kolekcji dzienników na urządzeniu Azure Stack EDGE Pro
description: Opisuje sposób, w jaki można przeprowadzić aktywne zbieranie dzienników na urządzeniu z systemem Azure Stack Edge i jak go wyłączyć.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: 064af116112f0b530ac0cc9b5755dcec2cf0bd07
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722084"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Aktywna kolekcja dzienników na urządzeniu z systemem Azure Stack Edge

Usługa Active log Collection zbiera wskaźniki kondycji systemu na urządzeniu z Azure Stack Edge, aby ułatwić efektywne Rozwiązywanie problemów z urządzeniami. Funkcja aktywnej kolekcji dzienników jest domyślnie włączona. W tym artykule opisano, co jest rejestrowane, jak firma Microsoft obsługuje dane, oraz jak wyłączyć lub włączyć funkcję aktywnej kolekcji dzienników. 

Informacje przedstawione w tym artykule mają zastosowanie do Azure Stack brzegowych procesorów GPU, Azure Stack EDGE Pro R i Azure Stack Edge.

## <a name="about-proactive-log-collection"></a>Informacje o aktywnej kolekcji dzienników

Zespoły obsługi klienta i inżynierów firmy Microsoft używają dzienników systemu z urządzenia brzegowego Azure Stack, aby skutecznie identyfikować i rozwiązywać problemy, które mogą wystąpić podczas operacji. Kolekcja aktywnych dzienników to metoda, która powiadamia firmę Microsoft o wykryciu problemu/zdarzenia przez urządzenie Azure Stack brzegowe klienta. Zobacz [wskaźniki aktywnej kolekcji dzienników](#proactive-log-collection-indicators) dla monitorowanych zdarzeń. Dzienniki pomocy technicznej związane z problemem są automatycznie przekazywane do konta usługi Azure Storage, które jest zarządzane i kontrolowane przez firmę Microsoft. Pomoc techniczna firmy Microsoft i inżynierów firmy Microsoft przeglądają te dzienniki pomocy technicznej w celu ustalenia najlepszego sposobu działania w celu rozwiązania problemu z klientem.

> [!NOTE]
> Te dzienniki są używane tylko do celów debugowania oraz w celu zapewnienia pomocy technicznej klientom w przypadku problemów.


## <a name="enabling-proactive-log-collection"></a>Włączanie aktywnej kolekcji dzienników

Funkcja aktywnej kolekcji dzienników jest domyślnie włączona. Można wyłączyć funkcję aktywnej kolekcji dzienników podczas próby aktywowania urządzenia za pomocą lokalnego interfejsu użytkownika. 

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź na stronę **wprowadzenie** .

2. Na kafelku **Aktywacja** wybierz pozycję **Aktywuj**. 

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. W okienku **Aktywuj** :

   1. Wprowadź **klucz aktywacji** uzyskany w polu [Pobierz klucz aktywacji dla Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

      Po aktywowaniu funkcja aktywnej kolekcji dzienników jest domyślnie włączona, co umożliwia firmie Microsoft zbieranie dzienników na podstawie stanu kondycji urządzenia. Te dzienniki są przekazywane do konta usługi Azure Storage. 

      Można wyłączyć funkcję aktywnej kolekcji dzienników, aby zatrzymać zbieranie dzienników przez firmę Microsoft.

   1. Jeśli chcesz wyłączyć funkcję aktywnej kolekcji dzienników na urządzeniu, wybierz pozycję **Wyłącz**.

   1. Wybierz pozycję **Aktywuj**.

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

Oprócz funkcji aktywnej kolekcji dzienników, która gromadzi określone dzienniki związane z określonym problemem, inne kolekcje dzienników mogą bardziej szczegółowo zrozumieć kondycję systemu i zachowanie. Zwykle te inne dzienniki mogą być zbierane w trakcie żądania obsługi lub wyzwalane przez firmę Microsoft na podstawie danych telemetrycznych z urządzenia.

## <a name="handling-data"></a>Obsługa danych

Po włączeniu aktywnej kolekcji dzienników Klient wyraża zgodę na zbieranie dzienników z Azure Stack urządzenia brzegowego zgodnie z opisem w niniejszym artykule. Klient potwierdzi również i wyraża zgodę na przekazywanie i przechowywanie tych dzienników na koncie usługi Azure Storage zarządzanym i kontrolowanym przez firmę Microsoft.

Firma Microsoft używa tych danych do rozwiązywania problemów z kondycją i błędami systemu. Dane nie są używane do marketingu, reklamy ani żadnych innych celów komercyjnych bez zgody klienta. 

Zbieranie danych przez firmę Microsoft jest obsługiwane zgodnie z naszymi standardowymi zasadami zachowania poufności informacji. Jeśli klient zdecyduje się odwołać swoją zgodę dla aktywnej kolekcji dzienników, nie wpłynie to na wszystkie dane zebrane z zgodą przed odwołaniem.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zebrać pakiet pomocy technicznej](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).