---
title: Rozwiązywanie problemów w trakcie korzystania z platformy Azure Percept DK
description: Uzyskaj porady dotyczące rozwiązywania problemów w przypadku niektórych typowych problemów występujących podczas korzystania z platformy
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662911"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z możliwością dołączania do usługi Azure Percept DK

Poniżej przedstawiono niektóre problemy, które można napotkać podczas dołączania do usługi Azure Percept. Jeśli po zastosowaniu kroków opisanych w tym przewodniku problem nadal występuje. Skontaktuj się z działem obsługi klienta platformy Azure.

|Problem|Przyczyna|Obejście|
|:-----|:------|:----------|
|Podczas nawiązywania połączenia z stronami rejestracji konta platformy Azure lub Azure Portal można automatycznie zalogować się przy użyciu konta w pamięci podręcznej. Jeśli nie jest to konto, którego zamierzasz używać, może to spowodować, że środowisko jest niespójne z dokumentacją.|Jest to zazwyczaj spowodowane ustawieniem w przeglądarce, aby "zapamiętać" konto, które było wcześniej używane.|Na stronie Azure kliknij prawy górny róg strony, w której wyświetlana jest nazwa konta, a następnie kliknij pozycję "Wyloguj się". Następnie będzie można zalogować się przy użyciu odpowiedniego konta.|
|Sieć punktu dostępu usługi Azure Percept DK (SCZ-xxxx) nie jest wyświetlana na liście dostępnych sieci Wi-Fi.|Jest to zwykle tymczasowy problem, który rozwiązuje się w niewielkim czasie.|Poczekaj na wyświetlenie sieci. Jeśli nie później niż 15 minut, należy ponownie uruchomić urządzenie.|
|Połączenie z punktem dostępu usługi Azure Percept DK jest często rozłączane.|Może to być spowodowane słabym połączeniem między urządzeniem i komputerem-hostem. Może to być również spowodowane zakłóceniami z innych połączeń Wi-Fi na komputerze-hoście.|Upewnij się, że anteny są prawidłowo dołączone do zestawu deweloperskiego. Jeśli zestaw deweloperski jest daleko od komputera hosta, spróbuj przenieść go bliżej siebie. Wyłącz inne połączenia internetowe, takie jak LTE/5G, jeśli są uruchomione na komputerze-hoście.|
|Na komputerze-hoście jest wyświetlane ostrzeżenie o zabezpieczeniach dotyczące połączenia z punktem dostępu do usługi Azure Percept DK.|Jest to znany problem, który zostanie rozwiązany w późniejszej aktualizacji.|Przechodzenie przez proces dołączania do punktu dostępu Devkit Wi-Fi jest bezpieczne.|
|Sieć z punktem dostępu (SCZ-xxxx) usługi Azure Percept jest wyświetlana na liście sieć, ale nie można nawiązać połączenia.|Może to być spowodowane tymczasowym uszkodzeniem punktu dostępu Wi-Fi Devkit.|Wykonaj ponowny rozruch Devkit i spróbuj ponownie.|
|Nie można nawiązać połączenia z siecią Wi-Fi podczas instalacji.|Sieć Wi-Fi musi mieć teraz połączenie z Internetem, aby można było komunikować się z platformą Azure. Protokół EAP [PEAP/MSCHAP], portale firmowe i łączność z użyciem protokołu EAP przedsiębiorstwa nie są obecnie obsługiwane.|Upewnij się, że typ Wi-Fi sieci, z którą nawiązujesz połączenie, jest obsługiwany i ma łączność z Internetem.|