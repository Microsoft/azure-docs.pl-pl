---
title: Tworzenie grup adresów IP w zaporze platformy Azure
description: Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394530"
---
# <a name="create-ip-groups"></a>Tworzenie grup adresów IP

Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi. Mogą mieć jeden adres IP, wiele adresów IP lub jeden lub więcej zakresów adresów IP.

## <a name="create-an-ip-group---azure-portal"></a>Tworzenie grupy adresów IP — Azure Portal

Aby utworzyć grupę adresów IP przy użyciu Azure Portal:

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
1. W polu wyszukiwania wprowadź **grupy adresów IP** , a następnie wybierz pozycję **grupy adresów IP**.
1. Wybierz przycisk **Utwórz**.
1. Wybierz subskrypcję.
1. Wybierz grupę zasobów lub Utwórz nową.
1. Wprowadź unikatową nazwę grupy adresów IP, a następnie wybierz region.
1. Wybierz pozycję **Dalej: adresy IP**.
1. Wpisz adres IP, wiele adresów IP lub zakresy adresów IP.

   Istnieją dwa sposoby wprowadzania adresów IP:
   - Możesz wprowadzić je ręcznie
   - Możesz zaimportować je z pliku

   Aby zaimportować plik z pliku, wybierz pozycję **Importuj z pliku**. Możesz przeciągnąć plik do pola lub wybrać pozycję **Przeglądaj w poszukiwaniu plików**. W razie potrzeby można przeglądać i edytować przekazane adresy IP.

   Po wpisaniu adresu IP Portal sprawdza jego poprawność, aby sprawdzić, czy występują problemy z nakładającymi się, duplikowaniem i formatowaniem.

1. Po zakończeniu wybierz pozycję **Przegląd + Utwórz**.
1. Wybierz przycisk **Utwórz**.

## <a name="create-an-ip-group---azure-powershell"></a>Tworzenie grupy adresów IP — Azure PowerShell

Ten przykład tworzy grupę adresów IP z prefiksem adresu i adresem IP przy użyciu Azure PowerShell:

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>Tworzenie grupy adresów IP — interfejs wiersza polecenia platformy Azure

Ten przykład tworzy grupę adresów IP z prefiksem adresu i adresem IP przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej na temat grup adresów IP](ip-groups.md)
