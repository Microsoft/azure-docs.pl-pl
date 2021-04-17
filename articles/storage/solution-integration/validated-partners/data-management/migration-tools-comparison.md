---
title: Porównanie narzędzi migracji usługi Azure Storage — dane bez struktury
description: Podstawowe funkcje i porównanie narzędzi używanych do migracji danych bez struktury
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 862feace6aab4f49ad3482c4ccd6510669c876a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576537"
---
# <a name="comparison-matrix"></a>Macierz porównawcza

W poniższej macierzy porównawczej przedstawiono podstawowe funkcje różnych narzędzi, których można użyć do migracji danych bez struktury. 

## <a name="supported-azure-services"></a>Obsługiwane usługi platformy Azure

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nazwa rozwiązania**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure Files (wszystkie warstwy)** | Yes                          | Yes                      | Yes            | Yes                            |
| **Azure NetApp Files pomocy technicznej**      | Nie                           | Yes                      | Yes            | Yes                            |
| **Obsługa gorącego/chłodnego obiektu blob platformy Azure**   | Nie                           | Tak (za pośrednictwem systemu plików NFS w wersji zapoznawczej)    | Yes            | Yes                            |
| **Obsługa warstwy Archiwum obiektów blob platformy Azure** | Nie                           | Nie                       | Nie             | Tak (jako miejsce docelowe migracji) |
| **Azure Data Lake Storage pomocy technicznej** | Nie                           | Nie                       | Nie             | Nie                             |
| **Obsługiwane źródła**      | Windows Server 2012 R2 i jego system | Systemy plików & NAS w chmurze | Wszystkie nas i S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Obsługiwane protokoły (źródło/miejsce docelowe)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nazwa rozwiązania**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Yes | Yes | Yes | Yes |
| **SMB 3.0**       | Yes | Yes | Yes | Yes |
| **SMB 3.1**       | Yes | Yes | Yes | Yes |
| **NFS v3**        | Nie  | Yes | Yes | Yes |
| **NFS 4.1**      | Nie  | Yes | Nie  | Yes |
| **Blob REST API** | Nie  | Nie  | Yes | Yes |
| **S3**            | Nie  | Yes | Yes | Yes |

