---
title: Azure Communications Services — przykład wywołania sieci Web
titleSuffix: An Azure Communication Services sample
description: Informacje na temat przykładowego wywoływania sieci Web usług komunikacyjnych
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: aadecd3c57f5a145efd43058a5113205d7517c0b
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486407"
---
# <a name="get-started-with-the-web-calling-sample"></a>Wprowadzenie do przykładowego wywoływania sieci Web

Przykładem wywoływania sieci Web jest aplikacja sieci Web, która służy jako przewodnik krok po kroku dotyczący różnych możliwości zapewnianych przez bibliotekę klienta wywołującego usługi komunikacyjne w sieci Web.

Ten przykład został skompilowany dla deweloperów i ułatwia rozpoczęcie pracy z usługami komunikacyjnymi. Interfejs użytkownika jest podzielony na wiele sekcji, z których każdy zawiera przycisk "Pokaż kod", który umożliwia kopiowanie kodu bezpośrednio z przeglądarki do własnej aplikacji usług komunikacyjnych.

## <a name="get-started-with-the-web-calling-sample"></a>Wprowadzenie do przykładowego wywoływania sieci Web

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


> [!IMPORTANT]
> [Ten przykład jest dostępny w witrynie GitHub.](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Postępuj zgodnie z/Project/Readme.MD, aby skonfigurować projekt i uruchomić go lokalnie na komputerze.
Po uruchomieniu [przykładowego wywoływania sieci Web](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) na komputerze zostanie wyświetlona następująca strona docelowa:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Samouczek dotyczący wywoływania sieci Web 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Samouczek dotyczący wywoływania sieci Web 2" lightbox="./media/web-calling-tutorial-page-2.png":::

## <a name="user-provisioning-and-sdk-initialization"></a>Inicjowanie obsługi administracyjnej użytkowników i Inicjalizacja zestawu SDK

Kliknij pozycję "Inicjowanie obsługi użytkownika i inicjowanie zestawu SDK", aby zainicjować zestaw SDK przy użyciu tokenu obsługiwanego przez usługę aprowizacji tokenu wewnętrznej bazy danych. Ta usługa zaplecza znajduje się w `/project/webpack.config.js` .

Kliknij przycisk "Pokaż kod", aby zobaczyć przykładowy kod, którego możesz użyć we własnym rozwiązaniu.

Po zainicjowaniu zestawu SDK powinny zostać wyświetlone następujące elementy:

:::image type="content" source="./media/user-provisioning.png" alt-text="Aprowizowanie użytkowników" lightbox="./media/user-provisioning.png":::

Teraz możesz zacząć umieszczać wywołania przy użyciu zasobu usług komunikacyjnych.

## <a name="placing-and-receiving-calls"></a>Umieszczanie i otrzymywanie wywołań

Zestaw SDK wywoływania sieci Web usług komunikacyjnych umożliwia korzystanie z **1:1**, **1: N** i **grup** wywołań.

W przypadku połączeń wychodzących 1:1 lub 1: N można określić wiele tożsamości użytkowników usług komunikacyjnych do wywołania przy użyciu wartości rozdzielanych przecinkami. Można również określić, aby można było wywoływać tradycyjne numery telefonów (PSTN), korzystając z wartości rozdzielanych przecinkami.

Podczas wywoływania numerów telefonów PSTN należy określić alternatywny identyfikator obiektu wywołującego. Kliknij przycisk "Umieść połączenie", aby nawiązać połączenie wychodzące:

:::image type="content" source="./media/place-a-call.png" alt-text="Umieść wywołanie" lightbox="./media/place-a-call.png":::

Aby przyłączyć się do wywołania grupy, wprowadź identyfikator GUID, który identyfikuje wywołanie, i kliknij przycisk "Dołącz grupę":

:::image type="content" source="./media/join-a-group-call.png" alt-text="Dołącz do wywołania grupy" lightbox="./media/join-a-group-call.png":::

Kliknij przycisk "Pokaż kod", aby wyświetlić przykładowy kod służący do umieszczania wywołań, otrzymywania wywołań i sprzęgania wywołań grup.

Aktywne wywołanie wygląda następująco:

:::image type="content" source="./media/group-call.png" alt-text="Wywołanie grupy" lightbox="./media/group-call.png":::

Ten przykład zawiera również fragmenty kodu dla następujących możliwości:

  - Kliknij ikonę filmu wideo, aby włączyć/wyłączyć kamerę wideo
  - Kliknięcie ikony mikrofonu umożliwiającej włączenie/wyłączenie mikrofonu
  - Kliknięcie ikony odtwarzania w celu wstrzymania lub odłączenia wywołania
  - Kliknięcie ikony ekranu, aby uruchomić/zatrzymać udostępnianie ekranu
  - Kliknięcie ikony osoby, aby dodać uczestnika do wywołania
  - Klikanie pozycji "Usuń uczestnika" w spisie uczestników, aby usunąć określonego uczestnika z wywołania


## <a name="next-steps"></a>Następne kroki

>[!div class="nextstepaction"]
>[Pobierz przykład z witryny GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z [pomocą biblioteki klienta wywołującego](../quickstarts/voice-video-calling/calling-client-samples.md)
- Dowiedz się więcej o [sposobie wywoływania programu](../concepts/voice-video-calling/about-call-types.md)
- Zapoznaj się z dokumentacją [interfejsu API](/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js)
- Zapoznaj się z przykładem [aplikacji Contoso Med](https://github.com/Azure-Samples/communication-services-contoso-med-app)

## <a name="additional-reading"></a>Materiały uzupełniające

- [Azure Communication GitHub](https://github.com/Azure/communication) — Znajdź więcej przykładów i informacji na oficjalnej stronie GitHub
- [Redux](https://redux.js.org/) — zarządzanie stanem po stronie klienta
- [FluentUI](https://aka.ms/fluent-ui) — Biblioteka interfejsu użytkownika zasilanego przez firmę Microsoft
- [Reagowanie](https://reactjs.org/) — Biblioteka do tworzenia interfejsów użytkownika
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) — platforma do tworzenia aplikacji sieci Web
