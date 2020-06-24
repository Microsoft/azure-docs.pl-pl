---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 537c86512db4706077d0089a4f71ee945623c26b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081430"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurowanie centrum powiadomień przy użyciu informacji o usłudze APNS

W obszarze **usługi powiadomień**wybierz pozycję **Apple** , a następnie postępuj zgodnie z odpowiednimi instrukcjami w sekcji [Tworzenie certyfikatu dla Notification Hubs](#creating-a-certificate-for-notification-hubs) .  

> [!NOTE]
> Użyj środowiska **produkcyjnego** w **trybie aplikacji** tylko wtedy, gdy chcesz wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu.

### <a name="option-1-using-a-p12-push-certificate"></a>Opcja 1: używanie certyfikatu wypychania z. p12

1. Wybierz pozycję **Certyfikat**.

1. Wybierz ikonę pliku.

1. Wybierz wyeksportowany wcześniej plik. P12, a następnie wybierz pozycję **Otwórz**.

1. W razie potrzeby określ poprawne hasło.

1. Wybierz tryb **Piaskownica**.

1. Wybierz pozycję **Zapisz**.

### <a name="option-2-using-token-based-authentication"></a>Opcja 2: korzystanie z uwierzytelniania opartego na tokenach

1. Wybierz pozycję **token**.
1. Wprowadź następujące wartości, które zostały nabyte wcześniej:

    - **Identyfikator klucza**
    - **Identyfikator pakietu**
    - **Identyfikator zespołu**
    - **Token**

1. Wybierz **piaskownicę**.
1. Wybierz pozycję **Zapisz**.
