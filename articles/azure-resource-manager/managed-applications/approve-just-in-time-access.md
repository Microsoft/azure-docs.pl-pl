---
title: Zatwierdzanie dostępu just in time
description: Opisuje, w jaki sposób konsumenci Azure Managed Applications zatwierdzają żądania dostępu just in Time do aplikacji zarządzanej.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651022"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurowanie i zatwierdzanie dostępu just in Time do Azure Managed Applications

Jako użytkownik aplikacji zarządzanej może nie mieć możliwości udzielenia wydawcy stałego dostępu do zarządzanej grupy zasobów. Aby zapewnić większą kontrolę nad udzieleniem dostępu do zarządzanych zasobów, Azure Managed Applications zapewnia funkcję o nazwie dostęp just-in-Time (JIT), która jest obecnie dostępna w wersji zapoznawczej. Umożliwia zatwierdzenie czasu i czasu, w którym Wydawca ma dostęp do grupy zasobów. Wydawca może wprowadzić wymagane aktualizacje w tym czasie, ale po przekroczeniu tego czasu dostęp wydawcy wygaśnie.

Przepływ pracy do udzielania dostępu to:

1. Wydawca dodaje aplikację zarządzaną do portalu Marketplace i określa, że dostęp JIT jest dostępny.

1. Podczas wdrażania można włączyć dostęp JIT dla swojego wystąpienia aplikacji zarządzanej.

1. Po wdrożeniu można zmienić ustawienia dostępu JIT.

1. Wydawca wysyła żądanie dostępu.

1. Zatwierdzenie żądania.

Ten artykuł koncentruje się na działaniach podejmowanych przez klientów w celu umożliwienia dostępu JIT i zatwierdzania żądań. Aby dowiedzieć się więcej o publikowaniu aplikacji zarządzanej z dostępem JIT, zobacz [żądanie dostępu just in Time w Azure Managed Applications](request-just-in-time-access.md).

> [!NOTE]
> Aby korzystać z dostępu just in Time, musisz mieć [licencję na Azure Active Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Włącz podczas wdrażania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź wpis w witrynie Marketplace dla aplikacji zarządzanej z włączoną funkcją JIT. Wybierz przycisk **Utwórz**.

1. Podczas udostępniania wartości dla nowej aplikacji zarządzanej, krok **konfiguracji JIT** umożliwia włączenie lub wyłączenie dostępu JIT dla aplikacji zarządzanej. Wybierz opcję **tak** , aby **włączyć dostęp JIT**. Ta opcja jest domyślnie wybrana dla aplikacji zarządzanych, które zdefiniowano przy użyciu JIT w portalu Marketplace.

   ![Konfigurowanie dostępu](./media/approve-just-in-time-access/configure-jit-access.png)

   Dostęp JIT można włączyć tylko podczas wdrażania. Jeśli wybierzesz opcję **nie**, Wydawca uzyska stały dostęp do zarządzanej grupy zasobów. Nie można włączyć dostępu JIT później.

1. Aby zmienić domyślne ustawienia zatwierdzania, wybierz pozycję **Dostosuj konfigurację JIT**.

   ![Dostosuj dostęp](./media/approve-just-in-time-access/customize-jit-access.png)

   Domyślnie aplikacja zarządzana z włączoną funkcją JIT ma następujące ustawienia:

   * Tryb zatwierdzania — automatyczne
   * Maksymalny czas trwania dostępu — 8 godzin
   * Osoby zatwierdzające — brak

   Gdy tryb zatwierdzania jest ustawiony na **automatyczny**, osoby zatwierdzające odbierają powiadomienie dla każdego żądania, ale żądanie jest automatycznie zatwierdzane. Po wybraniu opcji **ręcznie**osoby zatwierdzające odbierają powiadomienie dla każdego żądania, a jedna z nich musi je zatwierdzić.

   Maksymalny czas trwania aktywacji określa maksymalną ilość czasu, przez który Wydawca może zażądać dostępu do zarządzanej grupy zasobów.

   Lista osoby zatwierdzające to Azure Active Directory użytkownicy, którzy mogą zatwierdzać żądania dostępu JIT. Aby dodać osobę zatwierdzającą, wybierz pozycję **Dodaj osobę zatwierdzającą** i Wyszukaj użytkownika.

   Po zaktualizowaniu ustawienia wybierz pozycję **Zapisz**.

## <a name="update-after-deployment"></a>Aktualizuj po wdrożeniu

Można zmienić wartości sposobu zatwierdzania żądań. Jeśli jednak nie włączono dostępu JIT podczas wdrażania, nie można go włączyć później.

Aby zmienić ustawienia wdrożonej aplikacji zarządzanej:

1. W portalu wybierz pozycję Zarządzaj aplikacją.

1. Wybierz opcję **Konfiguracja JIT** i Zmień ustawienia zgodnie z potrzebami.

   ![Zmień ustawienia dostępu](./media/approve-just-in-time-access/change-settings.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="approve-requests"></a>Zatwierdzanie żądań

Gdy Wydawca zażąda dostępu, otrzymujesz powiadomienie o żądaniu. Żądania dostępu JIT mogą być zatwierdzane bezpośrednio za pośrednictwem aplikacji zarządzanej lub przez wszystkie zarządzane aplikacje za pośrednictwem usługi Azure AD Privileged Identity Management. Aby korzystać z dostępu just in Time, musisz mieć [licencję na Azure Active Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

Aby zatwierdzić żądania przez zarządzaną aplikację:

1. Wybierz pozycję **dostęp JIT** dla aplikacji zarządzanej, a następnie wybierz pozycję **Zatwierdź żądania**.

   ![Zatwierdzanie żądań](./media/approve-just-in-time-access/approve-requests.png)
 
1. Wybierz żądanie do zatwierdzenia.

   ![Wybieranie żądania](./media/approve-just-in-time-access/select-request.png)

1. W formularzu podaj przyczynę zatwierdzenia i wybierz pozycję **Zatwierdź**.

Aby zatwierdzić żądania przez Azure AD Privileged Identity Management:

1. Wybierz pozycję **wszystkie usługi** i rozpocznij wyszukiwanie **Azure AD Privileged Identity Management**. Wybierz ją z dostępnych opcji.

   ![Wyszukaj usługę](./media/approve-just-in-time-access/search.png)

1. Wybierz pozycję **Zatwierdź żądania**.

   ![Wybierz pozycję Zatwierdź żądania](./media/approve-just-in-time-access/select-approve-requests.png)

1. Wybierz pozycję **aplikacje zarządzane przez platformę Azure**i wybierz żądanie do zatwierdzenia.

   ![Wybierz żądania](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o publikowaniu aplikacji zarządzanej z dostępem JIT, zobacz [żądanie dostępu just in Time w Azure Managed Applications](request-just-in-time-access.md).
