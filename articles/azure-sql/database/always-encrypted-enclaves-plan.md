---
title: Planowanie usługi Intel SGX enclaves i zaświadczania w Azure SQL Database
description: Zaplanuj wdrożenie Always Encrypted za pomocą bezpiecznego enclaves w Azure SQL Database.
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
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732748"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Planowanie usługi Intel SGX enclaves i zaświadczania w Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted z opcją Secure enclaves for Azure SQL Database jest obecnie w **publicznej wersji zapoznawczej**.

[Always Encrypted z opcją Secure enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) w Azure SQL Database używa [rozszerzeń Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) Enclaves i wymaga [zaświadczania Microsoft Azure](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation).

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Planowanie dla Intel SGX w Azure SQL Database

Intel SGX to oparta na sprzęcie technologia środowiska do bezpiecznego wykonywania. Procesor Intel SGX jest dostępny dla baz danych, które korzystają z [modelu rdzeń wirtualny](service-tiers-vcore.md) i generowania sprzętu z [serii DC](service-tiers-vcore.md?#dc-series) . W związku z tym, aby upewnić się, że w bazie danych można użyć Always Encrypted z bezpiecznym enclavesem, należy wybrać generowanie sprzętu z serii DC podczas tworzenia bazy danych lub zaktualizować istniejącą bazę danych, tak aby korzystała z generacji sprzętu z serii kontrolerów domen.

> [!NOTE]
> Procesor Intel SGX nie jest dostępny w generacjach sprzętowych innych niż Seria DC. Na przykład procesor Intel SGX nie jest dostępny dla sprzętu 5 rdzeń i nie jest dostępny dla baz danych korzystających z [modelu DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Przed skonfigurowaniem generowania sprzętu z serii DC dla bazy danych, należy sprawdzić regionalną dostępność serii DC i upewnić się, że rozumiesz jej ograniczenia wydajności. Aby uzyskać szczegółowe informacje, zobacz [kontrolery domeny](service-tiers-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>Planowanie zaświadczania w Azure SQL Database

[Zaświadczanie o Microsoft Azure](../../attestation/overview.md) (wersja zapoznawcza) to rozwiązanie służące do zaświadczania zaufanych środowisk wykonywania (TEEs), w tym Intel SGX enclaves w bazach danych Azure SQL Database przy użyciu generacji sprzętu z serii DC.

Aby używać zaświadczania platformy Azure na potrzeby zaświadczania technologii Intel SGX enclaves w Azure SQL Database, musisz:

1. Utwórz [dostawcę zaświadczania](../../attestation/basic-concepts.md#attestation-provider) i skonfiguruj go przy użyciu zasad zaświadczania. 

2. Przyznaj serwerowi logicznemu usługi Azure SQL dostęp do utworzonego dostawcy zaświadczania.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Role i obowiązki podczas konfigurowania SGX enclaves i zaświadczania

Skonfigurowanie środowiska do obsługi technologii Intel SGX enclaves i zaświadczania Always Encrypted w Azure SQL Database obejmuje Konfigurowanie składników różnych typów Microsoft Azure: zaświadczanie, Azure SQL Database i aplikacje wyzwalające zaświadczanie enklawy. Konfigurowanie składników każdego typu jest wykonywane przez użytkowników przy założeniu, że jedna z poniższych odrębnych ról:

- Administrator zaświadczania — tworzy dostawcę zaświadczania w Microsoft Azure zaświadczania, autorów zasad zaświadczania, przyznaje serwerowi logicznemu usługi Azure SQL dostęp do dostawcy zaświadczania i udostępnia adres URL zaświadczania, który wskazuje zasady dla administratorów aplikacji.
- Azure SQL Database Administrator — umożliwia korzystanie z usługi SGX enclaves w bazach danych przez wybranie generacji sprzętu z serii DC i udostępnienie administratorowi zaświadczania tożsamości serwera logicznego SQL platformy Azure, który musi uzyskać dostęp do dostawcy zaświadczania.
- Administrator aplikacji — konfiguruje aplikacje przy użyciu adresu URL zaświadczania uzyskanego od administratora zaświadczania.

W środowiskach produkcyjnych (obsługujących dane poufne) ważne jest, aby organizacja była zgodna z separacją ról podczas konfigurowania zaświadczania, gdzie każda odrębna rola jest zajmowana przez różne osoby. W szczególności, jeśli celem wdrażania Always Encrypted w organizacji jest zredukowanie obszaru podatności na ataki przez zapewnienie, że Azure SQL Database Administratorzy nie będą mogli uzyskać dostępu do poufnych danych, Azure SQL Database Administratorzy nie powinni kontrolować zasad zaświadczania.

## <a name="next-steps"></a>Następne kroki

- [Włączanie technologii Intel SGX dla usługi Azure SQL Database](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Zobacz także

- [Samouczek: wprowadzenie do Always Encrypted za pomocą bezpiecznego enclaves w Azure SQL Database](always-encrypted-enclaves-getting-started.md)