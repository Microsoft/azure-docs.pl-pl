---
title: Przykłady interfejsu wiersza polecenia
description: Znajdź przykłady interfejsu wiersza polecenia platformy Azure dla niektórych typowych scenariuszy App Service. Dowiedz się, jak zautomatyzować zadania związane z wdrażaniem App Service lub zarządzaniem.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc
ms.openlocfilehash: beab87618b97da4e61b0525c0c5a6bdd134fb7f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169446"
---
# <a name="cli-samples-for-azure-app-service"></a>Przykłady interfejsu wiersza polecenia dla usługi Azure App Service

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| Skrypt | Opis |
|-|-|
|**Tworzenie aplikacji**||
| [Tworzenie aplikacji i wdrażanie plików za pomocą protokołu FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service i wdraża w niej plik przy użyciu protokołu FTP. |
| [Tworzenie aplikacji i wdrażanie kodu z usługi GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service i wdraża kod z publicznego repozytorium GitHub. |
| [Tworzenie aplikacji z ciągłym wdrażaniem z usługi GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service z ciągłym publikowaniem z własnego repozytorium GitHub. |
| [Tworzenie aplikacji i wdrażanie kodu z lokalnego repozytorium Git](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service i konfiguruje wypychanie kodu z lokalnego repozytorium Git. |
| [Tworzenie aplikacji i wdrażanie kodu w środowisku przejściowym](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service z miejscem wdrożenia dla przejściowych zmian kodu. |
| [Tworzenie aplikacji ASP.NET Core w kontenerze platformy Docker](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service w systemie Linux, a następnie ładuje obraz platformy Docker z usługi Docker Hub. |
| [Tworzenie aplikacji i udostępnianie jej w prywatnym punkcie końcowym](./scripts/cli-deploy-privateendpoint.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację App Service i prywatny punkt końcowy |
|**Konfigurowanie aplikacji**||
| [Mapowanie domeny niestandardowej na aplikację](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service i mapuje na nią nazwę domeny niestandardowej. |
| [Powiązywanie niestandardowego certyfikatu TLS/SSL z aplikacją](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację App Service i wiąże się z certyfikatem TLS/SSL nazwy domeny niestandardowej. |
|**Skalowanie aplikacji**||
| [Ręczne skalowanie aplikacji](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service i skaluje ją na 2 wystąpienia. |
| [Skalowanie aplikacji na całym świecie przy użyciu architektury wysokiej dostępności](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy dwie aplikacje usługi App Service w dwóch różnych regionach geograficznych i udostępnia je za pośrednictwem pojedynczego punktu końcowego przy użyciu usługi Azure Traffic Manager. |
|**Ochrona aplikacji**||
| [Integracja z usługą Azure Application Gateway](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację App Service i integruje ją z Application Gateway przy użyciu punktu końcowego usługi i ograniczeń dostępu. |
|**Łączenie aplikacji z zasobami**||
| [Łączenie aplikacji z programem SQL Database](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację App Service i bazę danych w Azure SQL Database, a następnie dodaje parametry połączenia z bazą danych do ustawień aplikacji. |
| [Łączenie aplikacji z kontem magazynu](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service i konto magazynu, a następnie dodaje parametry połączenia z magazynem do ustawień aplikacji. |
| [Łączenie aplikacji z usługą Azure Cache for Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service i usługę Azure Cache for Redis, a następnie dodaje szczegóły połączenia z serwerem Redis do ustawień aplikacji. |
| [Łączenie aplikacji z usługą Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service i usługę Cosmos DB, a następnie dodaje szczegóły połączenia z usługą Cosmos DB do ustawień aplikacji. |
|**Tworzenie i przywracanie kopii zapasowej aplikacji**||
| [Tworzenie kopii zapasowej aplikacji](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service oraz jej jednorazową kopię zapasową. |
| [Tworzenie zaplanowanej kopii zapasowej aplikacji](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service oraz jej zaplanowaną kopię zapasową. |
| [Przywracanie aplikacji z kopii zapasowej](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Przywraca aplikację usługi App Service z kopii zapasowej. |
|**Monitorowanie aplikacji**||
| [Monitorowanie aplikacji za pomocą dzienników serwera internetowego](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service, włącza dla niej rejestrowanie i pobiera dzienniki na komputer lokalny. |
| | |
