---
title: Serwery grupy do oceny przy użyciu Azure Migrate | Microsoft Docs
description: Opisuje sposób grupowania serwerów przed uruchomieniem oceny za pomocą usługi Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 0570ed73b86223025b250e269d7e2f358473f004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780858"
---
# <a name="create-a-group-for-assessment"></a>Tworzenie grupy do oceny

W tym artykule opisano sposób tworzenia grup serwerów do oceny przy użyciu Azure Migrate: odnajdywania i oceny.

[Azure Migrate](migrate-services-overview.md) ułatwia Migrowanie do platformy Azure. Azure Migrate udostępnia scentralizowany centrum do śledzenia odnajdywania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. 

## <a name="grouping-servers"></a>Grupowanie serwerów

Serwery można zbierać do grup, aby ocenić, czy są one odpowiednie do migracji na platformę Azure, i uzyskać dla nich szacunki dotyczące rozmiarów i kosztów platformy Azure. Istnieje kilka sposobów tworzenia grup:

- Jeśli wiesz, które serwery należy migrować ze sobą, możesz ręcznie utworzyć grupę w Azure Migrate.
- Jeśli nie masz pewności o serwerach, które muszą być zgrupowane razem, możesz użyć funkcji wizualizacji zależności w Azure Migrate, aby utworzyć grupy. 

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

## <a name="create-a-group-manually"></a>Ręczne tworzenie grupy

Można utworzyć grupę w tym samym czasie, w którym można [utworzyć ocenę](how-to-create-assessment.md).

Jeśli chcesz utworzyć grupę ręcznie poza tworzeniem oceny, wykonaj następujące czynności:

1. W > projekcie Azure Migrate **Przegląd** kliknij pozycję **Oceń i Przeprowadź migrację serwerów**. W **Azure Migrate: odnajdywanie i ocenianie**, kliknij przycisk **grupy** .
    - Jeśli nie dodano jeszcze Azure Migrate: narzędzia odnajdywania i oceny, kliknij, aby je dodać. [Dowiedz się więcej](how-to-assess.md).
    - Jeśli projekt Azure Migrate nie został jeszcze utworzony, [Dowiedz się więcej](./create-manage-projects.md).

    ![Wybieranie grup](./media/how-to-create-a-group/select-groups.png)

2. Kliknij ikonę **grupy** .
3. W obszarze **Tworzenie grupy** Określ nazwę grupy, a w polu **Nazwa urządzenia** wybierz urządzenie Azure Migrate, którego używasz do odnajdowania serwerów.
4. Z listy serwer wybierz serwery, które chcesz dodać do grupy > **utworzyć**.

    ![Tworzenie grupy:](./media/how-to-create-a-group/create-group.png)

Możesz teraz korzystać z tej grupy podczas [tworzenia oceny maszyny wirtualnej platformy Azure](how-to-create-assessment.md) lub [oceny rozwiązania Azure VMware (Automatyczna synchronizacja)](how-to-create-azure-vmware-solution-assessment.md) albo [oceny usługi Azure SQL](how-to-create-azure-sql-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Uściślij grupę z mapowaniem zależności

Mapowanie zależności ułatwia wizualizację zależności między serwerami. Mapowanie zależności jest zazwyczaj stosowane, gdy chcesz ocenić grupy serwerów z wyższym poziomem zaufania.
- Pomaga to w sprawdzaniu krzyżowego zależności serwera przed uruchomieniem oceny. 
- Ułatwia ona również efektywne planowanie migracji na platformę Azure, zapewniając, że nic nie pozostało w tle i w ten sposób unikając awarii podczas migracji.
- Można odkrywać systemy zależne, które muszą być migrowane razem, oraz sprawdzić, czy uruchomiony system nadal obsługuje użytkowników, czy też jest kandydatem do likwidowania zamiast migracji.

Jeśli [skonfigurowano już mapowanie zależności](how-to-create-group-machine-dependencies.md)i chcesz udoskonalić istniejącą grupę, wykonaj następujące czynności:

1. Na karcie **serwery** w **Azure Migrate: kafelka odnajdywanie i ocenianie** kliknij pozycję **grupy**.
2. Kliknij grupę, którą chcesz udoskonalić.
    - Jeśli nie skonfigurowano jeszcze mapowania zależności, w kolumnie **zależności** zostanie wyświetlony stan **wymaga instalacji** . Dla każdego serwera, dla którego chcesz wizualizować zależności, kliknij pozycję **wymaga instalacji**. Przed zamapowaniem zależności serwera należy zainstalować kilku agentów na każdym serwerze. [Dowiedz się więcej](how-to-create-group-machine-dependencies.md).

        ![Dodaj mapowanie zależności](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Jeśli skonfigurowano już mapowanie zależności, na stronie Grupa kliknij pozycję **Wyświetl zależności** , aby otworzyć mapę zależności grupy.

3. Po kliknięciu pozycji **Wyświetl zależności** Mapa zależności grupy pokazuje następujące elementy:

    - Ruch przychodzący (klientów) i ruch wychodzący (serwery) TCP do i ze wszystkich serwerów w grupie, w których zainstalowano agentów zależności.
    - Serwery zależne, na których nie zainstalowano agentów zależności, są pogrupowane według numerów portów.
    - Serwery zależne z zainstalowanymi agentami zależności są wyświetlane jako oddzielne pola.
    - Procesy działające na serwerze. Rozwiń wszystkie pola serwera, aby wyświetlić procesy.
    - Właściwości serwera (w tym nazwa FQDN, system operacyjny, adres MAC). Kliknij każde pole serwer, aby wyświetlić szczegóły.

4. Aby wyświetlić zależności w przedziale czasu, należy zmodyfikować zakres czasu (domyślnie godzinę), określając daty rozpoczęcia i zakończenia lub czas trwania.

    > [!NOTE]
    > Zakres czasu może być maksymalnie godzinę. Jeśli potrzebujesz dłuższego zakresu, użyj [Azure monitor do wykonywania zapytań dotyczących danych zależnych](how-to-create-group-machine-dependencies.md) przez dłuższy czas.

5. Po zidentyfikowaniu zależności, które chcesz dodać do grupy lub usunąć z niej, można zmodyfikować grupę. Naciśnij klawisz Ctrl i kliknij, aby dodać lub usunąć serwery z grupy.

    - Można dodawać tylko te serwery, które zostały odnalezione.
    - Dodawanie i usuwanie serwerów unieważnia przeszłe oceny dla grupy.
    - Opcjonalnie można utworzyć nową ocenę podczas modyfikowania grupy.


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować [Mapowanie zależności](how-to-create-group-machine-dependencies.md) i używać ich do tworzenia grup o wysokim poziomie pewności.