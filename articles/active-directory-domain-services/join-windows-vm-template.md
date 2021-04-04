---
title: Używanie szablonu do przyłączania maszyny wirtualnej z systemem Windows do usługi Azure AD DS | Microsoft Docs
description: Dowiedz się, jak za pomocą szablonów Azure Resource Manager przyłączyć nową lub istniejącą maszynę wirtualną z systemem Windows Server do domeny zarządzanej Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619440"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej Azure Active Directory Domain Services przy użyciu szablonu Menedżer zasobów

Aby zautomatyzować wdrażanie i konfigurację maszyn wirtualnych platformy Azure, możesz użyć szablonu Menedżer zasobów. Te szablony umożliwiają tworzenie spójnych wdrożeń za każdym razem. Rozszerzenia mogą być również zawarte w szablonach w celu automatycznego skonfigurowania maszyny wirtualnej w ramach wdrożenia. Jednym z przydatnych rozszerzeń jest przyłączanie maszyn wirtualnych do domeny, które mogą być używane z domenami zarządzanymi Azure Active Directory Domain Services (Azure AD DS).

W tym artykule pokazano, jak utworzyć maszynę wirtualną z systemem Windows Server i dołączyć ją do domeny zarządzanej AD DS platformy Azure przy użyciu szablonów Menedżer zasobów. Dowiesz się również, jak przyłączyć istniejącą maszynę wirtualną z systemem Windows Server do domeny AD DS platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności pierwszy samouczek [tworzy i konfiguruje domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Konto użytkownika, które jest częścią domeny zarządzanej.

## <a name="azure-resource-manager-template-overview"></a>Przegląd szablonu Azure Resource Manager

Szablony Menedżer zasobów umożliwiają definiowanie infrastruktury platformy Azure w kodzie. Wszystkie wymagane zasoby, połączenia sieciowe lub Konfiguracja maszyn wirtualnych można zdefiniować w szablonie. Szablony te tworzą spójne, powtarzające się wdrożenia za każdym razem i mogą być używane w miarę wprowadzania zmian. Aby uzyskać więcej informacji, zobacz [Omówienie szablonów Azure Resource Manager][template-overview].

Każdy zasób jest zdefiniowany w szablonie przy użyciu JavaScript Object Notation (JSON). Poniższy przykład JSON używa typu zasobu *Microsoft. COMPUTE/virtualMachines/Extensions* do zainstalowania rozszerzenia dołączania do domeny Active Directory. Parametry są używane w czasie wdrażania. Po wdrożeniu rozszerzenia maszyna wirtualna jest przyłączona do określonej domeny zarządzanej.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

To rozszerzenie maszyny wirtualnej można wdrożyć, nawet jeśli nie zostanie utworzona maszyna wirtualna w tym samym szablonie. W przykładach w tym artykule przedstawiono obie następujące podejścia:

* [Tworzenie maszyny wirtualnej z systemem Windows Server i dołączanie do domeny zarządzanej](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Przyłączanie istniejącej maszyny wirtualnej z systemem Windows Server do domeny zarządzanej](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Tworzenie maszyny wirtualnej z systemem Windows Server i dołączanie do domeny zarządzanej

Jeśli potrzebujesz maszyny wirtualnej z systemem Windows Server, możesz ją utworzyć i skonfigurować przy użyciu szablonu Menedżer zasobów. Po wdrożeniu maszyny wirtualnej rozszerzenie jest instalowane w celu przyłączenia maszyny wirtualnej do domeny zarządzanej. Jeśli masz już maszynę wirtualną, którą chcesz przyłączyć do domeny zarządzanej, przejdź do [domeny zarządzanej, aby dołączyć do istniejącej maszyny wirtualnej z systemem Windows Server](#join-an-existing-windows-server-vm-to-a-managed-domain).

Aby utworzyć maszynę wirtualną z systemem Windows Server, Przyłącz ją do domeny zarządzanej, wykonaj następujące czynności:

1. Przejdź do [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Wybierz opcję **wdrożenia na platformie Azure**.
1. Na stronie **wdrożenie niestandardowe** wprowadź następujące informacje, aby utworzyć maszynę wirtualną z systemem Windows Server i dołączyć ją do domeny zarządzanej:

    | Ustawienie                   | Wartość |
    |---------------------------|-------|
    | Subskrypcja              | Wybierz tę samą subskrypcję platformy Azure, w której włączono Azure AD Domain Services. |
    | Grupa zasobów            | Wybierz grupę zasobów dla maszyny wirtualnej. |
    | Lokalizacja                  | Wybierz lokalizację dla maszyny wirtualnej. |
    | Istniejąca nazwa sieci wirtualnej        | Nazwa istniejącej sieci wirtualnej, z którą ma zostać nawiązane połączenie z MASZYNą wirtualną, na przykład *myVnet*. |
    | Istniejąca nazwa podsieci      | Nazwa istniejącej podsieci sieci wirtualnej, na przykład *obciążeń*. |
    | Prefiks etykiety DNS          | Wprowadź nazwę DNS, która ma być używana dla maszyny wirtualnej, na przykład *MyVM*. |
    | Rozmiar maszyny wirtualnej                   | Określ rozmiar maszyny wirtualnej, na przykład *Standard_DS2_v2*. |
    | Domena do przyłączenia            | Nazwa DNS domeny zarządzanej, na przykład *aaddscontoso.com*. |
    | Nazwa użytkownika domeny           | Konto użytkownika w domenie zarządzanej, które ma zostać użyte do przyłączenia maszyny wirtualnej do domeny zarządzanej, na przykład `contosoadmin@aaddscontoso.com` . To konto musi być częścią domeny zarządzanej. |
    | Hasło domeny           | Hasło dla konta użytkownika określonego w poprzednim ustawieniu. |
    | Opcjonalna ścieżka jednostki organizacyjnej          | Niestandardowa jednostka organizacyjna, w której ma zostać dodana maszyna wirtualna. Jeśli nie określisz wartości tego parametru, maszyna wirtualna zostanie dodana do domyślnej jednostki organizacyjnej *domeny usługi AAD* . |
    | Nazwa użytkownika administratora maszyny wirtualnej         | Określ konto administratora lokalnego, które ma zostać utworzone na maszynie wirtualnej. |
    | Hasło administratora maszyny wirtualnej         | Określ hasło administratora lokalnego dla maszyny wirtualnej. Utwórz silne hasło administratora lokalnego, aby chronić przed atakami polegającymi na wymuszeniu hasła. |

1. Przejrzyj warunki i postanowienia, a następnie zaznacz pole wyboru **Akceptuję warunki i postanowienia podane powyżej**. Gdy wszystko będzie gotowe, wybierz pozycję **Kup** , aby utworzyć i dołączyć maszynę wirtualną do domeny zarządzanej.

> [!WARNING]
> **Obsługa haseł z zachowaniem ostrożności.**
> Plik parametrów szablonu żąda hasła dla konta użytkownika, które jest częścią domeny zarządzanej. Nie wprowadzaj ręcznie wartości do tego pliku i pozostaw je dostępnym dla udziałów plików lub innych udostępnionych lokalizacji.

Ukończenie wdrożenia może potrwać kilka minut. Po zakończeniu maszyna wirtualna z systemem Windows zostanie utworzona i przyłączona do domeny zarządzanej. Maszyna wirtualna może być zarządzana lub zapisywana przy użyciu kont domeny.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Przyłączanie istniejącej maszyny wirtualnej z systemem Windows Server do domeny zarządzanej

Jeśli masz istniejącą maszynę wirtualną lub grupę maszyn wirtualnych, które chcesz dołączyć do domeny zarządzanej, możesz użyć szablonu Menedżer zasobów, aby po prostu wdrożyć rozszerzenie maszyny wirtualnej.

Aby przyłączyć istniejącą maszynę wirtualną z systemem Windows Server do domeny zarządzanej, wykonaj następujące czynności:

1. Przejdź do [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Wybierz opcję **wdrożenia na platformie Azure**.
1. Na stronie **wdrożenie niestandardowe** wprowadź następujące informacje, aby dołączyć maszynę wirtualną do domeny zarządzanej:

    | Ustawienie                   | Wartość |
    |---------------------------|-------|
    | Subskrypcja              | Wybierz tę samą subskrypcję platformy Azure, w której włączono Azure AD Domain Services. |
    | Grupa zasobów            | Wybierz grupę zasobów z istniejącą maszyną wirtualną. |
    | Lokalizacja                  | Wybierz lokalizację istniejącej maszyny wirtualnej. |
    | Lista maszyn wirtualnych                   | Wprowadź rozdzieloną przecinkami listę istniejących maszyn wirtualnych do przyłączenia do domeny zarządzanej, takich jak *myVM1, myVM2*. |
    | Nazwa użytkownika przyłączania do domeny     | Konto użytkownika w domenie zarządzanej, które ma zostać użyte do przyłączenia maszyny wirtualnej do domeny zarządzanej, na przykład `contosoadmin@aaddscontoso.com` . To konto musi być częścią domeny zarządzanej. |
    | Hasło użytkownika dołączania do domeny | Hasło dla konta użytkownika określonego w poprzednim ustawieniu. |
    | Opcjonalna ścieżka jednostki organizacyjnej          | Niestandardowa jednostka organizacyjna, w której ma zostać dodana maszyna wirtualna. Jeśli nie określisz wartości tego parametru, maszyna wirtualna zostanie dodana do domyślnej jednostki organizacyjnej *domeny usługi AAD* . |

1. Przejrzyj warunki i postanowienia, a następnie zaznacz pole wyboru **Akceptuję warunki i postanowienia podane powyżej**. Gdy wszystko będzie gotowe, wybierz pozycję **Kup** , aby dołączyć maszynę wirtualną do domeny zarządzanej.

> [!WARNING]
> **Obsługa haseł z zachowaniem ostrożności.**
> Plik parametrów szablonu żąda hasła dla konta użytkownika, które jest częścią domeny zarządzanej. Nie wprowadzaj ręcznie wartości do tego pliku i pozostaw je dostępnym dla udziałów plików lub innych udostępnionych lokalizacji.

Ukończenie wdrożenia może potrwać kilka minut. Po zakończeniu określone maszyny wirtualne z systemem Windows są przyłączone do domeny zarządzanej i mogą być zarządzane lub zalogowane do korzystania z kont domeny.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto Azure Portal do konfigurowania i wdrażania zasobów przy użyciu szablonów. Możesz również wdrożyć zasoby za pomocą szablonów Menedżer zasobów przy użyciu [Azure PowerShell][deploy-powershell] lub [interfejsu wiersza polecenia platformy Azure][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
