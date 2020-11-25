---
title: dołączanie pliku
description: dołączanie pliku
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 69259023484457249acd4c7b4a65c4cbe3e660ca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002866"
---
## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usługi Recovery Services to jednostka, która przechowuje utworzone punkty odzyskiwania oraz udostępnia interfejs umożliwiający wykonywanie operacji tworzenia kopii zapasowych. Obejmują one wykonywanie kopii zapasowych na żądanie, wykonywanie operacji przywracania i tworzenie zasad kopii zapasowych.

Aby utworzyć magazyn usługi Recovery Services, wykonaj poniższe kroki.

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

1. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

    ![Wybieranie pozycji Wszystkie usługi](./media/backup-create-rs-vault/click-all-services.png)

1. W oknie dialogowym **Wszystkie usługi** wprowadź frazę *Recovery Services*. Lista zasobów jest filtrowana zgodnie z danymi wejściowymi. Na liście zasobów wybierz pozycję **Magazyny usługi Recovery Services**.

    ![Wprowadzanie i wybieranie pozycji Magazyny usługi Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

1. Na pulpicie nawigacyjnym **Magazyny usługi Recovery Services** wybierz pozycję **Dodaj**.

    ![Dodawanie magazynu usługi Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Zostanie otwarte okno dialogowe **Magazyn usługi Recovery Services**. Podaj wartości w polach **Nazwa**, **Subskrypcja**, **Grupa zasobów** i **Lokalizacja**.

    ![Konfigurowanie magazynu usługi Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nazwa**: Wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikalna w subskrypcji platformy Azure. Podaj nazwę zawierającą od 2 do 50 znaków. Nazwa musi rozpoczynać się od litery i składać się tylko z liter, cyfr i łączników.
   - **Subskrypcja**: Wybierz subskrypcję, która ma zostać użyta. Jeśli jesteś członkiem tylko jednej subskrypcji, zostanie wyświetlona jej nazwa. Jeśli nie masz pewności, której subskrypcji użyć, wybierz domyślną (sugerowaną). Większa liczba opcji do wyboru jest dostępna tylko w przypadku, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure.
   - **Grupa zasobów**: Użyj istniejącej grupy zasobów lub utwórz nową. Aby wyświetlić listę grup zasobów dostępnych w ramach subskrypcji, wybierz pozycję **Użyj istniejącej**, a następnie wybierz zasób z listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nową** i wprowadź nazwę. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Lokalizacja**: Wybierz region geograficzny dla magazynu. Aby utworzyć magazyn do ochrony jakiegokolwiek źródła danych, *musi* on znajdować się w tym samym regionie co źródło danych.

      > [!IMPORTANT]
      > Jeśli nie masz pewności co do lokalizacji źródła danych, zamknij okno dialogowe. Przejdź do listy zasobów w portalu. Jeśli Twoje źródła danych znajdują się w wielu regionach, utwórz magazyn usługi Recovery Services dla każdego regionu. Najpierw utwórz magazyn w pierwszej lokalizacji, a dopiero później w kolejnej. Nie trzeba określać kont magazynu do przechowywania danych kopii zapasowej. Magazyn usługi Recovery Services i usługa Azure Backup obsługują je automatyczne.
      >
      >

1. Po podaniu wartości wybierz pozycję **Przejrzyj i utwórz**.

    ![Zrzut ekranu pokazujący przycisk Recenzja i tworzenie w procesie tworzenia magazynu Recovery Services.](./media/backup-create-rs-vault/review-and-create.png)

1. Gdy wszystko będzie gotowe do utworzenia magazynu usługi Recovery Services, wybierz pozycję **Utwórz**.

    ![Tworzenie magazynu usługi Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Utworzenie magazynu usługi Recovery Services może zająć trochę czasu. Monitoruj powiadomienia o stanie wyświetlane w obszarze **Powiadomienia** w prawym górnym rogu okna portalu. Po utworzeniu magazynu będzie on widoczny na liście magazynów usługi Recovery Services. Jeśli magazyn nie jest widoczny, wybierz pozycję **Odśwież**.

     ![Odświeżanie listy magazynów kopii zapasowych](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> Przed skonfigurowaniem kopii zapasowych w magazynie zdecydowanie zalecamy zapoznanie się z ustawieniami domyślnymi **Typ replikacji magazynu** i **Ustawienia zabezpieczeń**. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Ustawianie nadmiarowości magazynu](../articles/backup/backup-create-rs-vault.md#set-storage-redundancy) .