---
title: Odbieranie powiadomień magazynu kluczy i reagowanie na nie za pomocą Azure Event Grid
description: Dowiedz się, jak zintegrować Key Vault z Azure Event Grid.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e46161812d122a1d5647e8589c58f9528578b878
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749838"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Odbieranie powiadomień magazynu kluczy i reagowanie na nie za pomocą Azure Event Grid

Azure Key Vault z usługą Azure Event Grid umożliwia powiadomienie użytkownika, gdy stan klucza tajnego przechowywanego w magazynie kluczy uległ zmianie. Aby uzyskać omówienie tej funkcji, zobacz [Monitorowanie Key Vault za pomocą Event Grid.](event-grid-overview.md)

W tym przewodniku opisano sposób odbierania powiadomień Key Vault za pośrednictwem Event Grid oraz reagowania na zmiany stanu za pośrednictwem Azure Automation.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Magazyn kluczy w ramach subskrypcji platformy Azure. Nowy magazyn kluczy można szybko utworzyć, korzystając z kroków instrukcje z tematu Set and retrieve a secret from Azure Key Vault using Azure CLI (Ustawianie i pobieranie klucza tajnego z usługi [Azure Key Vault interfejsu wiersza polecenia platformy Azure).](../secrets/quick-create-cli.md)

## <a name="concepts"></a>Pojęcia

Event Grid to usługa obsługi zdarzeń dla chmury. Zgodnie z instrukcjami w tym przewodniku zasubskrybujesz zdarzenia na Key Vault i przekierujesz zdarzenia do usługi Automation. Gdy jeden z wpisów tajnych w magazynie kluczy wygaśnie, Event Grid zostanie powiadomiony o zmianie stanu i zostanie wysłany wpis POST protokołu HTTP do punktu końcowego. Element web hook wyzwala następnie wykonywanie automatyzacji skryptu programu PowerShell.

