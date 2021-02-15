---
title: 'ML Studio (klasyczny): Włączanie rejestrowania usługi sieci Web — Azure'
description: Dowiedz się, jak włączyć rejestrowanie dla usług sieci Web Machine Learning Studio (klasycznych). Rejestrowanie zawiera dodatkowe informacje ułatwiające rozwiązywanie problemów z interfejsami API.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
ms.date: 06/15/2017
ms.openlocfilehash: c9ce9b7b0d739301e76abd43b265fe28195ed302
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518287"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Włącz rejestrowanie dla usług sieci Web Azure Machine Learning Studio (klasycznych)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Ten dokument zawiera informacje na temat możliwości rejestrowania usług sieci Web Machine Learning Studio (klasycznych). Rejestrowanie zawiera dodatkowe informacje, po których tylko numer błędu i komunikat mogą pomóc w rozwiązywaniu problemów z wywołaniami do interfejsów API Machine Learning Studio (klasycznego).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Jak włączyć rejestrowanie dla usługi sieci Web

Rejestrowanie można włączyć z poziomu portalu [usług sieci Web Azure Machine Learning Studio (klasycznego)](https://services.azureml.net) . 

1. Zaloguj się do portalu usług sieci Web Azure Machine Learning Studio (klasyczny) pod adresem [https://services.azureml.net](https://services.azureml.net) . W przypadku klasycznej usługi sieci Web można również przejść do portalu, klikając pozycję **nowe usługi sieci Web** na stronie usług sieci Web Machine Learning Studio (klasycznej) w programie Studio (klasyczny).

   ![Link nowego środowiska usług sieci Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na górnym pasku menu kliknij pozycję **usługi sieci Web** dla nowej usługi sieci Web lub kliknij pozycję **klasyczne usługi sieci Web** dla klasycznej usługi sieci Web.

   ![Wybieranie nowych lub klasycznych usług sieci Web](./media/web-services-logging/select-web-service.png)

3. W przypadku nowej usługi sieci Web kliknij nazwę usługi sieci Web. W przypadku klasycznej usługi sieci Web kliknij nazwę usługi sieci Web, a następnie na następnej stronie kliknij odpowiedni punkt końcowy.

4. Na górnym pasku menu kliknij pozycję **Konfiguruj**.

5. Ustaw opcję **Włącz rejestrowanie** na *błąd* (aby rejestrować tylko błędy) lub *wszystkie* (w celu pełnego rejestrowania).

   ![Wybierz poziom rejestrowania](./media/web-services-logging/enable-logging.png)

6. Kliknij pozycję **Zapisz**.

7. W przypadku klasycznych usług sieci Web Utwórz kontener **"ml-Diagnostics"** .

   Wszystkie dzienniki usługi sieci Web są przechowywane w kontenerze obiektów BLOB o nazwie **ml-Diagnostics** na koncie magazynu skojarzonym z usługą sieci Web. W przypadku nowych usług sieci Web ten kontener jest tworzony podczas pierwszego dostępu do usługi sieci Web. W przypadku klasycznych usług sieci Web należy utworzyć kontener, jeśli jeszcze nie istnieje. 

   1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu skojarzonego z usługą sieci Web.

   2. W obszarze **BLOB Service** kliknij pozycję **Containers (kontenery**).

   3. Jeśli kontener Container **-Diagnostics** nie istnieje, kliknij pozycję **+ kontener**, nadaj kontenerowi nazwę "ml-Diagnostics" i wybierz **Typ dostępu** jako "BLOB". Kliknij przycisk **OK**.

      ![Utwórz nowy kontener do przechowywania dzienników diagnostycznych](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> W przypadku klasycznej usługi sieci Web pulpit nawigacyjny usług sieci Web w Machine Learning Studio (klasyczny) ma również przełącznik umożliwiający włączenie rejestrowania. Ponieważ jednak rejestrowanie jest teraz zarządzane za pomocą portalu usług sieci Web, należy włączyć rejestrowanie w portalu, zgodnie z opisem w tym artykule. Jeśli włączono już rejestrowanie w programie Studio (klasyczne), w portalu usług sieci Web Wyłącz rejestrowanie i włącz je ponownie.


## <a name="the-effects-of-enabling-logging"></a>Skutki włączenia rejestrowania
Po włączeniu rejestrowania Diagnostyka i błędy w punkcie końcowym usługi sieci Web są rejestrowane w kontenerze obiektów BLOB **diagnostyki ml** na koncie usługi Azure Storage połączonym z obszarem roboczym użytkownika. Ten kontener zawiera wszystkie informacje diagnostyczne dla wszystkich punktów końcowych usługi sieci Web dla wszystkich obszarów roboczych skojarzonych z tym kontem magazynu.

Dzienniki można przeglądać przy użyciu dowolnego z kilku dostępnych narzędzi do eksplorowania konta usługi Azure Storage. Najłatwiejszym rozwiązaniem może być przechodzenie do konta magazynu w Azure Portal, kliknij pozycję **kontenery**, a następnie kliknij kontener **ml — Diagnostyka**.  

## <a name="log-blob-detail-information"></a>Informacje szczegółowe dotyczące dziennika obiektów BLOB
Każdy obiekt BLOB w kontenerze przechowuje informacje diagnostyczne dla dokładnie jednej z następujących akcji:

* Wykonywanie metody Batch-Execution  
* Wykonywanie metody Request-Response  
* Inicjowanie kontenera Request-Response

Nazwa każdego obiektu BLOB ma prefiks następującej postaci: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Gdzie _Typ dziennika_ jest jedną z następujących wartości:  

* partia  
* wyniki/żądania  
* wynik/init