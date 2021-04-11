---
title: Przypisywanie zasad dostępu Azure Key Vault (Portal)
description: Jak używać Azure Portal do przypisywania zasad dostępu Key Vault do podmiotu zabezpieczeń lub tożsamości aplikacji.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: a64a91e6f41f3fba7584630380ffb878979b4389
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968768"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Przypisywanie zasad dostępu Key Vault przy użyciu Azure Portal

Zasady dostępu Key Vault określają, czy dany podmiot zabezpieczeń, taki jak użytkownik, aplikacja lub Grupa użytkowników, może wykonywać różne operacje na Key Vault kluczach [tajnych](../secrets/index.yml), [kluczach](../keys/index.yml)i [certyfikatach](../certificates/index.yml). Zasady dostępu można przypisywać przy użyciu Azure Portal (w tym artykule), [interfejsu wiersza polecenia platformy Azure](assign-access-policy-cli.md)lub [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Aby uzyskać więcej informacji na temat tworzenia grup w Azure Active Directory za pomocą Azure Portal, zobacz [Tworzenie grupy podstawowej i Dodawanie członków](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Przypisywanie zasad dostępu

1.  W [Azure Portal](https://portal.azure.com)przejdź do zasobu Key Vault. 

1.  W obszarze **Ustawienia** wybierz pozycję **zasady dostępu**, a następnie wybierz pozycję **Dodaj zasady dostępu**:

    ![Wybierz pozycję Zasady dostępu, wybierając pozycję Dodaj przypisanie roli](../media/authentication/assign-policy-portal-01.png)

1.  Wybierz odpowiednie uprawnienia w obszarze **uprawnienia certyfikatów**, **uprawnienia klucza** i uprawnienia do wpisów **tajnych**. Możesz również wybrać szablon, który zawiera wspólne kombinacje uprawnień:

    ![Określanie uprawnień zasad dostępu](../media/authentication/assign-policy-portal-02.png)

1. W obszarze **Wybierz podmiot zabezpieczeń** wybierz łącze **Brak zaznaczenia** , aby otworzyć okienko wybór **podmiotu zabezpieczeń** . Wprowadź nazwę użytkownika, aplikacji lub nazwy głównej usługi w polu wyszukiwania, wybierz odpowiedni wynik, a następnie wybierz **pozycję Wybierz**.

    ![Wybieranie podmiotu zabezpieczeń dla zasad dostępu](../media/authentication/assign-policy-portal-03.png)

    Jeśli używasz tożsamości zarządzanej dla aplikacji, Wyszukaj i wybierz nazwę aplikacji. (Aby uzyskać więcej informacji na temat zarządzanych tożsamości i nazw podmiotów usługi, zobacz [Key Vault Authentication — tożsamość aplikacji i nazwy główne usługi](authentication.md#app-identity-and-security-principals)).
 
1.  W okienku **Dodawanie zasad dostępu** wybierz pozycję **Dodaj** , aby zapisać zasady dostępu.

    ![Dodawanie zasad dostępu z przypisanym podmiotem zabezpieczeń](../media/authentication/assign-policy-portal-04.png)

1. Na stronie **zasady dostępu** Sprawdź, czy zasady dostępu są wymienione w obszarze **bieżące zasady dostępu**, a następnie wybierz pozycję **Zapisz**. Zasady dostępu nie są stosowane, dopóki nie zostaną zapisane.

    ![Zapisywanie zmian zasad dostępu](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Następne kroki

- [Zabezpieczenia Azure Key Vault: Zarządzanie tożsamościami i dostępem](security-overview.md#identity-management)
- [Zabezpiecz swój magazyn kluczy](secure-your-key-vault.md).
- [Przewodnik dewelopera Azure Key Vault](developers-guide.md)