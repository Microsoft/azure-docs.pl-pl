---
title: Używanie tożsamości zarządzanej do łączenia usługi Azure SQL ze sprężyną aplikacji w chmurze platformy Azure
description: Skonfiguruj zarządzaną tożsamość, aby połączyć usługę Azure SQL z aplikacją w chmurze platformy Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 3f1cde0bf233c67d02b9b7266ce3b3c8a3696db8
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123455"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Używanie tożsamości zarządzanej do łączenia Azure SQL Database z aplikacją Azure wiosennej w chmurze

**Ten artykuł ma zastosowanie do:** ✔️ Java

W tym artykule pokazano, jak utworzyć zarządzaną tożsamość dla aplikacji w chmurze Azure wiosennej i za jej pomocą uzyskać dostęp do Azure SQL Database.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) to inteligentna, skalowalna usługa relacyjnej bazy danych skompilowana dla chmury. Jest ona zawsze aktualna, dzięki wbudowanym i zautomatyzowanym funkcjom, które optymalizują wydajność i trwałość. Opcje magazynu danych i skalowania bezserwerowego umożliwiają automatyczne skalowanie zasobów na żądanie, dzięki czemu można skupić się na tworzeniu nowych aplikacji bez konieczności zarządzania rozmiarem magazynu ani zarządzaniem zasobami.

## <a name="prerequisites"></a>Wymagania wstępne
Ten przykład używa następujących zasobów.
* Postępuj zgodnie z [samouczkiem JPA danych wiosennych](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) , aby zainicjować obsługę Azure SQL Database i rozpocząć korzystanie z aplikacji Java lokalnie
* Postępuj zgodnie z [samouczkiem dotyczącym zarządzanej tożsamości przypisanej do systemu Azure w chmurze](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) , aby udostępnić aplikację w chmurze Azure z WŁĄCZONĄ funkcją mi

## <a name="grant-permission-to-the-managed-identity"></a>Udziel uprawnienia do zarządzanej tożsamości
Połącz się z serwerem SQL i uruchom następujące zapytanie SQL:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Jest to zgodne <MIName> z regułą: `<service instance name>/apps/<app name>` , np. myspringcloud/Apps/sqldemonstracyjną. Możesz również wysyłać zapytania do MIName przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Konfigurowanie aplikacji Java do korzystania z tożsamości zarządzanej
Otwórz `src/main/resources/application.properties` plik i Dodaj `Authentication=ActiveDirectoryMSI;` na końcu następującego wiersza. Należy pamiętać, aby użyć poprawnej wartości zmiennej _DATABASE_NAME $AZ.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Kompilowanie i wdrażanie aplikacji w chmurze Azure wiosny
Skompiluj ponownie aplikację i Wdróż ją w aplikacji chmurowej ze sprężyną platformy Azure, która została zainicjowana w drugim punkcie punktora w obszarze wymagania wstępne. Teraz masz aplikację z rozruchem ze sprężyną, uwierzytelnioną przez zarządzaną tożsamość, która używa JPA do przechowywania i pobierania danych z Azure SQL Database w chmurze Azure wiosennej.

## <a name="next-steps"></a>Następne kroki

* [Jak uzyskać dostęp do obiektu blob magazynu z tożsamością zarządzaną w chmurze Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Jak włączyć tożsamość zarządzaną przypisaną przez system dla aplikacji w chmurze platformy Azure](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Dowiedz się więcej o tożsamościach zarządzanych dla zasobów platformy Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Uwierzytelnianie chmury Azure wiosennej za pomocą Key Vault w akcjach GitHub](./spring-cloud-github-actions-key-vault.md)