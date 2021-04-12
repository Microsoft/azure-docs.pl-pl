---
title: Zarządzanie harmonogramami przepustowości w usłudze Azure Data Box Gateway | Microsoft Docs
description: W tym artykule opisano sposób zarządzania użytkownikami w usłudze Azure Data Box Gateway przy użyciu witryny Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 9e941007ddc27f809de7d43cd33e44c5b521a6bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582482"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Zarządzanie użytkownikami w usłudze Azure Data Box Gateway przy użyciu witryny Azure Portal

W tym artykule opisano sposób zarządzania użytkownikami w usłudze Azure Data Box Gateway. Usługą Azure Data Box Gateway można zarządzać w witrynie Azure Portal lub za pomocą lokalnego internetowego interfejsu użytkownika. Witryna Azure Portal umożliwia dodawanie, modyfikowanie i usuwanie użytkowników. 

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

1. W Azure Portal przejdź do zasobu Data Box Gateway, a następnie przejdź do pozycji **Przegląd**. Na pasku poleceń kliknij przycisk **+ Dodaj użytkownika**.

    ![Klikanie przycisku Dodaj użytkownika](media/data-box-gateway-manage-users/add-user-1.png)

2. Określ nazwę i hasło użytkownika, którego chcesz dodać. Potwierdź hasło, a następnie kliknij przycisk **Dodaj**.

    ![Kliknij pozycję Dodaj użytkownika 2](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Oto użytkownicy zarezerwowani przez system, których nie należy używać: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Otrzymasz powiadomienie, gdy tworzenie użytkownika zostanie rozpoczęte i zakończone. Po utworzeniu użytkownika na pasku poleceń kliknij przycisk **Odśwież**, aby wyświetlić zaktualizowaną listę użytkowników.


## <a name="modify-user"></a>Modyfikowanie harmonogramu

Po utworzeniu użytkownika możesz zmienić przypisane do niego hasło. Kliknij wybraną pozycję na liście użytkowników. Podaj i potwierdź nowe hasło. Zapisz zmiany.
 
![Modyfikowanie harmonogramu](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Usuwanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć użytkownika.

1. Wybierz i kliknij użytkownika z listy użytkowników, a następnie kliknij przycisk **Usuń**.  

   ![Usuwanie użytkownika](media/data-box-gateway-manage-users/delete-user-1.png)

2. Po wyświetleniu monitu potwierdź usunięcie. 

   ![Usuwanie użytkownika 2](media/data-box-gateway-manage-users/delete-user-2.png)

Lista użytkowników zostanie zaktualizowana w celu uwzględnienia usuniętego użytkownika.

![Usuń użytkownika 3](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać przepustowością](data-box-gateway-manage-bandwidth-schedules.md).
