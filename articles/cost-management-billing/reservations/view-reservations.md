---
title: Uprawnienia do wyświetlania rezerwacji platformy Azure i zarządzania nimi
description: Dowiedz się, jak wyświetlać rezerwacje platformy Azure i zarządzać nimi w Azure Portal.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780465"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Uprawnienia do wyświetlania rezerwacji platformy Azure i zarządzania nimi

W tym artykule wyjaśniono, jak działają uprawnienia do rezerwacji oraz jak użytkownicy mogą wyświetlać rezerwacje platformy Azure i zarządzać nimi w Azure Portal.

## <a name="who-can-manage-a-reservation-by-default"></a>Kto może domyślnie zarządzać rezerwacją

Domyślnie rezerwacje mogą wyświetlać i zarządzać nimi następujący użytkownicy:

- Do zamówienia rezerwacji jest dodawana osoba, która kupuje rezerwację, i administrator konta subskrypcji rozliczeniowej używanej do zakupu rezerwacji.
- Administratorzy rozliczeń w umowie Enterprise Agreement i umowie klienta firmy Microsoft.
- Użytkownicy z podwyższonym poziomem dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure

Cykl życia rezerwacji jest niezależny od subskrypcji platformy Azure, więc rezerwacja nie jest zasobem w ramach subskrypcji platformy Azure. Zamiast tego jest to zasób na poziomie dzierżawy z własnym uprawnieniem RBAC platformy Azure oddzielonym od subskrypcji. Rezerwacje nie dziedziczą uprawnień z subskrypcji po zakupie.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Jak administratorzy rozliczeń mogą wyświetlać rezerwacje i zarządzać nimi

Jeśli jesteś administratorem rozliczeń, skorzystaj z poniższych kroków, aby wyświetlić wszystkie rezerwacje i transakcje rezerwacji oraz zarządzać nimi.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do **Cost Management + Billing**.
    - Jeśli jesteś administratorem EA, w menu po lewej stronie wybierz pozycję **Zakresy** rozliczeniowe, a następnie z listy zakresów rozliczeniowych wybierz jeden z nich.
    - Jeśli jesteś właścicielem profilu Umowa z Klientem Microsoft, w menu po lewej stronie wybierz pozycję **Profile rozliczeniowe.** Na liście profilów rozliczeniowych wybierz jeden z nich.
1. W menu po lewej stronie wybierz **pozycję Produkty i usługi**  >  **Rezerwacje.**
1. Zostanie wyświetlona pełna lista rezerwacji dla rejestracji umowy EA lub profilu rozliczeniowego.
1. Administratorzy rozliczeń mogą przejąć własność rezerwacji, wybierając ją, a następnie **wybierając** pozycję U przyznaj dostęp w wyświetlonym oknie.

### <a name="how-to-add-billing-administrators"></a>Jak dodać administratorów rozliczeń

Dodaj użytkownika jako administratora rozliczeń do umowy Enterprise Agreement lub umowy klienta firmy Microsoft:

- W przypadku umowy Enterprise Agreement dodaj użytkowników z rolą _Administrator przedsiębiorstwa_, aby wyświetlać wszystkie zamówienia rezerwacji dotyczące umowy Enterprise Agreement i zarządzać nimi. Administratorzy przedsiębiorstwa mogą wyświetlać rezerwacje i zarządzać nimi w **Cost Management + Billing**.
    - Użytkownicy z rolą _Administrator przedsiębiorstwa (tylko do odczytu)_ mogą wyświetlać rezerwację tylko z Cost Management + Billing **.** 
    - Administratorzy działu i właściciele kont nie mogą wyświetlać rezerwacji, _o ile_ nie zostali do nich jawnie dodani przy użyciu funkcji kontroli dostępu (IAM). Aby uzyskać więcej informacji, zobacz [Zarządzanie rolami w usłudze Azure Enterprise](../manage/understand-ea-roles.md).
- W przypadku umowy klienta firmy Microsoft użytkownicy z rolą właściciela profilu rozliczeniowego lub współautora profilu rozliczeniowego mogą zarządzać wszystkimi zakupami rezerwacji dokonanymi przy użyciu profilu rozliczeniowego. Czytelnicy profilów rozliczeniowych i menedżerowie faktur mogą wyświetlać wszystkie rezerwacje opłacane za pomocą profilu rozliczeniowego. Jednak nie mogą wprowadzać zmian w rezerwacjach.
    Aby uzyskać więcej informacji, zobacz [Zadania i role profilu rozliczeniowego](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Wyświetlanie rezerwacji z dostępem RBAC platformy Azure

Jeśli zakupiono rezerwację lub dodano cię do rezerwacji, użyj następujących kroków, aby wyświetlić rezerwacje i zarządzać nimi.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Wszystkie usługi** > **Rezerwacja** w celu wyświetlenia listy rezerwacji, do których masz dostęp.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Użytkownicy z podwyższonym poziomem dostępu mogą zarządzać wszystkimi subskrypcjami i grupami zarządzania platformy Azure

Możesz podnieść uprawnienia dostępu użytkownika, aby zarządzać wszystkimi subskrypcjami [platformy Azure i grupami zarządzania.](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)

Po podwyżseniu poziomu dostępu:

1. Przejdź do **strony**  >  **Rezerwacja wszystkich usług,** aby wyświetlić wszystkie rezerwacje w dzierżawie.
1. Aby wprowadzić modyfikacje rezerwacji, dodaj siebie jako właściciela zamówienia rezerwacji przy użyciu kontroli dostępu (IAM).

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Zapewnianie użytkownikom dostępu RBAC platformy Azure do poszczególnych rezerwacji

Użytkownicy, którzy mają dostęp właściciela do rezerwacji i administratorzy rozliczeń, mogą delegować zarządzanie dostępem do poszczególnych zamówień rezerwacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Wszystkie usługi** > **Rezerwacja** w celu wyświetlenia listy rezerwacji, do których masz dostęp.
1. Wybierz rezerwację, dla której chcesz delegować dostęp do innych użytkowników.
1. W obszarze Szczegóły rezerwacji wybierz zamówienie rezerwacji.
1. Wybierz pozycję **Kontrola dostępu (IAM)** .
1. Wybierz pozycję **Dodaj przypisanie roli** > **Rola** > **Właściciel**. Jeśli chcesz nadać ograniczony dostęp, wybierz inną rolę.
1. Wpisz adres e-mail użytkownika, którego chcesz dodać jako właściciela.
1. Wybierz użytkownika, a następnie wybierz polecenie **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md).