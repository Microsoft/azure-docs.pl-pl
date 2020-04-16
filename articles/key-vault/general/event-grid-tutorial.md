---
title: Odbieranie powiadomień o przechowalni kluczy i odpowiadanie na nie za pomocą usługi Azure Event Grid
description: Dowiedz się, jak zintegrować usługę Key Vault z usługą Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 300abc8ca6798866295c865e68bf5ec24a00cf5f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423256"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Odbieranie powiadomień o przechowalni kluczy i odpowiadanie na nie za pomocą usługi Azure Event Grid (wersja zapoznawcza)

Integracja usługi Azure Key Vault z usługą Azure Event Grid (obecnie w wersji zapoznawczej) umożliwia powiadamianie użytkowników po zmianie stanu klucza tajnego przechowywanego w magazynie kluczy. Aby uzyskać omówienie tej funkcji, zobacz [Monitorowanie magazynu kluczy za pomocą siatki zdarzeń](event-grid-overview.md).

W tym przewodniku opisano sposób odbierania powiadomień usługi Key Vault za pośrednictwem usługi Event Grid oraz reagowania na zmiany stanu za pośrednictwem usługi Azure Automation.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
- Magazyn kluczy w subskrypcji platformy Azure. Możesz szybko utworzyć nowy magazyn kluczy, wykonując kroki opisane w [obszarze Ustaw i pobierając klucz tajny z usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure.](../secrets/quick-create-cli.md)

## <a name="concepts"></a>Pojęcia

Usługa Event Grid to usługa eventingowa dla chmury. Wykonując kroki opisane w tym przewodniku, zasubskrybuj zdarzenia dla usługi Key Vault i rozsyłasz zdarzenia do automatyzacji. Gdy jeden z wpisów tajnych w magazynie kluczy ma wygasnąć, usługa Event Grid jest powiadamiana o zmianie stanu i powoduje wyświetlenie protokołu HTTP POST do punktu końcowego. Hak sieci web następnie wyzwala wykonanie automatyzacji skryptu programu PowerShell.

![Schemat blokowy HTTP POST](../media/image1.png)

## <a name="create-an-automation-account"></a>Tworzenie konta usługi Automation

