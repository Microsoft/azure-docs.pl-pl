---
title: Zbiorcze usuwanie członków grupy przez przekazanie pliku CSV — Usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Usuń członków grupy w operacjach zbiorczych w centrum administracyjnym platformy Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e6d0752245e3864a8ad25efd5181d5cc1eec7ae
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533267"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Zbiorcze usuwanie członków grupy w usłudze Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można usunąć dużą liczbę członków z grupy za pomocą pliku csv (uszecony przecinkami), aby zbiorczo usuwać członków grupy.

## <a name="to-bulk-remove-group-members"></a>Aby zbiorczo usunąć członków grupy

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta administratora użytkownika w organizacji. Właściciele grup mogą również zbiorczo usuwać członków grup, których są właścicielami.
1. W usłudze Azure AD wybierz pozycję **Grupy** > **Wszystkie grupy**.
1. Otwórz grupę, z której usuwasz członków, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz pozycję **Usuń członków**.
1. Na stronie **Zbiorcze usuwanie członków grupy** wybierz pozycję **Pobierz,** aby uzyskać szablon pliku CSV z wymaganymi właściwościami członka grupy.

   ![Polecenie Usuń członków znajduje się na stronie profilu grupy](./media/groups-bulk-remove-members/remove-panel.png)

1. Otwórz plik CSV i dodaj wiersz dla każdego członka grupy, którego chcesz usunąć z grupy (wymagane wartości to identyfikator obiektu członka lub główna nazwa użytkownika). Następnie zapisz plik.

   ![Plik CSV zawiera nazwy i identyfikatory członków do usunięcia](./media/groups-bulk-remove-members/csv-file.png)

1. Na stronie **Zbiorcze usuwanie członków grupy** w obszarze **Przekazywanie pliku csv**przejdź do pliku. Po wybraniu pliku rozpocznie się sprawdzanie poprawności pliku CSV.
1. Po sprawdzeniu poprawności zawartości pliku na stronie importu zbiorczego **zostanie wyświetlony plik przekazany pomyślnie**. Jeśli występują błędy, należy je naprawić, zanim będzie można przesłać zadanie.
1. Gdy plik przechodzi weryfikacji, wybierz **prześlij,** aby rozpocząć operację zbiorczą platformy Azure, która usuwa członków grupy z grupy.
1. Po zakończeniu operacji usuwania zostanie wyświetlone powiadomienie, że operacja zbiorcza powiodła się.

## <a name="check-removal-status"></a>Sprawdź stan usuwania

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **Wyniki operacji zbiorczej.**

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

Aby uzyskać szczegółowe informacje o każdym elemencie zamówienia w ramach operacji zbiorczej, wybierz wartości w kolumnach **# Sukces**, **# Niepowodzenie**lub Całkowita **liczba żądań.** Jeśli wystąpiły błędy, zostaną wyświetlone przyczyny awarii.

## <a name="bulk-removal-service-limits"></a>Limity usług usuwania zbiorczego

Każde działanie zbiorcze w celu usunięcia listy członków grupy można uruchomić przez maksymalnie jedną godzinę. Umożliwia to usunięcie listy co najmniej 40 000 członków.

## <a name="next-steps"></a>Następne kroki

- [Członkowie grupy importu zbiorczego](groups-bulk-import-members.md)
- [Pobieranie członków grupy](groups-bulk-download-members.md)
- [Pobierz listę wszystkich grup](groups-bulk-download.md)
