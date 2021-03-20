---
title: Zarządzanie pulą maszyn wirtualnych w Azure Lab Services z zespołów
description: Dowiedz się, jak zarządzać pulą maszyn wirtualnych w Azure Lab Services z zespołów.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91946759"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Zarządzanie pulą maszyn wirtualnych w usługach Lab Services z zespołów

Tworzenie maszyny wirtualnej rozpocznie się zaraz po pierwszym opublikowaniu szablonu VM. Maszyny wirtualne, które są równe liczbie użytkowników na liście użytkowników laboratorium, zostaną utworzone. Maszyny wirtualne są automatycznie przypisywane do uczniów przy pierwszej logowaniu do Azure Lab Services. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Publikowanie szablonu i zarządzanie pulą maszyn wirtualnych

Aby opublikować szablon, przejdź do okna zespoły Lab Services, wybierz kartę **szablon** — > **...**  ->  **Publikuj**.

Po skonfigurowaniu maszyny wirtualnej szablonu, gdy nauczycieli wybierze opcję opublikowania szablonu, zostanie utworzona liczba maszyn wirtualnych odpowiadających liczbie użytkowników na liście użytkowników laboratorium. Po opublikowaniu laboratorium i utworzeniu maszyn wirtualnych użytkownicy zostaną automatycznie zarejestrowani do laboratorium, a maszyny wirtualne zostaną przypisane do nich podczas pierwszego logowania do Azure Lab Services to, gdy najpierw uzyskują dostęp do karty mającej **Azure Lab Services** aplikację. 

Gdy zostanie wyzwolona Synchronizacja listy użytkowników, wydajność laboratorium (liczba maszyn wirtualnych w laboratorium) zostanie automatycznie zaktualizowana na podstawie zmian w członkostwie zespołu. Nowe maszyny wirtualne zostaną utworzone po dodaniu nowych użytkowników, a maszyny wirtualne przypisane do użytkowników usuniętych z zespołu również zostaną usunięte. Aby uzyskać więcej informacji [, zobacz jak zarządzać użytkownikami w zespołach](how-to-manage-user-lists-within-teams.md). 

Nauczyciele mogą nadal uzyskiwać dostęp do maszyn wirtualnych uczniów bezpośrednio z karty Pula maszyn wirtualnych. I wykładowcy mogą uzyskać dostęp do maszyn wirtualnych przypisanych do siebie albo z karty **Pula maszyn wirtualnych** , albo klikając przycisk **My Virtual Machines** (górny/prawy róg ekranu). 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="Pula maszyn wirtualnych":::

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Korzystanie z Azure Lab Services w programie Teams — Omówienie](lab-services-within-teams-overview.md)
- [Rozpocznij pracę i Utwórz laboratorium usług Lab Services dla zespołów](how-to-get-started-create-lab-within-teams.md)
- [Zarządzanie listami użytkowników usług Lab Services z zespołów](how-to-manage-user-lists-within-teams.md)
- [Tworzenie harmonogramów usług Lab dla zespołów](how-to-create-schedules-within-teams.md)
- [Dostęp do maszyny wirtualnej (widoku ucznia) w usługach laboratoryjnych z zespołów](how-to-access-vm-for-students-within-teams.md)


