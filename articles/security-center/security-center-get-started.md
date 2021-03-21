---
title: Uaktualnianie do usługi Azure Defender — Azure Security Center
description: W tym przewodniku szybki start przedstawiono, jak przeprowadzić uaktualnienie do usługi Azure Defender Security Center, aby uzyskać dodatkowe zabezpieczenia.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099356"
---
# <a name="quickstart-set-up-azure-security-center"></a>Szybki Start: Konfigurowanie Azure Security Center

Azure Security Center zapewnia ujednolicone Zarządzanie zabezpieczeniami i ochronę przed zagrożeniami w ramach obciążeń hybrydowych i obejmujących wiele chmur. Chociaż bezpłatne funkcje oferują ograniczone zabezpieczenia tylko dla zasobów platformy Azure, włączenie usługi Azure Defender rozszerza te możliwości do lokalnych i innych chmur. Usługa Azure Defender ułatwia znajdowanie i rozwiązywanie luk w zabezpieczeniach, stosowanie kontroli dostępu i aplikacji w celu blokowania złośliwych działań, wykrywanie zagrożeń przy użyciu analiz i analiz oraz szybkie reagowanie na ataki. Możesz bezpłatnie wypróbować usługę Azure Defender. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

Ten przewodnik Szybki Start przeprowadzi Cię przez wszystkie zalecane kroki umożliwiające włączenie Azure Security Center i usługi Azure Defender. Po zakończeniu wszystkich kroków przewodnika Szybki Start:

> [!div class="checklist"]
> * Security Center włączone w ramach subskrypcji platformy Azure
> * Usługa Azure Defender włączona w ramach subskrypcji platformy Azure
> * Automatyczne Konfigurowanie zbierania danych
> * Powiadomienia e-mail skonfigurowane pod kątem alertów zabezpieczeń
> * Hybrydowe i wielochmurowe maszyny połączone z platformą Azure

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Aby włączyć usługę Azure Defender w ramach subskrypcji, musisz mieć przypisaną rolę właściciela subskrypcji, współautora subskrypcji lub administratora zabezpieczeń.

## <a name="enable-security-center-on-your-azure-subscription"></a>Włączanie Security Center w ramach subskrypcji platformy Azure

> [!TIP]
> Aby włączyć Security Center we wszystkich subskrypcjach w grupie zarządzania, zobacz [włączanie Security Center w wielu subskrypcjach platformy Azure](onboard-management-group.md).

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. Z menu portalu wybierz pozycję **Security Center**. 

    Zostanie otwarta strona przeglądu Security Center.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Pulpit nawigacyjny przeglądu Security Center" lightbox="./media/security-center-get-started/overview.png":::

    Okno **Security Center — Przegląd** zapewnia ujednolicony wgląd w stan zabezpieczeń obciążeń chmury hybrydowej, dzięki czemu możesz odnajdywać i oceniać zabezpieczenia obciążeń oraz identyfikować i ograniczać ryzyko. Security Center automatycznie, bez żadnych kosztów, umożliwia korzystanie z subskrypcji platformy Azure, które nie zostały wcześniej dołączone przez Ciebie lub innego użytkownika subskrypcji.

Możesz wyświetlić i filtrować listę subskrypcji, wybierając element menu **subskrypcje** . Security Center dostosowuje wyświetlanie, aby odzwierciedlały stan zabezpieczeń wybranych subskrypcji. 

W ciągu kilku minut od uruchomienia Security Center po raz pierwszy zobaczysz:

- **Zalecenia** dotyczące sposobów zwiększania bezpieczeństwa połączonych zasobów.
- Spis zasobów, które są teraz oceniane przez Security Center, wraz z stan zabezpieczeń każdego z nich.

Aby w pełni wykorzystać możliwości Security Center, przejdź do następnych kroków sekcji Szybki Start.



## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start włączono Azure Security Center. Następnym krokiem jest włączenie usługi Azure Defender w celu zapewnienia ujednoliconego zarządzania zabezpieczeniami i ochrony przed zagrożeniami w ramach obciążeń chmury hybrydowej.

> [!div class="nextstepaction"]
> [Szybki Start: Włączanie usługi Azure Defender](enable-azure-defender.md)