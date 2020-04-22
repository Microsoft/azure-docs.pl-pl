---
title: Otwieranie i zapisywanie plików za pomocą pakietów SSIS wdrożonych na platformie Azure
description: Dowiedz się, jak otwierać i zapisywać pliki lokalnie i na platformie Azure podczas podnoszenia i przenoszenia pakietów SSIS korzystających z lokalnych systemów plików do SSIS na platformie Azure
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 630a8ead409ad60b9146838f062210a3301b43b5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760201"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Otwieranie i zapisywanie plików lokalnie i na platformie Azure za pomocą pakietów SSIS wdrożonych na platformie Azure

W tym artykule opisano sposób otwierania i zapisywania plików w środowisku lokalnym i na platformie Azure podczas podnoszenia i przesuwania pakietów SSIS korzystających z lokalnych systemów plików do ssis na platformie Azure.

## <a name="save-temporary-files"></a>Zapisywanie plików tymczasowych

Jeśli chcesz przechowywać i przetwarzać pliki tymczasowe podczas wykonywania pojedynczego pakietu, pakiety mogą używać bieżącego katalogu roboczego (`.`) lub folderu tymczasowego (`%TEMP%`) węzłów środowiska wykonawczego integracji platformy Azure-SSIS.

## <a name="use-on-premises-file-shares"></a>Używanie lokalnych udziałów plików

Aby nadal używać **lokalnych udziałów plików** podczas podnoszenia i przenoszenia pakietów korzystających z lokalnych systemów plików do SSIS na platformie Azure, wykonaj następujące czynności:

1. Przenoszenie plików z lokalnych systemów plików do lokalnych udziałów plików.

2. Dołącz do lokalnych udziałów plików w sieci wirtualnej platformy Azure.

3. Dołącz do usługi Azure-SSIS IR w tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie środowiska wykonawczego integracji platformy Azure-SSIS do sieci wirtualnej.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

4. Połącz urządzenie Azure-SSIS IR z lokalnymi udziałami plików w tej samej sieci wirtualnej, konfigurując poświadczenia dostępu korzystające z uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [Łączenie się z danymi i udziałami plików za pomocą uwierzytelniania systemu Windows](ssis-azure-connect-with-windows-auth.md).

5. Zaktualizuj lokalne ścieżki plików w pakietach do ścieżek UNC wskazujących lokalne udziały plików. Na przykład `C:\abc.txt` zaktualizuj do `\\<on-prem-server-name>\<share-name>\abc.txt`.

## <a name="use-azure-file-shares"></a>Korzystanie z udziałów plików platformy Azure

Aby używać **usługi Azure Files** podczas podnoszenia i przenoszenia pakietów korzystających z lokalnych systemów plików do SSIS na platformie Azure, wykonaj następujące czynności:

1. Przenoszenie plików z lokalnych systemów plików do usługi Azure Files. Aby uzyskać więcej informacji, zobacz [Usługi Azure Files](https://azure.microsoft.com/services/storage/files/).

2. Połącz urządzenie Azure-SSIS IR z usługą Azure Files, konfigurując poświadczenia dostępu korzystające z uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [Łączenie się z danymi i udziałami plików za pomocą uwierzytelniania systemu Windows](ssis-azure-connect-with-windows-auth.md).

3. Zaktualizuj lokalne ścieżki plików w pakietach do ścieżek UNC wskazujących na usługi Azure Files. Na przykład `C:\abc.txt` zaktualizuj do `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`.

## <a name="next-steps"></a>Następne kroki

- Wdrażanie pakietów. Aby uzyskać więcej informacji, zobacz [Wdrażanie projektu SSIS na platformie Azure za pomocą usługi SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Uruchom swoje pakiety. Aby uzyskać więcej informacji, zobacz [Uruchamianie pakietów SSIS na platformie Azure za pomocą usługi SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Zaplanuj swoje pakiety. Aby uzyskać więcej informacji, zobacz [Planowanie pakietów SSIS na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).
