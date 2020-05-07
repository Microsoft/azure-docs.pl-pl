---
title: Zarządzanie użytkownikami w Azure Stack Edge | Microsoft Docs
description: Opisuje sposób używania Azure Portal do zarządzania użytkownikami na Azure Stack brzegowej.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 410f9702184c4ac58fb905b6f2eeebaea3e3c881
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570332"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-azure-stack-edge"></a>Użyj Azure Portal, aby zarządzać użytkownikami na platformie Azure Azure Stack Edge

W tym artykule opisano sposób zarządzania użytkownikami na Azure Stackej krawędzi. Możesz zarządzać Azure Stack krawędzią za pośrednictwem Azure Portal lub za pośrednictwem lokalnego interfejsu użytkownika sieci Web. Witryna Azure Portal umożliwia dodawanie, modyfikowanie i usuwanie użytkowników.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie użytkownika
> * Modyfikowanie harmonogramu
> * Usuwanie użytkownika

## <a name="about-users"></a>Informacje o użytkownikach

Użytkownicy mogą mieć prawo tylko do odczytu lub pełne uprawnienia. Jak sugerują nazwy, użytkownicy z prawem tylko do odczytu mogą wyłącznie wyświetlać dane udziału. Użytkownicy z pełnymi uprawnieniami mogą odczytywać dane udziału, dokonywać zapisu w tych udziałach oraz modyfikować lub usuwać dane udziału.

 - **Użytkownik z pełnymi uprawnieniami** — użytkownik lokalny z pełnym dostępem.
 - **Użytkownik z prawem tylko do odczytu** — użytkownik lokalny z dostępem tylko do odczytu. Ci użytkownicy są powiązani z udziałami, które umożliwiają wykonywanie operacji tylko do odczytu.

Uprawnienia użytkownika są definiowane podczas jego tworzenia w czasie tworzenia udziału. Modyfikowanie uprawnień na poziomie udziału nie jest obecnie obsługiwane.

## <a name="add-a-user"></a>Dodawanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby dodać użytkownika.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do **omówienia > użytkowników**. Wybierz pozycję **+ Dodaj użytkownika** na pasku poleceń.

    ![Wybierz pozycję Dodaj użytkownika](media/azure-stack-edge-manage-users/add-user-1.png)

2. Określ nazwę i hasło użytkownika, którego chcesz dodać. Potwierdź hasło i wybierz pozycję **Dodaj**.

    ![Określ nazwę użytkownika i hasło](media/azure-stack-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Oto użytkownicy zarezerwowani przez system, których nie należy używać: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Powiadomienie jest wyświetlane po rozpoczęciu i zakończeniu tworzenia przez użytkownika. Po utworzeniu użytkownika na pasku poleceń wybierz pozycję **Odśwież** , aby wyświetlić zaktualizowaną listę użytkowników.


## <a name="modify-user"></a>Modyfikowanie harmonogramu

Po utworzeniu użytkownika możesz zmienić przypisane do niego hasło. Wybierz z listy użytkowników. Wprowadź i Potwierdź nowe hasło. Zapisz zmiany.
 
![Modyfikowanie harmonogramu](media/azure-stack-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Usuwanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć użytkownika.


1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do **omówienia > użytkowników**.

    ![Wybierz użytkownika do usunięcia](media/azure-stack-edge-manage-users/delete-user-1.png)

2. Wybierz użytkownika z listy użytkowników, a następnie wybierz pozycję **Usuń**.  

   ![Wybieranie pozycji Usuń](media/azure-stack-edge-manage-users/delete-user-2.png)

3. Po wyświetleniu monitu potwierdź usunięcie. 

   ![Potwierdzenie usunięcia](media/azure-stack-edge-manage-users/delete-user-3.png)

Lista użytkowników zostanie zaktualizowana w celu uwzględnienia usuniętego użytkownika.

![Zaktualizowana lista użytkowników](media/azure-stack-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać przepustowością](azure-stack-edge-manage-bandwidth-schedules.md).
