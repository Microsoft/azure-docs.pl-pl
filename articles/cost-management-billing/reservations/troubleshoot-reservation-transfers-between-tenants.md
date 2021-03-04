---
title: Rozwiązywanie problemów z transferami rezerwacji platformy Azure między dzierżawcami
description: Ten artykuł pomaga właścicielom rezerwacji przesłać zamówienie rezerwacji z jednej Azure Active Directory dzierżawy (katalogu) do innej.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055837"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>Rozwiązywanie problemów z transferami rezerwacji między dzierżawcami

Ten artykuł pomaga właścicielom rezerwacji przesłać zamówienie rezerwacji z jednej Azure Active Directory dzierżawy (katalogu) do innej. Zmiana katalogu zamówienia rezerwacji spowoduje usunięcie dowolnego dostępu z usługi Azure RBAC do zamówienia rezerwacji i rezerwacji zależnych. Tylko ty będziesz mieć dostęp po zmianie. Zmiana katalogu nie zmienia własności rozliczeń dla zamówienia rezerwacji. Katalog zostanie zmieniony dla nadrzędnej kolejności rezerwacji i rezerwacji zależnych.

Wymiana i anulowanie rezerwacji nie są konieczne do przetransferowania między dzierżawcami.

Po przeniesieniu rezerwacji do innej dzierżawy może być również konieczne dodanie kolejnych właścicieli do rezerwacji. Aby uzyskać więcej informacji, zobacz, [kto może zarządzać rezerwacją domyślnie](view-reservations.md#who-can-manage-a-reservation-by-default).

Po przeniesieniu zamówienia rezerwacji wszystkie rezerwacje w kolejności są przenoszone razem z nim.

## <a name="transfer-a-reservation"></a>Przenoszenie rezerwacji

Wykonaj następujące kroki, aby przesłać zamówienie rezerwacji i zależne rezerwacje od innej dzierżawy.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Jeśli nie jesteś administratorem rozliczeń, ale jesteś właścicielem rezerwacji, przejdź do pozycji **rezerwacje** , a następnie przejdź do kroku 6.
1. Przejdź na stronę **Zarządzanie kosztami i rozliczenia**.
    - Jeśli jesteś administratorem EA, w menu po lewej stronie wybierz pozycję **zakresy rozliczeń** , a następnie na liście zakresów rozliczeń wybierz jeden z nich.
    - Jeśli jesteś właścicielem profilu rozliczeń umowy klienta firmy Microsoft, w menu po lewej stronie wybierz pozycję **Profile rozliczeń**. Na liście profilów rozliczeń wybierz jeden z nich.
1. W menu po lewej stronie wybierz pozycję **transakcje rezerwacji**. Zostanie wyświetlona lista transakcji rezerwacji.
1. Transparent u góry strony odczytuje *teraz Administratorzy rozliczeń mogą zarządzać rezerwacjami. Kliknij tutaj, aby zarządzać rezerwacjami.* Wybierz transparent.
1. Zostanie wyświetlona pełna lista rezerwacji dla rejestracji EA lub profilu rozliczeń.
1. Wybierz zastrzeżenie, które chcesz przenieść.
1. W obszarze Szczegóły rezerwacji wybierz identyfikator zamówienia rezerwacji.
1. W kolejności rezerwacji wybierz pozycję **Zmień katalog**.
1. W okienku Zmień katalog wybierz katalog usługi Azure AD, do którego chcesz przenieść rezerwację, a następnie wybierz pozycję **Potwierdź**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat rezerwacji, zobacz temat [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)