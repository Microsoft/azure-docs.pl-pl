---
title: Rozwiązywanie problemów podczas instalacji usługi Azure Percept DK
description: Uzyskaj porady dotyczące rozwiązywania problemów w przypadku niektórych typowych problemów występujących podczas instalacji
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608497"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z instalacją platformy Azure Percept DK

Zapoznaj się z tabelą poniżej, aby zapoznać się z typowymi problemami znalezionymi podczas [pracy z instalacją platformy Azure Percept](./quickstart-percept-dk-set-up.md). Jeśli problem nadal występuje, skontaktuj się z działem obsługi klienta platformy Azure.

|Problem|Przyczyna|Obejście|
|:-----|:------|:----------|
|Podczas nawiązywania połączenia z stronami rejestracji konta platformy Azure lub Azure Portal można automatycznie zalogować się przy użyciu konta w pamięci podręcznej. Jeśli nie jest to konto, którego zamierzasz użyć, może to spowodować, że środowisko jest niespójne z dokumentacją.|Jest to zazwyczaj spowodowane ustawieniem w przeglądarce, aby "zapamiętać" konto, które było wcześniej używane.|Na stronie Azure kliknij nazwę swojego konta w prawym górnym rogu, a następnie wybierz pozycję **Wyloguj się**. Następnie będzie można zalogować się przy użyciu odpowiedniego konta.|
|Punkt dostępu do usługi Azure Percept DK Wi-Fi (SCZ-xxxx lub APD-xxxx) nie pojawia się na liście dostępnych sieci Wi-Fi.|Jest to zwykle tymczasowy problem, który jest rozpoznawany w ciągu 15 minut.|Poczekaj na wyświetlenie sieci. Jeśli nie jest on wyświetlany po upływie ponad 15 minut, należy ponownie uruchomić urządzenie.|
|Połączenie z punktem dostępu do usługi Azure Percept Wi-Fi DK jest często rozłączane.|Może to być spowodowane słabym połączeniem między urządzeniem i komputerem-hostem. Może to być również spowodowane zakłóceniami z innych połączeń Wi-Fi na komputerze-hoście.|Upewnij się, że anteny są prawidłowo dołączone do zestawu deweloperskiego. Jeśli zestaw deweloperski jest daleko od komputera hosta, spróbuj przenieść go bliżej siebie. Wyłącz inne połączenia internetowe, takie jak LTE/5G, jeśli są uruchomione na komputerze-hoście.|
|Na komputerze-hoście jest wyświetlane ostrzeżenie o zabezpieczeniach dotyczące połączenia z punktem dostępu do usługi Azure Percept DK.|Jest to znany problem, który zostanie rozwiązany w późniejszej aktualizacji.|Można bezpiecznie wykonać czynności konfiguracyjne.|
|Punkt dostępu do usługi Azure Percept DK Wi-Fi (SCZ-xxxx lub APD-xxxx) pojawia się na liście sieci, ale połączenie nie powiedzie się.|Może to być spowodowane tymczasowym uszkodzeniem punktu dostępu Wi-Fi zestawu deweloperskiego.|Wykonaj ponowny rozruch zestawu deweloperów i spróbuj ponownie.|
|Nie można nawiązać połączenia z siecią Wi-Fi podczas instalacji.|Sieć Wi-Fi musi obecnie mieć łączność z Internetem, aby komunikować się z platformą Azure. Protokół EAP [PEAP/MSCHAP], portale firmowe i łączność z użyciem protokołu EAP przedsiębiorstwa nie są obecnie obsługiwane.|Upewnij się, że typ sieci Wi-Fi jest obsługiwany i ma łączność z Internetem.|