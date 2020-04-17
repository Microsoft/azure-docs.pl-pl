---
title: Przykłady programu PowerShell
description: Znajdź przykłady programu Azure PowerShell dla niektórych typowych scenariuszy usługi App Service. Dowiedz się, jak zautomatyzować wdrażanie usługi App Service lub zadania zarządzania.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 03/08/2017
ms.custom: mvc
ms.openlocfilehash: f289bd453f2387282402394c807fe9700151f221
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532429"
---
# <a name="powershell-samples-for-azure-app-service"></a>Przykłady dla programu Azure PowerShell — Azure App Service

Poniższa tabela zawiera linki do skryptów programu PowerShell skompilowanych za pomocą programu Azure PowerShell.

| | |
|-|-|
|**Tworzenie aplikacji**||
| [Tworzenie aplikacji wraz wdrożeniem z usługi GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację usługi App Service, która ściąga kod z usługi GitHub. |
| [Tworzenie aplikacji z ciągłym wdrażaniem z usługi GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację usługi App Service, która w sposób ciągły wdraża kod z usługi GitHub. |
| [Tworzenie aplikacji i wdrażanie kodu za pomocą protokołu FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację usługi App Service i przekazuje pliki z katalogu lokalnego przy użyciu protokołu FTP. |
| [Tworzenie aplikacji i wdrażanie kodu z lokalnego repozytorium Git](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację usługi App Service i konfiguruje wypychanie kodu z lokalnego repozytorium Git. |
| [Tworzenie aplikacji i wdrażanie kodu w środowisku przejściowym](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację usługi App Service z miejscem wdrożenia dla przejściowych zmian kodu. |
|**Konfigurowanie aplikacji**||
| [Mapowanie domeny niestandardowej na aplikację](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację usługi App Service i mapuje na nią nazwę domeny niestandardowej. |
| [Powiąż niestandardowy certyfikat TLS/SSL z aplikacją](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację usługi App Service i wiąże certyfikat TLS/SSL niestandardowej nazwy domeny z nią. |
|**Skalowanie aplikacji**||
| [Ręczne skalowanie aplikacji](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację usługi App Service i skaluje ją na 2 wystąpienia. |
| [Skalowanie aplikacji na całym świecie przy użyciu architektury wysokiej dostępności](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy dwie aplikacje usługi App Service w dwóch różnych regionach geograficznych i udostępnia je za pośrednictwem pojedynczego punktu końcowego przy użyciu usługi Azure Traffic Manager. |
|**Łączenie aplikacji z zasobami**||
| [Łączenie aplikacji z programem SQL Database](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację usługi App Service i bazę danych SQL, a następnie dodaje parametry połączenia z bazą danych do ustawień aplikacji. |
| [Łączenie aplikacji z kontem magazynu](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy aplikację usługi App Service i konto magazynu, a następnie dodaje parametry połączenia z magazynem do ustawień aplikacji. |
|**Tworzenie i przywracanie kopii zapasowej aplikacji**||
| [Tworzenie kopii zapasowej aplikacji](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację usługi App Service oraz jej jednorazową kopię zapasową. |
| [Tworzenie zaplanowanej kopii zapasowej aplikacji](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację usługi App Service oraz jej zaplanowaną kopię zapasową. |
| [Usuwanie kopii zapasowej aplikacji](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Usuwa istniejącą kopię zapasową aplikacji. |
| [Przywracanie aplikacji z kopii zapasowej](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Przywraca aplikację z utworzonej wcześniej kopii zapasowej. |
| [Przywracanie kopii zapasowej między subskrypcjami](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Przywraca aplikację internetową z kopii zapasowej w innej subskrypcji. |
|**Monitorowanie aplikacji**||
| [Monitorowanie aplikacji za pomocą dzienników serwera internetowego](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy aplikację usługi App Service, włącza dla niej rejestrowanie i pobiera dzienniki na komputer lokalny. |
| | |
