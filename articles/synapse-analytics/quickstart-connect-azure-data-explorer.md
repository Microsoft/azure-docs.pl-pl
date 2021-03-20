---
title: 'Szybki Start: łączenie Eksplorator danych platformy Azure z obszarem roboczym usługi Azure Synapse Analytics'
description: Połącz klaster Eksplorator danych platformy Azure z obszarem roboczym usługi Azure Synapse Analytics, używając Apache Spark do analizy usługi Azure Synapse.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: ee9d137973bfa4eeb28bc6526437e76e781f3199
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92172283"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Nawiązywanie połączenia z usługą Azure Data Explorer przy użyciu platformy Apache Spark dla usługi Azure Synapse Analytics

W tym artykule opisano sposób uzyskiwania dostępu do usługi Azure Eksplorator danych Database z programu Synapse Studio przy użyciu Apache Spark usługi Azure Synapse Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz klaster Eksplorator danych i bazę danych platformy Azure](/azure/data-explorer/create-cluster-database-portal).
* Masz istniejący obszar roboczy usługi Azure Synapse Analytics lub Utwórz nowy obszar roboczy, wykonując kroki opisane w [przewodniku szybki start: Tworzenie obszaru roboczego usługi Azure Synapse](./quickstart-create-workspace.md).
* Mieć istniejącą pulę Apache Spark lub Utwórz nową pulę, wykonując czynności opisane w [przewodniku szybki start: Tworzenie puli Apache Spark przy użyciu Azure Portal](./quickstart-create-apache-spark-pool-portal.md).
* [Utwórz aplikację Azure Active Directory (Azure AD), udostępniając aplikację usługi Azure AD](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app).
* Aby umożliwić aplikacji usługi Azure AD dostęp do bazy danych, wykonaj kroki opisane w temacie [Zarządzanie uprawnieniami usługi azure Eksplorator danych Database](/azure/data-explorer/manage-database-permissions).

## <a name="go-to-synapse-studio"></a>Przejdź do Synapse Studio

W obszarze roboczym usługi Azure Synapse wybierz pozycję **Uruchom Synapse Studio**. Na stronie głównej programu Synapse Studio wybierz pozycję **dane** , aby przejść do **danych Eksplorator obiektów**.

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Łączenie bazy danych Eksplorator danych Azure z obszarem roboczym usługi Azure Synapse

Łączenie bazy danych Eksplorator danych Azure z obszarem roboczym odbywa się za pomocą połączonej usługi. Za pomocą połączonej usługi Eksplorator danych platformy Azure możesz przeglądać i eksplorować dane, czytać i pisać z Apache Spark na potrzeby usługi Azure Synapse. Zadania integracji można również uruchamiać w potoku.

W Eksplorator obiektów danych wykonaj następujące kroki, aby bezpośrednio połączyć klaster usługi Azure Eksplorator danych:

1. Wybierz **+** ikonę obok **danych**.
1. Wybierz pozycję **Połącz** , aby nawiązać połączenie z danymi zewnętrznymi.
1. Wybierz pozycję **Azure Eksplorator danych (Kusto)**.
1. Wybierz opcję **Kontynuuj**.
1. Użyj przyjaznej nazwy, aby nazwać połączoną usługę. Nazwa zostanie wyświetlona w Eksplorator obiektów danych i będzie używana przez środowisko uruchomieniowe usługi Azure Synapse do łączenia się z bazą danych.
1. Wybierz klaster usługi Azure Eksplorator danych z subskrypcji lub wprowadź identyfikator URI.
1. Wprowadź **Identyfikator jednostki usługi** i **klucz jednostki usługi**. Upewnij się, że ta jednostka usługi ma dostęp do widoku bazy danych na potrzeby operacji odczytu i uzyskiwania dostępu do pozyskiwania danych.
1. Wprowadź nazwę bazy danych usługi Azure Eksplorator danych.
1. Wybierz **Test connection** , aby upewnić się, że masz odpowiednie uprawnienia.
1. Wybierz przycisk **Utwórz**.

    ![Zrzut ekranu pokazujący nową połączoną usługę.](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > Obowiązkowe **Test connection** nie sprawdza poprawności dostępu do zapisu. Upewnij się, że identyfikator jednostki usługi ma dostęp do zapisu w bazie danych Azure Eksplorator danych.

1. Klastry usługi Azure Eksplorator danych i bazy danych są wyświetlane na karcie **połączonej** w sekcji **Eksplorator danych platformy Azure** .

    ![Zrzut ekranu przedstawiający przeglądanie w poszukiwaniu klastrów.](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE]
    > W bieżącej wersji obiekty bazy danych są wypełniane na podstawie uprawnień konta usługi Azure AD w bazach danych usługi Azure Eksplorator danych. Po uruchomieniu notesów Apache Spark lub zadań integracji zostanie użyta poświadczenie w usłudze link (na przykład nazwa główna usługi).

## <a name="quickly-interact-with-code-generated-actions"></a>Szybkie współdziałanie z akcjami generowanymi przez kod

Po kliknięciu prawym przyciskiem myszy bazy danych lub tabeli zostanie wyświetlona lista przykładowych notesów platformy Spark. Wybierz opcję odczytu, zapisu lub przesyłania strumieniowego danych do usługi Azure Eksplorator danych.

[![Zrzut ekranu pokazujący nowe przykładowe notesy.](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

Oto przykład odczytywania danych. Dołącz Notes do puli platformy Spark i uruchom komórkę.

[![Zrzut ekranu przedstawiający nowy Notes do odczytu.](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > Wykonanie sesji Spark może zająć więcej niż trzy minuty. Kolejne wykonania będą znacznie szybsze.

## <a name="limitations"></a>Ograniczenia

Łącznik usługi Azure Eksplorator danych nie jest obecnie obsługiwany w przypadku sieci wirtualnych zarządzanych przez usługę Azure Synapse.

## <a name="next-steps"></a>Następne kroki

* [Przykładowy kod z zaawansowanymi opcjami](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Łącznik Spark platformy Azure Eksplorator danych (Kusto)](https://github.com/Azure/azure-kusto-spark)