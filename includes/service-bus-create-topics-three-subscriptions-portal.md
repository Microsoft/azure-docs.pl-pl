---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183467"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Tworzenie tematu przy użyciu witryny Azure Portal
1. Na stronie **Przestrzeń nazw usługi Service Bus** wybierz pozycję **Tematy** z menu po lewej stronie.
2. Wybierz pozycję **+ Temat** na pasku narzędzi. 
4. Wprowadź **nazwę** tematu. Pozostaw inne opcje z wartościami domyślnymi.
5. Wybierz przycisk **Utwórz**.

    ![Tworzenie tematu](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Tworzenie subskrypcji w temacie
1. Wybierz **temat** utworzony w poprzedniej sekcji. 
    
    ![Wybieranie tematu](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Na stronie **Temat usługi Service Bus** wybierz pozycję **Subskrypcje** z menu po lewej stronie, a następnie wybierz pozycję **+ Subskrypcja** na pasku narzędzi. 
    
    ![Przycisk dodawania subskrypcji](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Na stronie **Tworzenie subskrypcji** wprowadź wartość **S1** jako **nazwę** subskrypcji, a następnie wybierz pozycję **Utwórz**. 

    ![Strona Tworzenie subskrypcji](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Powtórz poprzedni krok dwukrotnie, aby utworzyć subskrypcje o nazwach **S2** i **S3**.