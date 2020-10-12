---
author: baanders
description: Dołączanie pliku do przeglądu kroków i wymagań wstępnych dotyczących uprawnień w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009668"
---
>[!NOTE]
>Te operacje mają zostać wykonane przez użytkownika z możliwością zarządzania zasobami i dostępem użytkowników w ramach subskrypcji platformy Azure. Mimo że niektóre kroki można wykonać przy niższych uprawnieniach, aby całkowicie skonfigurować możliwe do użycia wystąpienie, współpraca osób z tymi uprawnieniami będzie wymagana. Więcej informacji na ten temat znajduje się w sekcji [*wymagania wstępne: wymagane uprawnienia*](#prerequisites-permission-requirements) poniżej.

Pełna Konfiguracja nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji składa się z trzech części:
1. **Tworzenie wystąpienia**
2. **Konfigurowanie uprawnień dostępu użytkowników**: Użytkownicy platformy Azure muszą mieć rolę *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)* w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, aby móc zarządzać nim i danymi. W tym kroku użytkownik jest przypisany do osoby, która będzie zarządzać wystąpieniem usługi Azure Digital bliźniaczych reprezentacji przez właściciela/administratora subskrypcji platformy Azure. Może to być samodzielne lub ktoś inny w organizacji.
3. **Konfigurowanie uprawnień dostępu dla aplikacji klienckich**: często należy napisać aplikację kliencką, która będzie używana do współpracy z wystąpieniem. Aby aplikacja kliencka mogła uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, należy skonfigurować *rejestrację aplikacji* w [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) , która będzie używana przez aplikację kliencką do uwierzytelniania w wystąpieniu.

Aby można było to zrobić, będzie potrzebna subskrypcja platformy Azure. Możesz bezpłatnie skonfigurować jedno z [nich.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="prerequisites-permission-requirements"></a>Warunki wstępne: wymagania dotyczące uprawnień

Aby można było wykonać wszystkie kroki opisane w tym artykule, musisz mieć [rolę w subskrypcji](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) , która ma następujące uprawnienia:
* Tworzenie zasobów platformy Azure i zarządzanie nimi
* Zarządzanie dostępem użytkowników do zasobów platformy Azure (w tym przyznawanie i delegowanie uprawnień)

Typowe role spełniające to wymaganie są *właścicielami*, *administratorami kont*lub kombinacją administratorów i *współautorów* *dostępu użytkowników* . Aby zapoznać się ze szczegółowymi informacjami o rolach i uprawnieniach, w tym o uprawnieniach dołączanych do innych ról, zobacz [*klasyczne role administratora subskrypcji, role platformy Azure i role usługi Azure AD*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) w dokumentacji usługi Azure RBAC.

Aby wyświetlić swoją rolę w subskrypcji, odwiedź [stronę subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w Azure Portal (możesz użyć tego linku lub wyszukać *subskrypcje* na pasku wyszukiwania portalu). Poszukaj nazwy używanej subskrypcji i Wyświetl jej rolę w kolumnie *My role* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Widok strony subskrypcji w Azure Portal, który pokazuje użytkownika jako właściciela" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Jeśli okaże się, że wartość jest *współautorem*lub inną rolą, która nie ma wymaganych uprawnień, można skontaktować się z użytkownikiem w ramach subskrypcji, *która ma* te uprawnienia (na przykład właściciela subskrypcji lub administratora konta) i wykonać jedną z następujących czynności:
* Zażądaj, aby wykonali kroki opisane w tym artykule w Twoim imieniu
* Poproś o podwyższenie poziomu roli w ramach subskrypcji, aby mieć uprawnienia do samodzielnego wykonywania tych działań. Niezależnie od tego, czy jest to odpowiednie, zależy od organizacji i Twojej roli.