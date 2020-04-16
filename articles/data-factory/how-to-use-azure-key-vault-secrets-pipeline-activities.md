---
title: Używanie wpisów tajnych usługi Azure Key Vault w działaniach potoku
description: Dowiedz się, jak pobrać przechowywane poświadczenia z magazynu kluczy platformy Azure i używać ich podczas przebiegów potoku fabryki danych.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: f2531ebfd8b1eafc04fa6eda660b0eec3d1147f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417074"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Używanie wpisów tajnych usługi Azure Key Vault w działaniach potoku

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Poświadczenia lub wartości tajne można przechowywać w magazynie azure key vault i używać ich podczas wykonywania potoku, aby przekazać do swoich działań.

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja opiera się na tożsamości zarządzanej fabrycznie danych.  Dowiedz się, jak to działa z [zarządzanej tożsamości dla usługi Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) i upewnij się, że fabryka danych jest skojarzona.

## <a name="steps"></a>Kroki

1. Otwórz właściwości fabryki danych i skopiuj wartość identyfikatora aplikacji tożsamości zarządzanej.

    ![Wartość tożsamości zarządzanej](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Otwórz zasady dostępu do magazynu kluczy i dodaj uprawnienia tożsamości zarządzanej do wpisów tajnych pobierz i lista.

    ![Zasady dostępu do usługi Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Zasady dostępu do usługi Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Kliknij **pozycję Dodaj**, a następnie kliknij pozycję **Zapisz**.

3. Przejdź do klucza tajnego magazynu kluczy i skopiuj tajny identyfikator.

    ![Tajny identyfikator](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Zanotuj tajny identyfikator URI, który chcesz uzyskać podczas uruchamiania potoku fabryki danych.

4. W potoku usługi Data Factory dodaj nową aktywność w sieci Web i skonfiguruj ją w następujący sposób.  

    |Właściwość  |Wartość  |
    |---------|---------|
    |Bezpieczne wyjście     |True         |
    |Adres URL     |[Tajna wartość identyfikatora URI]?api-version=7.0         |
    |Metoda     |GET         |
    |Authentication     |Tożsamość usługi zarządzanej         |
    |Zasób        |https://vault.azure.net       |

    ![Aktywność w sieci Web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Musisz dodać **?api-version=7.0** na końcu tajnego identyfikatora URI.  

    > [!CAUTION]
    > Ustaw opcję Bezpieczne wyjście na true, aby zapobiec rejestrowaniu wartości tajnej w postaci zwykłego tekstu.  Wszelkie dalsze działania, które zużywają tę wartość powinny mieć ich bezpieczne wejście opcja ustawiona na true.

5. Aby użyć wartości w innym działaniu, użyj następującego wyrażenia ** @activitykodu ("Web1").output.value**.

    ![Wyrażenie kodu](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać usługi Azure Key Vault do przechowywania poświadczeń dla magazynów danych i obliczeń, zobacz [Poświadczenia magazynu w usłudze Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
