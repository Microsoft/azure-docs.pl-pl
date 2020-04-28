---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67183181"
---
## <a name="next-steps"></a>Następne kroki

Po włączeniu integracji Azure Key Vault można włączyć szyfrowanie SQL Server na maszynie wirtualnej SQL. Najpierw należy utworzyć klucz asymetryczny w magazynie kluczy i klucz symetryczny w SQL Server na maszynie wirtualnej. Następnie można wykonać instrukcje języka T-SQL, aby włączyć szyfrowanie baz danych i kopii zapasowych.

Istnieje kilka form szyfrowania, z których można skorzystać:

* [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Szyfrowane kopie zapasowe](https://msdn.microsoft.com/library/dn449489.aspx)
* [Szyfrowanie na poziomie kolumny (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Poniższe skrypty języka Transact-SQL zawierają przykłady dla każdego z tych obszarów.

### <a name="prerequisites-for-examples"></a>Wymagania wstępne dotyczące przykładów

Każdy przykład jest oparty na dwóch wymaganiach wstępnych: klucz asymetryczny z magazynu kluczy o nazwie **CONTOSO_KEY** i poświadczenie utworzone przez funkcję integracji AKV o nazwie **Azure_EKM_TDE_cred**. Poniższe polecenia języka Transact-SQL konfigurują te wymagania wstępne do uruchamiania przykładów.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


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

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji na temat korzystania z tych funkcji szyfrowania, zobacz [using EKM with SQL Server Encryption Features](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Należy pamiętać, że kroki opisane w tym artykule założono, że masz już SQL Server uruchomione na maszynie wirtualnej platformy Azure. Jeśli nie, zobacz temat [udostępnianie maszyny wirtualnej SQL Server na platformie Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Aby uzyskać inne wskazówki dotyczące uruchamiania SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie Virtual Machines Azure — omówienie](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
