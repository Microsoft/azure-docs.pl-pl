---
title: Odbieraj powiadomienia magazynu kluczy i odpowiadaj na nie za pomocą Azure Event Grid
description: Dowiedz się, jak zintegrować Key Vault z Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: d7bb697879f40b45c886cd90bbb1e34906d35f66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96187378"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Odbieraj powiadomienia magazynu kluczy i odpowiadaj na nie za pomocą Azure Event Grid

Integracja Azure Key Vault z Azure Event Grid umożliwia użytkownikowi powiadomienia o zmianie stanu wpisu tajnego przechowywanego w magazynie kluczy. Aby zapoznać się z omówieniem tej funkcji, zobacz [monitorowanie Key Vault z Event Grid](event-grid-overview.md).

W tym przewodniku opisano, jak odbierać powiadomienia Key Vault przy użyciu Event Grid i jak reagować na zmiany stanu przy użyciu Azure Automation.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Magazyn kluczy w ramach subskrypcji platformy Azure. Nowy magazyn kluczy można szybko utworzyć, wykonując kroki z [zestawu i pobierając klucz tajny z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](../secrets/quick-create-cli.md).

## <a name="concepts"></a>Pojęcia

Event Grid to usługa obsługi zdarzeń dla chmury. Wykonując kroki opisane w tym przewodniku, zasubskrybujesz zdarzenia dotyczące Key Vault i kierowania zdarzeń do automatyzacji. Gdy jeden z wpisów tajnych w magazynie kluczy wkrótce wygaśnie, Event Grid zostanie powiadomiona o zmianie stanu i spowoduje WYSŁANie żądania HTTP do punktu końcowego. Element webhook wyzwala wykonywanie skryptu programu PowerShell w usłudze Automation.

