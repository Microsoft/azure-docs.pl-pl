---
title: Porównanie narzędzi migracji usługi Azure Storage — dane bez struktury
description: Podstawowe funkcje i porównanie między narzędziami używanymi do migracji danych bez struktury
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15daeb0e6bf320a0727d8e6ea502063a30e67ad0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231587"
---
# <a name="comparison-matrix"></a>Macierz porównawcza

W poniższej macierzy porównawczej przedstawiono podstawowe funkcje różnych narzędzi, których można użyć do migracji danych bez struktury. 

## <a name="supported-azure-services"></a>Obsługiwane usługi platformy Azure

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nazwa rozwiązania**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Obsługa Azure Files (wszystkie warstwy)** | Tak                          | Tak                      | Tak            | Tak                            |
| **Obsługa Azure NetApp Files**      | Nie                           | Tak                      | Tak            | Tak                            |
| **Gorąca i chłodna obsługa obiektów blob platformy Azure**   | Nie                           | Tak (za pośrednictwem systemu NFS — wersja zapoznawcza)    | Tak            | Tak                            |
| **Obsługa warstwy archiwum obiektów blob platformy Azure** | Nie                           | Nie                       | Nie             | Tak (jako miejsce docelowe migracji) |
| **Obsługa Azure Data Lake Storage** | Nie                           | Nie                       | Nie             | Nie                             |
| **Obsługiwane źródła**      | Windows Server 2012 R2 i w górę | Usługa NAS & systemy plików w chmurze | Dowolny serwer NAS i S3 | NAS, BLOB, S3                  |

## <a name="supported-protocols-source--destination"></a>Obsługiwane protokoły (Źródło/miejsce docelowe)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nazwa rozwiązania**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Tak | Tak | Tak | Tak |
| **SMB 3.0**       | Tak | Tak | Tak | Tak |
| **PROTOKÓŁ SMB 3,1**       | Tak | Tak | Tak | Tak |
| **System plików NFS v3**        | Nie  | Tak | Tak | Tak |
| **System plików NFS v 4.1**      | Nie  | Tak | Nie  | Tak |
| **Interfejs API REST usługi BLOB** | Nie  | Nie  | Tak | Tak |
| **S3**            | Nie  | Tak | Tak | Tak |

