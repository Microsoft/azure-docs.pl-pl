---
title: Udostępnianie poza organizacji (Azure Portal) — Przewodnik Szybki Start dotyczący udziału danych platformy Azure
description: Dowiedz się, jak udostępniać dane klientom i partnerom za pomocą udziału danych platformy Azure w tym przewodniku Szybki Start.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 08/19/2020
ms.openlocfilehash: 7dd81afb3189055c194b8a94b4bcbdf48f85d6f2
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658528"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Szybki Start: udostępnianie danych za pomocą udziału danych platformy Azure w Azure Portal

W tym przewodniku szybki start dowiesz się, jak skonfigurować nowy udział danych platformy Azure przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .


## <a name="create-a-data-share-account"></a>Tworzenie konta udziału danych

Utwórz zasób udziału danych platformy Azure w grupie zasobów platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

1. Wyszukaj *udział danych*.

1. Wybierz pozycję **udział danych** i wybierz pozycję **Utwórz**.

1. Wypełnij podstawowe szczegóły zasobu udziału danych platformy Azure, korzystając z poniższych informacji. 

   **Ustawienie** | **Sugerowana wartość** | **Opis pola**
   |---|---|---|
   | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla konta udziału danych.|
   | Grupa zasobów | *Testuj grupę zasobów* | Użyj istniejącej grupy zasobów lub utwórz nową. |
   | Lokalizacja | *Wschodnie stany USA 2* | Wybierz region dla konta udziału danych.
   | Nazwa | *datashareaccount* | Określ nazwę konta udziału danych. |

1. Wybierz pozycję **Przegląd + Utwórz**, a następnie pozycję **Utwórz** , aby zainicjować obsługę konta udziału danych. Inicjowanie obsługi nowego konta udziału danych zwykle trwa około 2 minuty.

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

## <a name="create-a-data-share"></a>Tworzenie udziału danych

1. Przejdź do strony Przegląd udostępniania danych.

   ![Udostępnianie danych](./media/share-receive-data.png "Udostępnianie danych") 

1. Wybierz pozycję **Rozpocznij udostępnianie danych**.

1. Wybierz pozycję **Utwórz**.

1. Wypełnij szczegóły dotyczące udziału danych. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

   ![EnterShareDetails](./media/enter-share-details.png "Wprowadź szczegóły udostępniania") 

1. Wybierz pozycję **Continue** (Kontynuuj).

1. Aby dodać zbiory danych do swojego udziału, wybierz pozycję **Dodaj zestawy**. 

   ![Zestawy danych](./media/datasets.png "Zestawy danych")

1. Wybierz typ zestawu danych, który chcesz dodać. Zostanie wyświetlona inna lista typów zestawów danych w zależności od typu udziału (migawka lub miejsce w miejscu) wybranej w poprzednim kroku. W przypadku udostępniania z poziomu Azure SQL Database lub Azure SQL Data Warehouse zostanie wyświetlony monit o podanie pewnych poświadczeń SQL. Uwierzytelnianie przy użyciu użytkownika utworzonego w ramach wymagań wstępnych.

   ![Adddatasets](./media/add-datasets.png "Dodaj zestawy danych")    

1. Przejdź do obiektu, który chcesz udostępnić, i wybierz pozycję "Dodaj zestawy danych". 

   ![SelectDatasets](./media/select-datasets.png "Wybierz zestawy danych")    

1. Na karcie adresaci wprowadź adres e-mail odbiorcy danych, wybierając pozycję "+ Dodaj odbiorcę".

   ![Addrecipients](./media/add-recipient.png "Dodawanie adresatów") 

1. Wybierz pozycję **Continue** (Kontynuuj).

1. W przypadku wybrania typu udziału migawek można skonfigurować harmonogram migawek w taki sposób, aby dostarczał aktualizacje danych do konsumenta danych. 

   ![EnableSnapshots](./media/enable-snapshots.png "Włącz migawki") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz pozycję **Continue** (Kontynuuj).

1. Na karcie Recenzja + tworzenie przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Wybierz pozycję **Utwórz**.

Udział danych platformy Azure został utworzony, a odbiorca Twojego udziału danych jest teraz gotowy do zaakceptowania Twojego zaproszenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasób nie jest już wymagany, przejdź do strony **Przegląd udziału danych** i wybierz pozycję **Usuń** , aby go usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia udziału danych platformy Azure. Aby dowiedzieć się, jak odbiorca danych może zaakceptować i odebrać udział danych, przejdź do samouczka [akceptowanie i odbieranie danych](subscribe-to-data-share.md) . 
