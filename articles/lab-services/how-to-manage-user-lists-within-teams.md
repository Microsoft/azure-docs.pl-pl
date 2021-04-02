---
title: Zarządzanie listami użytkowników Azure Lab Services z zespołów
description: Dowiedz się, jak zarządzać listami użytkowników Azure Lab Services z zespołów.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946713"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Zarządzanie listami użytkowników usług Lab Services z zespołów

Gdy laboratorium jest tworzone w ramach zespołów (zobacz [wprowadzenie i tworzenie laboratorium usług laboratoryjnych z zespołów](how-to-get-started-create-lab-within-teams.md)), lista użytkowników laboratorium jest automatycznie wypełniana i synchronizowana z członkostwem zespołu. Wszyscy użytkownicy zespołu, w tym właściciele, członkowie i Goście, zostaną automatycznie dodani do listy użytkowników laboratorium. Usługa Azure Lab Services utrzymuje synchronizację z członkostwem zespołu i automatyczna synchronizacja jest wyzwalana co 24 godziny. 

## <a name="sync-users"></a>Użytkownicy synchronizacji

Wykładowcy mogą użyć przycisku **Synchronizuj** , aby wyzwolić ręczną synchronizację po zaktualizowaniu członkostwa w zespole. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Użytkownicy synchronizacji":::

Po zakończeniu automatycznej lub ręcznej synchronizacji należy spełnić następujące kwestie w zależności od tego, czy laboratorium zostało opublikowane.

* Jeśli laboratorium nie zostało opublikowane co najmniej raz:
    * Użytkownicy będą dodawani lub usunięci z listy użytkowników laboratorium zgodnie z zmianami członkostwa w zespole. 
* Jeśli laboratorium zostało opublikowane co najmniej raz, oprócz dodawania lub usuwania użytkowników, wydajność laboratorium zostanie automatycznie zaktualizowana.
    * Jeśli do zespołu istnieją nowe dodatki, zostaną utworzone nowe maszyny wirtualne.
    * Jeśli dowolny użytkownik został usunięty z zespołu, skojarzona maszyna wirtualna również zostanie usunięta.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu maszyny wirtualnej szablonu, gdy nauczycieli wybierze opcję opublikowania szablonu, zostanie utworzona liczba maszyn wirtualnych odpowiadających liczbie użytkowników na liście użytkowników laboratorium. Po opublikowaniu laboratorium i utworzeniu maszyn wirtualnych użytkownicy zostaną automatycznie zarejestrowani do laboratorium, a maszyny wirtualne zostaną przypisane do nich podczas pierwszego logowania do Azure Lab Services to, gdy najpierw uzyskują dostęp do karty mającej **Azure Lab Services** aplikację. 

Aby opublikować szablon maszyny wirtualnej, przejdź do okna zespoły Lab Services, wybierz kartę **szablon** — > **...**  ->  **Publikuj**.

Aby zarządzać pulami maszyn wirtualnych, zobacz [Zarządzanie pulą maszyn wirtualnych w usługach Lab Services z zespołów](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>Przejrzyj również

Zobacz następujące artykuły:

- [Korzystanie z Azure Lab Services w programie Teams — Omówienie](lab-services-within-teams-overview.md)
- [Rozpocznij pracę i Utwórz laboratorium usług Lab Services dla zespołów](how-to-get-started-create-lab-within-teams.md)
- [Tworzenie harmonogramów usług Lab dla zespołów](how-to-create-schedules-within-teams.md)
- [Dostęp do maszyny wirtualnej (widoku ucznia) w usługach laboratoryjnych z zespołów](how-to-access-vm-for-students-within-teams.md)