## <a name="extended-features"></a>Funkcje rozszerzone

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nazwa rozwiązania**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Ponowne mapowanie UID/SID**                   | Nie  | Tak                        | Tak | Nie                             |
| **Ponowne mapowanie listy ACL protokołu**                | Nie  | Nie                         | Nie  | Nie                             |
| **Obsługa systemu plików DFS**                           | Tak | Tak                        | Tak | Tak                            |
| **Obsługa ograniczania przepustowości**                    | Tak | Tak                        | Tak | Tak                            |
| **Wykluczenia wzorców plików**               | Nie  | Tak                        | Tak | Tak (przy użyciu funkcji kopiowania) |
| **Obsługa atrybutów plików selektywnych** | Tak | Tak                        | Tak | Tak (dla atrybutów rozszerzonych)  |
| **Usuń propagacje**                   | Tak | Tak                        | Tak | Tak                            |
| **Śledź rozgałęzienia NTFS**                 | Nie  | Tak                        | Nie  | Tak                            |
| **Przesłoń właściciela protokołu SMB i właściciela grupy**    | Tak | Tak                        | Tak | Nie                             |
| **Łańcuch raportów o opiekach**            | Nie  | Tak                        | Nie  | Tak                            |
| **Obsługa alternatywnych strumieni danych**    | Nie  | Tak                        | Tak | Nie                             |
| **Planowanie migracji**              | Nie  | Tak                        | Tak | Tak                            |
| **Zachowywanie listy ACL**                        | Nie  | Tak                        | Tak | Tak                            |
| **Obsługa DACL**                          | Tak | Tak                        | Tak | Tak                            |
| **Obsługa SACL**                          | Tak | Tak                        | Tak | Nie                             |
| **Zachowanie czasu dostępu**                | Tak | Tak                        | Tak | Tak                            |
| **Zachowywanie czasu modyfikacji**              | Tak | Tak                        | Tak | Tak                            |
| **Zachowanie czasu utworzenia**              | Nie  | Tak                        | Tak | Tak                            |
| **Obsługa Azure Data Box**       | Tak | Tak                        | Nie  | Nie                             |
| **Migracja migawek**                | Nie  | Ręcznie                     | Tak | Nie                             |
| **Obsługa linku symbolicznego**                 | Nie  | Tak                        | Nie  | Tak                            |
| **Obsługa linków twardych**                     | Nie  | Zmigrowane jako osobne pliki | Tak | Tak                            |
| **Obsługa plików otwartych/zablokowanych**       | Tak | Tak                        | Tak | Tak                            |
| **Migracja przyrostowa**                 | Tak | Tak                        | Tak | Tak                            |
| **Obsługa przełączania**                    | Nie  | Tak                        | Tak | Nie (tylko ręczne)               |
| **[Pozostałe funkcje](#other-features)**         | [Link](#azure-file-sync)| [Link](#dobimigrate) | [Link](#data-mobility-and-migration) | [Link](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Ocena i raportowanie

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nazwa rozwiązania**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Pojemność**                        | Nie      | Tak | Tak | Tak            |
| **Liczba plików/folderów**            | Nie      | Tak | Tak | Tak            |
| **Rozkład wieku w czasie**      | Nie      | Tak | Tak | Tak            |
| **Czas dostępu**                     | Nie      | Tak | Tak | Tak            |
| **Godzina modyfikacji**                   | Nie      | Tak | Tak | Tak            |
| **Czas utworzenia**                   | Nie      | Tak | Tak | Tak (tylko SMB) |
| **Stan raportu na plik/obiekt** | Częściowe | Tak | Tak | Tak            |

## <a name="licensing"></a>Licencjonowanie

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nazwa rozwiązania**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | NIE DOTYCZY | Tak | Tak | Tak |
| **Zobowiązanie platformy Azure** | Tak   | Tak | Tak | Tak |

## <a name="other-features"></a>Pozostałe funkcje

### <a name="azure-file-sync"></a>Azure File Sync

- Wewnętrzna Walidacja skrótu

### <a name="dobimigrate"></a>DobiMigrate

- Sprawdzanie wstępne migracji
- Planowanie migracji
- Suche przebiegi w przypadku przecinania przez testowanie
- Wykryj i Zgłoś alert dotyczący działania użytkownika po stronie docelowej przed wycinaniem
- Migracje oparte na zasadach
- Iteracje kopiowania według harmonogramu
- Konfigurowalne opcje obsługi zabezpieczeń katalogu głównego
- Uruchomienia weryfikacji na żądanie
- Weryfikacja odczytywania danych z lokalizacji źródłowej i docelowej
- Graficzny, interaktywny przepływ pracy obsługi błędów
- Możliwość ograniczenia niektórych operacji propagowania, takich jak usunięcia i aktualizacje
- Możliwość zachowania czasu dostępu na źródle (poza miejscem docelowym)
- Możliwość wykonania wycofywania do źródła podczas przełączania migracji
- Możliwość migrowania wybranych atrybutów pliku SMB
- Możliwość czyszczenia deskryptorów zabezpieczeń systemu plików NTFS
- Możliwość przesłaniania uprawnień NFSv3 i zapisywania nowej usługi BITS w trybie docelowym
- Możliwość konwersji list kontroli dostępu NFSv3 POSIX do listy ACL NFSv4
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Mobilność i migracja danych

- Sprawdzanie poprawności wartości skrótu

### <a name="intelligent-data-management"></a>Inteligentne Zarządzanie danymi

- Migracje oparte na projekcie/katalogu
- Automatyczne ponawianie próby błędów
- Ocena/raportowanie: typy plików, rozmiar plików, na podstawie projektu
- Ocena/raportowanie: niestandardowe wyszukiwania oparte na metadanych
- Rozwiązanie do zarządzania pełnym cyklem życia danych dla archiwizacji, replikacji, analizy
- Dostęp do analiz opartych na czasie na obiektach Blob, danych S3
- Znakowanie
- Obsługa 24 x 7 x 365
- Sprawdzanie poprawności wartości skrótu

*Lista została ostatnio zweryfikowana, 31 marca 2021.*

## <a name="see-also"></a>Zobacz też

- [Omówienie migracji magazynu](../../../common/storage-migration-overview.md)
- [Wybieranie rozwiązania platformy Azure do transferu danych](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migracja do udziałów plików usługi Azure File](/azure/storage/files/storage-files-migration-overview)
- [Migrowanie do Data Lake Storage przy użyciu platformy WANdisco LiveData dla platformy Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą AzCopy](https://aka.ms/azcopy)
- [Migrowanie dużych zestawów danych do usługi Azure Blob Storage za pomocą AzReplicate (przykładowa aplikacja)](https://github.com/Azure/AzReplicate/tree/master/)
