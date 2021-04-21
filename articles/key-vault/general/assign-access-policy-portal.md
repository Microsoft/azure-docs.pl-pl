---
title: Przypisywanie zasad Azure Key Vault dostępu do aplikacji (portal)
description: Jak za pomocą Azure Portal przypisać zasady dostępu Key Vault do podmiotu zabezpieczeń lub tożsamości aplikacji.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 443b269e7155fc206ee50e7907a7acded2c22f53
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751494"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Przypisywanie Key Vault dostępu przy użyciu Azure Portal

Zasady dostępu Key Vault określają, czy dany podmiot zabezpieczeń, czyli użytkownik, aplikacja lub grupa użytkowników, może [](../keys/index.yml)wykonywać różne operacje na wpisach tajnych Key Vault [,](../secrets/index.yml)kluczach i [certyfikatach.](../certificates/index.yml) Zasady dostępu można przypisywać przy użyciu Azure Portal (w tym artykule), interfejsu wiersza polecenia platformy [Azure](assign-access-policy-cli.md) [lub Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Aby uzyskać więcej informacji na temat tworzenia grup w Azure Active Directory za pośrednictwem Azure Portal, zobacz Tworzenie grupy [podstawowej i dodawanie członków](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Przypisywanie zasad dostępu

1.  W [Azure Portal](https://portal.azure.com)przejdź do Key Vault zasobu. 

1.  W **obszarze Ustawienia** wybierz pozycję Zasady **dostępu,** a następnie wybierz **pozycję Dodaj zasady dostępu:**

    ![Wybierz pozycję Zasady dostępu, a następnie pozycję Dodaj przypisanie roli](../media/authentication/assign-policy-portal-01.png)

1.  Wybierz odpowiednie uprawnienia w obszarze **Uprawnienia certyfikatu,** **Uprawnienia klucza** i Uprawnienia do **kluczy tajnych.** Możesz również wybrać szablon, który zawiera typowe kombinacje uprawnień:

    ![Określanie uprawnień zasad dostępu](../media/authentication/assign-policy-portal-02.png)

1. W **obszarze Wybierz podmiot** zabezpieczeń wybierz link Brak **wybrany,** aby otworzyć **okienko** wyboru Podmiot zabezpieczeń. Wprowadź nazwę użytkownika, aplikacji lub jednostki usługi w polu wyszukiwania, wybierz odpowiedni wynik, a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie podmiotu zabezpieczeń dla zasad dostępu](../media/authentication/assign-policy-portal-03.png)

    Jeśli używasz tożsamości zarządzanej dla aplikacji, wyszukaj i wybierz nazwę samej aplikacji. (Aby uzyskać więcej informacji na temat tożsamości zarządzanej i jednostki usługi, zobacz Key Vault — tożsamość aplikacji i [jednostki usługi).](authentication.md#app-identity-and-security-principals)
 
1.  W okienku **Dodawanie zasad dostępu** wybierz pozycję **Dodaj,** aby zapisać zasady dostępu.

    ![Dodawanie zasad dostępu z przypisanym podmiotem zabezpieczeń](../media/authentication/assign-policy-portal-04.png)

1. Na stronie **Zasady dostępu sprawdź,** czy zasady dostępu są wyświetlane w obszarze **Bieżące zasady** dostępu, a następnie wybierz pozycję **Zapisz.** Zasady dostępu nie są stosowane do momentu ich zapisania.

    ![Zapisywanie zmian zasad dostępu](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault zabezpieczeń: zarządzanie tożsamościami i dostępem](security-overview.md#identity-management)
- [Zabezpiecz magazyn kluczy.](security-overview.md)
- [Azure Key Vault dewelopera](developers-guide.md)