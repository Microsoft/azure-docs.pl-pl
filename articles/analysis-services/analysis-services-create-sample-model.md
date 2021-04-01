---
title: Samouczek — Dodawanie przykładowego modelu — Azure Analysis Services | Microsoft Docs
description: W tym samouczku dowiesz się, jak dodać przykładowy model w Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f882a40940a5c7202e9cf1f5c8b8927f008f4a39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92013614"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Samouczek: Dodawanie przykładowego modelu z portalu

W tym samouczku dodasz na serwerze przykładową tabelaryczną modelową bazę danych Adventure Works. Przykładowy model to gotowa wersja przykładowego modelu danych projektu Adventure Works Internet Sales (1200). Przykładowy model przydaje się do testowania zarządzania modelami, nawiązywania połączeń z narzędziami i aplikacjami klienckimi oraz wykonywania zapytań w danych modelu. W tym samouczku użyto witryny [Azure Portal](https://portal.azure.com) i programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), aby wykonać następujące czynności: 

> [!div class="checklist"]
> * Dodawanie na serwerze gotowego przykładowego modelu danych tabelarycznych 
> * Nawiązywanie połączenia z modelem za pomocą programu SSMS

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Serwer usług Azure Analysis Services. Więcej informacji można znaleźć w temacie [Tworzenie serwera — portal](analysis-services-create-server.md).
- Uprawnienia administratora serwera
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [portalu](https://portal.azure.com/).

## <a name="add-a-sample-model"></a>Dodawanie przykładowego modelu

1. W oknie **Omówienie** serwera kliknij pozycję **Nowy model**.

    ![Tworzenie przykładowego modelu](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. W obszarze **Nowy model**  >  **Wybierz źródło danych**, sprawdź, czy wybrano **przykładowe dane** , a następnie kliknij przycisk **Dodaj**.

    ![Wybierz nowy model](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. W oknie **Omówienie** sprawdź, czy został dodany przykładowy model `adventureworks`.

    ![Wybieranie przykładowych danych](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Przykładowy model korzysta z zasobów pamięci podręcznej. Jeśli nie używasz przykładowego modelu do testowania, usuń go z serwera.

W poniższych krokach opisano sposób usuwania modelu z serwera za pomocą programu SSMS.

1. W programie SSMS wybierz pozycję **Eksplorator obiektów**, a następnie kliknij pozycję **Połącz** > **Analysis Services**.

2. W polu **Połącz z serwerem** wklej nazwę serwera, a następnie w polu **Uwierzytelnianie** wybierz pozycję **Active Directory — Uniwersalne z obsługą uwierzytelniania wieloskładnikowego**, wprowadź nazwę użytkownika, a następnie kliknij pozycję **Połącz**.

    ![Zaloguj](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy przykładową bazę danych `adventureworks`, a następnie kliknij pozycję **Usuń**.

    ![Usuwanie przykładowej bazy danych](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Następne kroki 

W tym samouczku przedstawiono sposób dodawania podstawowego, przykładowego modelu na serwerze. Teraz, gdy już masz bazę danych modelu, możesz nawiązać z nią połączenie z programu SQL Server Management Studio i dodać role użytkowników. Aby dowiedzieć się więcej, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: Konfigurowanie ról administratora i użytkowników serwera](tutorials/analysis-services-tutorial-roles.md)