## <a name="extended-features"></a>Funkcje rozszerzone

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nazwa rozwiązania**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Ponowne mapowanie identyfikatorów UID/SID**                   | Nie  | Yes                        | Yes | Nie                             |
| **Ponowne mapowanie listy ACL protokołu**                | Nie  | Nie                         | Nie  | Nie                             |
| **Obsługa systemu plików DFS**                           | Yes | Yes                        | Yes | Yes                            |
| **Obsługa ograniczania przepustowości**                    | Yes | Yes                        | Yes | Yes                            |
| **Wykluczenia wzorców plików**               | Nie  | Yes                        | Yes | Tak (przy użyciu funkcji kopiowania) |
| **Obsługa atrybutów selektywnego pliku** | Yes | Yes                        | Yes | Tak (w przypadku atrybutów rozszerzonych)  |
| **Usuwanie propagacji**                   | Yes | Yes                        | Yes | Yes                            |
| **Postępuj zgodnie z łącznikami NTFS**                 | Nie  | Yes                        | Nie  | Yes                            |
| **Zastępowanie właściciela I właściciela grupy SMB**    | Yes | Yes                        | Yes | Nie                             |
| **Raportowanie łańcucha opiekunów**            | Nie  | Yes                        | Nie  | Yes                            |
| **Obsługa alternatywnych strumieni danych**    | Nie  | Yes                        | Yes | Nie                             |
| **Planowanie migracji**              | Nie  | Yes                        | Yes | Yes                            |
| **Zachowywanie listy ACL**                        | Nie  | Yes                        | Yes | Yes                            |
| **Obsługa listy DACL**                          | Yes | Yes                        | Yes | Yes                            |
| **Obsługa SACL**                          | Yes | Yes                        | Yes | Nie                             |
| **Zachowywanie czasu dostępu**                | Yes | Yes                        | Yes | Yes                            |
| **Zachowywanie zmodyfikowanego czasu**              | Yes | Yes                        | Yes | Yes                            |
| **Zachowywanie czasu tworzenia**              | Nie  | Yes                        | Yes | Yes                            |
| **Azure Data Box pomocy technicznej**       | Yes | Yes                        | Nie  | Nie                             |
| **Migracja migawek**                | Nie  | Ręcznie                     | Yes | Nie                             |
| **Obsługa linków symbolicznych**                 | Nie  | Yes                        | Nie  | Yes                            |
| **Obsługa twardych linków**                     | Nie  | Migrowane jako oddzielne pliki | Yes | Yes                            |
| **Obsługa otwartych/zablokowanych plików**       | Yes | Yes                        | Yes | Yes                            |
| **Migracja przyrostowa**                 | Yes | Yes                        | Yes | Yes                            |
| **Obsługa przełączania**                    | Nie  | Yes                        | Yes | Nie (tylko ręcznie)               |
| **[Pozostałe funkcje](#other-features)**         | [Link](#azure-file-sync)| [Link](#dobimigrate) | [Link](#data-mobility-and-migration) | [Link](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Ocena i raportowanie

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nazwa rozwiązania**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Pojemność**                        | Nie      | Yes | Yes | Yes            |
| **Liczba plików/folderów**            | Nie      | Yes | Yes | Yes            |
| **Rozkład wiekowy w czasie**      | Nie      | Yes | Yes | Yes            |
| **Czas dostępu**                     | Nie      | Yes | Yes | Yes            |
| **Czas modyfikacji**                   | Nie      | Yes | Yes | Yes            |
| **Czas tworzenia**                   | Nie      | Yes | Yes | Yes            |
| **Stan raportu na plik/obiekt** | Częściowe | Yes | Yes | Yes            |

## <a name="licensing"></a>Licencjonowanie

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nazwa rozwiązania**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilność i migracja danych](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentne Zarządzanie danymi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | N /A | Yes | Yes | Yes |
| **Zobowiązanie platformy Azure** | Yes   | Yes | Yes | Yes |

## <a name="other-features"></a>Pozostałe funkcje

### <a name="azure-file-sync"></a>Azure File Sync

- Wewnętrzna walidacja skrótu

### <a name="dobimigrate"></a>DobiMigrate

- Wstępne kontrole migracji
- Planowanie migracji
- Dry Run for cut over testing (Przebieg próbny na czas próbny)
- Wykrywanie aktywności użytkownika po stronie docelowej i ostrzeganie przed jej przecięciem
- Migracje oparte na zasadach
- Zaplanowane iteracje kopiowania
- Konfigurowalne opcje obsługi zabezpieczeń katalogu głównego
- Przebiegi weryfikacji na żądanie
- Weryfikacja odczytu danych z powrotem w źródle i miejscu docelowym
- Graficzny, interaktywny przepływ pracy obsługi błędów
- Możliwość ograniczenia propagacji niektórych operacji, takich jak operacje usuwania i aktualizacji
- Możliwość zachowania czasu dostępu w źródle (oprócz miejsca docelowego)
- Możliwość wykonania wycofywania do źródła podczas przełączania migracji
- Możliwość migrowania wybranych atrybutów pliku SMB
- Możliwość czyszczenia deskryptorów zabezpieczeń NTFS
- Możliwość zastępowania uprawnień NFSv3 i zapisu nowych bitów trybu do obiektu docelowego
- Możliwość przekonwertowania wersji roboczej ACLS NFSv3 POSIX na listy ACLS NFSv4
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Mobilność i migracja danych

- Walidacja skrótu

### <a name="intelligent-data-management"></a>Inteligentne Zarządzanie danymi

- Migracje oparte na projekcie/katalogu
- Automatyczne ponawianie próby błędów
- Ocena/raportowanie: Typy plików, rozmiar pliku, oparte na projekcie
- Ocena/raportowanie: Niestandardowe wyszukiwania oparte na metadanych
- Rozwiązanie do zarządzania pełnym cyklem życia danych na potrzeby archiwizacji, replikacji, analizy
- Uzyskiwanie dostępu do analizy opartej na czasie na obiektach blob, danych S3
- Znakowanie
- Obsługa 24 x 7 x 365
- Walidacja skrótu

*Lista została ostatnio zweryfikowana 31 marca 2021 r.*

## <a name="see-also"></a>Zobacz też

- [Omówienie migracji magazynu](../../../common/storage-migration-overview.md)
- [Wybieranie rozwiązania platformy Azure do transferu danych](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migracja do udziałów plików usługi Azure File](/azure/storage/files/storage-files-migration-overview)
- [Migrowanie do Data Lake Storage za pomocą platformy WANdisco LiveData Platform for Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą narzędzia AzCopy](https://aka.ms/azcopy)
- [Migrowanie dużych zestawów danych do Azure Blob Storage za pomocą aplikacji AzReplicate (przykładowa aplikacja)](https://github.com/Azure/AzReplicate/tree/master/)
