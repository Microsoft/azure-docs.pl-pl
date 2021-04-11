---
title: Samouczek `:` Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do Azure Key Vault-Windows-Azure AD
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Key Vault.
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
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f29c9daba80ffc57b96cf5bd82690dea9ac6429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093731"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek pokazuje, w jaki sposób maszyna wirtualna z systemem Windows może korzystać z tożsamości zarządzanej przypisanej przez system w celu uzyskania dostępu do [Azure Key Vault](../../key-vault/general/overview.md). Obsługa jako Bootstrap, Key Vault umożliwia aplikacji klienckiej używanie klucza tajnego w celu uzyskania dostępu do zasobów niezabezpieczonych przez Azure Active Directory (AD). Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure i umożliwiają uwierzytelnianie w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez uwzględnienia informacji o uwierzytelnianiu w kodzie.

Omawiane kwestie:

> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault 

## <a name="prerequisites"></a>Wymagania wstępne

- Zrozumienie zarządzanych tożsamości. Jeśli nie znasz funkcji tożsamości zarządzanych dla zasobów platformy Azure, zobacz to [omówienie](overview.md). 
- Konto platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Uprawnienia "właściciel" w odpowiednim zakresie (subskrypcji lub grupy zasobów) do wykonywania wymaganych kroków tworzenia zasobów i zarządzania rolami. Jeśli potrzebujesz pomocy z przypisaniem roli, zobacz [Przypisywanie ról platformy Azure do zarządzania dostępem do zasobów subskrypcji platformy Azure](../../role-based-access-control/role-assignments-portal.md).
- Potrzebna jest również maszyna wirtualna z systemem Windows z włączonymi tożsamościami zarządzanymi przez system.
  - Jeśli musisz utworzyć maszynę wirtualną dla tego samouczka, możesz wykonać czynności opisane w artykule zatytułowanym [Tworzenie maszyny wirtualnej z włączoną tożsamością przypisaną przez system](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy  

W tej sekcji pokazano, jak udzielić dostępu do maszyny wirtualnej do wpisu tajnego przechowywanego w Key Vault. Używając tożsamości zarządzanych dla zasobów platformy Azure, kod może uzyskiwać tokeny dostępu, aby uwierzytelniać się w zasobach obsługujących uwierzytelnianie usługi Azure AD.Jednak nie wszystkie usługi platformy Azure obsługują uwierzytelnianie usługi Azure AD. Aby użyć tożsamości zarządzanych dla zasobów platformy Azure z tymi usługami, zapisz poświadczenia usługi w usłudze Azure Key Vault, a następnie użyj tożsamości zarządzanej maszyny wirtualnej, aby uzyskać dostęp do usługi Key Vault i pobrać te poświadczenia.

Najpierw musimy utworzyć usługę Key Vault i udzielić przypisanej przez system tożsamości zarządzanej naszej maszyny wirtualnej dostępu do usługi Key Vault.

1. Otwórz [Portal](https://portal.azure.com/) Azure
1. W górnej części lewego paska nawigacyjnego wybierz pozycję **Utwórz zasób** .  
1. W polu **Wyszukaj wpisz witrynę Marketplace** w **Key Vault** i naciśnij klawisz **Enter**.  
1. Wybierz **Key Vault** z wyników.
1. Wybierz pozycję **Utwórz**
1. Podaj **Nazwę** dla nowej usługi Key Vault.

    ![Tworzenie ekranu magazynu kluczy](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

1. Wypełnij wszystkie wymagane informacje, aby wybrać subskrypcję i grupę zasobów, w której utworzono maszynę wirtualną używaną w ramach tego samouczka.
1. Wybieranie opcji **Recenzja + tworzenie**
1. Wybierz pozycję **Utwórz**

### <a name="create-a-secret"></a>Utwórz klucz tajny

Następnie Dodaj klucz tajny do Key Vault, aby można było go później pobrać przy użyciu kodu uruchomionego na maszynie wirtualnej. Na potrzeby tego samouczka korzystamy z programu PowerShell, ale te same koncepcje dotyczą dowolnego kodu wykonywanego na tej maszynie wirtualnej.

1. Przejdź do nowo utworzonego Key Vault.
1. Wybierz opcję **Wpisy tajne** i kliknij opcję **Dodaj**.
1. Wybierz pozycję **Generuj/Importuj**
1. Na ekranie **Tworzenie wpisu tajnego** z **opcji przekazywania** pozostaw **ręcznie** zaznaczone.
1. Wprowadź nazwę i wartość wpisu tajnego.  Wartość może być dowolna. 
1. Pozostaw pustą datę aktywacji i datę wygaśnięcia oraz zostaw opcję **Włączone** ustawioną na wartość **Tak**. 
1. Kliknij pozycję **Utwórz**, aby utworzyć wpis tajny.

   ![Utwórz klucz tajny](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Udzielanie dostępu

Zarządzaną tożsamością używaną przez maszynę wirtualną musi być udzielenie dostępu w celu odczytu wpisu tajnego, który będzie przechowywany w Key Vault.

1. Przejdź do nowo utworzonego Key Vault
1. Z menu po lewej stronie wybierz pozycję **zasady dostępu** .
1. Wybierz pozycję **Dodaj zasady dostępu**

   ![ekran tworzenia zasad dostępu w magazynie kluczy](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. W sekcji **Dodawanie zasad dostępu** w obszarze **Konfiguruj z szablonu (opcjonalnie)** wybierz pozycję **Zarządzanie kluczami tajnymi** z menu rozwijanego.
1. Wybierz opcję **Wybierz podmiot zabezpieczeń**, a następnie w polu wyszukiwania wprowadź nazwę wcześniej utworzonej maszyny wirtualnej.  Na liście wyników wybierz maszynę wirtualną, a następnie wybierz **pozycję Wybierz**.
1. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Zapisz**.


## <a name="access-data"></a>Uzyskiwanie dostępu do danych  

W tej sekcji pokazano, jak uzyskać token dostępu przy użyciu tożsamości maszyny wirtualnej i użyć go do pobrania klucza tajnego z Key Vault. Jeśli nie masz zainstalowanego programu PowerShell 4.3.1 (lub nowszej wersji), musisz [pobrać i zainstalować najnowszą wersję](/powershell/azure/).

Najpierw użyjemy przypisanej przez system tożsamości zarządzanej maszyny wirtualnej, aby uzyskać token dostępu i przeprowadzić uwierzytelnianie w usłudze Key Vault:
 
1. W portalu przejdź do pozycji **Maszyny wirtualne**, a następnie przejdź do swojej maszyny wirtualnej z systemem Windows i w pozycji **Przegląd** kliknij przycisk **Połącz**.
2. Wprowadź **nazwę użytkownika** i **hasło** , które zostały dodane podczas tworzenia **maszyny wirtualnej z systemem Windows**.  
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program PowerShell w sesji zdalnej.  
4. W programie PowerShell wywołaj żądanie internetowe w dzierżawie, aby uzyskać token dla hosta lokalnego w konkretnym porcie dla maszyny wirtualnej.  

Żądanie programu PowerShell:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

Możesz zobaczyć, jak wygląda odpowiedź poniżej:

![żądanie z odpowiedzią na token](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

Następnie wyodrębnij token dostępu z odpowiedzi.  

```powershell
   $KeyVaultToken = $Response.access_token
```

Na koniec użyj polecenia Invoke-WebRequest programu PowerShell, aby pobrać wpis tajny utworzony wcześniej w usłudze Key Vault, przekazując token dostępu w nagłówku autoryzacji.  Będziesz potrzebować adresu URL usługi Key Vault, który znajduje się w sekcji **Podstawowe elementy** na stronie **Przegląd** usługi Key Vault.  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

Odpowiedź będzie wyglądać następująco: 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

Po pobraniu wpisu tajnego z usługi Key Vault możesz użyć go do uwierzytelnienia w usłudze wymagającej nazwy i hasła.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby, odwiedź [Azure Portal](https://portal.azure.com), wybierz pozycję **grupy zasobów**, Znajdź i wybierz grupę zasobów, która została utworzona w procesie tego samouczka (na przykład `mi-test` ), a następnie użyj polecenia **Usuń grupę zasobów** .

Alternatywnie możesz to zrobić za pomocą [programu PowerShell lub interfejsu wiersza polecenia](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób używania tożsamości zarządzanej przypisanej przez system Windows VM do uzyskiwania dostępu do Azure Key Vault.  Dowiedz się więcej o usłudze Azure Key Vault:

> [!div class="nextstepaction"]
>[Usługa Azure Key Vault](../../key-vault/general/overview.md)