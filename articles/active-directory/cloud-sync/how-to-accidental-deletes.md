---
title: Azure AD Connect przypadkowe Usuwanie synchronizacji w chmurze
description: W tym temacie opisano, jak używać funkcji przypadkowego usuwania, aby zapobiec usuwaniu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785189"
---
# <a name="accidental-delete-prevention"></a>Zapobieganie przypadkowemu usuwaniu

W poniższym dokumencie opisano funkcję przypadkowego usuwania Azure AD Connect synchronizacji w chmurze.  Funkcja przypadkowego usuwania jest przeznaczona do ochrony przed przypadkowymi zmianami konfiguracji i zmianami w katalogu lokalnym, które wpłyną na wielu użytkowników i grupy.  Ta funkcja umożliwia:

- Skonfiguruj możliwość automatycznego usuwania przypadkowych usunięć. 
- Ustaw liczbę obiektów (próg), po których ta konfiguracja zacznie obowiązywać 
- Skonfiguruj adres e-mail powiadomienia, aby móc otrzymywać powiadomienia e-mail, gdy zadanie synchronizacji w danym scenariuszu zostanie umieszczone w kwarantannie dla tego scenariusza 

Aby użyć tej funkcji, należy ustawić wartość progową liczby obiektów, które zostały usunięte, synchronizacja powinna zostać zatrzymana.  Dlatego jeśli ta liczba zostanie osiągnięta, synchronizacja zostanie zatrzymana, a powiadomienie zostanie wysłane do określonego adresu e-mail.  To powiadomienie umożliwi zbadanie tego, co się dzieje.


## <a name="configure-accidental-delete-prevention"></a>Skonfiguruj zapobieganie przypadkowemu usuwaniu
Aby skorzystać z nowej funkcji, wykonaj poniższe kroki.


1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
2.  Wybierz **Azure AD Connect**.
3.  Wybierz pozycję **Zarządzaj synchronizacją w chmurze**.
4. W obszarze **Konfiguracja** wybierz konfigurację.
5. W obszarze **Ustawienia** wypełnij następujące elementy:
    - **Wiadomość e-mail z powiadomieniem** — wiadomość e-mail używana do powiadomień
    - **Zapobiegaj przypadkowemu usuwaniu** — zaznacz to pole, aby włączyć tę funkcję
    - **Próg przypadkowego usunięcia** — wprowadź liczbę obiektów do zatrzymania synchronizacji i Wyślij powiadomienie

![Przypadkowe usunięcie](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Odzyskiwanie z wystąpienia przypadkowego usuwania
W przypadku wystąpienia przypadkowego usunięcia zostanie on wyświetlony w obszarze stan konfiguracji agenta aprowizacji.  Zostanie wyświetlony komunikat o **przekroczonym progu usuwania**.
 
![Stan przypadkowego usuwania](media/how-to-accidental-deletes/delete-1.png)

Po kliknięciu **wartości progu usuwania** zostanie wyświetlony informacja o stanie synchronizacji.  Spowoduje to dostarczenie dodatkowych informacji. 
 
 ![Stan synchronizacji](media/how-to-accidental-deletes/delete-2.png)

Klikając prawym przyciskiem myszy wielokropek, zostaną wyświetlone następujące opcje:
 - Wyświetl dziennik aprowizacji
 - Wyświetl agenta
 - Zezwalaj na usuwanie

 ![Kliknij prawym przyciskiem myszy](media/how-to-accidental-deletes/delete-3.png)

Za pomocą **widoku dziennik aprowizacji** można zobaczyć wpisy **StagedDelete** i przejrzeć informacje podane dla użytkowników, którzy zostali usunięci.
 
 ![Dzienniki aprowizowania](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Zezwalanie na usuwanie

Akcja **Zezwalaj na** usuwanie spowoduje usunięcie obiektów, które wyzwoliły próg przypadkowego usunięcia.  Aby zaakceptować usunięcia, należy wykonać poniższą procedurę.  

1. Kliknij prawym przyciskiem myszy wielokropek i wybierz polecenie **Zezwalaj na usuwanie**.
2. Kliknij przycisk **tak** w potwierdzeniu, aby zezwolić na usuwanie.
 
 ![Tak po potwierdzeniu](media/how-to-accidental-deletes/delete-4.png)

3. Zostanie wyświetlone potwierdzenie, że usunięcia zostały zaakceptowane, a stan powróci do dobrej kondycji z następnym cyklem. 
 
 ![Zaakceptuj usunięcia](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Odrzucanie usunięć

Aby nie zezwalać na usuwanie, należy wykonać następujące czynności:
- Zbadaj Źródło usunięć
- Rozwiąż problem (przykład: jednostka organizacyjna została przeniesiona poza zakres przypadkowo i teraz ponownie dodaliśmy ją z powrotem do zakresu)
- Uruchom **synchronizację ponowną** w konfiguracji agenta

## <a name="next-steps"></a>Następne kroki 

- [Azure AD Connect Rozwiązywanie problemów z synchronizacją w chmurze?](how-to-troubleshoot.md)
- [Azure AD Connect kody błędów synchronizacji w chmurze](reference-error-codes.md)
 

