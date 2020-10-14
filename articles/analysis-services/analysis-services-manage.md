---
title: Zarządzanie Azure Analysis Services | Microsoft Docs
description: W tym artykule opisano narzędzia służące do zarządzania zadaniami administracyjnymi i zarządzania dla serwera Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 697e5484c007c095201344fb4829a92eb642a6e3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014855"
---
# <a name="manage-analysis-services"></a>Zarządzanie usługami Analysis Services
Po utworzeniu serwera Analysis Services na platformie Azure mogą istnieć pewne zadania administracyjne i administracyjne, które należy wykonać od razu lub w dół. Na przykład możesz uruchomić przetwarzanie do danych odświeżania, kontrolować, kto może uzyskać dostęp do modeli na serwerze, lub monitorować kondycję serwera. Niektóre zadania zarządzania można wykonywać tylko w Azure Portal, innych w programie SQL Server Management Studio (SSMS), a niektóre zadania można wykonać w dowolnym z nich.

## <a name="azure-portal"></a>Azure Portal
[Azure Portal](https://portal.azure.com/) to miejsce, w którym można tworzyć i usuwać serwery, monitorować zasoby serwera, zmieniać rozmiar i zarządzać osobami, które mają dostęp do serwerów.  Jeśli masz jakieś problemy, możesz również przesłać żądanie pomocy technicznej.

![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Nawiązywanie połączenia z serwerem na platformie Azure odbywa się tak samo jak łączenie z wystąpieniem serwera w organizacji. W programie SSMS można wykonać wiele z tych samych zadań, takich jak przetwarzanie danych lub Tworzenie skryptu przetwarzania, zarządzanie rolami i korzystanie z programu PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Pobieranie i instalowanie programu SSMS
Aby uzyskać najnowsze funkcje i bezproblemowe środowisko podczas nawiązywania połączenia z serwerem Azure Analysis Services, upewnij się, że używasz najnowszej wersji programu SSMS. 

[Pobierz SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Aby nawiązać połączenie za pomocą programu SSMS
 W przypadku korzystania z programu SSMS przed nawiązaniem połączenia z serwerem po raz pierwszy upewnij się, że nazwa użytkownika jest uwzględniona w grupie Administratorzy Analysis Services. Aby dowiedzieć się więcej, zobacz temat [Administratorzy serwera i użytkownicy bazy danych](#server-administrators-and-database-users) w dalszej części tego artykułu.

1. Przed nawiązaniem połączenia należy uzyskać nazwę serwera. Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
    ![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. W programie SSMS wybierz pozycję **Eksplorator obiektów**, a następnie kliknij pozycję **Połącz** > **Analysis Services**.
3. W oknie dialogowym **łączenie z serwerem** Wklej w polu Nazwa serwera, a następnie w obszarze **uwierzytelnianie**wybierz jeden z następujących typów uwierzytelniania:   
    > [!NOTE]
    > Zalecany jest typ uwierzytelniania, **Active Directory uniwersalny z obsługą uwierzytelniania WIELOskładnikowego**.

    > [!NOTE]
    > Jeśli zalogujesz się przy użyciu konta Microsoft, usługi Live ID, usługi Yahoo, usługi Gmail itp., pozostaw pole hasło puste. Po kliknięciu przycisku Połącz zostanie wyświetlony monit o podanie hasła.

    **Uwierzytelnianie systemu Windows** w celu użycia poświadczeń systemu Windows domena\nazwa użytkownika i hasła.

    **Active Directory uwierzytelnianie hasła** w celu korzystania z konta organizacyjnego. Na przykład podczas nawiązywania połączenia z komputera, który nie jest przyłączony do domeny.

    **Active Directory — uniwersalna obsługa usługi MFA** w celu korzystania z [uwierzytelniania nieinterakcyjnego lub wieloskładnikowego](../azure-sql/database/authentication-mfa-ssms-overview.md). 
   
    ![Nawiązywanie połączenia w programie SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Administratorzy serwera i użytkownicy bazy danych
W Azure Analysis Services istnieją dwa typy użytkowników, Administratorzy serwerów i użytkownicy baz danych. Oba typy użytkowników muszą znajdować się w Azure Active Directory i muszą być określone przy użyciu adresu e-mail organizacji lub nazwy UPN. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Rozwiązywanie problemów z połączeniem
W przypadku nawiązywania połączenia przy użyciu programu SSMS może być konieczne wyczyszczenie pamięci podręcznej logowania. Nic nie jest buforowane na dysku. Aby wyczyścić pamięć podręczną, Zamknij i uruchom ponownie proces łączenia. 

## <a name="next-steps"></a>Następne kroki
Jeśli model tabelaryczny nie został jeszcze wdrożony na nowym serwerze, teraz jest to dobry czas. Aby dowiedzieć się więcej, zobacz artykuł [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Wdrażanie w usługach Azure Analysis Services).

Jeśli model został wdrożony na serwerze, możesz połączyć się z nim za pomocą klienta lub przeglądarki. Aby dowiedzieć się więcej, zobacz [pobieranie danych z serwera Azure Analysis Services](analysis-services-connect.md).