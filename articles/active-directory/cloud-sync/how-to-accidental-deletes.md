---
title: Azure AD Connect przypadkowe Usuwanie synchronizacji w chmurze
description: W tym temacie opisano, jak używać funkcji przypadkowego usuwania, aby zapobiec usuwaniu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613803"
---
# <a name="accidental-delete-prevention"></a>Zapobieganie przypadkowemu usuwaniu

W poniższym dokumencie opisano funkcję przypadkowego usuwania Azure AD Connect synchronizacji w chmurze.  Funkcja przypadkowego usuwania jest przeznaczona do ochrony przed przypadkowymi zmianami konfiguracji i zmianami w katalogu lokalnym, które wpłyną na wielu użytkowników i grupy.  Ta funkcja umożliwia:

- Skonfiguruj możliwość automatycznego usuwania przypadkowych usunięć. 
- Ustaw liczbę obiektów (próg), po których ta konfiguracja zacznie obowiązywać 
- Skonfiguruj adres e-mail powiadomienia, aby móc otrzymywać powiadomienia e-mail, gdy w danym zadaniu synchronizacji zostanie umieszczona Kwarantanna w tym scenariuszu 

Aby użyć tej funkcji, należy ustawić wartość progową liczby obiektów, które zostały usunięte, synchronizacja powinna zostać zatrzymana.  Dlatego jeśli ta liczba zostanie osiągnięta, synchronizacja zostanie zatrzymana, a powiadomienie zostanie wysłane do określonego adresu e-mail.  Dzięki temu można sprawdzić, co się dzieje.


## <a name="configure-accidental-delete-prevention"></a>Skonfiguruj zapobieganie przypadkowemu usuwaniu
Aby skorzystać z nowej funkcji, wykonaj poniższe kroki.


1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
2.  Wybierz **Azure AD Connect**.
3.  Wybierz pozycję **Zarządzaj synchronizacją w chmurze**.
4. W obszarze **Konfiguracja** wybierz konfigurację.
5. W obszarze **Ustawienia** wypełnij następujące elementy:
    - **Wiadomość e-mail z powiadomieniem** — wiadomość e-mail używana do powiadomień
    - **Zapobiegaj przypadkowemu usuwaniu** — zaznacz to pole, aby włączyć tę funkcję
    - **Próg przypadkowego usunięcia** — wprowadź liczbę obiektów, aby wyzwolić zatrzymanie synchronizacji i powiadomienia

![Przypadkowe usunięcie](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>Następne kroki 

- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)
- [Jak zainstalować Azure AD Connect synchronizacji w chmurze](how-to-install.md)
 