![Schemat blokowy HTTP POST](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Tworzenie konta usługi Automation

Utwórz konto usługi Automation za pomocą [Azure Portal](https://portal.azure.com):

1.  Przejdź do portal.azure.com i zaloguj się do swojej subskrypcji.

1.  W polu wyszukiwania wprowadź **konta usługi Automation**.

1.  W sekcji **usługi** listy rozwijanej na pasku wyszukiwania wybierz pozycję **konta usługi Automation**.

1.  Wybierz pozycję **Dodaj**.

    ![Okienko kont usługi Automation](../media/event-grid-tutorial-2.png)

1.  Wprowadź wymagane informacje w okienku **Dodawanie konta usługi Automation** , a następnie wybierz pozycję **Utwórz**.

## <a name="create-a-runbook"></a>Tworzenie elementu runbook

Po przygotowaniu konta usługi Automation Utwórz element Runbook.

![Tworzenie interfejsu użytkownika elementu Runbook](../media/event-grid-tutorial-3.png)

1.  Wybierz właśnie utworzone konto usługi Automation.

1.  Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.

1.  Wybierz pozycję **Utwórz element Runbook**.

1.  Nadaj nazwę elementowi Runbook i wybierz pozycję **PowerShell** jako typ elementu Runbook.

1.  Wybierz utworzony element Runbook, a następnie wybierz przycisk **Edytuj** .

1.  Wprowadź następujący kod (na potrzeby testowania) i wybierz przycisk **Publikuj** . Ta akcja zwraca wynik odebranego żądania POST.

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

![Publikowanie interfejsu użytkownika elementu Runbook](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>Tworzenie elementu webhook

Utwórz element webhook, aby wyzwolić nowo utworzony element Runbook.

1.  Wybierz elementy **webhook** z sekcji **resources** elementu Runbook, który właśnie został opublikowany.

1.  Wybierz pozycję **Dodaj element webhook**.

    ![Dodaj przycisk elementu webhook](../media/event-grid-tutorial-5.png)

1.  Wybierz pozycję **Utwórz nowy element webhook**.

1. Nadaj nazwę elementowi webhook, ustaw datę wygaśnięcia i skopiuj adres URL.

    > [!IMPORTANT] 
    > Nie można wyświetlić adresu URL po jego utworzeniu. Pamiętaj, aby zapisać kopię w bezpiecznej lokalizacji, w której możesz uzyskać do niej dostęp w pozostałej części tego przewodnika.

1. Wybierz **Parametry i Uruchom ustawienia** , a następnie wybierz przycisk **OK**. Nie wprowadzaj żadnych parametrów. Spowoduje to włączenie przycisku **Utwórz** .

1. Wybierz przycisk **OK** , a następnie wybierz pozycję **Utwórz**.

    ![Utwórz nowy interfejs użytkownika elementu webhook](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

Utwórz subskrypcję Event Grid przy użyciu [Azure Portal](https://portal.azure.com).

1.  Przejdź do magazynu kluczy i wybierz kartę **zdarzenia** .

    ![Karta zdarzenia w Azure Portal](../media/event-grid-tutorial-7.png)

1.  Wybierz przycisk **subskrypcja zdarzeń** .

1.  Utwórz opisową nazwę subskrypcji.

1.  Wybierz **Event Grid schemat**.

1.  **Zasób tematu** powinien być magazynem kluczy, który ma być monitorowany pod kątem zmian stanu.

1.  W przypadku **filtru do typów zdarzeń** pozostaw wszystkie wybrane opcje (**9 wybrane**).

1.  W polu **Typ punktu końcowego** wybierz pozycję **Element webhook**.

1.  Wybierz **pozycję Wybierz punkt końcowy**. W okienku nowy kontekst Wklej adres URL elementu webhook z kroku [Utwórz element webhook](#create-a-webhook) do pola **punkt końcowy subskrybenta** .

1.  W okienku kontekstowym wybierz pozycję **Potwierdź wybór** .

1.  Wybierz przycisk **Utwórz**.

    ![Utwórz subskrypcję zdarzeń](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

Sprawdź, czy subskrypcja Event Grid została prawidłowo skonfigurowana. W tym teście przyjęto założenie, że zasubskrybowano powiadomienie "wpis tajny nowej wersji utworzone" w [subskrypcji tworzenia Event Grid](#create-an-event-grid-subscription)i że masz uprawnienia niezbędne do utworzenia nowej wersji wpisu tajnego w magazynie kluczy.

![Konfiguracja testu subskrypcji Event Grid](../media/event-grid-tutorial-9.png)

![Okienko Utwórz-a-Secret](../media/event-grid-tutorial-10.png)

1.  Przejdź do magazynu kluczy na Azure Portal.

1.  Utwórz nowy wpis tajny. Dla celów testowych Ustaw datę wygaśnięcia na następny dzień.

1.  Na karcie **zdarzenia** w magazynie kluczy Wybierz utworzoną subskrypcję Event Grid.

1.  W obszarze **metryki** Sprawdź, czy zdarzenie zostało przechwycone. Oczekiwane są dwa zdarzenia: SecretNewVersion i SecretNearExpiry. Te zdarzenia sprawdzają, czy Event Grid pomyślnie przechwycić zmianę stanu wpisu tajnego w magazynie kluczy.

    ![Okienko metryk: sprawdzanie przechwytywanych zdarzeń](../media/event-grid-tutorial-11.png)

1.  Przejdź do konta usługi Automation.

1.  Wybierz kartę **elementy Runbook** , a następnie wybierz utworzony element Runbook.

1.  Wybierz kartę elementy **webhook** i upewnij się, że sygnatura czasowa "Ostatnia wyzwolona" mieści się w przedziale od 60 sekund od momentu utworzenia nowego klucza tajnego. Ten wynik pozwala potwierdzić, że Event Grid wprowadził wpis do elementu webhook ze szczegółowymi informacjami o stanie zmiany stanu w magazynie kluczy i wyzwoleniem elementu webhook.

    ![Karta elementy webhook, Ostatnia wyzwolona sygnatura czasowa](../media/event-grid-tutorial-12.png)

1. Wróć do elementu Runbook i wybierz kartę **Przegląd** .

1. Zapoznaj się z listą **ostatnich zadań** . Powinno się zobaczyć, że zadanie zostało utworzone i że stan jest zakończony. Pozwala to upewnić się, że element webhook wyzwolił elementu Runbook, aby rozpocząć wykonywanie jego skryptu.

    ![Lista ostatnich zadań elementu webhook](../media/event-grid-tutorial-13.png)

1. Wybierz ostatnie zadanie i sprawdź żądanie POST wysłane z Event Grid do elementu webhook. Sprawdź kod JSON i upewnij się, że parametry magazynu kluczy i typu zdarzenia są poprawne. Jeśli parametr "typ zdarzenia" w obiekcie JSON pasuje do zdarzenia, które wystąpiło w magazynie kluczy (w tym przykładzie Microsoft. Key. SecretNearExpiry), test zakończył się pomyślnie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="you-cant-create-an-event-subscription"></a>Nie można utworzyć subskrypcji zdarzeń

Zarejestruj Event Grid i dostawcę magazynu kluczy w dostawcach zasobów subskrypcji platformy Azure. Zobacz [dostawcy zasobów platformy Azure i ich typy](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Następne kroki

Gratulacje! Jeśli wykonano wszystkie te kroki, możesz teraz przystąpić do programistycznego reagowania na zmiany stanu wpisów tajnych przechowywanych w magazynie kluczy.

Jeśli używasz systemu opartego na sondowaniu do wyszukiwania zmian stanu wpisów tajnych w magazynach kluczy, możesz teraz zacząć korzystać z tej funkcji powiadomień. Możesz również zastąpić skrypt testowy w elemencie Runbook za pomocą kodu, aby programowo odnowić wpisy tajne, gdy niedługo wygasną.

Więcej informacji:


- Przegląd: [monitorowanie Key Vault z Azure Event Grid](event-grid-overview.md)
- Instrukcje: [otrzymywanie wiadomości e-mail po zmianie wpisu tajnego magazynu kluczy](event-grid-logicapps.md)
- [Schemat zdarzeń Azure Event Grid dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Przegląd Azure Key Vault](overview.md)
- [Omówienie usługi Azure Event Grid](../../event-grid/overview.md)
- [Przegląd Azure Automation](../../automation/index.yml)
