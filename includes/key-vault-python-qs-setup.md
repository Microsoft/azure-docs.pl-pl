---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482140"
---
1. Upewnij się, że masz [konto platformy Azure z aktywną subskrypcją](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Zainstaluj środowisko [Python 2.7 + lub 3.5.3 +](https://www.python.org/downloads).

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Postępuj zgodnie z instrukcjami dotyczącymi [konfiguracji uwierzytelniania lokalnego](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication), za pomocą którego tworzysz lokalną jednostkę usługi i udostępnić ją klientowi Azure Key Vault dla języka Python za pomocą zmiennych środowiskowych. 

    Podczas uruchamiania kodu bezpośrednio na platformie Azure, oddzielna jednostka usługi nie jest wymagana, jeśli aplikacja korzysta z tożsamości zarządzanej.

1. W terminalu lub wierszu polecenia Utwórz odpowiedni folder projektu, a następnie utwórz i aktywuj środowisko wirtualne języka Python zgodnie z opisem w temacie [Korzystanie z środowisk wirtualnych języka Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Zainstaluj bibliotekę tożsamości Azure Active Directory:

    ```terminal
    pip install azure.identity
    ```
