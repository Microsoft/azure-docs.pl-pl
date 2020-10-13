---
title: 'ML Studio (klasyczny): Tworzenie punktów końcowych usługi sieci Web — Azure'
description: Utwórz punkty końcowe usługi sieci Web w Azure Machine Learning Studio (klasyczny). Każdy punkt końcowy w usłudze sieci Web jest niezależnie kierowany, ograniczany i zarządzany.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 5f06374e67d1aad689662c77084fa593bb09c8ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362473"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Utwórz punkty końcowe dla wdrożonych usług sieci Web Azure Machine Learning Studio (klasycznych)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


> [!NOTE]
> W tym temacie opisano techniki mające zastosowanie do **klasycznej** usługi sieci Web Machine Learning.

Po wdrożeniu usługi sieci Web dla tej usługi tworzony jest domyślny punkt końcowy. Domyślny punkt końcowy może być wywoływany przy użyciu właściwego dla niego klucza interfejsu API. Możesz dodać więcej punktów końcowych z własnymi kluczami z portalu usług sieci Web.
Każdy punkt końcowy w usłudze sieci Web jest niezależnie kierowany, ograniczany i zarządzany. Każdy punkt końcowy jest unikatowym adresem URL z kluczem autoryzacji, który można dystrybuować do klientów.

## <a name="add-endpoints-to-a-web-service"></a>Dodawanie punktów końcowych do usługi sieci Web

Punkt końcowy można dodać do usługi sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning. Po utworzeniu punktu końcowego można go wykorzystać za pomocą synchronicznych interfejsów API, interfejsów API wsadowych i arkuszy programu Excel.

> [!NOTE]
> Jeśli dodano dodatkowe punkty końcowe do usługi sieci Web, nie można usunąć domyślnego punktu końcowego.

1. W Machine Learning Studio (klasyczny), w lewej kolumnie nawigacyjnej kliknij pozycję usługi sieci Web.
2. W dolnej części pulpitu nawigacyjnego usługi sieci Web kliknij pozycję **Zarządzaj punktami końcowymi**. Portal usług sieci Web Azure Machine Learning zostanie otwarty na stronie punkty końcowe usługi sieci Web.
3. Kliknij pozycję **Nowy**.
4. Wpisz nazwę i opis nowego punktu końcowego. Nazwy punktów końcowych muszą składać się z co najmniej 24 znaków i muszą składać się z małych liter lub cyfr. Wybierz poziom rejestrowania i czy włączono przykładowe dane. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [Włączanie rejestrowania dla usług sieci web Machine Learning](web-services-logging.md).

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a> Skalowanie usługi sieci Web przez dodanie dodatkowych punktów końcowych

Domyślnie każda opublikowana usługa sieci Web jest skonfigurowana do obsługi 20 współbieżnych żądań i może mieć maksymalnie 200 współbieżnych żądań. Azure Machine Learning Studio (klasyczny) automatycznie optymalizuje ustawienie, aby zapewnić najlepszą wydajność usługi sieci Web, a wartość portalu jest ignorowana.

Jeśli planujesz wywołać interfejs API o wyższym obciążeniu niż wartość maksymalna liczba współbieżnych wywołań 200, będzie można utworzyć wiele punktów końcowych w tej samej usłudze sieci Web. Następnie można losowo rozłożyć obciążenie na wszystkie z nich.

Skalowanie usługi sieci Web jest typowym zadaniem. Niektóre z powodów skalowania to obsługa ponad 200 współbieżnych żądań, zwiększania dostępności za pomocą wielu punktów końcowych lub udostępniania oddzielnych punktów końcowych dla usługi sieci Web. Możesz zwiększyć skalę, dodając dodatkowe punkty końcowe dla tej samej usługi sieci Web za pomocą portalu [usługi sieci web Azure Machine Learning](https://services.azureml.net/) .

Należy pamiętać, że użycie wysokiej współbieżności może być niekorzystne, jeśli interfejs API nie jest wywoływany z odpowiadającą im wysoką szybkością. Czasami można zobaczyć sporadyczne przekroczenia limitu czasu i/lub opóźnienia w przypadku nadmiernego niskiego obciążenia interfejsu API skonfigurowanego do dużego obciążenia.

Synchroniczne interfejsy API są zwykle używane w sytuacjach, gdy jest potrzebne małe opóźnienia. Opóźnienie w tym miejscu oznacza czas potrzebny na ukończenie jednego żądania przez interfejs API i nie uwzględnia żadnych opóźnień sieci. Załóżmy, że masz interfejs API z opóźnieniem 50-ms. Aby w pełni wykorzystać dostępną pojemność z poziomem dławienia High i Max współbieżnych wywołań = 20, należy wywołać ten interfejs API 20 * 1000/50 = 400 razy na sekundę. W ten sposób Maksymalna liczba współbieżnych wywołań 200 umożliwia wywołanie interfejsu API 4000 razy na sekundę, przy założeniu opóźnienia 50-ms.

## <a name="next-steps"></a>Następne kroki

[Jak korzystać z usługi sieci web Azure Machine Learning](consume-web-services.md).
