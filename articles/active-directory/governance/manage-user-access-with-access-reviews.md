---
title: Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu — Azure AD
description: Dowiedz się, jak za pomocą przeglądów dostępu w usłudze Azure Active Directory zarządzać dostępem użytkowników — członkostwem w grupie lub przypisaniem do aplikacji
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc12b4cb7e97a0808405baebc64ca83cdb742bf1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696952"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD

Usługa Azure Active Directory (Azure AD) pozwala łatwo zapewnić użytkownikom dostęp na odpowiednim poziomie. Możesz poprosić bezpośrednio użytkowników lub osobę podejmującą decyzje o udział w przeglądzie dostępu i ponowne certyfikowanie (potwierdzenie) dostępu użytkowników. Na podstawie sugestii pochodzących z usługi Azure AD recenzenci mogą wyrazić opinię dotyczącą przedłużenia dostępu poszczególnych użytkowników. Po zakończeniu przeglądu dostępu można wprowadzić zmiany i odebrać dostęp użytkownikom, którzy już go nie potrzebują.

> [!NOTE]
> Jeśli zamiast wszystkich typów użytkowników chcesz przejrzeć tylko dostęp gości, zobacz [Manage guest user access with access reviews (Zarządzanie dostępem gości za pomocą przeglądów dostępu)](manage-guest-access-with-access-reviews.md). Jeśli chcesz przejrzeć członkostwo użytkowników w rolach administracyjnych, takich jak administrator globalny, zobacz [Start an access review in Azure AD Privileged Identity Management (Rozpoczynanie przeglądu dostępu w usłudze Azure AD Privileged Identity Management)](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — wersja Premium P2

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące licencji](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review"></a>Tworzenie i przeprowadzanie przeglądu dostępu

W przeglądzie dostępu może uczestniczyć jeden lub większa liczba recenzentów.  

1. Wybierz grupę w usłudze Azure AD, która ma co najmniej jednego członka. Możesz też wybrać aplikację połączoną z usługą Azure AD, do której jest przypisany co najmniej jeden użytkownik. 

2. Zdecyduj, czy poszczególni użytkownicy mają dokonać przeglądu własnego dostępu, czy wybrana grupa użytkowników ma przeprowadzić przegląd dostępu wszystkich użytkowników.

3. W jednej z następujących ról: Administrator globalny, administrator użytkownika lub (wersja zapoznawcza) właściciel grupy zabezpieczeń usługi M365 lub AAD grupy, która ma zostać poddana przeglądowi, przejdź do [strony zarządzania tożsamościami](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Utwórz przegląd dostępu. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md).

5. Po rozpoczęciu przeglądu dostępu podawaj recenzentów, aby wprowadzić dane wejściowe. Domyślnie każdy z nich otrzymuje wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu, który umożliwia [przejrzenie dostępu do grup lub aplikacji](perform-access-review.md).

6. Jeśli recenzenci nie przekażą opinii, można wysłać im przypomnienia za pośrednictwem usługi Azure AD. Domyślnie usługa Azure AD automatycznie wysyła przypomnienia recenzentom, którzy jeszcze nie odpowiedzieli, po upływie połowy czasu przeznaczonego na przekazanie opinii.

7. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu do grup lub aplikacji](complete-access-review.md).


## <a name="next-steps"></a>Następne kroki

[Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md)




