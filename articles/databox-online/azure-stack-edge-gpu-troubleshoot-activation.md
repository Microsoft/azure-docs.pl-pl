---
title: Użyj Azure Portal do rozwiązywania problemów z błędami aktywacji związanych z Azure Stack EDGE Pro z procesorem GPU | Microsoft Docs
description: Opisuje sposób rozwiązywania problemów dotyczących aktywacji procesora GPU w programie Azure Stack Edge i problemów związanych z magazynem kluczy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 66e62bffe28cc10bd49e1456fdd6e2ac1faebf6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442149"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Rozwiązywanie problemów z aktywacją na urządzeniu z systemem Azure Stack Edge — procesor GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób rozwiązywania problemów dotyczących aktywacji na urządzeniu z systemem Azure Stack Edge z procesorem GPU. 


## <a name="activation-errors"></a>Błędy aktywacji

W poniższej tabeli zestawiono błędy związane z aktywacją urządzenia i odpowiednim zalecanym rozwiązaniem.

| Komunikat o błędzie| Zalecane rozwiązanie |
|------------------------------------------------------|--------------------------------------|
| Jeśli Azure Key Vault używany do aktywacji zostanie usunięty przed aktywowaniem urządzenia przy użyciu klucza aktywacji, zostanie wyświetlony ten błąd. <br> ![Błąd magazynu kluczy 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Jeśli magazyn kluczy został usunięty, można odzyskać Magazyn kluczy, jeśli magazyn jest w trakcie przeczyszczania i ochrony. Wykonaj kroki opisane w sekcji [odzyskiwanie magazynu kluczy](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). <br>W przypadku upływu czasu trwania ochrony przed przeczyszczeniem nie można odzyskać magazynu kluczy. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| Jeśli Azure Key Vault zostanie usunięta po aktywowaniu urządzenia, a następnie spróbuj wykonać dowolną operację, która obejmuje szyfrowanie, na przykład: **Dodaj użytkownika**, **Dodaj udział**, **Skonfiguruj obliczenia**, a następnie pojawi się ten błąd. <br> ![Błąd magazynu kluczy 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Jeśli magazyn kluczy został usunięty, można odzyskać Magazyn kluczy, jeśli magazyn jest w trakcie przeczyszczania i ochrony. Wykonaj kroki opisane w sekcji odzyskiwanie magazynu kluczy. <br>W przypadku upływu czasu trwania ochrony przed przeczyszczeniem nie można odzyskać magazynu kluczy. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| Jeśli klucz integralności kanału w Azure Key Vault został usunięty, a następnie spróbuj wykonać wszystkie operacje, które obejmują szyfrowanie, na przykład: **Dodaj użytkownika**, **Dodaj udział**, **Skonfiguruj obliczenia** — ten błąd zostanie wyświetlony. <br> ![Błąd magazynu kluczy 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | Jeśli klucz integralności kanału w magazynie kluczy jest usuwany, ale nadal trwa przeczyszczanie, wykonaj kroki opisane w [cofnięciu usunięcia klucza magazynu kluczy](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval). <br>Jeśli czas trwania ochrony przed przeczyszczeniem upłynął, a jeśli masz kopię zapasową klucza, można przywrócić z kopii zapasowej, nie można odzyskać klucza. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| Jeśli generowanie klucza aktywacji zakończy się niepowodzeniem z powodu błędu, zostanie wyświetlony następujący błąd. W powiadomieniu znajdują się dodatkowe szczegółowe informacje. <br> ![Błąd magazynu kluczy 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Upewnij się, że porty i adresy URL określone w oknie [dostępu Azure Key Vault za zaporą](../key-vault/general/access-behind-firewall.md) , są otwarte w zaporze w celu uzyskania dostępu do Key Vault. Poczekaj kilka minut i spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzał, skontaktuj się z pomoc techniczna firmy Microsoft. |
| Jeśli użytkownik ma uprawnienia tylko do odczytu, użytkownik nie może wygenerować klucza aktywacji i ten błąd jest wyświetlany. <br> ![Błąd magazynu kluczy 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Może to być spowodowane tym, że nie masz odpowiedniego dostępu lub  *Microsoft. magazyn* kluczy nie jest zarejestrowany.<li>Upewnij się, że masz uprawnienia właściciela lub współautora na poziomie grupy zasobów używanym dla zasobu usługi Azure Stack Edge.</li><li>Upewnij się, że dostawca zasobów Microsoft. kluczy jest zarejestrowany. Aby zarejestrować dostawcę zasobów, przejdź do subskrypcji używanej na potrzeby Azure Stackego zasobu brzegowego. Przejdź do pozycji **dostawcy zasobów**, wyszukaj ciąg *Microsoft.* , a następnie wybierz pozycję i **zarejestruj** się.</li> |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [rozwiązywania problemów z urządzeniami](azure-stack-edge-gpu-troubleshoot.md).
