---
title: Żądanie dostępu just in time
description: Opisuje, w jaki sposób wydawcy Azure Managed Applications żądania dostępu just in Time do zarządzanej aplikacji.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651386"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Włącz i Żądaj dostępu just in Time do Azure Managed Applications

Odbiorcy aplikacji zarządzanej mogą być zniechęcić w celu udzielenia użytkownikowi stałego dostępu do zarządzanej grupy zasobów. Jako Wydawca aplikacji Menedżera możesz preferować, aby klienci wiedzieli się dokładnie, gdy trzeba uzyskać dostęp do zarządzanych zasobów. Aby zapewnić konsumentom większą kontrolę nad udzieleniem dostępu do zarządzanych zasobów, Azure Managed Applications zapewnia funkcję o nazwie dostęp just-in-Time (JIT), która jest obecnie dostępna w wersji zapoznawczej.

Dostęp JIT umożliwia żądanie dostępu z podwyższonym poziomem uprawnień do zasobów aplikacji zarządzanej w celu rozwiązywania problemów lub konserwacji. Zawsze masz dostęp tylko do odczytu do zasobów, ale przez określony okres możesz mieć większy dostęp.

Przepływ pracy do udzielania dostępu to:

1. Dodaj aplikację zarządzaną do portalu Marketplace i określ, że dostęp JIT jest dostępny.

1. Podczas wdrażania klient umożliwia dostęp JIT dla tego wystąpienia aplikacji zarządzanej.

1. Po wdrożeniu klient może zmienić ustawienia dostępu JIT.

1. Wysłano żądanie dostępu, gdy trzeba rozwiązać problemy lub zaktualizować zarządzane zasoby.

1. Odbiorca zatwierdza Twoje żądanie.

Ten artykuł koncentruje się na akcjach podejmowanych przez wydawców w celu włączenia dostępu JIT i przesyłania żądań. Aby dowiedzieć się więcej o zatwierdzaniu żądań dostępu JIT, zobacz [zatwierdzanie dostępu just in Time w Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Dodaj krok dostępu JIT do interfejsu użytkownika

CreateUiDefinition.jsw pliku jest dokładnie taki sam jak plik interfejsu użytkownika tworzony na potrzeby stałego dostępu, z wyjątkiem tego, że należy uwzględnić krok, który umożliwia klientom włączenie dostępu JIT. Aby dowiedzieć się więcej o publikowaniu pierwszej oferty zarządzanej aplikacji w portalu Azure Marketplace, zobacz [Azure Managed Applications w portalu Marketplace](publish-marketplace-app.md).

Aby zapewnić obsługę funkcji JIT dla oferty, Dodaj następującą zawartość do CreateUiDefinition.jsw pliku:

W obszarze "kroki":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
W obszarze "dane wyjściowe":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> Dostęp JIT jest w wersji zapoznawczej. Schemat konfiguracji JIT może ulec zmianie w przyszłych iteracjach.

## <a name="enable-jit-access"></a>Włącz dostęp JIT

Podczas definiowania oferty w portalu Marketplace upewnij się, że włączono dostęp JIT.

1. Zaloguj się do [portalu publikowania partnerów w chmurze](https://cloudpartner.azure.com).

1. Podaj wartości, aby opublikować zarządzaną aplikację w portalu Marketplace. Wybierz opcję **tak** , aby **włączyć dostęp JIT?**

   ![Włącz dostęp just in Time](./media/request-just-in-time-access/marketplace-enable.png)

Dodano krok konfiguracji JIT do interfejsu użytkownika i włączono dostęp JIT w ofercie Marketplace. Gdy użytkownicy wdrażają aplikację zarządzaną, mogą [włączyć dostęp JIT dla swojego wystąpienia](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Żądanie dostępu

Gdy musisz uzyskać dostęp do zarządzanych zasobów konsumenta, Wyślij żądanie dotyczące określonej roli, godziny i czasu trwania. Użytkownik musi następnie zatwierdzić żądanie.

Aby wysłać żądanie dostępu JIT:

1. Wybierz **dostęp JIT** dla aplikacji zarządzanej, do której chcesz uzyskać dostęp.

1. Wybierz pozycję **kwalifikujące się role**i wybierz pozycję **Aktywuj** w kolumnie Akcja dla wybranej roli.

   ![Aktywuj żądanie dostępu](./media/request-just-in-time-access/send-request.png)

1. W formularzu **Uaktywnij rolę** wybierz czas rozpoczęcia i czas trwania roli, która ma być aktywna. Wybierz pozycję **Aktywuj** , aby wysłać żądanie.

   ![Aktywuj dostęp](./media/request-just-in-time-access/activate-access.png) 

1. Wyświetl powiadomienia, aby zobaczyć, że nowe żądanie JIT zostało pomyślnie wysłane do konsumenta.

   ![Powiadomienie](./media/request-just-in-time-access/in-progress.png)

   Teraz musisz poczekać, aż konsument [zatwierdzi Twoje żądanie](approve-just-in-time-access.md#approve-requests).

1. Aby wyświetlić stan wszystkich żądań JIT dla aplikacji zarządzanej, wybierz pozycję **dostęp JIT** i **historię żądań**.

   ![Wyświetl stan](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Znane problemy

Identyfikator podmiotu zabezpieczeń konta żądającego dostępu JIT musi być jawnie uwzględniony w definicji aplikacji zarządzanej. Konto nie może zostać dołączone tylko za pomocą grupy określonej w pakiecie. To ograniczenie zostanie naprawione w przyszłym wydaniu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zatwierdzaniu żądań dostępu JIT, zobacz [zatwierdzanie dostępu just in Time w Azure Managed Applications](approve-just-in-time-access.md).
