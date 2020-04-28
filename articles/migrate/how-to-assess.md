---
title: Dodawanie narzędzi do oceny w Azure Migrate
description: Dowiedz się, jak dodać narzędzia do oceny w Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185908"
---
# <a name="add-assessment-tools"></a>Dodawanie narzędzi oceny

W tym artykule opisano sposób dodawania narzędzi do oceny w [Azure Migrate](migrate-overview.md).

Azure Migrate oferuje centrum narzędzi do oceny i migracji na platformę Azure. Zawiera narzędzia Azure Migrate, a także inne narzędzia i oferty niezależnych dostawców oprogramowania (ISV).

Jeśli chcesz dodać narzędzie do oceny i nie masz jeszcze projektu Azure Migrate, postępuj zgodnie z tym [artykułem](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Wybierz narzędzie

W przypadku wybrania narzędzia innego niż Azure Migrate do oceny Zacznij od uzyskania licencji lub zarejestrowania się w celu skorzystania z bezpłatnej wersji próbnej zgodnie z zasadami dotyczącymi narzędzi. Narzędzia mają opcję nawiązywania połączenia z Azure Migrate. Postępuj zgodnie z instrukcjami i dokumentacją, aby podłączyć narzędzie do Azure Migrate. [Dowiedz się więcej](migrate-services-overview.md) o narzędziach.


## <a name="select-an-assessment-scenario"></a>Wybierz scenariusz oceny

1. W projekcie Azure Migrate kliknij pozycję **Przegląd**.
2. Wybierz scenariusz oceny, którego chcesz użyć:

    - Aby odnajdywać i oceniać maszyny i obciążenia do migracji na platformę Azure, wybierz pozycję **Oceń i Przeprowadź migrację serwerów**.
    - Aby ocenić lokalne maszyny SQL, wybierz pozycję **Oceń i Przeprowadź migrację baz danych**.
    - Aby ocenić lokalne aplikacje sieci Web, wybierz pozycję **Oceń i Migruj aplikacje sieci Web**.

    ![Scenariusz oceny](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Wybierz narzędzie do oceny serwera 

1. Kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.
2. W obszarze **serwery Azure Migrate**, jeśli nie dodano narzędzia do oceny, w obszarze **Narzędzia do oceny**wybierz **pozycję kliknij tutaj, aby dodać narzędzie do oceny**. Jeśli dodano już narzędzia do oceny, w obszarze **Dodaj więcej narzędzi do oceny**wybierz pozycję **Zmień**.

    > [!NOTE]
    > Jeśli chcesz przejść do innego projektu, w obszarze **serwery Azure Migrate**, kliknij **przycisk kliknij tutaj**, aby **wyświetlić szczegóły dotyczące innego projektu migracji**.

3. W **Azure Migrate**wybierz narzędzie do oceny, którego chcesz użyć.

    - W przypadku korzystania z oceny serwera Azure Migrate można konfigurować, uruchamiać i przeglądać oceny bezpośrednio w projekcie Azure Migrate.
    - Jeśli używasz innego narzędzia do oceny, przejdź do linku dostępnego dla swojej lokacji i uruchom ocenę zgodnie z instrukcjami, które zapewnia.


## <a name="select-a-database-assessment-tool"></a>Wybierz narzędzie do oceny bazy danych

1. Kliknij pozycję **Oceń i Przeprowadź migrację baz danych**
2. W obszarze **bazy danych**kliknij pozycję **Dodaj narzędzia**.
3. W obszarze Dodawanie narzędzia > **Wybieranie**narzędzia do oceny wybierz narzędzie, którego chcesz użyć do oceny bazy danych.

## <a name="select-a-web-app-assessment-tool"></a>Wybierz narzędzie do oceny aplikacji sieci Web

1. Kliknij pozycję **Oceń i Migruj aplikacje sieci Web**.
2. Użyj linku do narzędzia migracji dla Azure App Service. Użyj narzędzia migracji, aby:

    - **Oceń aplikacje online**: możesz ocenić aplikacje za pomocą publicznego adresu URL w trybie online, używając Asystent migracji Azure App Service.
    - **.NET/php**: w przypadku wewnętrznych aplikacji platformy .NET i php można pobrać i uruchomić Asystent migracji.



## <a name="next-steps"></a>Następne kroki

Wypróbuj ocenę przy użyciu Azure Migrate oceny serwera dla maszyn wirtualnych [VMware](tutorial-prepare-vmware.md) , [funkcji Hyper-V](tutorial-prepare-hyper-v.md)lub [serwerów fizycznych](tutorial-prepare-physical.md)
