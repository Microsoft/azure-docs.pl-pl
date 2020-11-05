---
title: Samouczek `:` Używanie tożsamości zarządzanej do uzyskiwania dostępu do Azure Key Vault-Linux — Azure AD
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cfcaec38a939291090da7d2229c4a95f984bf28
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360444"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku pokazano, w jaki sposób maszyna wirtualna z systemem Linux może korzystać z tożsamości zarządzanej przypisanej przez system w celu uzyskania dostępu do [Azure Key Vault](../../key-vault/general/overview.md). Obsługa jako Bootstrap, Key Vault umożliwia aplikacji klienckiej używanie klucza tajnego w celu uzyskania dostępu do zasobów niezabezpieczonych przez Azure Active Directory (AD). Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure i umożliwiają uwierzytelnianie w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez uwzględnienia informacji o uwierzytelnianiu w kodzie.

Omawiane kwestie:

> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault 
 
## <a name="prerequisites"></a>Wymagania wstępne

- Zrozumienie zarządzanych tożsamości. Jeśli nie znasz funkcji tożsamości zarządzanych dla zasobów platformy Azure, zobacz to [omówienie](overview.md). 
- Konto platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Uprawnienia "właściciel" w odpowiednim zakresie (subskrypcji lub grupy zasobów) do wykonywania wymaganych kroków tworzenia zasobów i zarządzania rolami. Jeśli potrzebujesz pomocy dotyczącej przypisania roli, zobacz [Korzystanie z kontroli dostępu opartej na rolach do zarządzania dostępem do zasobów subskrypcji platformy Azure](../../role-based-access-control/role-assignments-portal.md).
- Potrzebna jest również maszyna wirtualna z systemem Linux z włączonymi tożsamościami zarządzanymi.
  - Jeśli musisz utworzyć maszynę wirtualną dla tego samouczka, możesz wykonać artykuł zatytułowany [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy  

W tej sekcji pokazano, jak udzielić dostępu do maszyny wirtualnej do wpisu tajnego przechowywanego w Key Vault. Używając tożsamości zarządzanych dla zasobów platformy Azure, kod może uzyskiwać tokeny dostępu, aby uwierzytelniać się w zasobach obsługujących uwierzytelnianie usługi Azure AD.Jednak nie wszystkie usługi platformy Azure obsługują uwierzytelnianie usługi Azure AD. Aby użyć tożsamości zarządzanych dla zasobów platformy Azure z tymi usługami, zapisz poświadczenia usługi w usłudze Azure Key Vault, a następnie użyj tożsamości zarządzanej maszyny wirtualnej, aby uzyskać dostęp do usługi Key Vault i pobrać te poświadczenia.

Najpierw musimy utworzyć usługę Key Vault i udzielić przypisanej przez system tożsamości zarządzanej naszej maszyny wirtualnej dostępu do usługi Key Vault.

1. Otwórz [Portal](https://portal.azure.com/) Azure
1. W górnej części lewego paska nawigacyjnego wybierz pozycję **Utwórz zasób** .  
1. W polu **Wyszukaj wpisz witrynę Marketplace** w **Key Vault** i naciśnij klawisz **Enter**.  
1. Wybierz **Key Vault** z wyników.
1. Wybierz pozycję **Utwórz**
1. Podaj **Nazwę** dla nowej usługi Key Vault.

    ![Tworzenie ekranu magazynu kluczy](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. Wypełnij wszystkie wymagane informacje, aby wybrać subskrypcję i grupę zasobów, w której utworzono maszynę wirtualną używaną w ramach tego samouczka.
1. Wybieranie opcji **Recenzja + tworzenie**
1. Wybierz pozycję **Utwórz**

## <a name="grant-access"></a>Udzielanie dostępu

Zarządzaną tożsamością używaną przez maszynę wirtualną musi być udzielenie dostępu w celu odczytu wpisu tajnego, który będzie przechowywany w Key Vault.

1. Przejdź do nowo utworzonego Key Vault
1. Z menu po lewej stronie wybierz pozycję **zasady dostępu** .
1. Wybierz pozycję **Dodaj zasady dostępu**

   ![ekran tworzenia zasad dostępu w magazynie kluczy](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. W sekcji **Dodawanie zasad dostępu** w obszarze **Konfiguruj z szablonu (opcjonalnie)** wybierz pozycję **Zarządzanie kluczami tajnymi** z menu rozwijanego.
1. Wybierz opcję **Wybierz podmiot zabezpieczeń** , a następnie w polu wyszukiwania wprowadź nazwę wcześniej utworzonej maszyny wirtualnej.  Na liście wyników wybierz maszynę wirtualną, a następnie wybierz **pozycję Wybierz**.
1. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Zapisz**.

## <a name="create-a-secret"></a>Utwórz klucz tajny

Następnie Dodaj klucz tajny do Key Vault, aby można było go później pobrać przy użyciu kodu uruchomionego na maszynie wirtualnej. Na potrzeby tego samouczka korzystamy z programu PowerShell, ale te same koncepcje dotyczą dowolnego kodu wykonywanego na tej maszynie wirtualnej.

1. Przejdź do nowo utworzonego Key Vault.
1. Wybierz opcję **Wpisy tajne** i kliknij opcję **Dodaj**.
1. Wybierz pozycję **Generuj/Importuj**
1. Na ekranie **Tworzenie wpisu tajnego** z **opcji przekazywania** pozostaw **ręcznie** zaznaczone.
1. Wprowadź nazwę i wartość wpisu tajnego.  Wartość może być dowolna. 
1. Pozostaw pustą datę aktywacji i datę wygaśnięcia oraz zostaw opcję **Włączone** ustawioną na wartość **Tak**. 
1. Kliknij pozycję **Utwórz** , aby utworzyć wpis tajny.

   ![Utwórz klucz tajny](./media/tutorial-linux-vm-access-nonaad/create-secret.png)
 
## <a name="access-data"></a>Uzyskiwanie dostępu do danych

Aby wykonać te kroki, potrzebujesz klienta SSH.  Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](/windows/wsl/about). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. W portalu przejdź do maszyny wirtualnej z systemem Linux i w pozycji **Przegląd** kliknij opcję **Połącz**. 
2. **Połącz** się z maszyną wirtualną za pomocą wybranego klienta SSH. 
3. W oknie terminalu, używając narzędzia CURL, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanych dla zasobów platformy Azure, aby uzyskać token dostępu do usługi Azure Key Vault.  
 
    Żądanie programu CURL dla tokenu dostępu znajduje się poniżej.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    Odpowiedź zawiera token dostępu wymagany do uzyskania dostępu do usługi Resource Manager. 
    
    Odpowiedź:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Możesz użyć tego tokenu dostępu, aby przeprowadzić uwierzytelnianie do usługi Azure Key Vault.  Kolejne żądanie programu CURL przedstawia sposób odczytu wpisu tajnego z usługi Key Vault przy użyciu programu CURL i interfejsu API REST usługi Key Vault.  Będziesz potrzebować adresu URL usługi Key Vault, który znajduje się w sekcji **Podstawowe elementy** na stronie **Przegląd** usługi Key Vault.  Ponadto będziesz potrzebować tokenu dostępu uzyskanego w poprzednim wywołaniu. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpowiedź będzie wyglądać następująco: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Po pobraniu wpisu tajnego z usługi Key Vault możesz użyć go do uwierzytelnienia w usłudze wymagającej nazwy i hasła.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby, odwiedź [Azure Portal](https://portal.azure.com), wybierz pozycję **grupy zasobów** , Znajdź i wybierz grupę zasobów, która została utworzona w procesie tego samouczka (na przykład `mi-test` ), a następnie użyj polecenia **Usuń grupę zasobów** .

Alternatywnie możesz to zrobić za pomocą [programu PowerShell lub interfejsu wiersza polecenia](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób używania przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Key Vault.  Dowiedz się więcej o usłudze Azure Key Vault:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)