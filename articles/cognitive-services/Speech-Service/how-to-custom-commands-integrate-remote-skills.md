---
title: 'Instrukcje: eksportowanie aplikacji poleceń niestandardowych jako umiejętności zdalnych — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak wyeksportować aplikację poleceń niestandardowych jako umiejętność
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 8c7cb1e9f39b1de7897da29467a607953b42bb24
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565742"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Eksportuj aplikację poleceń niestandardowych jako umiejętność zdalną

W tym artykule dowiesz się, jak wyeksportować aplikację poleceń niestandardowych jako umiejętność zdalną.

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class="checklist"]
> * [Zrozumienie umiejętności platformy bot Framework](https://aka.ms/speech/cc-skill-overview)
> * [Zrozumienie manifestu umiejętności](https://aka.ms/speech/cc-skill-manifest)
> * [Jak wywoływać umiejętność z bot Framework bot](https://aka.ms/speech/cc-skill-consumer)
> * Niestandardowa aplikacja poleceń niestandardowych. Jeśli nie masz żadnej aplikacji z poleceniami niestandardowymi, Wypróbuj program — [Szybki Start: Tworzenie asystenta głosowego za pomocą poleceń niestandardowych](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>Niestandardowe polecenia jako umiejętności zdalne
* Umiejętności platformy bot Framework są do ponownego użycia w przypadku rozmów konstrukcyjnych obejmujących konwersacje. dzięki temu można dodawać rozległe funkcje do bot w ciągu kilku minut. Aby dowiedzieć się więcej na ten temat, przejdź do [umiejętności bot Framework](https://microsoft.github.io/botframework-solutions/overview/skills/).
* Niestandardowa aplikacja poleceń może być eksportowana jako umiejętność. Takie umiejętności można następnie wywołać za pośrednictwem protokołu umiejętności zdalnych z bot Framework bot.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Skonfiguruj aplikację, aby była ujawniana jako umiejętność zdalna

### <a name="application-level-settings"></a>Ustawienia poziomu aplikacji
1. Na panelu po lewej stronie wybierz pozycję **Ustawienia**  >  **umiejętności zdalne**.
1. Ustaw opcję Włącz **włączone umiejętności zdalne** na wartość włączone.

### <a name="authentication-to-skills"></a>Uwierzytelnianie do umiejętności
1. Jeśli chcesz włączyć uwierzytelnianie, Dodaj identyfikatory aplikacji firmy Microsoft bot Framework botów, które chcesz skonfigurować do wywoływania aplikacji poleceń niestandardowych.
      > [!div class="mx-imgBorder"]
      > ![Dodawanie identyfikatora MSA do umiejętności](media/custom-commands/skill-add-msa-id.png)

1. Jeśli masz co najmniej jeden wpis dodany do listy, uwierzytelnianie zostanie włączone w aplikacji, a tylko dozwolony botów będzie mógł wywołać aplikację.
> [!TIP]
>  Aby wyłączyć uwierzytelnianie, Usuń wszystkie identyfikatory aplikacji firmy Microsoft z listy dozwolonych. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Włącz/Wyłącz polecenia, aby zostać ujawnione jako umiejętność

Istnieje możliwość wybrania poleceń, które mają zostać wyeksportowane za pośrednictwem umiejętności zdalnych.

1. Aby udostępnić polecenie za pomocą umiejętności, wybierz pozycję **Włącz nowe polecenie** w obszarze **polecenia Włącz dla umiejętności**.
1. Z listy rozwijanej wybierz polecenie, które ma zostać dodane.
1. Wybierz pozycję **Zapisz**.

### <a name="configure-triggering-utterances-for-commands"></a>Konfigurowanie wyzwalania wyrażenia długości dla poleceń
Polecenia niestandardowe wykorzystują przykładowe zdania, które są skonfigurowane dla poleceń w celu wygenerowania umiejętności wyzwalających wyrażenia długości. Te **wyzwalacze wyrażenia długości** zostaną użyte do wygenerowania [**manifestu umiejętności**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)sekcji **dyspozytora** .

Autor może chcieć kontrolować, które z **przykładowych zdań** są używane do generowania wyzwalających wyrażenia długości dla umiejętności.
1. Domyślnie wszystkie **przykłady wyzwalania** z polecenia zostaną uwzględnione w pliku manifestu.
1. Jeśli chcesz jawnie wyeliminować jeden z nich, wybierz opcję **Edytuj** ikonę w sekcji polecenie z **włączonych poleceń dla umiejętności** .
    > [!div class="mx-imgBorder"]
    > ![Edytuj włączone polecenie dla umiejętności](media/custom-commands/skill-edit-enabled-command.png)

1. Następnie na przykład zdania, które chcesz pominąć, **kliknij prawym przyciskiem myszy pozycję**  >  **Wyłącz przykładowe zdanie**.
    > [!div class="mx-imgBorder"]
    > ![Wyłącz przykłady](media/custom-commands/skill-disable-example-sentences.png)

1. Wybierz pozycję **Zapisz**.
1. Zobaczysz, że w tym oknie nie można dodać nowego przykładu. Jeśli jest to konieczne, przejdź do sekcji wyjście z ustawień i wybierz odpowiednie polecenie z **poleceń** zgodnie z opisem. W tym momencie można dodać nowy wpis w sekcji **przykładowe zdania** . Ta zmiana zostanie automatycznie odzwierciedlona w wartości ustawień umiejętności zdalnych dla polecenia.

> [!IMPORTANT]
> W przypadku gdy istniejące przykładowe zdania zawierają odwołania do **ciągu > dane katalogu** , te zdania zostaną automatycznie pominięte na liście umiejętności wyzwalające wyrażenia długości. 

## <a name="download-skill-manifest"></a>Pobierz manifest umiejętności
1. Po **opublikowaniu** aplikacji można pobrać plik manifestu umiejętności.
1. Użyj manifestu umiejętności, aby skonfigurować swoją Botę bot platformy do wywoływania w programie w celu uzyskania umiejętności poleceń niestandardowych.
> [!IMPORTANT]
> Aby można było pobrać manifest umiejętności, należy **opublikować** aplikację poleceń niestandardowych. </br>
> Ponadto, jeśli wprowadzono **jakiekolwiek zmiany** w aplikacji, należy ponownie opublikować aplikację, aby uzyskać najnowsze zmiany, które zostaną odzwierciedlone w pliku manifestu.

> [!NOTE]
> Jeśli występują problemy z publikowaniem aplikacji, a błąd kieruje się do umiejętności wyzwalających wyrażenia długości, należy ponownie sprawdzić konfigurację **włączonych poleceń dla umiejętności**. Każde uwidocznione polecenia musi mieć co najmniej jeden prawidłowy wyzwalacz wypowiedź.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aktualizowanie polecenia z poziomu klienta](./how-to-custom-commands-update-command-from-client.md)
