---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 6195b949cc71043dfa7a12bdece7a311dbde5e21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95563774"
---
## <a name="next-steps"></a>Następne kroki

Po włączeniu integracji Azure Key Vault można włączyć szyfrowanie SQL Server na maszynie wirtualnej SQL. Najpierw należy utworzyć klucz asymetryczny w magazynie kluczy i klucz symetryczny w SQL Server na maszynie wirtualnej. Następnie można wykonać instrukcje języka T-SQL, aby włączyć szyfrowanie baz danych i kopii zapasowych.

Istnieje kilka form szyfrowania, z których można skorzystać:

* [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [Szyfrowane kopie zapasowe](/sql/relational-databases/backup-restore/backup-encryption)
* [Szyfrowanie na poziomie kolumny (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)

Poniższe skrypty języka Transact-SQL zawierają przykłady dla każdego z tych obszarów.

### <a name="prerequisites-for-examples"></a>Wymagania wstępne dotyczące przykładów

Każdy przykład jest oparty na dwóch wymaganiach wstępnych: klucz asymetryczny z magazynu kluczy o nazwie **CONTOSO_KEY** i poświadczenie utworzone przez funkcję integracji AKV o nazwie **Azure_EKM_cred**. Poniższe polecenia języka Transact-SQL konfigurują te wymagania wstępne do uruchamiania przykładów.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL Azure_EKM_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL Azure_EKM_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

1. Utwórz nazwę logowania SQL Server, która będzie używana przez aparat bazy danych dla TDE, a następnie Dodaj do niej poświadczenia.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Utwórz klucz szyfrowania bazy danych, który będzie używany na potrzeby TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Szyfrowane kopie zapasowe

1. Utwórz nazwę logowania SQL Server, która będzie używana przez aparat bazy danych do szyfrowania kopii zapasowych, i Dodaj do niej poświadczenia.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Utwórz kopię zapasową bazy danych, określając szyfrowanie za pomocą klucza asymetrycznego przechowywanego w magazynie kluczy.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Szyfrowanie na poziomie kolumny (CLE)

Ten skrypt tworzy klucz symetryczny chroniony przez klucz asymetryczny w magazynie kluczy, a następnie używa klucza symetrycznego do szyfrowania danych w bazie danych.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat korzystania z tych funkcji szyfrowania, zobacz [using EKM with SQL Server Encryption Features](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server#UsesOfEKM).

Należy pamiętać, że kroki opisane w tym artykule założono, że masz już SQL Server uruchomione na maszynie wirtualnej platformy Azure. Jeśli nie, zobacz temat [udostępnianie maszyny wirtualnej SQL Server na platformie Azure](../articles/azure-sql/virtual-machines/windows/create-sql-vm-portal.md). Aby uzyskać inne wskazówki dotyczące uruchamiania SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie Virtual Machines Azure — omówienie](../articles/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).