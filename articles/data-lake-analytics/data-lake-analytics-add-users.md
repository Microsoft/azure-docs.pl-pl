---
title: Dodawanie użytkowników do konta Azure Data Lake Analytics
description: Dowiedz się, jak poprawnie dodawać użytkowników do konta Data Lake Analytics przy użyciu Kreatora dodawania użytkownika i Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: 4819b5cb252d2c646905d277fcbedc57a55fe692
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450216"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Dodawanie użytkownika w witrynie Azure Portal

## <a name="start-the-add-user-wizard"></a>Uruchom Kreatora dodawania użytkownika
1. Otwórz Azure Data Lake Analytics za pośrednictwem https://portal.azure.com .
2. Kliknij przycisk **Dodaj użytkownika Kreatora**.
3. W kroku **Wybierz użytkownika** Znajdź użytkownika, który chcesz dodać. Kliknij pozycję **Wybierz**.
4. **Wybierz krok roli** , wybierz **Data Lake Analytics deweloper**. Ta rola ma minimalny zestaw uprawnień wymaganych do przesyłania i monitorowania zadań U-SQL oraz zarządzania nimi. Przypisz do tej roli, jeśli grupa nie jest przeznaczona do zarządzania usługami platformy Azure.
5. W kroku **Wybierz uprawnienia katalogu** wybierz Wszystkie dodatkowe bazy danych, do których użytkownik będzie potrzebował dostępu. Do przesyłania zadań wymagane są uprawnienia do odczytu i zapisu do domyślnej statycznej bazy danych o nazwie "Master". Gdy wszystko będzie gotowe, kliknij pozycję **OK**.
6. W ostatnim kroku o nazwie **Przypisz wybrane uprawnienia** Przejrzyj zmiany wprowadzone przez kreatora. Kliknij przycisk **OK**.


## <a name="configure-acls-for-data-folders"></a>Konfigurowanie list ACL dla folderów danych
W razie konieczności Udziel "R-X" lub "RWX" w folderach zawierających dane wejściowe i dane wyjściowe.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Opcjonalnie Dodaj użytkownika do roli **czytnika** roli Azure Data Lake Storage Gen1.
1.  Znajdź konto Azure Data Lake Storage Gen1.
2.  Kliknij pozycję **Użytkownicy**.
3. Kliknij pozycję **Dodaj**.
4.  Wybierz rolę platformy Azure, aby przypisać tę grupę.
5.  Przypisz do roli czytelnik. Ta rola ma minimalny zestaw uprawnień wymaganych do przeglądania i zarządzania danymi przechowywanymi w ADLSGen1. Przypisz do tej roli, jeśli grupa nie jest przeznaczona do zarządzania usługami platformy Azure.
6.  Wpisz nazwę grupy.
7.  Kliknij przycisk **OK**.

## <a name="adding-a-user-using-powershell"></a>Dodawanie użytkownika przy użyciu programu PowerShell

1. Postępuj zgodnie z instrukcjami w tym przewodniku: [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/).
2. Pobierz skrypt programu [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell.
3. Uruchom skrypt programu PowerShell. 

Przykładowe polecenie umożliwiające użytkownikowi dostęp do przesyłania zadań, wyświetlania nowych metadanych zadań i wyświetlania starych metadanych:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Wprowadzenie do Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-get-started-portal.md)
* [Zarządzanie Azure Data Lake Analytics przy użyciu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
