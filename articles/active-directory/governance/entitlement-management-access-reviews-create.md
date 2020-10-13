---
title: Tworzenie przeglądu dostępu do pakietu dostępu w usłudze Azure AD uprawnienia zarządzania
description: Dowiedz się, jak utworzyć zasady przeglądu dostępu dla pakietów dostępu do zarządzania prawami w Azure Active Directory przeglądy dostępu (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798515"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Tworzenie przeglądu dostępu do pakietu dostępu w usłudze Azure AD uprawnienia zarządzania

Aby zmniejszyć ryzyko nieaktualnego dostępu, należy włączyć okresowe przeglądy użytkowników, którzy mają aktywne przypisania do pakietu dostępu w usłudze Azure AD uprawnienia do zarządzania. Możesz włączyć przeglądy podczas tworzenia nowego pakietu dostępu lub edytowania istniejącego pakietu. W tym artykule opisano, jak włączyć przeglądy dostępu do pakietów dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć przeglądy pakietów dostępu, musisz spełnić wymagania wstępne dotyczące tworzenia pakietu dostępu:
- Usługa Azure AD — wersja Premium P2
- Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące licencji](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Tworzenie przeglądu dostępu do pakietu dostępu

Przeglądy dostępu można włączyć podczas [tworzenia nowego pakietu dostępu](entitlement-management-access-package-create.md) lub [edytowania istniejących zasad pakietu](entitlement-management-access-package-lifecycle-policy.md) . Wykonaj następujące kroki, aby włączyć przeglądy dostępu do pakietu dostępu:

1. Otwórz kartę **cykl życia** dla pakietu dostępu i przewiń w dół do **omówienia dostępu**.

1. Przenieś opcję **Wymagaj przeglądów dostępu** na **wartość tak**.

    ![Dodawanie przeglądu dostępu](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Określ **datę rozpoczęcia przeglądu obok początku.**

1. Następnie ustaw **częstotliwość przeglądu** na **roczną**, co **rok**, co **kwartał** lub **co miesiąc**.
To ustawienie określa, jak często będą przeprowadzane przeglądy dostępu.

1. Ustaw **czas trwania** , aby określić, ile dni każdy przegląd cyklicznej serii będzie otwarty do wprowadzania danych od recenzentów. Na przykład można zaplanować coroczne przeglądy, które zaczynają się od 1 stycznia i są otwarte do przeglądu przez 30 dni, tak aby recenzenci musieli odpowiedzieć na koniec miesiąca.

1. Obok pozycji **recenzenci**wybierz opcję **samodzielna Recenzja** , jeśli chcesz, aby użytkownicy korzystali z własnego przeglądu dostępu lub wybierali **określonych recenzentów** , jeśli chcesz wyznaczyć recenzenta.

    ![Wybierz pozycję Dodaj recenzentów](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. W przypadku wybrania **określonych recenzentów**Określ, którzy użytkownicy będą wykonywać przegląd dostępu:
    1. Wybierz pozycję **Dodaj recenzentów**.
    1. W okienku **Wybierz recenzentów** Wyszukaj i wybierz użytkowników, którym chcesz być recenzentem.
    1. Po wybraniu recenzentów kliknij przycisk **Wybierz** .

    ![Określ recenzentów](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Kliknij przycisk **Przegląd + Utwórz** , jeśli tworzysz nowy pakiet lub **aktualizację** dostępu, Jeśli edytujesz pakiet dostępu w dolnej części strony.

## <a name="view-the-status-of-the-access-review"></a>Wyświetlanie stanu przeglądu dostępu

Po dacie rozpoczęcia przegląd dostępu zostanie wyświetlony w sekcji **przeglądy dostępu** . Wykonaj następujące kroki, aby wyświetlić stan przeglądu dostępu:

1. W obszarze **Zarządzanie tożsamościami**kliknij pozycję **pakiety dostępu** , a następnie wybierz pakiet dostępu ze stanem przeglądu dostępu, który chcesz sprawdzić.   

1. Po przeprowadzeniu przeglądu pakietu dostępu kliknij pozycję **przeglądy dostępu** w menu po lewej stronie.
    
    ![Wybierz przeglądy dostępu](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Zostanie wyświetlona lista zawierająca wszystkie zasady, które mają skojarzone z nimi przeglądy dostępu. Kliknij przycisk Przeglądaj, aby wyświetlić jego raport.

    ![Lista przeglądów dostępu](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Podczas wyświetlania raportu wyświetlana jest liczba przeglądanych użytkowników oraz akcje podjęte przez recenzenta.

    ![Wyświetl stan przeglądu](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Powiadomienia e-mail dotyczące przeglądów dostępu
Możesz wyznaczyć recenzentów lub sprawdzić ich dostęp. Domyślnie usługa Azure AD wyśle wiadomość e-mail do recenzentów lub samorecenzentów wkrótce po rozpoczęciu przeglądu.

Wiadomość e-mail zawiera instrukcje dotyczące przeglądania dostępu do pakietów dostępu. Jeśli przegląd jest przeznaczony dla użytkowników w celu przejrzenia ich dostępu, należy wyświetlić instrukcje dotyczące sposobu przeprowadzania samodzielnej oceny swoich pakietów dostępu.
  
Jeśli przypisano użytkowników-Gości jako recenzentów, którzy nie zaakceptowali zaproszenia gościa usługi Azure AD, nie będą oni otrzymywać wiadomości e-mail z przeglądu dostępu do usługi Azure AD. Najpierw muszą zaakceptować zaproszenie i utworzyć konto w usłudze Azure AD, zanim będą mogły otrzymywać wiadomości e-mail. 

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj dostęp do pakietów dostępu](entitlement-management-access-reviews-review-access.md)
- [Samoprzegląd pakietów dostępu](entitlement-management-access-reviews-self-review.md)
