---
title: Używanie tożsamości zarządzanej do łączenia Azure SQL z Azure Spring Cloud aplikacji
description: Skonfiguruj tożsamość zarządzaną, aby połączyć Azure SQL z Azure Spring Cloud aplikacją.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: ed729dde51316b9a67f396e3f7de3d7d9f6d4568
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378792"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Używanie tożsamości zarządzanej do łączenia Azure SQL Database z Azure Spring Cloud aplikacji

**Ten artykuł dotyczy: ✔️** Java

W tym artykule pokazano, jak utworzyć tożsamość zarządzaną dla aplikacji Azure Spring Cloud i używać jej do uzyskiwania dostępu do Azure SQL Database.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) to inteligentna, skalowalna, relacyjnej bazy danych, która została zbudowana dla chmury. Jest on zawsze aktualny, dzięki zautomatyzowanym i opartym na AI funkcjam, które optymalizują wydajność i trwałość. Opcje magazynu bez serwera i magazynu w hiperskali automatycznie skaluje zasoby na żądanie, dzięki czemu możesz skoncentrować się na budowania nowych aplikacji bez martwienia się o rozmiar magazynu lub zarządzanie zasobami.

## <a name="prerequisites"></a>Wymagania wstępne
W tym przykładzie użyto następujących zasobów.
* Postępuj zgodnie z [samouczkiem Spring Data JPA,](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) aby aprowizować Azure SQL Database i lokalnie pracować z aplikacją Java
* Postępuj zgodnie [z Azure Spring Cloud tożsamości zarządzanej przypisanej](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) przez system, aby aprowizować aplikację Azure Spring Cloud z włączoną usługą migrowania

## <a name="grant-permission-to-the-managed-identity"></a>Udzielanie uprawnień tożsamości zarządzanej
Połącz się z serwerem SQL i uruchom następujące zapytanie SQL:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Jest <MIName> to zgodna z `<service instance name>/apps/<app name>` regułą: , np. myspringcloud/apps/sqldemo. Możesz również odpytać nazwę MIName za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Konfigurowanie aplikacji Java do używania tożsamości zarządzanej
Otwórz `src/main/resources/application.properties` plik i dodaj `Authentication=ActiveDirectoryMSI;` go na końcu poniższego wiersza. Pamiętaj, aby użyć poprawnej wartości dla zmiennej $AZ_DATABASE_NAME.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Kompilowanie i wdrażanie aplikacji w Azure Spring Cloud
Ponownie skompilować aplikację i wdrożyć ją w Azure Spring Cloud aprowizowanej w drugim punkcie w obszarze Wymagania wstępne. Teraz masz aplikację Spring Boot uwierzytelnioną przez tożsamość zarządzaną, która używa usługi JPA do przechowywania i pobierania danych z Azure SQL Database w Azure Spring Cloud.

## <a name="next-steps"></a>Następne kroki

* [Jak uzyskać dostęp do obiektu blob usługi Storage przy użyciu tożsamości zarządzanej w Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Jak włączyć tożsamość zarządzaną przypisaną przez system dla Azure Spring Cloud aplikacji](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Dowiedz się więcej o tożsamościach zarządzanych dla zasobów platformy Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Uwierzytelnianie Azure Spring Cloud za pomocą Key Vault w GitHub Actions](./spring-cloud-github-actions-key-vault.md)