Tworzenie konta automatyzacji za pośrednictwem [portalu Azure:](https://portal.azure.com)

1.  Przejdź do portal.azure.com i zaloguj się do subskrypcji.

1.  W polu wyszukiwania wprowadź konto **automatyzacji**.

1.  W sekcji **Usługi** listy rozwijanej na pasku wyszukiwania wybierz pozycję **Konta automatyzacji**.

1.  Wybierz pozycję **Dodaj**.

    ![Okienko Kont automatyzacji](../media/image2.png)

1.  Wprowadź wymagane informacje w okienku **Dodawanie konta automatyzacji,** a następnie wybierz pozycję **Utwórz**.

## <a name="create-a-runbook"></a>Tworzenie elementu runbook

Gdy konto automatyzacji będzie gotowe, utwórz element runbook.

![Tworzenie interfejsu użytkownika umeryc](../media/image3.png)

1.  Wybierz właśnie utworzone konto automatyzacji.

1.  Wybierz **elementy runbook w** obszarze **Automatyzacja procesów**.

1.  Wybierz **pozycję Utwórz fragment .**

1.  Nadaj nazwę emuńiku i wybierz program **PowerShell** jako typ przewodniczącego.

1.  Wybierz utworzony projekt runbook, a następnie wybierz przycisk **Edytuj.**

1.  Wprowadź poniższy kod (do celów testowych) i wybierz przycisk **Publikuj.** Ta akcja zwraca wynik odebranego żądania POST.

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

![Publikowanie interfejsu użytkownika ekwidu](../media/image4.png)

## <a name="create-a-webhook"></a>Tworzenie elementu webhook

Utwórz element webhook, aby wyzwolić nowo utworzony element runbook.

1.  Wybierz **pozycję Webhooks** z sekcji **Zasoby** elementu runbook, który właśnie opublikowano.

1.  Wybierz **pozycję Dodaj element Webhook**.

    ![Przycisk Dodaj element Webhook](../media/image5.png)

1.  Wybierz **pozycję Utwórz nowy element Webhook**.

1. Nazwij element webhook, ustaw datę wygaśnięcia i skopiuj adres URL.

    > [!IMPORTANT] 
    > Po utworzeniu adresu URL nie można go wyświetlić. Upewnij się, że zapisujesz kopię w bezpiecznej lokalizacji, w której można uzyskać do niej dostęp do pozostałej części tego przewodnika.

1. Wybierz **pozycję Parametry i uruchom ustawienia,** a następnie wybierz przycisk **OK**. Nie wprowadzaj żadnych parametrów. Spowoduje to włączenie przycisku **Utwórz.**

1. Wybierz **przycisk OK,** a następnie wybierz pozycję **Utwórz**.

    ![Tworzenie nowego interfejsu użytkownika elementu webhook](../media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

Utwórz subskrypcję usługi Event Grid za pośrednictwem [witryny Azure portal](https://portal.azure.com).

1.  Przejdź do magazynu kluczy i wybierz kartę **Wydarzenia.** Jeśli go nie widzisz, upewnij się, że używasz [wersji zapoznawczej portalu](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![Karta Zdarzenia w witrynie Azure portal](../media/image7.png)

1.  Wybierz przycisk **Subskrypcja zdarzeń.**

1.  Utwórz opisową nazwę subskrypcji.

1.  Wybierz pozycję **Schemat siatki zdarzeń**.

1.  **Zasób tematu** powinien być magazynem kluczy, który ma być monitorowany pod kątem zmian stanu.

1.  W przypadku **filtruj do typów zdarzeń**pozostaw wszystkie zaznaczone opcje (**9 zaznaczonych**opcji ).

1.  W polu **Typ punktu końcowego** wybierz pozycję **Element webhook**.

1.  Wybierz **pozycję Wybierz punkt końcowy**. W nowym okienku kontekstu wklej adres URL elementu webhook z kroku Utwórz element [webhook](#create-a-webhook) w polu **Punkt końcowy subskrybenta.**

1.  Wybierz **pozycję Potwierdź zaznaczenie** w okienku kontekstu.

1.  Wybierz pozycję **Utwórz**.

    ![Tworzenie subskrypcji zdarzeń](../media/image8.png)

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

Sprawdź, czy subskrypcja usługi Event Grid jest poprawnie skonfigurowana. Ten test zakłada, że zasubskrybowałeś powiadomienie "Tajna nowa wersja utworzona" w [subskrypcji Utwórz siatkę zdarzeń](#create-an-event-grid-subscription)i masz uprawnienia niezbędne do utworzenia nowej wersji klucza tajnego w magazynie kluczy.

![Testowanie konfiguracji subskrypcji usługi Event Grid](../media/image9.png)

![Okienko Utwórz klucz tajny](../media/image10.png)

1.  Przejdź do magazynu kluczy w witrynie Azure portal.

1.  Utwórz nowy klucz tajny. W celu przeprowadzenia testów ustaw datę wygaśnięcia na następny dzień.

1.  Na karcie **Zdarzenia** w magazynie kluczy wybierz utworzoną subskrypcję usługi Event Grid.

1.  W **obszarze Metryki**sprawdź, czy zdarzenie zostało przechwycone. Oczekuje się dwóch zdarzeń: SecretNewVersion i SecretNearExpiry. Te zdarzenia sprawdzają, czy usługa Event Grid pomyślnie przechwyciła zmianę stanu klucza tajnego w magazynie kluczy.

    ![Okienko Metryki: sprawdzanie przechwyconych zdarzeń](../media/image11.png)

1.  Przejdź do swojego konta Automatyzacja.

1.  Wybierz kartę **Księgi śmięty,** a następnie wybierz utworzony projekt runbook.

1.  Wybierz kartę **Webhooks** i upewnij się, że sygnatura czasowa "ostatnio wyzwalane" znajduje się w ciągu 60 sekund od utworzenia nowego klucza tajnego. Ten wynik potwierdza, że usługa Event Grid dokonała postu do elementu webhook ze szczegółami zdarzenia zmiany stanu w magazynie kluczy i że element webhook został wyzwolony.

    ![Karta Webhooks, ostatnia wyzwalana sygnatura czasowa](../media/image12.png)

1. Wróć do ego księgi runbook i wybierz kartę **Przegląd.**

1. Spójrz na listę **Ostatnie zadania.** Powinno się zobaczyć, że zadanie zostało utworzone i że stan został ukończony. Potwierdza to, że element webhook wyzwolił element runbook, aby rozpocząć wykonywanie jego skryptu.

    ![Lista ostatnich ofert pracy elementu Webhook](../media/image13.png)

1. Wybierz ostatnie zadanie i spójrz na żądanie POST, które zostało wysłane z siatki zdarzeń do elementu webhook. Sprawdź JSON i upewnij się, że parametry magazynu kluczy i typu zdarzenia są poprawne. Jeśli parametr "typ zdarzenia" w obiekcie JSON jest zgodny ze zdarzeniem, które wystąpiło w magazynie kluczy (w tym przykładzie Microsoft.KeyVault.SecretNearExpiry), test zakończył się pomyślnie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="you-cant-create-an-event-subscription"></a>Nie można utworzyć subskrypcji wydarzenia

Ponowne rejestrowanie usługi Event Grid i dostawcy magazynu kluczy w dostawcach zasobów subskrypcji platformy Azure. Zobacz [dostawców i typy zasobów platformy Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Następne kroki

Gratulacje! Jeśli poprawnie po wszystkie te kroki, jesteś teraz gotowy do programowo reagować na zmiany stanu wpisów tajnych przechowywanych w magazynie kluczy.

Jeśli używasz systemu opartego na sondowaniu do wyszukiwania zmian stanu wpisów tajnych w magazynach kluczy, możesz teraz rozpocząć korzystanie z tej funkcji powiadomień. Można również zastąpić skrypt testowy w uruchomieniu kodem, aby programowo odnowić swoje wpisy tajne, gdy wkrótce wygasną.

Więcej informacji:


- Omówienie: [Monitorowanie magazynu kluczy za pomocą usługi Azure Event Grid (wersja zapoznawcza)](event-grid-overview.md)
- Jak: [Odbieranie wiadomości e-mail po zmianie klucza tajnego magazynu](event-grid-logicapps.md)
- [Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Key Vault (wersja zapoznawcza)](../../event-grid/event-schema-key-vault.md)
- [Omówienie usługi Azure Key Vault](overview.md))
- [Omówienie usługi Azure Event Grid](../../event-grid/overview.md)
- [Omówienie usługi Azure Automation](../../automation/index.yml)
