---
title: Zarządzanie kontem Video Indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak zarządzać kontem Video Indexer połączonym z platformą Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79499669"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Zarządzanie kontem usługi Video Indexer połączonym z platformą Azure

W tym artykule pokazano, jak zarządzać kontem Video Indexer połączonym z subskrypcją platformy Azure i kontem Azure Media Services.

> [!NOTE]
> Musisz być właścicielem konta Video Indexer, aby dostosować konfigurację konta omówione w tym temacie.

## <a name="prerequisites"></a>Wymagania wstępne

Połącz konto Video Indexer z platformą Azure zgodnie z opisem w artykule [połączono z platformą Azure](connect-to-azure.md).

Upewnij się, że [spełniono wymagania wstępne](connect-to-azure.md#prerequisites) i zapoznaj się z [zagadnieniami](connect-to-azure.md#considerations) w artykule.

## <a name="examine-account-settings"></a>Sprawdzanie ustawień konta

Ta sekcja bada ustawienia konta Video Indexer.

Aby wyświetlić ustawienia:

1. Kliknij ikonę użytkownika w prawym górnym rogu, a następnie wybierz pozycję **Ustawienia**.

    ![Ustawienia w Video Indexer](./media/manage-account-connected-to-azure/select-settings.png)

2. Na stronie **Ustawienia** wybierz kartę **konto** .

Jeśli konto indeksatora wideo jest połączone z platformą Azure, zobaczysz następujące kwestie:

* Nazwa podstawowego konta Azure Media Services.
* Liczba zadań indeksowania uruchomionych i umieszczonych w kolejce.
* Liczba i typ przydzielonej jednostki zarezerwowanej.

Jeśli Twoje konto wymaga korekt, zobaczysz odpowiednie błędy i ostrzeżenia dotyczące konfiguracji konta na stronie **Ustawienia** . Komunikaty zawierają linki do dokładnych miejsc w Azure Portal, w których należy wprowadzić zmiany. Aby uzyskać więcej informacji, zobacz sekcję [błędy i ostrzeżenia](#errors-and-warnings) poniżej.

## <a name="repair-the-connection-to-azure"></a>Naprawianie połączenia z platformą Azure

W oknie dialogowym **Aktualizowanie połączenia do Azure Media Services** strony [Video Indexer](https://www.videoindexer.ai/) zostanie wyświetlony monit o podanie wartości dla następujących ustawień:

|Ustawienie|Opis|
|---|---|
|Identyfikator subskrypcji platformy Azure|Identyfikator subskrypcji można pobrać z Azure Portal. Kliknij pozycję **wszystkie usługi** w lewym panelu i wyszukaj frazę "subskrypcje". Wybierz pozycję **subskrypcje** i wybierz żądany identyfikator z listy subskrypcji.|
|Nazwa grupy zasobów Azure Media Services|Nazwa grupy zasobów, w której utworzono konto Media Services.|
|Identyfikator aplikacji|Identyfikator aplikacji usługi Azure AD (z uprawnieniami dla określonego konta Media Services) utworzonym dla tego konta Video Indexer. <br/><br/>Aby uzyskać identyfikator aplikacji, przejdź do Azure Portal. W obszarze konto Media Services wybierz swoje konto i przejdź do pozycji **dostęp do interfejsu API**. Wybierz pozycję **Połącz z interfejsem API Media Services przy użyciu jednostki usługi**  ->  **aplikacja usługi Azure AD**. Skopiuj odpowiednie parametry.|
|Klucz aplikacji|Klucz aplikacji usługi Azure AD skojarzony z Twoim kontem Media Services określonym powyżej. <br/><br/>Aby uzyskać klucz aplikacji, przejdź do Azure Portal. W obszarze konto Media Services wybierz swoje konto i przejdź do pozycji **dostęp do interfejsu API**. Wybierz pozycję **Połącz z interfejsem API Media Services przy użyciu nazwy głównej usługi**  ->  **Zarządzaj**  ->  **certyfikatami aplikacji &** Secret. Skopiuj odpowiednie parametry.|

## <a name="autoscale-reserved-units"></a>Jednostki zarezerwowane autoskalowania

Strona **Ustawienia** umożliwia ustawienie automatycznego skalowania jednostek zarezerwowanych multimediów (ru). Jeśli opcja jest **włączona**, można przydzielić maksymalną liczbę jednostek ru i upewnić się, że Video Indexer zatrzyma/uruchamia jednostek ru automatycznie. W przypadku tej opcji nie należy uiszczać dodatkowych opłat za czas bezczynności, ale również nie czekać na ukończenie zadań indeksowania, gdy obciążenie indeksowania jest wysokie.

Skalowanie automatyczne nie jest skalowane poniżej 1 RU ani powyżej domyślnego limitu konta Media Services. Aby zwiększyć limit, Utwórz żądanie obsługi. Aby uzyskać informacje na temat przydziałów i ograniczeń oraz sposobu otwierania biletu pomocy technicznej, zobacz [limity przydziału i ograniczenia](../../media-services/previous/media-services-quotas-and-limitations.md).

![Automatyczne skalowanie jednostek zarezerwowanych Video Indexer](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia

Jeśli Twoje konto wymaga pewnych dostosowań, zobaczysz odpowiednie błędy i ostrzeżenia dotyczące konfiguracji konta na stronie **Ustawienia** . Komunikaty zawierają linki do dokładnych miejsc w Azure Portal, w których należy wprowadzić zmiany. Ta sekcja zawiera więcej szczegółów o komunikatach o błędach i ostrzeżeniach.

* EventGrid

    Należy zarejestrować dostawcę zasobów EventGrid za pomocą Azure Portal. W [Azure Portal](https://portal.azure.com/)przejdź do pozycji **subskrypcje** > [subskrypcja] > **ResourceProviders**  >  **Microsoft. EventGrid**. Jeśli nie jest w **zarejestrowanym** stanie, wybierz pozycję **zarejestruj**. Rejestracja może potrwać kilka minut.

* Punkt końcowy przesyłania strumieniowego

    Upewnij się, że konto bazowe Media Services ma domyślny **punkt końcowy przesyłania strumieniowego** w stanie uruchomienia. W przeciwnym razie nie możesz oglądać filmów wideo z tego konta Media Services ani Video Indexer.

* Jednostki zarezerwowane multimediów

    Aby można było indeksować wideo, należy przydzielić jednostki zarezerwowane multimediów w ramach zasobu usługi multimediów. Aby zapewnić optymalną wydajność indeksowania, zaleca się przydzielenie co najmniej 10 jednostek zarezerwowanych S3. Aby uzyskać informacje o cenach, zapoznaj się z sekcją często zadawanych pytań na stronie [cennika Media Services](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>Następne kroki

Możesz programowo korzystać z konta próbnego lub Video Indexer kont, które są połączone z platformą Azure, postępując zgodnie z instrukcjami w temacie: [Korzystanie z interfejsów API](video-indexer-use-apis.md).

Użyj tego samego użytkownika usługi Azure AD, który był używany podczas nawiązywania połączenia z platformą Azure.
