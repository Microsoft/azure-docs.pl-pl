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
ms.date: 11/20/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6756d66f176314ad5abd0c94f7bdf96c42a460a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255313"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób używania tożsamości zarządzanej przypisanej przez system dla maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Key Vault. Usługa Key Vault, używana przy uruchamianiu, umożliwia aplikacji klienckiej użycie wpisu tajnego do uzyskania dostępu do zasobów, które nie są zabezpieczane przez usługę Azure Active Directory (AD). Tożsamości zarządzane dla zasobów platformy Azure są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. 

Omawiane kwestie:

> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault 
 
## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault  

Przy użyciu tożsamości usługi zarządzanej dla zasobów platformy Azure kod może uzyskać tokeny dostępu, aby uwierzytelniać zasoby obsługujące uwierzytelnianie usługi Azure Active Directory.Jednak nie wszystkie usługi platformy Azure obsługują uwierzytelnianie usługi Azure AD.Aby użyć tożsamości zarządzanych dla zasobów platformy Azure z tymi usługami, zapisz poświadczenia usługi w usłudze Azure Key Vault, a następnie użyj tożsamości zarządzanych dla zasobów platformy Azure, aby uzyskać dostęp do usługi Key Vault i pobrać te poświadczenia. 

Najpierw musimy utworzyć usługę Key Vault i udzielić przypisanej przez system tożsamości zarządzanej naszej maszyny wirtualnej dostępu do usługi Key Vault.   

1. W górnej części lewego paska nawigacyjnego wybierz pozycję **Utwórz zasób**  >  **zabezpieczenia + tożsamość**  >  **Key Vault**.  
2. Podaj **Nazwę** dla nowej usługi Key Vault. 
3. Znajdź usługę Key Vault w tej samej subskrypcji i grupie zasobów co wcześniej utworzona maszyna wirtualna. 
4. Wybierz opcję **Zasady dostępu** i kliknij opcję **Dodaj nową**. 
5. W pozycji Konfiguruj na podstawie szablonu wybierz opcję **Zarządzanie wpisami tajnymi**. 
6. Wybierz opcję **Wybierz podmiot zabezpieczeń**, a następnie w polu wyszukiwania wprowadź nazwę wcześniej utworzonej maszyny wirtualnej.Wybierz maszynę wirtualną z listy wyników i kliknij opcję **Wybierz**. 
7. Kliknij przycisk **OK**, aby zakończyć dodawanie nowych zasad dostępu, a następnie kliknij przycisk **OK**, aby zakończyć wybór zasad dostępu. 
8. Kliknij przycisk **Utwórz**, aby zakończyć tworzenie usługi Key Vault. 

    ![Alternatywny tekst obrazu](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Następnie dodaj wpis tajny do usługi Key Vault, aby umożliwić późniejsze pobranie wpisu tajnego przy użyciu kodu uruchomionego na maszynie wirtualnej: 

1. Wybierz opcję **Wszystkie zasoby**, a następnie znajdź i wybierz utworzoną usługę Key Vault. 
2. Wybierz opcję **Wpisy tajne** i kliknij opcję **Dodaj**. 
3. Wybierz opcję **Ręczne** z pozycji **Opcje przekazywania**. 
4. Wprowadź nazwę i wartość wpisu tajnego.Wartość może być dowolna. 
5. Pozostaw pustą datę aktywacji i datę wygaśnięcia oraz zostaw opcję **Włączone** ustawioną na wartość **Tak**. 
6. Kliknij pozycję **Utwórz**, aby utworzyć wpis tajny. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault  

Aby wykonać te kroki, potrzebujesz klienta SSH.Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](/windows/wsl/about). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
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
    
    Możesz użyć tego tokenu dostępu, aby przeprowadzić uwierzytelnianie do usługi Azure Key Vault.Kolejne żądanie programu CURL przedstawia sposób odczytu wpisu tajnego z usługi Key Vault przy użyciu programu CURL i interfejsu API REST usługi Key Vault.Będziesz potrzebować adresu URL usługi Key Vault, który znajduje się w sekcji **Podstawowe elementy** na stronie **Przegląd** usługi Key Vault.Ponadto będziesz potrzebować tokenu dostępu uzyskanego w poprzednim wywołaniu. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpowiedź będzie wyglądać następująco: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Po pobraniu wpisu tajnego z usługi Key Vault możesz użyć go do uwierzytelnienia w usłudze wymagającej nazwy i hasła.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób używania przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Key Vault.  Dowiedz się więcej o usłudze Azure Key Vault:

> [!div class="nextstepaction"]
>[Usługa Azure Key Vault](../../key-vault/general/overview.md)