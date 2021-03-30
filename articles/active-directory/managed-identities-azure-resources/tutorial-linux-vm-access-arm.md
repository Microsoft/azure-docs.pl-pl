---
title: Szybki Start `:` Użyj tożsamości zarządzanej, aby uzyskać dostęp do Azure Resource Manager — Azure AD
description: Szybki start przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 653159c2e40d3375a422f0da14274f57130de1fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93359684"
---
# <a name="use-a-linux-vm-system-assigned-managed-identity-to-access-azure-resource-manager"></a>Używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Resource Manager

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek przedstawia sposób używania tożsamości przypisanej przez system do maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do interfejsu API usługi Azure Resource Manager. Tożsamości zarządzane dla zasobów platformy Azure są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej dostępu do grupy zasobów w usłudze Azure Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania usługi Azure Resource Manager 

## <a name="prerequisites"></a>Wymagania wstępne

- Zrozumienie zarządzanych tożsamości. Jeśli nie znasz funkcji tożsamości zarządzanych dla zasobów platformy Azure, zobacz to [omówienie](overview.md). 
- Konto platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Potrzebna jest również maszyna wirtualna z systemem Linux z włączonymi tożsamościami zarządzanymi.
  - Jeśli musisz utworzyć maszynę wirtualną dla tego samouczka, możesz wykonać artykuł zatytułowany [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

## <a name="grant-access"></a>Udzielanie dostępu

Używając tożsamości zarządzanych dla zasobów platformy Azure, kod może uzyskiwać tokeny dostępu, aby uwierzytelniać się w zasobach obsługujących uwierzytelnianie usługi Azure AD. Interfejs API usługi Azure Resource Manager obsługuje uwierzytelnianie usługi Azure AD. Najpierw musimy zapewnić tożsamości tej maszyny wirtualnej dostęp do zasobu w usłudze Azure Resource Manager — w tym przypadku do grupy zasobów, która zawiera maszynę wirtualną.  

1. Przejdź do karty **Grupy zasobów**.
2. Wybierz **grupę zasobów** , która została użyta dla maszyny wirtualnej.
3. Przejdź do pozycji **Kontrola dostępu (IAM)** w panelu po lewej stronie.
4. Kliknij, aby **dodać** nowy przydział roli do maszyny wirtualnej. W pozycji **Rola** wybierz opcję **Czytelnik**.
5. Na następnej liście rozwijanej **Przypisz dostęp do** **maszyny wirtualnej** zasobu.
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej **Subskrypcja**. W pozycji **Grupa zasobów** wybierz opcję **Wszystkie grupy zasobów**.
7. Na koniec w pozycji **Wybierz** użyj listy rozwijanej, aby wybrać maszynę wirtualną z systemem Linux, i kliknij przycisk **Zapisz**.

    ![Alternatywny tekst obrazu](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu przypisanej przez system tożsamości zarządzanej maszyny wirtualnej i używanie jej do wywołania usługi Resource Manager

Aby wykonać te kroki, będziesz potrzebować klienta SSH. Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](/windows/wsl/about). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu przejdź do maszyny wirtualnej z systemem Linux i w pozycji **Przegląd** kliknij opcję **Połącz**.  
2. **Połącz** się z maszyną wirtualną za pomocą wybranego klienta SSH. 
3. W oknie terminala wyślij za pomocą narzędzia `curl` żądanie do lokalnego punktu końcowego tożsamości zarządzanych dla zasobów platformy Azure, aby uzyskać token dostępu do usługi Azure Resource Manager.  
 
    Żądanie programu `curl` dla tokenu dostępu znajduje się poniżej.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' -H Metadata:true   
    ```
    
    > [!NOTE]
    > Wartość parametru „resource” musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD.  W przypadku użycia identyfikatora zasobu usługi Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI. 
    
    Odpowiedź zawiera token dostępu wymagany do uzyskania dostępu do usługi Azure Resource Manager. 
    
    Odpowiedź:  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    Możesz użyć tego tokenu dostępu w celu uzyskania dostępu do usługi Azure Resource Manager, np. aby odczytać właściwości grupy zasobów, do której wcześniej udzielono dostępu tej maszynie wirtualnej. Zastąp wartości \<SUBSCRIPTION ID\> , \<RESOURCE GROUP\> i dla \<ACCESS TOKEN\> utworzonych wcześniej reguł. 
    
    > [!NOTE]
    > W adresie URL rozróżniana jest wielkość liter, więc upewnij się, że użyto takich samych wartości jak wcześniej, podczas nazywania grupy zasobów — z wielką literą „G” w nazwie „resourceGroup”.  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpowiedz przy użyciu konkretnych informacji o grupie zasobów: 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób używania tożsamości zarządzanej przypisanej przez system w celu uzyskania dostępu do interfejsu API usługi Azure Resource Manager.  Aby dowiedzieć się więcej o usłudze Azure Resource Manager, zobacz:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md) 
> [Tworzenie, wyświetlanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure PowerShell](how-to-manage-ua-identity-powershell.md)