![Schemat blokowy HTTP POST](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Tworzenie konta usługi Automation

Utwórz konto usługi Automation za pomocą [Azure Portal](https://portal.azure.com):

1.  Przejdź do portal.azure.com i zaloguj się do subskrypcji.

1.  W polu wyszukiwania wprowadź wartość **Konta usługi Automation.**

1.  W sekcji **Usługi** na liście rozwijanej na pasku wyszukiwania wybierz pozycję **Konta usługi Automation.**

1.  Wybierz pozycję **Dodaj**.

    ![Okienko konta usługi Automation](../media/event-grid-tutorial-2.png)

1.  Wprowadź wymagane informacje w okienku **Dodawanie konta usługi Automation,** a następnie wybierz pozycję **Utwórz**.

## <a name="create-a-runbook"></a>Tworzenie elementu runbook

Gdy konto usługi Automation będzie gotowe, utwórz go.

![Tworzenie interfejsu użytkownika elementów Runbook](../media/event-grid-tutorial-3.png)

1.  Wybierz właśnie utworzone konto usługi Automation.

1.  Wybierz pozycję **Runbook w** obszarze **Automatyzacja procesów.**

1.  Wybierz **pozycję Utwórz runbook.**

1.  Nazwij swój typ runbook **i wybierz program PowerShell.**

1.  Wybierz utworzony przez Ciebie podręcznik Runbook, a następnie wybierz **przycisk** Edytuj.

1.  Wprowadź następujący kod (w celach testowych) i wybierz **przycisk Publikuj.** Ta akcja zwraca wynik odebranego żądania POST.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Interfejs użytkownika publikowania elementów Runbook](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>Tworzeniehook webhook

Utwórz webhook, aby wyzwolić nowo utworzony podręcznik Runbook.

1.  Wybierz **pozycję Webhook** w **sekcji Zasoby** właśnie opublikowanego runbook.

1.  Wybierz **pozycję Dodaj pozycję Webhook.**

    ![Przycisk Dodaj pozycję Webhook](../media/event-grid-tutorial-5.png)

1.  Wybierz **pozycję Utwórz nowy pozycję Webhook.**

1. Nadaj elementowi webhook nazwę, ustaw datę wygaśnięcia i skopiuj adres URL.

    > [!IMPORTANT] 
    > Nie można wyświetlić adresu URL po jego utworzeniu. Pamiętaj, aby zapisać kopię w bezpiecznej lokalizacji, w której można uzyskać do niego dostęp w pozostałej części tego przewodnika.

1. Wybierz **pozycję Parametry i uruchom ustawienia,** a następnie wybierz przycisk **OK.** Nie wprowadzaj żadnych parametrów. Spowoduje to włączenie **przycisku** Utwórz.

1. Wybierz **przycisk OK,** a następnie wybierz **pozycję Utwórz.**

    ![Tworzenie nowego interfejsu użytkownika elementów webhook](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

Utwórz Event Grid subskrypcji za pośrednictwem [Azure Portal](https://portal.azure.com).

1.  Przejdź do magazynu kluczy i wybierz **kartę** Zdarzenia.

    ![Karta Zdarzenia w Azure Portal](../media/event-grid-tutorial-7.png)

1.  Wybierz przycisk **Subskrypcja** zdarzeń.

1.  Utwórz opisową nazwę subskrypcji.

1.  Wybierz **Event Grid schemacie**.

1.  **Zasób tematu** powinien być magazynem kluczy, który chcesz monitorować pod względu na zmiany stanu.

1.  W **przypadku opcji Filtruj do typów zdarzeń** pozostaw zaznaczone wszystkie opcje **(9).**

1.  W polu **Typ punktu końcowego** wybierz pozycję **Element webhook**.

1.  Wybierz **pozycję Wybierz punkt końcowy.** W okienku nowego kontekstu wklej adres URL webhook z kroku [Tworzenie urządzenia webhook](#create-a-webhook) w polu **Punkt końcowy subskrybenta.**

1.  Wybierz **pozycję Potwierdź** wybór w okienku kontekstu.

1.  Wybierz przycisk **Utwórz**.

    ![Tworzenie subskrypcji zdarzeń](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

Sprawdź, czy Event Grid jest prawidłowo skonfigurowana. W tym teście przyjęto założenie, że masz subskrypcję powiadomienia "Utworzono nową wersję klucza tajnego" w temacie Tworzenie subskrypcji usługi [Event Grid](#create-an-event-grid-subscription)oraz że masz uprawnienia niezbędne do utworzenia nowej wersji klucza tajnego w magazynie kluczy.

![Testowanie konfiguracji Event Grid subskrypcji](../media/event-grid-tutorial-9.png)

![Okienko tworzenia i tworzenia tajnego](../media/event-grid-tutorial-10.png)

1.  Przejdź do magazynu kluczy na stronie Azure Portal.

1.  Utwórz nowy klucz tajny. W celach testowych ustaw datę wygaśnięcia na następny dzień.

1.  Na karcie **Zdarzenia** w magazynie kluczy wybierz Event Grid utworzoną subskrypcję.

1.  W **obszarze Metryki** sprawdź, czy zdarzenie zostało przechwycone. Oczekiwane są dwa zdarzenia: SecretNewVersion i SecretNearExpiry. Te zdarzenia weryfikują Event Grid pomyślnie przechwyciły zmianę stanu klucza tajnego w magazynie kluczy.

    ![Okienko Metryki: sprawdzanie przechwyconych zdarzeń](../media/event-grid-tutorial-11.png)

1.  Przejdź do swojego konta usługi Automation.

1.  Wybierz **kartę Runbook,** a następnie wybierz utworzony przez Ciebie.

1.  Wybierz **kartę Webhook** i upewnij się, że sygnatura czasowa "ostatnio wyzwolona" znajduje się w ciągu 60 sekund od utworzenia nowego tajnego. Ten wynik potwierdza, Event Grid wpis POST do element webhook ze szczegółami zdarzenia zmiany stanu w magazynie kluczy oraz że element webhook został wyzwolony.

    ![Karta Webhook, sygnatura czasowa ostatniego wyzwolenia](../media/event-grid-tutorial-12.png)

1. Wróć do swojego runbooka i wybierz **kartę** Przegląd.

1. Przyjrzyj się **liście Ostatnie** zadania. Powinno być widać, że zadanie zostało utworzone i że stan jest ukończony. Potwierdza to, że wyzwolony przez webhook runbook rozpoczynał wykonywanie skryptu.

    ![Lista ostatnich zadań elementów webhook](../media/event-grid-tutorial-13.png)

1. Wybierz ostatnie zadanie i przyjrzyj się żądaniu POST, które zostało wysłane z Event Grid do webhook. Sprawdź dane JSON i upewnij się, że parametry magazynu kluczy i typu zdarzenia są poprawne. Jeśli parametr "typ zdarzenia" w obiekcie JSON pasuje do zdarzenia, które wystąpiło w magazynie kluczy (w tym przykładzie Microsoft.KeyVault.SecretNearExpiry), test zakończył się pomyślnie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="you-cant-create-an-event-subscription"></a>Nie można utworzyć subskrypcji zdarzeń

Ponownie zarejestruj Event Grid i dostawcę magazynu kluczy u dostawców zasobów subskrypcji platformy Azure. Zobacz [Azure resource providers and types (Dostawcy zasobów i typy zasobów platformy Azure).](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="next-steps"></a>Następne kroki

Gratulacje! Jeśli wszystkie te kroki zostały wykonane prawidłowo, możesz teraz programowo reagować na zmiany stanu wpisów tajnych przechowywanych w magazynie kluczy.

Jeśli używasz systemu opartego na sondowaniach do wyszukiwania zmian stanu wpisów tajnych w magazynach kluczy, możesz teraz rozpocząć korzystanie z tej funkcji powiadomień. Możesz również zastąpić skrypt testowy w swoim runbook kodem, aby programowo odnowić wpisy tajne, gdy nie będą one wygasać.

Więcej informacji:


- Omówienie: [Monitorowanie Key Vault za pomocą Azure Event Grid](event-grid-overview.md)
- Dzieje się tak: [odbieranie wiadomości e-mail po zmianie klucza tajnego magazynu kluczy](event-grid-logicapps.md)
- [Azure Event Grid zdarzeń dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Azure Key Vault omówienie](overview.md)
- [Omówienie usługi Azure Event Grid](../../event-grid/overview.md)
- [Azure Automation omówienie](../../automation/index.yml)
