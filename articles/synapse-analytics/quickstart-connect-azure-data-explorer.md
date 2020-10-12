---
title: 'Szybki Start: łączenie Eksplorator danych platformy Azure z obszarem roboczym Synapse'
description: Jak połączyć klaster Eksplorator danych platformy Azure z obszarem roboczym Synapse przy użyciu usługi Azure Synapse Apache Spark
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 53092f5c49073098f28d2fd06f38391e858b2b9d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946400"
---
# <a name="connect-to-azure-data-explorer-using-synapse-apache-spark"></a>Nawiązywanie połączenia z usługą Azure Eksplorator danych przy użyciu usługi Synapse Apache Spark

W tym artykule opisano sposób uzyskiwania dostępu do baz danych usługi Azure Eksplorator danych z programu Synapse Studio z Synapse Apache Spark. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz klaster Eksplorator danych i bazę danych platformy Azure](/azure/data-explorer/create-cluster-database-portal).
* Istniejący obszar roboczy Synapse lub Utwórz nowy obszar roboczy po tym [przewodniku szybki start](./quickstart-create-workspace.md) 
* Istniejąca Synapse puli Apache Spark lub Utwórz nową pulę po tym [przewodniku szybki start](./quickstart-create-apache-spark-pool-portal.md)
* [Utwórz aplikację usługi Azure AD, udostępniając aplikację usługi Azure AD.](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* Przyznaj aplikacji usługi Azure AD dostęp do bazy danych w następujący sposób: [Zarządzanie uprawnieniami usługi azure Eksplorator danych Database](/azure/data-explorer/manage-database-permissions)

## <a name="navigate-to-synapse-studio"></a>Przejdź do Synapse Studio

W obszarze roboczym usługi Synapse wybierz pozycję **Uruchom Synapse Studio**. Na stronie głównej programu Synapse Studio wybierz pozycję **dane**, co spowoduje przejście do **Eksplorator obiektów danych**.

## <a name="connect-an-azure-data-explorer-database-to-a-synapse-workspace"></a>Łączenie bazy danych Eksplorator danych Azure z obszarem roboczym usługi Synapse

Łączenie bazy danych Eksplorator danych Azure z obszarem roboczym odbywa się za pomocą połączonej usługi. Połączona usługa Eksploruj dane platformy Azure umożliwia użytkownikom przeglądanie i Eksplorowanie danych, Odczyt i zapis z Apache Spark usługi Azure Synapse Analytics oraz uruchamianie zadań integracji w potoku.

W Eksplorator obiektów danych wykonaj następujące kroki, aby bezpośrednio połączyć klaster usługi Azure Eksplorator danych:

1. Wybierz **+** ikonę obok danych
2. Wybierz pozycję **Połącz** z danymi zewnętrznymi
3. Wybierz pozycję **Azure Eksplorator danych (Kusto)**
5. Wybierz przycisk **Kontynuuj**
6. Nazwij połączoną usługę. Nazwa zostanie wyświetlona w Eksplorator obiektów i użyta przez Synapse Run-Times do nawiązania połączenia z bazą danych. Zalecamy używanie przyjaznej nazwy
7. Wybierz klaster eksplorowania danych platformy Azure z subskrypcji lub wprowadź identyfikator URI.
8. Wprowadź "identyfikator jednostki usługi" i "klucz główny usługi" (Upewnij się, że ta jednostka usługi ma dostęp do widoku w bazie danych na potrzeby operacji odczytu i dostępu do pozyskiwania danych).
9. Wprowadź nazwę bazy danych usługi Azure Eksplorator danych
10. Kliknij przycisk **Test connection** , aby upewnić się, że masz odpowiednie uprawnienia
11. Wybierz pozycję **Utwórz**

    ![Nowa połączona usługa](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > Obowiązkowe Test connection nie sprawdza poprawności dostępu do zapisu, upewnij się, że identyfikator jednostki usługi ma dostęp do zapisu w bazie danych Azure Eksplorator danych.

12. Klastry usługi Azure Eksplorator danych i bazy danych są widoczne na karcie  **połączonej** w sekcji Eksplorator danych platformy Azure. 

    ![Przeglądaj klastry](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE] 
    > W bieżącej wersji obiekty bazy danych są wypełniane na podstawie uprawnień konta usługi AAD w bazach danych Eksplorator danych Azure. Po uruchomieniu notesów Apache Spark lub zadań integracji zostanie użyta poświadczenie w usłudze link (tj. Nazwa główna usługi).


## <a name="quickly-interact-with-code-generated-actions"></a>Szybkie współdziałanie z akcjami generowanymi przez kod

* Po kliknięciu prawym przyciskiem myszy bazy danych lub tabeli masz listę gestów, która spowoduje wyzwolenie przykładowego notesu platformy Spark na potrzeby odczytywania danych, zapisywanie danych i przesyłanie strumieniowe danych do usługi Azure Eksplorator danych. 
    [![Nowe przykładowe notesy](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

* Oto przykład odczytywania danych. Dołączono Notes do puli platformy Spark i uruchamiasz [ ![ Nowy Notes do odczytu](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE] 
   > Pierwsze wykonanie może potrwać więcej niż trzy minuty na zainicjowanie sesji platformy Spark. Kolejne wykonania będą znacznie szybsze.  


## <a name="limitations"></a>Ograniczenia
Usługa Azure Eksplorator danych Connector nie jest obecnie obsługiwana w przypadku zarządzanej sieci wirtualnej platformy Azure Synapse.


## <a name="next-steps"></a>Następne kroki

* [Przykładowy kod z zaawansowanymi opcjami](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Łącznik Spark platformy Azure Eksplorator danych (Kusto)](https://github.com/Azure/azure-kusto-spark)