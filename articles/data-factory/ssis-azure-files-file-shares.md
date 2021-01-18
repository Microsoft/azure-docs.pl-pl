---
title: Otwieranie i zapisywanie plików z pakietami SSIS wdrożonymi na platformie Azure
description: Dowiedz się, jak otwierać i zapisywać pliki lokalnie i na platformie Azure podczas podnoszenia i przesunięcia pakietów SSIS korzystających z lokalnych systemów plików do usług SSIS na platformie Azure
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 97ed9b6f3f21b6d9e220c1f4e17d4ba6b0d8b485
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555512"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Otwieranie i zapisywanie plików lokalnych i na platformie Azure z pakietami SSIS wdrożonymi na platformie Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób otwierania i zapisywania plików lokalnych i na platformie Azure podczas podnoszenia i przesunięcia pakietów SSIS, które używają lokalnych systemów plików do usług SSIS na platformie Azure.

## <a name="save-temporary-files"></a>Zapisz pliki tymczasowe

Jeśli zachodzi potrzeba przechowywania i przetwarzania plików tymczasowych podczas wykonywania pojedynczego pakietu, pakiety mogą korzystać z bieżącego katalogu roboczego ( `.` ) lub folderu tymczasowego ( `%TEMP%` ) węzłów Azure-SSIS Integration Runtime.

## <a name="use-on-premises-file-shares"></a>Korzystanie z lokalnych udziałów plików

Aby nadal korzystać z lokalnych **udziałów plików** podczas podnoszenia i przesunięcia pakietów, które korzystają z systemów plików w systemie SSIS na platformie Azure, wykonaj następujące czynności:

1. Przetransferuj pliki z lokalnych systemów plików do lokalnego udziału plików.

2. Dołącz udziały plików lokalnych do sieci wirtualnej platformy Azure.

3. Dołącz do Azure-SSIS IR do tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Połącz Azure-SSIS IR z lokalnymi udziałami plików w tej samej sieci wirtualnej przez skonfigurowanie poświadczeń dostępu, które używają uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z danymi i udziałami plików przy użyciu uwierzytelniania systemu Windows](ssis-azure-connect-with-windows-auth.md).

5. Zaktualizuj lokalne ścieżki plików w Twoich pakietach do ścieżek UNC wskazujących udziały plików lokalnych. Na przykład zaktualizuj `C:\abc.txt` do programu `\\<on-prem-server-name>\<share-name>\abc.txt` .

## <a name="use-azure-file-shares"></a>Korzystanie z udziałów plików platformy Azure

Aby użyć **Azure Files** podczas podnoszenia i przesunięcia pakietów, które używają lokalnych systemów plików do usług SSIS na platformie Azure, wykonaj następujące czynności:

1. Transferowanie plików z lokalnych systemów plików do Azure Files. Aby uzyskać więcej informacji, zobacz [Azure Files](https://azure.microsoft.com/services/storage/files/).

2. Połącz Azure-SSIS IR, aby Azure Files przez skonfigurowanie poświadczeń dostępu, które używają uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z danymi i udziałami plików przy użyciu uwierzytelniania systemu Windows](ssis-azure-connect-with-windows-auth.md).

3. Zaktualizuj lokalne ścieżki plików w Twoich pakietach do ścieżek UNC wskazujących Azure Files. Na przykład zaktualizuj `C:\abc.txt` do programu `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt` .

## <a name="next-steps"></a>Następne kroki

- Wdróż pakiety. Aby uzyskać więcej informacji, zobacz [wdrażanie projektu SSIS na platformie Azure za pomocą programu SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Uruchom pakiety. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS na platformie Azure za pomocą programu SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Zaplanuj pakiety. Aby uzyskać więcej informacji, zobacz [Planowanie pakietów usług SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).