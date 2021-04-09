---
title: Włącz technologię Intel SGX dla Azure SQL Database
description: Dowiedz się, jak włączyć technologię Intel SGX dla Always Encrypted z bezpiecznym enclaves w Azure SQL Database, wybierając generowanie sprzętu z obsługą SGX.
keywords: Szyfruj dane, szyfrowanie SQL, szyfrowanie bazy danych, dane poufne, Always Encrypted, Secure enclaves, SGX, zaświadczanie
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733760"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Włącz technologię Intel SGX dla Azure SQL Database 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted z opcją Secure enclaves for Azure SQL Database jest obecnie w **publicznej wersji zapoznawczej**.

[Always Encrypted z opcją Secure enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) w Azure SQL Database używa [rozszerzeń firmy Intel (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) enclaves. Aby można było uzyskać dostęp do platformy Intel SGX, baza danych musi używać [modelu rdzeń wirtualny](service-tiers-vcore.md) i generowania sprzętu z [serii kontrolerów domen](service-tiers-vcore.md#dc-series) .

Skonfigurowanie generowania sprzętu z serii DC w celu włączenia rozwiązania Intel SGX enclaves jest odpowiedzialne za administratora Azure SQL Database. Zobacz [role i obowiązki podczas KONFIGUROWANIA SGX enclaves i zaświadczania](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> Procesor Intel SGX nie jest dostępny w generacjach sprzętowych innych niż Seria DC. Na przykład procesor Intel SGX nie jest dostępny dla sprzętu 5 rdzeń i nie jest dostępny dla baz danych korzystających z [modelu DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Przed skonfigurowaniem generowania sprzętu z serii DC dla bazy danych, należy sprawdzić regionalną dostępność serii DC i upewnić się, że rozumiesz jej ograniczenia wydajności. Aby uzyskać więcej informacji, zobacz [kontrolery domeny](service-tiers-vcore.md#dc-series).

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania nowej lub istniejącej bazy danych do korzystania z określonej generacji sprzętu, zobacz [Wybieranie generacji sprzętu](service-tiers-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie zaświadczania platformy Azure dla serwera usługi Azure SQL Database](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Zobacz też

- [Samouczek: wprowadzenie do Always Encrypted za pomocą bezpiecznego enclaves w Azure SQL Database](always-encrypted-enclaves-getting-started.md)