---
author: baanders
description: Dołączanie pliku dla samouczków Digital bliźniaczych reprezentacji systemu Azure — wymagania wstępne w celu skonfigurowania wystąpienia
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 302a478dd32c93a555bb17e9d7d0f60bd7859a50
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91292714"
---
### <a name="prepare-an-azure-digital-twins-instance"></a>Przygotowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

Do ukończenia tego samouczka potrzebne jest **wystąpienie usługi Azure Digital bliźniaczych reprezentacji** . Jeśli masz już skonfigurowane wystąpienie usługi Azure Digital bliźniaczych reprezentacji na podstawie poprzedniej pracy, możesz użyć tego wystąpienia.

* W przeciwnym razie **Skonfiguruj wystąpienie i uwierzytelnianie** , korzystając z instrukcji podanymi w temacie [*How to: Set a instance and Authentication*](../articles/digital-twins/how-to-set-up-instance-portal.md). Instrukcje zawierają również kroki umożliwiające zweryfikowanie, czy każdy krok został pomyślnie ukończony i jest gotowy do przejścia do korzystania z nowego wystąpienia.

W tym samouczku podczas konfigurowania wystąpienia będą potrzebne następujące wartości. Aby ponownie zebrać te wartości, Skorzystaj z poniższych linków, aby znaleźć je w [Azure Portal](https://portal.azure.com).
* **_Nazwa_** wystąpienia bliźniaczych reprezentacji cyfrowych platformy Azure ([Znajdź w portalu](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* **_Nazwa hosta_** wystąpienia usługi Azure Digital bliźniaczych reprezentacji ([Znajdź w portalu](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Identyfikator aplikacji rejestracji aplikacji usługi Azure AD **_(klient)_** ([Znajdź w portalu](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))
* Identyfikator rejestracji w usłudze Azure AD ( **_dzierżawa)_** ([Znajdź w portalu](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))