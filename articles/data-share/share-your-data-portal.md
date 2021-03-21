---
title: Udostępnianie poza organizacji (Azure Portal) — Przewodnik Szybki Start dotyczący udziału danych platformy Azure
description: Dowiedz się, jak udostępniać dane klientom i partnerom za pomocą udziału danych platformy Azure w tym przewodniku Szybki Start.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 10/30/2020
ms.openlocfilehash: 1442720fdf48aaa7da76e181b168a04306ff3e33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96186460"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Szybki Start: udostępnianie danych za pomocą udziału danych platformy Azure w Azure Portal

W tym przewodniku szybki start dowiesz się, jak skonfigurować nowy udział danych platformy Azure w celu udostępniania danych z konta magazynu przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Adres e-mail logowania odbiorcy platformy Azure (przy użyciu aliasu poczty e-mail nie będzie działał).
* Jeśli źródłowy magazyn danych platformy Azure znajduje się w innej subskrypcji platformy Azure niż ta, która będzie używana do tworzenia zasobu udziału danych, zarejestruj [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji, w której znajduje się magazyn danych platformy Azure. 

### <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu

* Konto usługi Azure Storage: Jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](../storage/common/storage-account-create.md)
* Uprawnienie do zapisu na koncie magazynu, które jest obecne w usłudze *Microsoft. Storage/storageAccounts/Write*. To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie do dodawania przypisania roli do konta magazynu, które jest obecne w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. To uprawnienie istnieje w roli **Właściciel**. 

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

## <a name="create-a-share"></a>Tworzenie udziału

1. Przejdź do strony Przegląd udostępniania danych.

   ![Udostępnianie danych](./media/share-receive-data.png "Udostępnianie danych") 

1. Wybierz pozycję **Rozpocznij udostępnianie danych**.

1. Wybierz przycisk **Utwórz**.

1. Wprowadź szczegółowe informacje o udziale. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

   ![EnterShareDetails](./media/enter-share-details.png "Wprowadź szczegóły udostępniania") 

1. Wybierz opcję **Kontynuuj**.

1. Aby dodać zestawy danych do udziału, wybierz pozycję **Dodaj zestawy danych**. 

   ![Dodawanie zestawów danych do udziału](./media/datasets.png "Zestawy danych")

1. Wybierz typ zestawu danych, który chcesz dodać. Zostanie wyświetlona inna lista typów zestawów danych w zależności od typu udziału (migawka lub miejsce w miejscu) wybranej w poprzednim kroku. 

   ![Adddatasets](./media/add-datasets.png "Dodaj zestawy danych")    

1. Przejdź do obiektu, który chcesz udostępnić, i wybierz pozycję "Dodaj zestawy danych". 

   ![SelectDatasets](./media/select-datasets.png "Wybierz zestawy danych")    

1. Na karcie adresaci wprowadź adres e-mail odbiorcy danych, wybierając pozycję "+ Dodaj odbiorcę".

   ![Addrecipients](./media/add-recipient.png "Dodawanie adresatów") 

1. Wybierz opcję **Kontynuuj**.

1. W przypadku wybrania typu udziału migawek można skonfigurować harmonogram migawek w taki sposób, aby dostarczał aktualizacje danych do konsumenta danych. 

   ![EnableSnapshots](./media/enable-snapshots.png "Włącz migawki") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz opcję **Kontynuuj**.

1. Na karcie Recenzja + tworzenie przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Wybierz przycisk **Utwórz**.

Udział danych platformy Azure został utworzony, a odbiorca Twojego udziału danych jest teraz gotowy do zaakceptowania Twojego zaproszenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasób nie jest już wymagany, przejdź do strony **Przegląd udziału danych** i wybierz pozycję **Usuń** , aby go usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia udziału danych platformy Azure. Aby dowiedzieć się, jak odbiorca danych może zaakceptować i odebrać udział danych, przejdź do samouczka [akceptowanie i odbieranie danych](subscribe-to-data-share.md) . 
