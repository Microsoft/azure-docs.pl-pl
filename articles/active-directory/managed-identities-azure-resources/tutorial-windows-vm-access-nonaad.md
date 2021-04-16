---
title: 'Samouczek: używanie tożsamości zarządzanej do uzyskiwania dostępu do Azure Key Vault — Windows — Azure AD'
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
ms.openlocfilehash: 92ded2d327fd7c75633bf7ef6b7dd3041725c921
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378265"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku pokazano, jak maszyna wirtualna z systemem Windows może używać przypisanej przez system tożsamości zarządzanej w celu uzyskania dostępu [do Azure Key Vault](../../key-vault/general/overview.md). Służy jako bootstrap, Key Vault umożliwia aplikacji klienckiej następnie użyć tajnego dostępu do zasobów, które nie są zabezpieczone przez Azure Active Directory (AD). Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure i umożliwiają uwierzytelnianie w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez uwzględnienia informacji o uwierzytelnianiu w kodzie.

Omawiane kwestie:

> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault 

## <a name="prerequisites"></a>Wymagania wstępne

- Znajomość tożsamości zarządzanych. Jeśli nie znasz funkcji tożsamości zarządzanych dla zasobów platformy Azure, zobacz to [omówienie](overview.md). 
- Konto platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto.](https://azure.microsoft.com/free/)
- Uprawnienia "Właściciel" w odpowiednim zakresie (subskrypcja lub grupa zasobów) do wykonywania wymaganych kroków tworzenia zasobów i zarządzania rolami. Jeśli potrzebujesz pomocy w przypisaniu ról, zobacz Przypisywanie ról platformy Azure w [celu zarządzania dostępem do zasobów subskrypcji platformy Azure.](../../role-based-access-control/role-assignments-portal.md)
- Potrzebna jest również maszyna wirtualna z systemem Windows z włączonymi tożsamościami zarządzanymi przypisanymi przez system.
  - Jeśli musisz utworzyć maszynę wirtualną na potrzeby tego samouczka, możesz postępować zgodnie z artykułem Tworzenie maszyny wirtualnej z włączoną tożsamością [przypisaną przez system](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy  

W tej sekcji pokazano, jak udzielić maszynie wirtualnej dostępu do tajnego Key Vault. Używając tożsamości zarządzanych dla zasobów platformy Azure, kod może uzyskiwać tokeny dostępu, aby uwierzytelniać się w zasobach obsługujących uwierzytelnianie usługi Azure AD.Jednak nie wszystkie usługi platformy Azure obsługują uwierzytelnianie usługi Azure AD. Aby użyć tożsamości zarządzanych dla zasobów platformy Azure z tymi usługami, zapisz poświadczenia usługi w usłudze Azure Key Vault, a następnie użyj tożsamości zarządzanej maszyny wirtualnej, aby uzyskać dostęp do usługi Key Vault i pobrać te poświadczenia.

Najpierw musimy utworzyć usługę Key Vault i udzielić przypisanej przez system tożsamości zarządzanej naszej maszyny wirtualnej dostępu do usługi Key Vault.

1. Otwieranie witryny Azure [Portal](https://portal.azure.com/)
1. W górnej części lewego paska nawigacyjnego wybierz pozycję **Utwórz zasób**  
1. W polu **Wyszukaj w witrynie Marketplace** wpisz **Key Vault** naciśnij klawisz **Enter.**  
1. Wybierz **Key Vault** z wyników.
1. Wybierz pozycję **Utwórz**
1. Podaj **Nazwę** dla nowej usługi Key Vault.

    ![Ekran tworzenia magazynu kluczy](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

1. Wypełnij wszystkie wymagane informacje, aby wybrać subskrypcję i grupę zasobów, w której utworzono maszynę wirtualną używaną na potrzeby tego samouczka.
1. Wybierz **pozycję Przeglądanie+ tworzenie**
1. Wybierz pozycję **Utwórz**

### <a name="create-a-secret"></a>Utwórz klucz tajny

Następnie dodaj klucz tajny do Key Vault, aby można go było później pobrać przy użyciu kodu uruchomionego na maszynie wirtualnej. Na potrzeby tego samouczka używamy programu PowerShell, ale te same pojęcia dotyczą każdego kodu wykonywanego na tej maszynie wirtualnej.

1. Przejdź do nowo utworzonego Key Vault.
1. Wybierz opcję **Wpisy tajne** i kliknij opcję **Dodaj**.
1. Wybierz pozycję **Wygeneruj/zaimportuj**
1. Na **ekranie Tworzenie tajnego** w **skrypcie Opcje przekazywania** **pozostaw zaznaczoną opcję** Ręcznie.
1. Wprowadź nazwę i wartość wpisu tajnego.  Wartość może być dowolna. 
1. Pozostaw pustą datę aktywacji i datę wygaśnięcia oraz zostaw opcję **Włączone** ustawioną na wartość **Tak**. 
1. Kliknij pozycję **Utwórz**, aby utworzyć wpis tajny.

   ![Utwórz klucz tajny](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Udzielanie dostępu

Tożsamość zarządzana używana przez maszynę wirtualną musi mieć dostęp do odczytu tajnego, który będzie zapisywany w Key Vault.

1. Przejdź do nowo utworzonego Key Vault
1. Wybierz **pozycję Zasady dostępu** z menu po lewej stronie.
1. Wybierz **pozycję Dodaj zasady dostępu**

   ![Ekran tworzenia zasad dostępu magazynu kluczy](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. W sekcji **Dodawanie zasad dostępu w** obszarze Konfiguruj na podstawie szablonu **(opcjonalnie)** wybierz pozycję **Zarządzanie** kluczami tajnymi z menu rozwijanego.
1. Wybierz opcję **Wybierz podmiot zabezpieczeń**, a następnie w polu wyszukiwania wprowadź nazwę wcześniej utworzonej maszyny wirtualnej.  Wybierz maszynę wirtualną z listy wyników, a następnie wybierz **pozycję Wybierz**.
1. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Zapisz**.


## <a name="access-data"></a>Uzyskiwanie dostępu do danych  

W tej sekcji pokazano, jak uzyskać token dostępu przy użyciu tożsamości maszyny wirtualnej i użyć go do pobrania tajnego Key Vault. Jeśli nie masz zainstalowanego programu PowerShell 4.3.1 (lub nowszej wersji), musisz [pobrać i zainstalować najnowszą wersję](/powershell/azure/).

Najpierw użyjemy przypisanej przez system tożsamości zarządzanej maszyny wirtualnej, aby uzyskać token dostępu i przeprowadzić uwierzytelnianie w usłudze Key Vault:
 
1. W portalu przejdź do pozycji **Maszyny wirtualne**, a następnie przejdź do swojej maszyny wirtualnej z systemem Windows i w pozycji **Przegląd** kliknij przycisk **Połącz**.
2. Wprowadź nazwę użytkownika **i** hasło **dodane** podczas tworzenia maszyny wirtualnej **z systemem Windows.**  
3. Teraz, po utworzeniu serwera **Podłączanie pulpitu zdalnego** z maszyną wirtualną, otwórz program PowerShell w sesji zdalnej.  
4. W programie PowerShell wywołaj żądanie internetowe w dzierżawie, aby uzyskać token dla hosta lokalnego w konkretnym porcie dla maszyny wirtualnej.  

Żądanie programu PowerShell:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

Poniżej widać, jak wygląda odpowiedź:

![żądanie z odpowiedzią tokenu](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

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

Jeśli chcesz wyczyścić zasoby, odwiedź witrynę programu [Azure Portal,](https://portal.azure.com)wybierz pozycję Grupy **zasobów,** znajdź i wybierz grupę zasobów, która została utworzona w trakcie tego samouczka (na przykład ), a następnie użyj polecenia Usuń `mi-test` **grupę** zasobów.

Można to również zrobić za pomocą programu [PowerShell lub interfejsu wiersza polecenia](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano sposób używania przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do Azure Key Vault.  Dowiedz się więcej o usłudze Azure Key Vault:

> [!div class="nextstepaction"]
>[Usługa Azure Key Vault](../../key-vault/general/overview.md)
